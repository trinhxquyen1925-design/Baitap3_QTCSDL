# BaiTap3_QTCSDL

## Thông tin sinh viên

- Họ tên: Trịnh Xuân Quyền
- Lớp: K59KMT.K01
- MSSV: K235480106059
- Đề tài: Thiết kế và cài đặt CSDL quản lý cầm đồ

---

# Nhiệm vụ 1: Thiết kế CSDL

## 1. Mô tả bài toán

Bài toán quản lý cầm đồ yêu cầu xây dựng một cơ sở dữ liệu dùng để quản lý khách hàng, hợp đồng vay tiền, tài sản cầm cố và lịch sử trả nợ.

Trong hệ thống này, khách hàng mang tài sản đến cửa hàng để vay tiền. Mỗi lần vay sẽ lập một hợp đồng. Một khách hàng có thể có nhiều hợp đồng khác nhau. Mỗi hợp đồng có thể gắn với một hoặc nhiều tài sản cầm cố.

Ngoài việc lưu thông tin ban đầu, hệ thống còn cần theo dõi trạng thái hợp đồng, trạng thái tài sản và các lần khách hàng thanh toán. Việc lưu lịch sử thanh toán giúp tránh mất dấu vết dòng tiền và hỗ trợ tính toán công nợ chính xác hơn.

---

## 2. Tạo cơ sở dữ liệu

Tên cơ sở dữ liệu sử dụng trong bài:

`QuanLyCamDo_K235480106059`


<img width="1917" height="1077" alt="ảnh 1" src="https://github.com/user-attachments/assets/d7874eb2-63c9-4667-add7-c746a67b3ef0" />



*Ảnh tạo cơ sở dữ liệu mới trong SQL Server Management Studio với tên `QuanLyCamDo_K235480106059`.*

---

## 3. Tạo các bảng trong cơ sở dữ liệu

Cơ sở dữ liệu được thiết kế gồm 4 bảng chính:

1. `KhachHang`
2. `HopDongCamDo`
3. `TaiSanCamCo`
4. `LichSuThanhToan`


<img width="1917" height="1077" alt="ảnh 2" src="https://github.com/user-attachments/assets/7619f122-fd1f-4d6f-82eb-04ff1bbd4e70" />



<img width="1917" height="1077" alt="ảnh 3" src="https://github.com/user-attachments/assets/90d737d8-b96e-46a5-b72d-1d9d71cea6d6" />


*Ảnh tạo bảng `KhachHang` và `HopDongCamDo`, kèm khóa chính, khóa ngoại và các ràng buộc kiểm tra.*





<img width="1917" height="1077" alt="ảnh 4" src="https://github.com/user-attachments/assets/58834b56-b69d-49db-8b5d-73a034db68b6" />



<img width="1917" height="1077" alt="ảnh 5" src="https://github.com/user-attachments/assets/f0cbe0cf-43ec-45aa-8f2e-bf54bd3c3477" />




*Ảnh tạo bảng `TaiSanCamCo` và `LichSuThanhToan`, dùng để quản lý tài sản cầm cố và lịch sử trả nợ.*

---

# 4. Chi tiết các bảng

## 4.1. Bảng KhachHang

Bảng `KhachHang` dùng để lưu thông tin cá nhân của khách hàng đến cầm đồ.

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---|---|---|---|
| MaKH | CHAR(10) | PRIMARY KEY | Mã khách hàng, dùng để định danh duy nhất |
| HoTen | NVARCHAR(100) | NOT NULL | Họ tên khách hàng |
| SoDienThoai | VARCHAR(15) | NOT NULL | Số điện thoại liên hệ |
| CanCuocCongDan | CHAR(12) | UNIQUE, NOT NULL | Số căn cước công dân, không được trùng |
| DiaChi | NVARCHAR(200) | NULL | Địa chỉ của khách hàng |
| NgayTao | DATE | DEFAULT GETDATE() | Ngày khách hàng được thêm vào hệ thống |

