---
title: "GMiner: Tìm kiếm luật kết hợp Apriori sử dụng GPU (Phần 2)"
date: 2025-02-23
categories: [Sharing Knowledge]
tags: "Apriori Gminer"
author: Nguyen The Vu
math: true
---
Ở [phần trước](https://nrhevu.github.io/posts/apriori-gpu/) ta đã tìm hiểu về thuật toán Apriori trong tìm kiếm luật kết hợp, và tổng quan thuật toán Gminer. Bây giờ hãy cùng mình đi vào từng thành phần của thuật toán nhé.

## **Chiến lược "Traversal from the First Level" (TFL)**

Thuật toán TFL (Traversal from the First Level) được triển khai qua 7
bước chính. Thuật toán được mô tả qua hình vẽ dưới.
Dưới đây là giải thích chi tiết từng bước:

1.  Biến đổi tập mục được sinh ra thành dạng Relative Memory Address
    (RA) sử dụng một từ điển. Nếu tập RA được sinh ra lớn hơn bộ đệm GPU
    dành cho RABuf thì RA sẽ được chia ra thành Q phần

2.  Chuyển tập mục được mã hóa vào bộ đệm của GPU (RA -\> RABuf)

3.  Tập mục thường xuyên 1-itemset (F1) sẽ được biểu diễn dưới dạng
    bitmap giao dịch. Cấu trúc bitmap cho phép thực hiện các phép toán
    bitwise hiệu quả trên GPU, chẳng hạn như phép AND để kiểm tra hỗ
    trợ. Sau đó từng khối giao dịch sẽ được cắt ra theo chiều dọc và đưa
    vào bộ đệm TBBuf trong GPU.

4.  Một bộ nhân GPU sẽ được sử dụng để thực hiện phép toán trên bit AND
    ở TBBuf, nhằm mục đích tổng hợp các khối tương tác của các sản phẩm
    chỉ định để ra được biểu diễn bit của các tương tác xuất hiện chung
    các sản phẩm bitV.

5.  Tính toán ra được những độ hỗ trợ thành phần của các itemsets trong
    RABuf và lưu vào PSBuf.

6.  Thuật toán sao chép các độ hỗ trợ thành phần trong PSBuf vào một
    mảng PSArray ở bộ nhớ chính.

7.  Tổng hợp các độ hỗ trợ thành phần trong PSArray để tính toán ra độ
    hỗ trợ của các tập mục thường xuyên $\sigma(x)$. Sau đó thuật toán
    sẽ tìm những tập mục thường xuyên L-itemsets $F_{L}$ là những tập có
    độ hỗ trợ lớn hơn giá trị minsup.

![alt text](/assets/images/gminer/TFL.png)
Thuật toán TFL

## **Hàm lõi (Kernel Function)**

Trong thuật toán TFL và HIL đều có sự góp mặt của hàm lõi Kernel
Function. Hàm Kernel có 2 chức năng

1.  Tính toán độ hỗ trợ thành phần bằng cách thực hiện phép toán bitwise
    AND trên các bitmap để xác định số lượng giao dịch chứa tất cả các
    mục trong một tập mục ứng viên.

2.  Sử dụng kỹ thuật parallel reduction để tổng hợp hỗ trợ từ các
    threads trên cùng một GPU block.

Kernel Function được thực hiện như sau và thể hiện qua
hình vẽ dưới.

**Tham số đầu vào của Kernel Function**

1.  **$RA_{j}$**:

    -   Phân vùng ( j )-th của các địa chỉ bộ nhớ tương đối (Relative
        Addresses) cho các tập mục ứng viên.

2.  **$TB_{k}$**:

    -   Khối giao dịch ( k )-th của Transaction Bitmap.

3.  *doneIdx*:

    -   Chỉ số của ứng viên cuối cùng đã được xử lý trong ( $RA_{j}$ ).

4.  *maxThr*:

    -   Số threads tối đa trong một GPU block.

** Các bước xử lý **\
**1. Kiểm tra điều kiện xử lý**

-   Nếu GPU block hiện tại không được gán bất kỳ tập mục ứng viên nào
    (dựa trên chỉ số ( doneIdx + BID )), hàm kernel sẽ kết thúc ngay lập
    tức.

-   Điều này tránh lãng phí tài nguyên GPU khi không có công việc cần
    thực hiện.

**Bước 2: Chuẩn bị biến tạm thời trong Shared Memory**

-   Hai biến chính:

    1.  **can**: Lưu tập mục ứng viên mà block hiện tại đang xử lý.

    2.  **sup**: Một mảng chứa hỗ trợ tạm thời, mỗi phần tử do một
        thread quản lý.

-   Các biến này được lưu trữ trong **shared memory** để tăng tốc độ
    truy cập so với việc sử dụng global memory.

**Bước 3: Tính toán hỗ trợ tạm thời**

-   Lặp qua các phần của ( TB_k ):

    1.  Mỗi thread thực hiện phép toán bitwise AND trên các bitmap tương
        ứng với các mục trong tập mục ứng viên ( can ).

    2.  Kết quả của phép AND được lưu vào biến tạm ( bitV ).

    3.  Sử dụng hàm **popCount** (hoặc ( popc ) trong CUDA) để đếm số
        lượng bit 1 trong ( bitV ), biểu thị số giao dịch khớp.

**Bước 4: Tổng hợp kết quả hỗ trợ**

-   Sử dụng kỹ thuật **parallel reduction** để tổng hợp các giá trị hỗ
    trợ từ tất cả các threads trong block.

-   Kết quả cuối cùng được ghi vào buffer hỗ trợ ( PSBuf ) trong GPU
    global memory.

![alt text](/assets/images/gminer/kernel.png)
Kernel Function

## **Chiến lược "Hopping from the Intermediate Level" (HIL)**

HIL là một chiến lược bổ sung trong thuật toán GMiner nhằm tăng hiệu
suất xử lý khi tập mục thường xuyên có độ dài lớn, bằng cách khai thác
hiệu quả hơn bộ nhớ GPU và giảm số lượng phép tính.

Chiến lược HIL dựa trên ý tưởng:

1.  **Chia nhỏ dữ liệu giao dịch**:

    -   Dữ liệu được chia thành các khối nhỏ hơn gọi là **Fragment
        Blocks**, mỗi khối đại diện cho một phần giao dịch nhỏ hơn trong
        Transaction Block.

    -   Mỗi **Fragment Block** lưu trữ không chỉ các mục thường xuyên
        cấp 1 mà còn các tập mục thường xuyên cấp thấp (tức là từ cấp 2
        đến ( H )).

2.  **Tận dụng dữ liệu tiền xử lý**:

    -   Thay vì tính toán lại tất cả các tập mục thường xuyên ở mỗi cấp,
        HIL sử dụng các kết quả đã tính sẵn từ các Fragment Blocks.

**Các bước thực hiện của chiến lược HIL:**

1.  Chia nhỏ các Transaction Block thành Fragment Blocks

-   Mỗi **Transaction Block** ( $\text{TB}_{k}$ ) được chia thành nhiều
    **Fragment Blocks** ( $\text{TB}_{k,l}$ ).

-   Một **Fragment Block** lưu trữ:

    -   Bitmap của các mục thường xuyên cấp 1.

    -   Bitmap của các tập mục thường xuyên cấp thấp (dưới cấp ( H )).

-   Kích thước mỗi Fragment Block được xác định bởi tham số ( H ), số
    mục thường xuyên tối đa trong mỗi khối.

2.  Xây dựng và lưu trữ các Fragment Blocks

-   Với mỗi Fragment Block:

    -   Tạo các bitmap của tất cả các tập mục thường xuyên trong khối.

    -   Lưu các bitmap này vào GPU memory hoặc main memory.

-   Kỹ thuật **nested-loop streaming** được sử dụng để tính toán nhanh
    các Fragment Blocks song song trên GPU.

3.  Lựa chọn các Fragment Blocks cần thiết

-   Tại mỗi cấp ( L ), chỉ lựa chọn các Fragment Blocks có liên quan đến
    tập ứng viên ( $C_{L}$ ).

-   Điều này giảm đáng kể kích thước dữ liệu cần truyền từ main memory
    vào GPU memory.

4.  Tính toán hỗ trợ (Support)

-   Sử dụng các Fragment Blocks đã chọn thay vì toàn bộ Transaction
    Block.

-   Kỹ thuật bitwise AND vẫn được áp dụng, nhưng với số phép toán ít hơn
    do các Fragment Blocks đã lưu trữ sẵn các kết quả trung gian.

![alt text](/assets/images/gminer/hil.png)
Thuật toán HIL

## Khai thác tối ưu GPU

Thuật toán này đã khai tác tối ưu GPU như sau:

**1. Cấp phát bộ nhớ GPU hợp lý**

-   **Bộ đệm sử dụng lại (Reusable Buffers)**:

    -   Thuật toán chỉ cấp phát các bộ đệm ( TBBuf ), ( RABuf ), và (
        PSBuf ) một lần trong GPU global memory.

    -   Các bộ đệm này được tái sử dụng cho toàn bộ quá trình khai thác
        dữ liệu, thay vì cấp phát và giải phóng nhiều lần, giúp giảm chi
        phí quản lý bộ nhớ.

-   **Chia nhỏ dữ liệu để tránh vượt bộ nhớ (Out-of-Memory)**:

    -   Nếu dữ liệu lớn hơn kích thước bộ đệm GPU, thuật toán chia nhỏ
        dữ liệu thành các khối con, truyền lần lượt vào bộ nhớ GPU.

    -   Như vậy, thuật toán đảm bảo luôn hoạt động trong giới hạn bộ nhớ
        của GPU.

**2. Sử dụng Shared Memory**

-   **Tăng tốc truy cập dữ liệu**:

    -   GPU global memory chậm hơn so với shared memory. Vì vậy, thuật
        toán sử dụng shared memory để lưu trữ các biến truy cập thường
        xuyên, như:

        -   Số lượng bit được đếm trong các vector bit (( bitV )).

        -   Giá trị hỗ trợ tạm thời (( sup )).

-   **Hạn chế truy cập bộ nhớ toàn cục**:

    -   Thuật toán chỉ truy cập global memory một lần để ghi giá trị hỗ
        trợ vào ( PSBuf ) sau khi hoàn thành tính toán.

**3. Cấu hình các GPU Threads**

-   **Phân phối công việc hợp lý**:

    -   GPU hoạt động theo mô hình SIMT (Single Instruction Multiple
        Threads), với các thread xử lý song song dữ liệu.

    -   Mỗi block GPU xử lý một tập itemset và sử dụng ( maxThr )
        threads để thực hiện các phép toán bitwise AND trên các bitmap.

    -   Ví dụ: Với ( maxThr = 32 ), mỗi thread xử lý 32 bits của bitmap
        cùng lúc.

-   **Sử dụng song song nhiều GPU Blocks**:

    -   Thuật toán chia số lượng itemsets thành các nhóm nhỏ để mỗi
        block GPU xử lý riêng biệt.

    -   Tối ưu hóa thông qua số lượng threads và blocks được cấu hình,
        giúp khai thác tối đa khả năng xử lý song song.

**4. Truyền tải dữ liệu bất đồng bộ (Asynchronous Streaming)**

-   **Giảm độ trễ truyền tải**:

    -   Thuật toán sử dụng nhiều luồng GPU bất đồng bộ (asynchronous
        streams), cho phép chồng chéo các hoạt động:

        -   Truyền dữ liệu từ CPU sang GPU.

        -   Thực thi kernel trên GPU.

        -   Truyền kết quả từ GPU về CPU.

-   **Ví dụ**:

    -   Khi một khối giao dịch (( TB_k )) đang được xử lý trên GPU, khối
        tiếp theo (( $\text{TB}_{k + 1}$ )) có thể được truyền vào GPU
        song song.

**5. Tối ưu hóa các phép toán trong Kernel**

-   **Tính toán hiệu quả**:

    -   GPU thực hiện các phép toán bitwise AND trên các bitmap để tính
        hỗ trợ cho các itemset.

    -   Hàm kernel sử dụng thuật toán giảm (parallel reduction) để tổng
        hợp kết quả hỗ trợ từ các threads trong cùng một block.

-   **Ví dụ chi tiết**:

    -   Một itemset ( x ) với ( n ) mục:

        -   Kernel thực hiện ( n-1 ) phép AND trên các bitmap tương ứng.

        -   Sử dụng hàm ( popCount ) để đếm số lượng bit 1, tính hỗ trợ
            của ( x ).

**6. Sử dụng nhiều GPU**

-   **Chia sẻ dữ liệu bitmap (Transaction Bitmap Sharing)**:

    -   Các GPU chia sẻ cùng một transaction bitmap, nhưng mỗi GPU xử lý
        một tập hợp itemset khác nhau (( RA_j )).

    -   Điều này giảm thiểu mất cân bằng tải và tối ưu hiệu suất khi sử
        dụng nhiều GPU.

-   **Tăng tuyến tính hiệu suất**:

    -   Khi tăng số lượng GPU, hiệu suất tính toán tăng gần như tuyến
        tính nhờ việc phân phối công việc đồng đều.

# Tài liệu tham khảo 
[1] M. Hegland, “The apriori algorithm–a tutorial”, Mathematics and computation in imaging science and information processing, tr 209–262, 2007.

[2] K. Chon, S.-H. Hwang, và M. Kim, “GMiner: A Fast GPU-based Frequent Itemset Mining Method for Large-scale Data”, Information Sciences, vol 439, tr , 2018, doi: 10.1016/j.ins.2018.01.046 .
