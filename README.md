#Tổng quan về Filesystem(Linux)

##1.Định nghĩa

Filesystem là các cách thức và cấu trúc dữ liệu mà một hệ điều hành sử dụng để theo dõi các tập tin trên một ổ đĩa hoặc phân vùng, định nghĩa các file được tổ chức trên ổ cứng.

##2.Journaling

Trước khi đi vào tìm hiểu các loại filesystem,chúng ta cần biết thế nào là Journaling.Vậy Journaling là gì?

######a.Khái niệm:

Là hệ thống file có khả năng ghi lại (log) được các hoạt động mà hệ điều hành đã và đang thao tác trên dữ liệu thì hệ thống xác định được ngay những file bị sự cố mà không cần phải quét lại toàn bộ hệ thống file, giúp quá trình phục hồi dữ liệu trở nên tin cậy và nhanh chóng hơn.

######b.Hoạt động

**Bước 1:**Trước tiên file sẽ được ghi vào journal, đẩy vào bên trong lớp quản lý dữ liệu, 

**Bước 2:**Journal sẽ ghi file đó vào phân vùng ổ cứng khi đã sẵn sàng. 

**Bước 3** Và khi thành công, file sẽ được xóa bỏ khỏi journal,đẩy ngược ra bên ngoài và quá trình hoàn tất. 

(Nếu xảy ra lỗi trong khi thực hiện thì file hệ thống có thể kiểm tra lại journal và tất cả các thao tác chưa được hoàn tất, đồng thời ghi nhớ lại đúng vị trí xảy ra lỗi đó)

######c.Ưu,nhược:

* Ưu:Ghi dữ liệu với tính ổn định,nếu xảy ra lỗi,hệ điều hành sẽ biết ngay lỗi ở đâu.

* Nhược:Đánh đổi hiệu suất của hệ điều hành.

##3.Các loại filesystem

**1.Ext:**

- Là định dạng file hệ thống đầu tiên được thiết kế dành riêng cho Linux
- Hiện nay có nhiều hạn chế, không còn được hỗ trợ trên nhiều distribution

**2.Ext2:**

- Kế thừa các thuộc tính của file hệ thống cũ
- Không sử dụng Journal
- Hỗ trợ dung lượng ổ cứng lên tới 2 TB
- Phù hợp với những thiết bị lưu trữ bên ngoài như thẻ nhớ, ổ USB...

**3.Ext3:**

- Tương thích ngược với Ext2
- Hoạt động nhanh, ổn định
- Có Journaling
- Không thực sự phù hợp để làm file hệ thống dành cho máy chủ.

**4.Ext4:**

- Tương thích ngược với các phiên bản trước đó.
- Giảm bớt hiện tượng phân mảnh dữ liệu trong ổ cứng, hỗ trợ các file và phân vùng có dung lượng lớn.
- Lưu giữ được những ưu điểm của Ext3.

**5.BtrFs**

- Đang trong giai đoạn phát triển bởi Oracle và có nhiều tính năng giống với ReiserFS
- Hỗ trợ tính năng pool trên ổ cứng, tạo và lưu trữ snapshot, nén dữ liệu ở mức độ cao, chống phân mảnh dữ liệu nhanh chóng... 
được thiết kế riêng biệt dành cho các doanh nghiệp có quy mô lớn.
- BtrFS rất phù hợp để hoạt động với server

**6.ReiserFs**

- Lần đầu được công bố vào năm 2001 với nhiều tính năng mới mà file hệ thống Ext khó có thể đạt được
- Năm 2004, ReiserFS đã được thay thế bởi Reiser4 với nhiều cải tiến hơn nữa.
- Đạt hiệu suất hoạt động rất cao đối với những file nhỏ như file log, phù hợp với database và server email.
- Tuy nhiên, quá trình nghiên cứu, phát triển của Reiser4 khá “chậm chạp” và vẫn không hỗ trợ đầy đủ hệ thống kernel của Linux.

**7.XFS**

- Phát triển bởi Silicon Graphics từ năm 1994 .
- Hạn chế được tình trạng phân mảnh dữ liệu, không cho phép các snapshot tự động kết hợp với nhau, hỗ trợ nhiều file dung lượng lớn, có thể thay đổi kích thước file dữ liệu... nhưng không thể shrink – chia nhỏ phân vùng XFS.
- Phù hợp với việc áp dụng vào mô hình server media vì khả năng truyền tải file video rất tốt.

**8.ZFS**

- Hiện tại vẫn đang trong giai đoạn phát triển bởi Oracle với nhiều tính năng tương tự như Btrfs và ReiserFS.
- Người sử dụng có thể gặp khó khăn khi cài đặt hệ điều hành Linux vì có yêu cầu FUSE và có thể không được hỗ trợ bởi distributor.

**9.JFS**

- Được IBM phát triển lần đầu tiên năm 1990
- Tiêu tốn ít tài nguyên hệ thống, đạt hiệu suất hoạt động tốt với nhiều file dung lượng lớn và nhỏ khác nhau.
- Các phân vùng JFS có thể thay đổi kích thước được nhưng lại không thể shrink như ReiserFS và XFS, tuy nhiên nó lại có tốc độ
kiểm tra ổ đĩa nhanh nhất so với các phiên bản Ext.

##4.Một số câu lệnh về file,thư mục

**1.Lệnh ls**

    ls [option]

ls -l:Hiển thị toàn bộ thông tin thư mục

<img src="http://i.imgur.com/oAzGYEd.png">

ls -a:Hiển thị tất cả cả các thư mục,kể cả thư mục ẩn

<img src="http://i.imgur.com/sm3qVXx.png">

**2.Lệnh du**

    du [option] [file]

du -a:ghi số đếm cho mọi tập tin,ko chỉ thư mục

<img src="http://i.imgur.com/jfs3rO5.png">

du -sh:Hiển thị tổng dung lượng trong thư mục 

<img src="http://i.imgur.com/7sLGNnv.png">

**3.Lệnh df**

    df [option] [file]

df -h:Hiển thị thông tin dung lượng ổ cứng

<img src="http://i.imgur.com/PhdnpnZ.png">