### Giải thích

Mỗi khách hàng được quản lý bằng một mã riêng là `MaKH`.

Cột `CanCuocCongDan` được đặt ràng buộc `UNIQUE` để đảm bảo một người không bị nhập trùng nhiều lần trong hệ thống.

Bảng này không lưu thông tin hợp đồng trực tiếp, vì một khách hàng có thể có nhiều hợp đồng khác nhau.

---

## 4.2. Bảng HopDongCamDo

Bảng `HopDongCamDo` dùng để lưu thông tin từng hợp đồng vay tiền.

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---|---|---|---|
| MaHD | CHAR(10) | PRIMARY KEY | Mã hợp đồng |
| MaKH | CHAR(10) | FOREIGN KEY | Mã khách hàng lập hợp đồng |
| TienVayGoc | DECIMAL(18,2) | NOT NULL, CHECK > 0 | Số tiền vay ban đầu |
| NgayLap | DATE | DEFAULT GETDATE() | Ngày lập hợp đồng |
| HanLaiDon | DATE | NOT NULL | Mốc thời gian trước khi tính lãi kép |
| HanThanhLy | DATE | NOT NULL | Mốc thời gian xử lý thanh lý tài sản |
| TrangThaiHD | NVARCHAR(30) | DEFAULT N'Đang vay', CHECK | Trạng thái hiện tại của hợp đồng |

### Các ràng buộc chính

| Ràng buộc | Ý nghĩa |
|---|---|
| PRIMARY KEY (MaHD) | Mỗi hợp đồng có một mã duy nhất |
| FOREIGN KEY (MaKH) | Hợp đồng phải thuộc về một khách hàng đã tồn tại |
| CHECK (TienVayGoc > 0) | Số tiền vay phải lớn hơn 0 |
| CHECK (HanThanhLy > HanLaiDon) | Hạn thanh lý phải sau hạn lãi đơn |
| CHECK (NgayLap <= HanLaiDon) | Ngày lập hợp đồng không được sau hạn lãi đơn |

### Các trạng thái hợp đồng

| Trạng thái | Ý nghĩa |
|---|---|
| Đang vay | Hợp đồng còn trong thời gian vay bình thường |
| Quá hạn | Hợp đồng đã vượt hạn lãi đơn |
| Đang trả góp | Khách đã thanh toán một phần nhưng chưa hết nợ |
| Đã thanh toán | Khách đã trả hết tiền gốc và lãi |
| Đã thanh lý | Hợp đồng đã bị xử lý bằng cách thanh lý tài sản |

### Giải thích

Bảng này là bảng trung tâm của hệ thống.  
Mỗi hợp đồng liên kết với một khách hàng thông qua `MaKH`.

Thông tin `HanLaiDon` và `HanThanhLy` được tách rõ để phục vụ các nghiệp vụ tính lãi, xử lý quá hạn và thanh lý tài sản ở các nhiệm vụ sau.

---

## 4.3. Bảng TaiSanCamCo

Bảng `TaiSanCamCo` dùng để lưu danh sách tài sản mà khách hàng đem đến cầm cố.

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---|---|---|---|
| MaTS | CHAR(10) | PRIMARY KEY | Mã tài sản |
| MaHD | CHAR(10) | FOREIGN KEY | Mã hợp đồng chứa tài sản |
| TenTaiSan | NVARCHAR(150) | NOT NULL | Tên tài sản cầm cố |
| NhomTaiSan | NVARCHAR(50) | NULL | Nhóm tài sản, ví dụ điện thoại, xe máy, trang sức |
| GiaThamDinh | DECIMAL(18,2) | NOT NULL, CHECK > 0 | Giá trị tài sản được thẩm định |
| TinhTrangTS | NVARCHAR(30) | DEFAULT N'Đang cầm cố', CHECK | Trạng thái hiện tại của tài sản |
| GhiChu | NVARCHAR(255) | NULL | Ghi chú thêm về tài sản |

