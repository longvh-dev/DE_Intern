# Hadoop

## 1. Hadoop là gì?
Hadoop là 1 Apache framework nguồn mở viết bằng Java cho phép phát triển các ứng dụng phân tán có cường độ dữ liệu  lớn. 

## 2. Kiến trúc Hadoop
HDFS và MapReduce là hai thành phần chính của một Hadoop cluster. Nhìn chung kiến trúc của Hadoop là kiến trúc `master-slave`. Cả hai thành phần HDFS và MapReduce đều tuân theo kiến trúc `master-slave` này.
- `master` node gồm JobTracker, TaskTracker, NameNode, và DataNode. Có chức năng gán một tác vụ cho các node slave khác nhau và quản lý tài nguyên.
- có nhiều node `slave` , mỗi node sẽ đóng vai trò  DataNode hoặc TaskTracker. 

![Hadoop Architecture](https://media.geeksforgeeks.org/wp-content/uploads/20200621102239/Hadoop-componants.png)
4 module trong Hadoop
- HDFS: Lưu trữ dữ liệu
- MapReduce: Xử lý và tính toán
- YARN: Quản lý tài nguyên, lên lịch cho tác vụ
- Hadoop common: các thư viện viết bằng ngôn ngữ Java

### 2.1. HDFS

HDFS là một hệ thống tệp phân tán được thiết kế để lưu trữ dữ liệu lớn trên nhiều máy tính.

HDFS có kiến trúc `master/slave`. Trên một cluster chạy HDFS có hai loại node là NameNode và DataNode. Một cluster có duy nhất một NameNode và có một hay nhiều DataNode.

![HDFS Architecture](https://i.pinimg.com/originals/c6/c3/1a/c6c31aa5f418ab6ac2f4122ba3f4db3b.jpg)

`NameNode` Quản lý siêu dữ liệu (metadata) về các block dữ liệu và vị trí của chúng trên các DataNode.

`DataNode` Lưu trữ thực tế của dữ liệu và thực hiện các thao tác đọc/ghi dữ liệu theo hướng dẫn của NameNode.

Bên cạnh NameNode còn có một tiến trình gọi là SecondaryNamenode hoạt động như một tiến trình hỗ trợ NameNode và không dùng để thay thế NameNode. 

![](https://scontent-hkg4-2.xx.fbcdn.net/v/t1.6435-9/90150166_2139297192882685_5449445752021450752_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=9f7757&_nc_eui2=AeEPRZoVjxUciSImVHMSy4hn_3Xm9z3GmgP_deb3PcaaA6hc2hDnKnnyc35dWYRig-MkCYmLZ2YxVgl78Dlriyqu&_nc_ohc=tRAOED7jzcoAX-NHpwr&_nc_ht=scontent-hkg4-2.xx&oh=00_AfCz8hEn6lfVX93OyHwdcrA11Cdl4mz__qOXCV5BCx1ziw&oe=657D0FFA)



### 2.2. MapReduce

MapReduce là một mô hình lập trình và một khung làm việc phân tán để xử lý lớn lượng dữ liệu.

Thành phần
- JobTracker: Quản lý và lên lịch các Job trên cluster.
- TaskTracker: Thực hiện các tác vụ cụ thể của một Job, theo sự điều phối của JobTracker.

Nhiệm vụ: 
- Phân chia công việc thành các nhiệm vụ con (map và reduce) và phân phối chúng trên cluster.
- Thu thập và tổng hợp kết quả từ các nhiệm vụ con.

### 2.3. Yarn resource
YARN là một hệ thống quản lý tài nguyên và lên lịch cho các ứng dụng chạy trên Hadoop.

Thành Phần Cấu Tạo:
- ResourceManager: Quản lý tài nguyên trên toàn bộ cluster và quyết định vị trí cho ứng dụng chạy.
- NodeManager: Quản lý tài nguyên trên mỗi máy tính và thực hiện các nhiệm vụ theo hướng dẫn của ResourceManager.

Nhiệm vụ:
- Phân phối tài nguyên cho các ứng dụng chạy trên cluster.
- Lên lịch cho các ứng dụng và theo dõi việc sử dụng tài nguyên.

### 2.4. Hadoop Ecosystem 
Bên cạnh các thành phần cơ bản, Hadoop có một hệ sinh thái phong phú với nhiều dự án và thành phần bổ sung như Apache Hive (SQL-like query language), Apache HBase (NoSQL database), Apache Spark (in-memory data processing), và nhiều dự án khác.

Thành Phần Cấu Tạo:
- Các dự án và thành phần bổ sung trong hệ sinh thái Hadoop.

Nhiệm Vụ:
- Cung cấp các công cụ và dịch vụ bổ sung cho phân tích và xử lý dữ liệu.

Tổng quan về kiến trúc Hadoop cho thấy sự phân tán và linh hoạt của nó, cho phép xử lý và lưu trữ lớn lượng dữ liệu trên nhiều máy tính. Hadoop Ecosystem mở rộng khả năng của Hadoop bằng cách cung cấp nhiều công cụ và dự án phụ trợ để đáp ứng các yêu cầu đặc biệt của ứng dụng và ngành công nghiệp cụ thể.

## 3. Cơ chế lưu dữ liệu dưới HDFS
### 3.1 Block
Dữ liệu trong HDFS luôn được lưu trữ theo block. Vì vậy, khối dữ liệu duy nhất được chia thành nhiều block có kích thước 128MB (mặc định) và có thể thay đổi thủ công.
![](https://media.geeksforgeeks.org/wp-content/uploads/20200621121959/3164-1.png)

Việc chia dữ liệu thành các block giúp quản lý dữ liệu một cách hiệu quả và cho phép xử lý song song trên nhiều máy tính.

### 3.2 Replicate

Mỗi khối dữ liệu được sao lưu (replicate) trên nhiều máy tính trong cluster để đảm bảo sự chịu lỗi và tin cậy.

Mặc định, HDFS sao lưu mỗi block 3 lần (replication factor là 3). Các bản sao của block được lưu trữ trên các máy tính khác nhau để đảm bảo sự chịu lỗi trong trường hợp một hoặc nhiều máy tính gặp sự cố.

### 3.3 Distribution

Các block và các bản sao của chúng được phân tán trên nhiều máy tính trong cluster.

Mỗi máy tính trong HDFS cluster đảm nhận vai trò của một DataNode và lưu trữ một số khối dữ liệu và bản sao.

### 3.4 Metadata và NameNode:

Metadata về các khối dữ liệu, vị trí của chúng, và các thông tin khác được quản lý bởi một thành phần gọi là NameNode. NameNode duy trì một bản đồ toàn bộ hệ thống file trong HDFS và theo dõi vị trí của từng khối dữ liệu.

### 3.5 DataNode:

Mỗi máy tính trong cluster chạy một tiến trình gọi là DataNode, chịu trách nhiệm lưu trữ dữ liệu thực tế. DataNode chủ động báo cáo về sự trạng thái và sức khỏe của dữ liệu mà nó lưu trữ đến NameNode.

## 4. Why Hadoop?
### 4.1 Ưu điểm

- Tính mở rộng cao: Cho phép người dùng thêm node mới và thay đổi chúng khi muốn lưu trữ nhiều dữ liệu hơn
- Chi phí hiệu quả: Giá rẻ, hiệu quả (ko yêu cầu phần cứng cao)
- Linh hoạt: Xử lý đc cả dữ liệu có cấu trúc và ko cấu trúc
- Nhanh: Phương pháp lưu trữ độc đáo của Hadoop dựa trên một hệ thống tập tin phân phối theo các “bản đồ” dữ liệu. Các công cụ xử lý dữ liệu thường nằm trên cùng một máy chủ với dữ liệu nên kết quả xử lý dữ liệu nhanh hơn nhiều. 
-> Nếu chúng ta đang làm việc với khối dữ liệu lớn phi cấu trúc, Hadoop có thể xử lý hiệu quả mỗi tetabyte dữ liệu chỉ trong vài phút và mỗi petabye trong vài giờ.
- Khả năng chịu lỗi: Khi node lỗi, tự động đổi sang node mới ->  không làm gián đoạn hệ thống

### 4.2 Nhược điểm
- Hadoop không có mô hình bảo mật và tính phức tạp cao
- Hadoop không cung cấp lưu trữ hoặc mã hóa dữ liệu mức mạng, trong khi đây là mối quan tâm rất lớn đối với dữ liệu ứng dụng có đối tượng khách hàng cần độ bảo mật thông tin như chính phủ, ngân hàng…
- HDFS không hiệu quả để xử lý các tập tin nhỏ
- MapReduce không thích hợp để sử dụng các trường hợp có nhu cầu truy cập dữ liệu thời gian thực
- MapReduce khó biểu diễn dữ liệu đầu ra theo nhu cầu cần sử dụng.
-> có thể thay mapreduce bằng framework tốt hơn

## 5. Đọc, ghi File
### 5.1 Đọc File
  1. Block đọc
  Dữ liệu trong HDFS được chia thành các khối có kích thước cố định. Khi một ứng dụng cần đọc một tập tin, NameNode cung cấp thông tin về các DataNode chứa các khối dữ liệu của tập tin đó.
  2. DataNode chuyển dữ liệu
  Client (ứng dụng Hadoop hoặc người dùng) yêu cầu DataNode trực tiếp để đọc dữ liệu. DataNode chuyển các khối dữ liệu về cho Client.
  3. Gom Cụm (Aggregation):
  Nếu dữ liệu được chia thành nhiều khối, Hadoop có thể tổng hợp kết quả từ nhiều DataNode để gửi về cho Client.

### 5.2 Ghi File
  1. Chia Dữ Liệu thành Các Khối (Data Splitting): Khi một tập tin cần được ghi vào HDFS, Hadoop chia tập tin thành các khối dữ liệu có kích thước cố định.

  2. Xác Định Vị Trí Lưu Trữ (Determine Storage Locations): NameNode xác định vị trí của các DataNode nơi dữ liệu sẽ được lưu trữ.
  
  3. Chuyển Dữ Liệu Đến DataNode (Data Transfer to DataNode): Client chuyển các khối dữ liệu đến các DataNode được xác định bởi NameNode.
  
  4. Lưu Trữ Dữ Liệu (Data Storage): Các DataNode nhận dữ liệu và lưu trữ nó. Nếu có nhiều bản sao của dữ liệu (replication), các bản sao cũng được lưu trữ trên các DataNode khác nhau để đảm bảo tính chịu lỗi.
  
  5. Báo Cáo Cho NameNode (Report to NameNode): DataNode báo cáo lại cho NameNode về vị trí và tình trạng của dữ liệu mà nó lưu trữ.
  
  6. NameNode Ghi Metadata (NameNode Metadata Update): NameNode cập nhật siêu dữ liệu (metadata) về tập tin, bao gồm thông tin về vị trí của các khối dữ liệu và các DataNode tương ứng.


### Cơ Chế Chịu Lỗi:
- HDFS sử dụng cơ chế sao lưu để đảm bảo tính chịu lỗi. Mặc định, mỗi khối dữ liệu được sao lưu thành ba bản để ngăn chặn mất dữ liệu do sự cố trên một hoặc hai DataNode.
- Trong trường hợp một DataNode gặp sự cố, NameNode có thể chuyển dữ liệu từ bản sao khác để đảm bảo tính nhất quán và độ tin cậy của dữ liệu.


Cơ chế đọc và ghi file trong Hadoop đều được tối ưu hóa cho việc xử lý dữ liệu lớn và có tính chất phân tán của nó, đặc biệt là trong môi trường có nhiều máy tính làm việc cùng một lúc.
