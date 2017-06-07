Pacemaker
=========

# Table of Contents
[1. Cluster là gì](#1Cluster là gì)


##1. Cluster là gì
- Cluster là một kiến trúc nhằm đảm bảo nâng cao khả năng sẵn sàng cho các hệ thống mạng máy tính
- Clustering cho phép sử dụng nhiều máy chủ kết hợp với nhau tạo thành một cụm có khả năng chịu đựng hay chấp nhận sai sót (fault-tolerant) nhằm nâng cao độ sẵn sàng của hệ thống mạng.

##2. Mô hình triển khai
 - High Performance Cluster
 - Load Balancing Cluster
 - High Availability Cluster
###2.1 High Performance Cluster (HPC)
 - Nhiều máy tính khác nhau làm việc cùng nhau để xử lý một hoặc nhiều tác vụ yêu cầu nhiều tài nguyên tính toán
 - Mô hình tổng quan HPC
<img src="http://imgur.com/yX8ueM8">
###2.2 Load Balancing Cluster (LBC)
 - Mỗi máy tính trong cluster sẽ xử lý 1 công việc riêng biệt
 - Cần có Load Balancer (Frontend) và Server Farm (Backend)
 - Mô hình tổng quan LBC
<img src="http://imgur.com/6uVfBLU">
###2.3 High Availabality Cluster (HAC)
 - Mục đích của kiểu cluster này là đảm bảo các tài nguyên quan trọng sẵn sàng tối đa có thể đạt được
 - Mô hình tổng quan HAC
<img src="http://imgur.com/a/A7OPp">
##3. Lịch sử HAC trong linux
 - HA có một lịch sử dài nó bắt đầu vào thập niên 90 của thế kỉ trước và nó là một giải pháp đơn giản với tên Hearbeat.
 - Một Hearbeat cluster đơn giản chỉ làm 2 việc:
 <ul><li>Monitor 2 nodes và không hơn 2 node</li>
 <li>Nó được cấu   hình để start 1 hoặc nhiều dịch vụ trên 2 node</li> </ul>
 - Nếu resoure trên node này bị down nó sẽ start resouce trên node còn lại
 ##4. Các thành phần trong HAC
 - các thành phần sau đây đươc sử dụng trong hầu hết clusters.
 <ul><li>Shared Storage</li>
 <li>Different networks</li>
 <li>Bonded networks devices</li>
 <li>Multipathing</li>
 <li>Fencing/ STONITH device</li></ul>
 - Mô hình kiến trúc HA Cluster
 <img src="http://imgur.com/a/99gKT">
 - Resource agents:
 <ul><li>Pacemaker là một phần của cluster, có trách nhiệm quản lý các tài nguyên.</li>
 <li>Để quản lý tài nguyên, Resource agent được sử dụng.</li>
 <li>Một resource agent là một script mà cluster sử dụng để start, stop và monitor resource. Nó có thể so sách systemctl hoặc 1 script chạy có mức độ. Nhưng nó được điều chỉnh để sử dụng trong cluster. Nó cũng có thể định nghĩa các thuộc tính có thể quản lý bởi cluster. Đối với 1 admin, nó rất quan trọng để biết được thuộc tính nào có thể sử dụng trước khi bắt đầu cấu hình resource.</li></ul> 
 - Corosync
 <ul><li>Corosync là một layer có nhiệm vụ quản lý các node thành viên.</li>
 <li>Nó cũng được cấu hình để giao tiếp với pacemaker.</li>
 <li>Pacemaker nhận update về những sự thay đổi trạng thái của các node trong cluster. Dựa vào đó nó có thể bắt đầu một sự kiện nào đó ví dụ như migrate resource.</li><ul>
 - Storage layer:
 Pacemaker cũng được sử dụng để quản lý các thiết bị lưu trữ.
 Một quản lý khóa phân phối (Distribute Lock Manage DLM) cần phải có. DLM quản lý việc đồng bộ hóa các khóa giữa các nodes.
 Nó đặc biệt quan tronjng nếu là share storage như là cLVM2 cluster logical volumn hoặc GFS2 và OCFS2 cluster file system.
 