### Các trạng thái tài sản

| Trạng thái | Ý nghĩa |
|---|---|
| Đang cầm cố | Tài sản đang được cửa hàng giữ |
| Chờ thanh lý | Tài sản đã quá hạn và có thể đưa vào xử lý |
| Đã bán thanh lý | Tài sản đã được bán để thu hồi nợ |
| Đã trả khách | Tài sản đã được hoàn trả cho khách hàng |

### Giải thích

Một hợp đồng có thể có nhiều tài sản cầm cố nên bảng `TaiSanCamCo` có khóa ngoại `MaHD`.

Cột `GiaThamDinh` phải lớn hơn 0 để tránh nhập sai giá trị tài sản.

Trạng thái tài sản được lưu riêng để dễ theo dõi tài sản nào còn đang giữ, tài sản nào đã trả khách hoặc đã bán thanh lý.

---

## 4.4. Bảng LichSuThanhToan

Bảng `LichSuThanhToan` dùng để lưu lại các lần khách hàng trả tiền.

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---|---|---|---|
| MaTT | CHAR(10) | PRIMARY KEY | Mã thanh toán |
| MaHD | CHAR(10) | FOREIGN KEY | Mã hợp đồng được thanh toán |
| NgayThanhToan | DATE | DEFAULT GETDATE() | Ngày khách hàng trả tiền |
| SoTienThanhToan | DECIMAL(18,2) | NOT NULL, CHECK > 0 | Số tiền khách trả trong lần đó |
| DuNoSauThanhToan | DECIMAL(18,2) | NOT NULL, CHECK >= 0 | Dư nợ còn lại sau thanh toán |
| NguoiThu | NVARCHAR(100) | NULL | Người thu tiền |
| HinhThuc | NVARCHAR(30) | DEFAULT N'Tiền mặt' | Hình thức thanh toán |

### Giải thích

Bảng này đóng vai trò là bảng ghi lịch sử giao dịch.

Khi khách hàng trả tiền nhiều lần, mỗi lần trả sẽ được lưu thành một dòng riêng. Cách thiết kế này giúp hệ thống không bị mất lịch sử thanh toán.

Ví dụ, nếu khách trả 1.000.000 đồng hôm nay và 2.000.000 đồng vào ngày khác thì hệ thống sẽ có 2 dòng thanh toán khác nhau, thay vì chỉ cập nhật đè lên một số tổng.

---

# 5. Sơ đồ ERD

<img width="1917" height="1077" alt="ảnh 6" src="https://github.com/user-attachments/assets/f40c3f3a-2331-48db-bdc2-1093b1adb8ab" />


*Ảnh sơ đồ ERD thể hiện quan hệ giữa các bảng trong cơ sở dữ liệu.*

---

# 6. Mô tả các quan hệ

## 6.1. Quan hệ KhachHang - HopDongCamDo

`KhachHang (1) -------- (N) HopDongCamDo`

Một khách hàng có thể có nhiều hợp đồng cầm đồ.  
Một hợp đồng chỉ thuộc về một khách hàng.

Khóa liên kết:

`KhachHang.MaKH = HopDongCamDo.MaKH`

Ý nghĩa ràng buộc:

Không thể tạo hợp đồng cho một khách hàng chưa tồn tại trong bảng `KhachHang`.

---

## 6.2. Quan hệ HopDongCamDo - TaiSanCamCo

`HopDongCamDo (1) -------- (N) TaiSanCamCo`

Một hợp đồng có thể có nhiều tài sản cầm cố.  
Một tài sản chỉ thuộc về một hợp đồng cụ thể.

Khóa liên kết:

`HopDongCamDo.MaHD = TaiSanCamCo.MaHD`

Ý nghĩa ràng buộc:

Không thể thêm tài sản cầm cố cho một hợp đồng chưa tồn tại trong bảng `HopDongCamDo`.

