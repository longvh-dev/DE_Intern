# 1. Hadoop là gì?
Hadoop là 1 Apache framework nguồn mở viết bằng Java cho phép phát triển các ứng dụng phân tán có cường độ dữ liệu  lớn. 

# 2. Kiến trúc Hadoop
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

Là thuật ngữ dùng để chỉ hệ thống file phân tán siêu lớn. Bởi hệ thống này cần phải cung cấp khả năng lưu trữ dữ liệu khổng lồ.

HDFS có kiến trúc `master/slave`. Trên một cluster chạy HDFS có hai loại node là NameNode và DataNode. Một cluster có duy nhất một NameNode và có một hay nhiều DataNode.

![HDFS Architecture](https://i.pinimg.com/originals/c6/c3/1a/c6c31aa5f418ab6ac2f4122ba3f4db3b.jpg)

`NameNode` là nơi lưu trữ thông tin metadata của cụm, bao gồm các thông tin như vị trí, số lượng block của file, quyền truy cập vào file đó,… NameNode là nơi tiếp nhận các yêu cầu đọc ghi từ client và điều phối hoạt động của cụm. 

`DataNode` là nơi lưu trữ dữ liệu vật lý, đây là nơi lưu trữ block của file. Các DataNode có nhiệm vụ báo cáo tình trạng đến NameNode, thông báo về danh sách các block mà nó quản lý. Nếu 1 DataNode chết, NameNode sẽ thực hiện replicate các block của DataNode này đến các DataNode khác để đảm bảo dữ liệu luôn sẵn sàng. Dựa theo sự điều phối của NameNode mà DataNode là nơi thực hiện các yêu cầu đọc, ghi file của người dùng.

Bên cạnh NameNode còn có một tiến trình gọi là SecondaryNamenode hoạt động như một tiến trình hỗ trợ NameNode và không dùng để thay thế NameNode. 

![](https://scontent-hkg4-2.xx.fbcdn.net/v/t1.6435-9/90150166_2139297192882685_5449445752021450752_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=9f7757&_nc_eui2=AeEPRZoVjxUciSImVHMSy4hn_3Xm9z3GmgP_deb3PcaaA6hc2hDnKnnyc35dWYRig-MkCYmLZ2YxVgl78Dlriyqu&_nc_ohc=tRAOED7jzcoAX-NHpwr&_nc_ht=scontent-hkg4-2.xx&oh=00_AfCz8hEn6lfVX93OyHwdcrA11Cdl4mz__qOXCV5BCx1ziw&oe=657D0FFA)



### 2.2. MapReduce

Là 1 Framework. Chức năng chính là dùng để viết các ứng dụng xử lý song song dữ liệu với dung lượng lớn.

Bộ phận này cũng sẽ có khả năng chịu lỗi cao xuyên suốt hàng ngàn cluster máy tính mà không bị ngưng bất ngờ.

Gồm map và reduce
- Map: được thực hiện đầu tiên với có chức năng tải, phân tích dữ liệu đầu vào. Sau đó Map sẽ được chuyển đổi thành tập dữ liệu theo cặp key/value 
- Reduce: có chức năng nhận kết quả đầu ra từ tác vụ Map xuất ra. Sau đó, Reduce sẽ kết hợp dữ liệu lại với nhau thành tập dữ liệu nhỏ hơn. 

### 2.3. Yarn resource
Quản lý tài nguyên của các hệ thống lưu trữ dữ liệu và chạy phân tích.

### 2.4. Common 
Là các thư viện và tiện ích cần thiết của Java để các module khác sử dụng. Những thư viện này cung cấp hệ thống file và lớp OS trừu tượng, đồng thời chứa các mã lệnh Java để khởi động Hadoop.

# Cơ chế lưu dữ liệu dưới HDFS
### Block
Dữ liệu trong HDFS luôn được lưu trữ theo block. Vì vậy, khối dữ liệu duy nhất được chia thành nhiều block có kích thước 128MB (mặc định) và có thể thay đổi thủ công.
![](https://media.geeksforgeeks.org/wp-content/uploads/20200621121959/3164-1.png)

### Replication
Đảm bảo tính sẵn có của data: Tạo 1 bản sao của  dữ liệu và số lần tạo bản sao đc gọi là Replication Factor. Như đã thấy trong file block thì HDFS lưu trữ dữ liệu dưới dạng nhiều block khác nhau và cùng lúc Hadoop tạo bản sao của các khối đó.

Theo mặc định, Replication Factor cho Hadoop được đặt thành 3, có thể thay đổi thủ công. Như trong ví dụ trên, ta đã tạo 4 block, nghĩa là có nghĩa là số bản sao của mỗi khối tệp được tạo tổng cộng 4×3 = 12 block được tạo ra cho mục đích backup.

Điều này là do để chạy Hadoop, ta đang sử dụng phần cứng thông thường (phần cứng rẻ tiền) có thể bị hỏng bất kỳ lúc nào. Đó là lý do tại sao chúng ta cần một tính năng như vậy trong HDFS có thể tạo bản sao của các khối tệp đó cho mục đích sao lưu, điều này gọi là **khả năng chịu lỗi**.
--> Phần cứng rẻ tiền - > Dễ hỏng -> cần sao lưu nhiều

### Rack awareness
Là tập hợp vật lý của các node  trong Hadoop cluster. 1 cụm Hadoop lớn gồm nhiều  Rack. Với sự trợ giúp của Racks,  Namenode sẽ chọn Datanode gần nhất để đạt được hiệu suất tối đa trong khi thực hiện đọc/ghi thông tin giúp giảm Lưu lượng mạng.

Khi sao chép các bản ghi của block, HDFS thực hiện ghi vào DataNode đầu tiên trên một máy, 2 bản ghi còn lại sẽ ghi vào 2 DataNode thuộc Rack khác Rack ban đầu. 

Việc sử dụng Rack Awareness Algorithm này giúp nâng cao khả năng chịu lỗi của HDFS, giả sử trong trường hợp 1 DataNode lỗi vẫn có 2 DataNode khác sẵn sàng, rủi ro hơn nữa là tất cả DataNode thuộc cùng 1 Rack chết thì vẫn còn DataNode ở Rack khác sẵn sàng.

 ![](https://scontent-hkg4-1.xx.fbcdn.net/v/t1.6435-9/90299586_2139299106215827_9074506172567912448_n.jpg?stp=dst-jpg_p720x720&_nc_cat=102&ccb=1-7&_nc_sid=9f7757&_nc_eui2=AeHhJiJzYT44dabutSF7Dp-hcMeADl952dlwx4AOX3nZ2c-43RAppeEWA65_ugdE5QdEyzzphbhwi9UuxOG52vWz&_nc_ohc=9ozXv052bmkAX-CDxG1&_nc_ht=scontent-hkg4-1.xx&oh=00_AfA6qX0apFiasvVNHCQ3Paw8n9WcSPCF--C2iYlHX3L1BQ&oe=657CFC8B)

# 3. Why Hadoop?
### 3.1 Ưu điểm

- Tính mở rộng cao: Cho phép người dùng thêm node mới và thay đổi chúng khi muốn lưu trữ nhiều dữ liệu hơn
- Chi phí hiệu quả: Giá rẻ, hiệu quả (ko yêu cầu phần cứng cao)
- Linh hoạt: Xử lý đc cả dữ liệu có cấu trúc và ko cấu trúc
- Nhanh: Phương pháp lưu trữ độc đáo của Hadoop dựa trên một hệ thống tập tin phân phối theo các “bản đồ” dữ liệu. Các công cụ xử lý dữ liệu thường nằm trên cùng một máy chủ với dữ liệu nên kết quả xử lý dữ liệu nhanh hơn nhiều. 
-> Nếu chúng ta đang làm việc với khối dữ liệu lớn phi cấu trúc, Hadoop có thể xử lý hiệu quả mỗi tetabyte dữ liệu chỉ trong vài phút và mỗi petabye trong vài giờ.
- Khả năng chịu lỗi: Khi node lỗi, tự động đổi sang node mới ->  không làm gián đoạn hệ thống

### 3.2 Nhược điểm
- Hadoop không có mô hình bảo mật và tính phức tạp cao
- Hadoop không cung cấp lưu trữ hoặc mã hóa dữ liệu mức mạng, trong khi đây là mối quan tâm rất lớn đối với dữ liệu ứng dụng có đối tượng khách hàng cần độ bảo mật thông tin như chính phủ, ngân hàng…
- HDFS không hiệu quả để xử lý các tập tin nhỏ
- MapReduce không thích hợp để sử dụng các trường hợp có nhu cầu truy cập dữ liệu thời gian thực
- MapReduce khó biểu diễn dữ liệu đầu ra theo nhu cầu cần sử dụng.
-> có thể thay mapreduce bằng framework tốt hơn
