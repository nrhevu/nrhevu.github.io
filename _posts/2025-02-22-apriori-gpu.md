---
title: "GMiner: Tìm kiếm luật kết hợp Apriori sử dụng GPU (Phần 1)"
date: 2025-02-21
categories: [Sharing Knowledge]
tags: "Apriori Gminer"
author: Nguyen The Vu
math: true
---

Luật kết hợp (Association Rule) là một trong những kỹ thuật khai phá dữ liệu quan trọng, được sử dụng để tìm kiếm các mối quan hệ ẩn trong tập dữ liệu lớn. Các quy luật này giúp phát hiện những liên kết giữa các mục dữ liệu, chẳng hạn như mối quan hệ giữa các sản phẩm trong giỏ hàng của khách hàng. Việc khai phá luật kết hợp có ứng dụng rộng rãi trong thương mại điện tử, y tế, tài chính và nhiều lĩnh vực khác, nhằm hỗ trợ các quyết định dựa trên dữ liệu.

Tuy nhiên, việc tìm kiếm luật kết hợp yêu cầu xử lý lượng dữ liệu lớn, đòi hỏi thời gian tính toán dài, đặc biệt khi dữ liệu có kích thước lớn hoặc khi yêu cầu độ chính xác cao. Sử dụng GPU (Graphics Processing Unit) với khả năng xử lý song song cao, đã trở thành một phương pháp tối ưu để tăng tốc độ khai phá luật kết hợp, giúp giảm thời gian tính toán và nâng cao hiệu quả xử lý.

# Thuật toán Apriori
Thuật toán Apriori là một trong những thuật toán phổ biến nhất để khai phá luật kết hợp. Mục tiêu của Apriori là tìm ra các tập mục thường xuất hiện cùng nhau trong dữ liệu. Thuật toán dựa trên hai khái niệm quan trọng:

- Độ hỗ trợ (Support): Đo lường tần suất xuất hiện của một tập mục trong toàn bộ tập dữ liệu. 
  
$$ support(I) = \frac{Number\ of\ transactions\ containing\ I}{Total\ number\ of\ transactions} $$

- Độ tự tin (Confidence): Đánh giá khả năng một mục xuất hiện khi một mục khác đã xuất hiện.

$$ confidence(X -> Y) = \frac{Number\ of\ transactions\ containing\ X\ and\ Y}{Number\ of\ transactions\ containing\ X} $$

Quy trình chính của thuật toán Apriori bao gồm:

1. Tạo ra các tập mục ứng viên có kích thước tăng dần, dựa trên ngưỡng hỗ trợ tối thiểu, cắt tỉa các nhánh có độ hỗ trợ nhỏ hơn độ hỗ trợ tối thiểu.
2. Xác định các tập mục phổ biến đáp ứng ngưỡng hỗ trợ tối thiểu và sử dụng các tập mục này để tạo các tập luật ứng viên.
3. Tính toán độ tự tin cho các tập luật và giữ lại các luật có độ tự tin cao hơn ngưỡng yêu cầu.

![alt text](/assets/images/gminer/apriori.png)

Apriori là một thuật toán cơ bản và dễ triển khai trên CPU. Tuy nhiên, khi dữ liệu có kích thước lớn, số lượng các tập mục cần kiểm tra tăng lên nhanh chóng, làm tăng thời gian xử lý. CPU có cấu trúc xử lý tuần tự, hạn chế khả năng thực thi nhiều phép toán đồng thời, do đó khi dữ liệu càng lớn, thuật toán Apriori trên CPU càng mất nhiều thời gian, làm giảm hiệu quả.



# GPGPU và CUDA

Với nhu cầu tăng cao về việc sử dụng tối ưu phần cứng phục vụ cho việc tính toán hiệu năng cao nhằm giảm thời gian chạy của các thuât toán, thuật ngữ GPGPU đã ra đời. 

- GPGPU (General-Purpose computing on Graphics Processing Units) là thuật ngữ dùng để chỉ việc sử dụng GPU cho các tác vụ tính toán ngoài đồ họa, biến GPU trở thành một bộ xử lý đa năng. GPGPU phát huy sức mạnh trong các bài toán yêu cầu xử lý dữ liệu lớn và tính toán song song, như học sâu, mô phỏng vật lý, xử lý ảnh, và khai phá dữ liệu. Nhờ GPGPU và các công cụ như CUDA, lập trình viên có thể tận dụng sức mạnh của GPU để đạt được hiệu suất cao hơn nhiều lần so với CPU trong các ứng dụng tính toán tổng quát, đặc biệt là khi xử lý dữ liệu lớn và phức tạp.

NVIDIA đã phát triển một nền tảng lập trình làm thay đổi lĩnh vực tính toán hiệu năng cao là CUDA (Compute Unified Device Architecture). CUDA cho phép khai thác sức mạnh tính toán của GPU (Graphics Processing Unit) cho các tác vụ tính toán song song. Khác với CPU, GPU có hàng nghìn lõi xử lý nhỏ, có khả năng thực hiện các phép toán đồng thời, giúp tăng tốc độ xử lý cho các bài toán yêu cầu khối lượng lớn tính toán. CUDA cung cấp các API và thư viện để lập trình viên dễ dàng triển khai các thuật toán song song trên GPU, tối ưu cho các tác vụ từ xử lý đồ họa đến khoa học dữ liệu và trí tuệ nhân tạo.

# Thuật toán GMiner
GMiner là một phương pháp khai phá tập phổ biến dựa trên GPU, được thiết kế để xử lý dữ liệu quy mô lớn với hiệu suất cao. GMiner khai thác sức mạnh tính toán của GPU bằng cách sử dụng chiến lược "Traversal from the First Level" (TFL), giúp khai phá các mẫu từ cấp đầu tiên của cây liệt kê mà không lưu trữ các mẫu trung gian. Điều này giúp giảm đáng kể việc sử dụng bộ nhớ GPU, cho phép xử lý các bộ dữ liệu lớn mà không gặp vấn đề hết bộ nhớ. Để tiếp tục giải quyết vấn đề tràn bộ nhớ với tập phổ biến quá dài, GMiner đã đề xuất phương pháp HIL (Hopping from the Intermediate Level), giúp tối ưu sử dụng các tập mục đã được tính trước nhằm hạn chế bộ nhớ cần sử dụng. Ngoài ra, GMiner còn giải quyết vấn đề "workload skewness" (sự lệch tải công việc) trong các phương pháp song song hiện tại, giúp tăng hiệu suất gần như tuyến tính khi tăng số lượng GPU. 

# Tài liệu tham khảo 
[1] M. Hegland, “The apriori algorithm–a tutorial”, Mathematics and computation in imaging science and information processing, tr 209–262, 2007.
[2] K. Chon, S.-H. Hwang, và M. Kim, “GMiner: A Fast GPU-based Frequent Itemset Mining Method for Large-scale Data”, Information Sciences, vol 439, tr , 2018, doi: 10.1016/j.ins.2018.01.046 .

# To be continue
Ở phần tiếp theo mình sẽ đi sâu hơn vào chi tiết cách thuật toán GMiner hoạt động nhé. 