---

## 6.3. Quan hệ HopDongCamDo - LichSuThanhToan

`HopDongCamDo (1) -------- (N) LichSuThanhToan`

Một hợp đồng có thể có nhiều lần thanh toán.  
Mỗi lần thanh toán chỉ thuộc về một hợp đồng.

Khóa liên kết:

`HopDongCamDo.MaHD = LichSuThanhToan.MaHD`

Ý nghĩa ràng buộc:

Không thể ghi nhận thanh toán nếu hợp đồng chưa tồn tại.

---

# 7. Đánh giá chuẩn hóa

Cơ sở dữ liệu được thiết kế theo hướng tách riêng các nhóm thông tin:

- Thông tin khách hàng lưu trong bảng `KhachHang`.
- Thông tin hợp đồng lưu trong bảng `HopDongCamDo`.
- Thông tin tài sản lưu trong bảng `TaiSanCamCo`.
- Thông tin trả nợ lưu trong bảng `LichSuThanhToan`.

Thiết kế này giúp hạn chế trùng lặp dữ liệu và dễ bảo trì.

## Mức 1NF

Các cột trong bảng đều chứa giá trị đơn, không lưu danh sách nhiều giá trị trong cùng một ô.

## Mức 2NF

Mỗi bảng có khóa chính riêng. Các thuộc tính trong bảng phụ thuộc vào khóa chính của bảng đó.

## Mức 3NF

Các bảng được tách theo đúng nhóm dữ liệu, tránh phụ thuộc bắc cầu.  
Ví dụ, thông tin khách hàng không lưu lặp lại trong bảng hợp đồng mà chỉ liên kết bằng `MaKH`.

---

# 8. Kết luận nhiệm vụ 1

Sau khi hoàn thành nhiệm vụ 1, cơ sở dữ liệu đã có đầy đủ các bảng chính để quản lý nghiệp vụ cầm đồ.

Các bảng đã được thiết kế với khóa chính, khóa ngoại và ràng buộc kiểm tra dữ liệu.  
Sơ đồ ERD thể hiện rõ quan hệ một-nhiều giữa khách hàng, hợp đồng, tài sản và lịch sử thanh toán.

Phần thiết kế này là nền tảng để tiếp tục xây dựng các procedure, function và trigger ở nhiệm vụ 2.
# Nhiệm vụ 2: Cài đặt SQL

## Event 1: Đăng ký hợp đồng mới
<img width="1920" height="1080" alt="ảnh 7" src="https://github.com/user-attachments/assets/26b9dae3-1fa3-46af-a06b-6777f2567f39" />


<img width="1920" height="1080" alt="ảnh 8" src="https://github.com/user-attachments/assets/60bdf7a7-b081-472a-93e0-0f0bb29211d2" />



<img width="1920" height="1080" alt="ảnh 9" src="https://github.com/user-attachments/assets/ccf152fe-2cbb-4e26-8721-e4f09d2881a7" />



<img width="1920" height="1080" alt="ảnh 10" src="https://github.com/user-attachments/assets/a2a3a066-0f51-427f-95a0-b3b33c78668b" />



<img width="1920" height="1080" alt="ảnh 11" src="https://github.com/user-attachments/assets/e689f4fa-0a5c-4dcf-b7bb-95fd480f2a4e" />



<img width="1920" height="1080" alt="ảnh 12" src="https://github.com/user-attachments/assets/df616ae1-ccf7-4870-9587-da40343d76e2" />



*Ảnh tạo Store Procedure `sp_DangKyHopDongCamDo` dùng để đăng ký hợp đồng cầm đồ mới.*

Procedure này thực hiện các công việc:

| Công việc | Mô tả |
|---|---|
| Kiểm tra khách hàng | Nếu CCCD đã tồn tại thì dùng lại khách hàng cũ |
| Thêm khách hàng mới | Nếu chưa có khách hàng thì tự sinh mã khách hàng |
| Tạo hợp đồng | Lưu tiền vay gốc, ngày lập, hạn lãi đơn, hạn thanh lý |
| Thêm tài sản | Nhận danh sách nhiều tài sản từ XML |
| Tự sinh mã | Tự sinh mã khách hàng, hợp đồng, tài sản |
| Transaction | Nếu lỗi thì rollback để tránh sai dữ liệu |

---

## Event 2: Tính toán công nợ thời gian thực

### Function 1: Tính dư nợ sau một giao dịch

<img width="1917" height="1077" alt="ảnh 13" src="https://github.com/user-attachments/assets/6fc83364-8cda-4dd6-a801-55142080e5a6" />


Function `fn_TinhNoSauGiaoDich` nhận vào mã thanh toán và ngày cần tính, sau đó trả về số dư nợ còn lại sau giao dịch đó.

| Bước | Nội dung |
|---|---|
| 1 | Nhận mã thanh toán |
| 2 | Tìm giao dịch trong bảng `LichSuThanhToan` |
| 3 | Lấy giá trị `DuNoSauThanhToan` |
| 4 | Trả về số tiền còn nợ |

### Function 2: Tính tổng nợ của hợp đồng

<img width="1920" height="1080" alt="ảnh 14" src="https://github.com/user-attachments/assets/23aedf96-bbf3-4cbf-9f26-15cb85b91e89" />


<img width="1920" height="1080" alt="ảnh 15" src="https://github.com/user-attachments/assets/0c73355e-8c13-48aa-bbd3-1015f4aac437" />


Function `fn_TinhTongNoHopDong` dùng để tính số tiền khách còn phải trả của một hợp đồng tại một ngày bất kỳ.

Công thức áp dụng:

| Trường hợp | Cách tính |
|---|---|
| Trước hạn lãi đơn | Gốc + lãi đơn |
| Sau hạn lãi đơn | Gốc + lãi đơn đã tích lũy + lãi kép |
| Đã trả một phần | Tổng nợ - tổng tiền đã thanh toán |

Lãi đơn được tính theo quy định: `5.000đ / 1.000.000đ / ngày`, tương đương `0.005` mỗi ngày.

---

## Event 3: Xử lý trả nợ và hoàn trả tài sản

<img width="1920" height="1080" alt="ảnh 16" src="https://github.com/user-attachments/assets/df937b87-6086-470a-b219-2863ed731982" />




<img width="1920" height="1080" alt="ảnh 17" src="https://github.com/user-attachments/assets/9f1c1d77-0e10-4caf-b4a1-5aef2f1a9e2e" />


<img width="1920" height="1080" alt="ảnh 18" src="https://github.com/user-attachments/assets/207de451-a944-469a-9523-f654d0360bf5" />



<img width="1920" height="1080" alt="ảnh 19" src="https://github.com/user-attachments/assets/333e4bda-5089-467c-96b9-249e0a897b7c" />


<img width="1920" height="1080" alt="ảnh 20" src="https://github.com/user-attachments/assets/a01f4354-47ad-4f8d-89c9-c9b41901bdb2" />





*Ảnh tạo Store Procedure `sp_XuLyTraNo` dùng để xử lý khi khách hàng đến trả tiền.*

Procedure này thực hiện các công việc:

| Công việc | Mô tả |
|---|---|
| Kiểm tra hợp đồng | Kiểm tra hợp đồng có tồn tại không |
| Kiểm tra thanh lý | Nếu tài sản đã bán thanh lý thì không thu tiền |
| Tính tổng nợ | Gọi function `fn_TinhTongNoHopDong` |
| Trừ tiền khách trả | Tính lại số tiền còn nợ |
| Ghi log | Lưu lịch sử thanh toán vào bảng `LichSuThanhToan` |
| Cập nhật trạng thái | Nếu trả hết thì chuyển sang `Đã thanh toán` |
| Gợi ý trả tài sản | Đưa ra tài sản có thể trả nếu giá trị còn lại vẫn đủ đảm bảo khoản nợ |
## Event 4: Truy vấn danh sách nợ xấu

<img width="1920" height="1080" alt="ảnh 21" src="https://github.com/user-attachments/assets/915cd117-6171-4082-b2c4-ffd9a785b873" />


*Ảnh tạo Store Procedure `sp_DanhSachNoXau` dùng để truy vấn danh sách hợp đồng đã quá hạn lãi đơn nhưng chưa thanh toán xong.*

Procedure này thực hiện các công việc:

| STT | Công việc | Giải thích |
|---|---|---|
| 1 | Lọc hợp đồng quá hạn | Chọn hợp đồng có ngày hiện tại lớn hơn `HanLaiDon` |
| 2 | Lấy thông tin khách hàng | Lấy họ tên, số điện thoại từ bảng `KhachHang` |
| 3 | Tính số ngày quá hạn | Tính từ `HanLaiDon` đến ngày hiện tại |
| 4 | Tính tổng nợ hiện tại | Gọi function `fn_TinhTongNoHopDong` |
| 5 | Tính nợ sau 1 tháng | Gọi function và cộng thêm 1 tháng vào ngày hiện tại |

---

## Event 5: Quản lý thanh lý tài sản

### Trigger 1: Tự động chuyển hợp đồng sang quá hạn

<img width="1920" height="1080" alt="ảnh 22" src="https://github.com/user-attachments/assets/70b68ee7-9d79-4891-9b31-d2575b57c0bc" />


Trigger `trg_CapNhatHopDongQuaHan` dùng để chuyển trạng thái hợp đồng từ `Đang vay` sang `Quá hạn` nếu hợp đồng đã vượt quá `HanLaiDon`.

### Trigger 2: Tự động chuyển tài sản sang chờ thanh lý

<img width="1920" height="1080" alt="ảnh 23" src="https://github.com/user-attachments/assets/6ffe5d34-fab9-4998-a3bc-140f90c94618" />


Trigger `trg_TaiSanChoThanhLy` dùng để chuyển tài sản sang trạng thái `Chờ thanh lý` khi hợp đồng đã quá hạn và vượt quá `HanThanhLy`.

### Trigger 3: Tự động chuyển tài sản thành đã bán thanh lý

<img width="1920" height="1080" alt="ảnh 24" src="https://github.com/user-attachments/assets/e94c63ac-66ab-44dc-9d7d-e63f71a5057f" />


Trigger `trg_TaiSanDaBanThanhLy` dùng để chuyển tài sản sang trạng thái `Đã bán thanh lý` khi hợp đồng được cập nhật sang trạng thái `Đã thanh lý`.

### Bảng trạng thái tài sản

| Trạng thái | Ý nghĩa | Khi nào xảy ra |
|---|---|---|
| Đang cầm cố | Tài sản đang được cửa hàng giữ | Khi mới tạo hợp đồng |
| Chờ thanh lý | Tài sản đã quá hạn, chuẩn bị xử lý | Khi hợp đồng quá hạn và vượt `HanThanhLy` |
| Đã bán thanh lý | Tài sản đã được bán để thu hồi nợ | Khi hợp đồng chuyển sang `Đã thanh lý` |
| Đã trả khách | Tài sản đã trả lại cho khách | Khi khách thanh toán hết nợ |

---

## Sự kiện bổ sung: Gia hạn hợp đồng

<img width="1920" height="1080" alt="ảnh 25" src="https://github.com/user-attachments/assets/000ebce8-fef0-4ef9-a589-49124ab39fda" />



<img width="1920" height="1080" alt="ảnh 26" src="https://github.com/user-attachments/assets/e13cdf7b-bde0-4829-881e-63bfc1da5c17" />




<img width="1920" height="1080" alt="ảnh 27" src="https://github.com/user-attachments/assets/683f40e6-0164-4ed5-8842-d09ab71ccdc2" />



*Ảnh tạo Store Procedure `sp_GiaHanHopDong` dùng để gia hạn hợp đồng khi khách hàng trả tiền lãi đến thời điểm hiện tại.*

Procedure này thực hiện các công việc:

| STT | Công việc | Giải thích |
|---|---|---|
| 1 | Nhận mã hợp đồng | Kiểm tra hợp đồng có tồn tại không |
| 2 | Tính tiền cần trả | Gọi function `fn_TinhTongNoHopDong` |
| 3 | Ghi lịch sử thanh toán | Thêm một dòng vào `LichSuThanhToan` |
| 4 | Cập nhật hạn mới | Cập nhật `HanLaiDon`, `HanThanhLy` |
| 5 | Đưa về trạng thái đang vay | Hợp đồng được tiếp tục theo kỳ hạn mới |
| 6 | Trả thông báo | Hiển thị số tiền đã thu và hạn mới |

---

# Chạy thử các chức năng
## Thêm dữ liệu
<img width="1917" height="1077" alt="ảnh 28" src="https://github.com/user-attachments/assets/dc51fb9e-8548-4e1f-a2ca-803e6c0515ed" />

*Thêm dữ liệu mẫu gồm khách hàng, hợp đồng, tài sản cầm cố và lịch sử thanh toán.*
## Kiểm tra function tính dư nợ sau giao dịch

<img width="1920" height="1080" alt="ảnh 29" src="https://github.com/user-attachments/assets/b1b27998-d586-49d8-9d63-c5ff1ce0ede8" />


*Kiểm tra function `fn_TinhNoSauGiaoDich` trả về số dư nợ sau một lần thanh toán.*

## Kiểm tra function tính tổng nợ hợp đồng

<img width="1920" height="1080" alt="ảnh 30" src="https://github.com/user-attachments/assets/d13f256e-be9b-412d-89aa-aa030881a27a" />


*Kiểm tra function `fn_TinhTongNoHopDong` để tính công nợ của hợp đồng tại ngày hiện tại.*

## Kiểm tra xử lý trả nợ

<img width="1917" height="1077" alt="ảnh 31" src="https://github.com/user-attachments/assets/a90690a9-a2a3-4d27-9c8d-34b5a50d7337" />

<img width="1917" height="1077" alt="ảnh 32" src="https://github.com/user-attachments/assets/2f021f37-0497-4af7-84b3-c95e986f0641" />



*Kiểm tra procedure `sp_XuLyTraNo`, sau khi khách trả tiền hệ thống cập nhật trạng thái hợp đồng và ghi lịch sử thanh toán.*

## Kiểm tra danh sách nợ xấu

<img width="1920" height="1080" alt="ảnh 33" src="https://github.com/user-attachments/assets/27793b82-c6b9-4ad3-9890-4de24a98888e" />


*Kiểm tra procedure `sp_DanhSachNoXau`, hiển thị các hợp đồng đã quá hạn lãi đơn.*

## Kiểm tra trigger tự động

<img width="1920" height="1080" alt="ảnh 34" src="https://github.com/user-attachments/assets/c8f702dc-4134-4559-b647-ae5a34b15576" />

<img width="1920" height="1080" alt="ảnh 35" src="https://github.com/user-attachments/assets/deb425b7-c516-4422-ad33-867628f22cef" />



*Kiểm tra các trigger tự động cập nhật trạng thái hợp đồng và tài sản khi quá hạn hoặc thanh lý.*

## Kiểm tra gia hạn hợp đồng

<img width="1920" height="1080" alt="ảnh 36" src="https://github.com/user-attachments/assets/a82f7abf-28f7-4bfa-9a46-4b0d46fff64d" />


*Kiểm tra procedure `sp_GiaHanHopDong`, sau khi gia hạn hệ thống cập nhật deadline mới và ghi log thanh toán.*
