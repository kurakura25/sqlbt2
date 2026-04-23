## PHẦN MỞ ĐẦU
**Thông tin sinh viên:**

- Họ và tên: Từ Văn Hải

- Mã sinh viên: K235480106022

- Lớp: K59KMT.K01

- Khoa: Điện tử
### Phần 1: Khởi tạo database và bảng
- Tạo cơ sở dữ liệu

- Bảng [KhachHang]:

Sử dụng MaKhachHang làm Khóa chính (PK), tự động tăng (IDENTITY(1,1)).

Họ tên dùng NVARCHAR để hỗ trợ tiếng Việt có dấu.

Điểm đánh giá (DiemDanhGia) có ràng buộc CHECK từ 1 đến 5 sao.
```sql
CREATE TABLE [KhachHang] (
    [MaKhachHang] INT IDENTITY(1,1) NOT NULL,
    [HoTenKhachHang] NVARCHAR(100) NOT NULL,
    [NgaySinh] DATE,
    [SoDienThoai] VARCHAR(15) NOT NULL,
    [DiemDanhGia] INT DEFAULT 5,
    CONSTRAINT [PK_KhachHang] PRIMARY KEY ([MaKhachHang]),
    CONSTRAINT [CK_DiemDanhGia] CHECK ([DiemDanhGia] >= 1 AND [DiemDanhGia] <= 5)
);
``` 

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/ccd4aab3-7b2d-4dd6-9316-8d5ba0eb9ce4" />


<p align="center">Tạo bảng KhachHang</p>

- Bảng [Phong]:

Sử dụng MaPhong (VARCHAR) làm Khóa chính (PK) vì tên phòng thường có chữ và số (VD: P101, P102).

Kiểu MONEY được dùng cho giá phòng (GiaPhongTheoNgay) để tối ưu việc lưu trữ và tính toán tiền tệ. Ràng buộc CHECK giá phải > 0.

```sql
CREATE TABLE [Phong] (
    [MaPhong] VARCHAR(10) NOT NULL,
    [LoaiPhong] NVARCHAR(50) NOT NULL,
    [DienTich] FLOAT,
    [GiaPhongTheoNgay] MONEY NOT NULL,
    CONSTRAINT [PK_Phong] PRIMARY KEY ([MaPhong]),
    CONSTRAINT [CK_GiaPhong] CHECK ([GiaPhongTheoNgay] > 0)
);
``` 

<img width="1913" height="1079" alt="image" src="https://github.com/user-attachments/assets/6f615ef1-bc74-43c6-8bd9-da9533d70936" />


<p align="center">Tạo bảng Phong</p>
- Bảng [DatPhong]:


Có MaDatPhong làm Khóa chính (PK).
Chứa 2 Khóa ngoại (FK) trỏ về MaKhachHang và MaPhong để thiết lập quan hệ.
Ràng buộc logic: ThoiGianTra luôn phải lớn hơn hoặc bằng ThoiGianNhan. Tiền cọc (TienDatCoc) lớn hơn hoặc bằng 0.


```sql
CREATE TABLE [DatPhong] (
    [MaDatPhong] INT IDENTITY(1,1) NOT NULL,
    [MaKhachHang] INT NOT NULL,
    [MaPhong] VARCHAR(10) NOT NULL,
    [ThoiGianNhan] DATETIME NOT NULL,
    [ThoiGianTra] DATETIME,
    [TienDatCoc] MONEY DEFAULT 0,
    CONSTRAINT [PK_DatPhong] PRIMARY KEY ([MaDatPhong]),
    CONSTRAINT [FK_DatPhong_KhachHang] FOREIGN KEY ([MaKhachHang]) REFERENCES [KhachHang]([MaKhachHang]),
    CONSTRAINT [FK_DatPhong_Phong] FOREIGN KEY ([MaPhong]) REFERENCES [Phong]([MaPhong]),
    CONSTRAINT [CK_ThoiGianTra] CHECK ([ThoiGianTra] >= [ThoiGianNhan]),
    CONSTRAINT [CK_TienDatCoc] CHECK ([TienDatCoc] >= 0)
);
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/bfc2153b-a362-4eaa-b36c-95d9e783a28f" />
<p align="center">Tạo bảng DatPhong</p>


### Phần 2: Xây dựng Function


1. Các loại hàm có sẵn (Built-in Functions)

SQL Server cung cấp hàng trăm hàm có sẵn để xử lý dữ liệu, được chia thành các nhóm chính:

- Hàm chuỗi (String Functions): LEN(), SUBSTRING(), REPLACE(), UPPER(), LOWER()...

- Hàm ngày tháng (Date/Time Functions): GETDATE(), DATEDIFF(), DATEADD(), YEAR(), MONTH()...

- Hàm toán học (Mathematical Functions): ABS(), ROUND(), CEILING(), FLOOR()...

- Hàm tổng hợp (Aggregate Functions): SUM(), COUNT(), MAX(), MIN(), AVG()...

- Hàm chuyển đổi kiểu (Conversion Functions): CAST(), CONVERT()...

- Hàm hệ thống (System Functions): ISNULL(), COALESCE(), NEWID(), CHOOSE()...

Tìm hiểu chi tiết 1 số Built-in Functions

- NEWID(): Tạo ra một mã định danh duy nhất (GUID). Rất tuyệt vời khi cần sắp xếp dữ liệu ngẫu nhiên.
```sql
-- Lấy ngẫu nhiên 1 phòng bất kỳ để gợi ý cho khách hàng
SELECT TOP 1 MaPhong, LoaiPhong FROM [Phong] ORDER BY NEWID();
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/96aad3cc-0840-4545-952f-2023647be74b" />

<p align="center">Lấy ngẫu nhiên 1 phòng bất kỳ để gợi ý cho khách hàng</p>


- CHOOSE(): Trả về một mục từ một danh sách dựa trên chỉ mục (giống như mảng). Giúp tránh phải viết CASE WHEN dài dòng.
```sql
-- Dịch điểm đánh giá (1-5) thành text
SELECT HoTenKhachHang, DiemDanhGia, 
       CHOOSE(DiemDanhGia, N'Rất Tệ', N'Tệ', N'Bình Thường', N'Tốt', N'Tuyệt Vời') AS XepLoai
FROM [KhachHang];

```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/9c44707b-38af-4d2e-948d-870ac3306157" />


<p align="center">Dịch điểm đánh giá (1-5) thành text</p>


- DATEDIFF(): Tính khoảng cách giữa 2 mốc thời gian (theo ngày, tháng, năm, giờ...). Vô cùng quan trọng trong bài toán quản lý khách sạn.

```sql
-- Tính số ngày khách đã ở
SELECT MaDatPhong, ThoiGianNhan, ThoiGianTra,
       DATEDIFF(DAY, ThoiGianNhan, ThoiGianTra) AS SoNgayO
FROM [DatPhong] WHERE ThoiGianTra IS NOT NULL;
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/720a60d4-8acd-42cb-a43a-52067d0f0e02" />

<p align="center">Tính số ngày khách đã ở</p>


2. Hàm do người dùng tự viết (User-Defined Functions - UDFs)


a) Mục đích
- UDF được sử dụng để đóng gói các logic nghiệp vụ phức tạp thành một khối xử lý có thể tái sử dụng nhiều lần.
- Giúp các câu lệnh như SELECT, UPDATE trở nên ngắn gọn, dễ đọc và dễ bảo trì.
- Tăng tính tái sử dụng và nhất quán trong hệ thống cơ sở dữ liệu.


b) Phân loại và khi sử dụng

- Scalar Function (Hàm vô hướng)

Trả về một giá trị duy nhất (số, chuỗi, ngày tháng…).
Phù hợp khi cần tính toán trên từng dòng dữ liệu riêng lẻ.
Ví dụ: tính tiền, tính thuế, định dạng số điện thoại.

- Inline Table-Valued Function (ITVF)

Trả về một bảng dữ liệu từ một câu lệnh SELECT duy nhất.
Hoạt động tương tự như View nhưng có thể truyền tham số.
Dùng khi cần lọc hoặc truy vấn dữ liệu theo điều kiện động.

- Multi-statement Table-Valued Function (MSTVF)

Trả về một bảng dữ liệu, nhưng cho phép sử dụng nhiều câu lệnh trong thân hàm.
Có thể dùng các cấu trúc như IF...ELSE, WHILE, biến tạm, hoặc chèn dữ liệu vào bảng trung gian.
Phù hợp khi xử lý logic phức tạp, nhiều bước, không thể biểu diễn bằng một câu lệnh SELECT đơn giản.


c) Tại sao vẫn cần UDF khi đã có Built-in Functions?

Mặc dù hệ quản trị cơ sở dữ liệu cung cấp nhiều hàm dựng sẵn (Built-in Functions), nhưng các hàm này chỉ xử lý các tác vụ cơ bản và mang tính chung như: tính toán số học, xử lý chuỗi, ngày tháng,...

Tuy nhiên, trong thực tế:

- Mỗi hệ thống đều có quy tắc nghiệp vụ riêng (Business Logic)
- Các quy tắc này thường phức tạp và đặc thù, không thể dùng trực tiếp hàm có sẵn
- Việc viết lặp lại logic nhiều lần trong các câu lệnh SQL sẽ gây khó bảo trì và dễ sai sót



👉 Vì vậy, cần sử dụng UDF để:

- Đóng gói logic nghiệp vụ thành một hàm riêng biệt
- Tái sử dụng nhiều lần trong các truy vấn khác nhau
- Đảm bảo tính nhất quán và dễ bảo trì cho hệ thống


3. Viết các Function
# Viết 1 Scalar Function (Hàm trả về 1 giá trị)


Viết hàm tính tổng tiền phòng cho một lần đặt phòng cụ thể. Tổng tiền = (Số ngày ở) * (Giá phòng). Nếu khách thuê và trả phòng trong cùng 1 ngày thì tính là 1 ngày. Nếu khách chưa trả phòng (ThoiGianTra bị NULL) thì tính số ngày từ lúc nhận đến thời điểm hiện tại.

### Phân tích logic hàm fn_TinhTongTienPhong

> Hàm được xây dựng nhằm tính tổng tiền thuê phòng dựa trên thông tin đặt phòng và giá phòng theo ngày.

**Luồng xử lý tổng quát:**

Bước 1. Hàm nhận vào mã đặt phòng làm tham số đầu vào.

Bước 2. Từ mã này, hệ thống truy xuất dữ liệu liên quan gồm:
   - Thời gian nhận phòng
   - Thời gian trả phòng (nếu chưa trả thì lấy thời điểm hiện tại)
   - Giá thuê phòng theo ngày

Bước 3. Dựa trên thời gian nhận và trả, hệ thống tính số ngày thuê:
   - Lấy chênh lệch giữa ngày trả và ngày nhận
   - Nếu thời gian thuê trong cùng một ngày thì vẫn tính là 1 ngày

Bước 4. Sau khi xác định số ngày thuê, hệ thống tiến hành:
   - Nhân số ngày với giá thuê mỗi ngày để ra tổng tiền

Bước 5. Cuối cùng, hàm trả về tổng tiền đã tính được.


```sql
CREATE FUNCTION fn_TinhTongTienPhong (
    @MaDatPhong INT
)
RETURNS MONEY
AS
BEGIN
    DECLARE @TienPhong MONEY;
    DECLARE @SoNgay INT;
    DECLARE @GiaNgay MONEY;
    DECLARE @NgayNhan DATETIME;
    DECLARE @NgayTra DATETIME;

    -- Lấy thông tin từ bảng DatPhong và Phong
    SELECT @NgayNhan = d.ThoiGianNhan, 
           @NgayTra = ISNULL(d.ThoiGianTra, GETDATE()), -- Nếu chưa trả, lấy ngày hiện tại
           @GiaNgay = p.GiaPhongTheoNgay
    FROM [DatPhong] d
    JOIN [Phong] p ON d.MaPhong = p.MaPhong
    WHERE d.MaDatPhong = @MaDatPhong;

    -- Tính số ngày (nếu cùng ngày thì tính 1 ngày)
    SET @SoNgay = DATEDIFF(DAY, @NgayNhan, @NgayTra);
    IF @SoNgay = 0 SET @SoNgay = 1;

    -- Tính tổng tiền
    SET @TienPhong = @SoNgay * @GiaNgay;

    RETURN @TienPhong;
END;
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/6d11295c-15bb-4011-ac92-2de795581a8c" />


<p align="center">Tạo hàm TinhTongTienPhong</p>

```sql
-- Hiển thị danh sách đặt phòng kèm tổng tiền cần thanh toán
SELECT MaDatPhong, 
       MaKhachHang, 
       MaPhong, 
       ThoiGianNhan, 
       ThoiGianTra,
       dbo.fn_TinhTongTienPhong(MaDatPhong) AS TongTienPhaiTra
FROM [DatPhong];

```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/d41bf1b4-e8f3-41d1-b050-6d16044fea28" />


<p align="center">Hiển thị danh sách đặt phòng kèm tổng tiền cần thanh toán</p>

# Viết 1 Inline Table-Valued Function (ITVF)
Viết hàm lấy ra danh sách Lịch sử đặt phòng chi tiết của một khách hàng cụ thể khi truyền vào Mã Khách Hàng.

**Luồng xử lý tổng quát:**

Bước 1. Hàm nhận vào mã khách hàng làm tham số đầu vào.

Bước 2. Hệ thống truy xuất dữ liệu từ bảng đặt phòng để lấy các thông tin liên quan đến các lần đặt phòng của khách hàng.

Bước 3. Đồng thời, hệ thống kết nối với bảng phòng để lấy thêm thông tin về loại phòng tương ứng.

Bước 4. Thực hiện liên kết giữa hai bảng thông qua mã phòng.

Bước 5. Lọc dữ liệu theo mã khách hàng đã truyền vào để chỉ lấy các giao dịch của khách đó.

Bước 6. Trả về danh sách kết quả bao gồm:
   - Mã đặt phòng
   - Loại phòng
   - Thời gian nhận phòng
   - Thời gian trả phòng
   - Tiền đặt cọc

```sql
CREATE FUNCTION fn_LichSuGiaoDichCuaKhach (
    @MaKhachHang INT
)
RETURNS TABLE
AS
RETURN (
    SELECT 
        d.MaDatPhong,
        p.LoaiPhong,
        d.ThoiGianNhan,
        d.ThoiGianTra,
        d.TienDatCoc
    FROM [DatPhong] d
    JOIN [Phong] p ON d.MaPhong = p.MaPhong
    WHERE d.MaKhachHang = @MaKhachHang
);
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/01f4650c-5e08-4469-8a6f-229f22c487de" />


<p align="center">Tạo Function LichSuGiaoDichCuaKhach</p>


```sql
-- Xem lịch sử đặt phòng của khách hàng có mã số 1
SELECT * FROM dbo.fn_LichSuGiaoDichCuaKhach(1)
ORDER BY ThoiGianNhan DESC;
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/93506185-4228-4cb6-9b4f-ddc0b3e56c57" />
<p align="center">Xem lịch sử đặt phòng của khách hàng có mã số 1</p>

# Viết 1 Multi-statement Table-Valued Function (MSTVF)
Ban giám đốc cần một hàm báo cáo thống kê tình trạng hoạt động của các phòng trong một Tháng và Năm cụ thể. Hàm sẽ thống kê số lần được đặt của từng phòng, và dùng biến số phức tạp để gán nhãn:

- Từ 3 lần đặt trở lên: N'Phòng Hot'

- Từ 1 đến 2 lần: N'Bình thường'

- 0 lần (không ai đặt): N'Ế - Cần khuyến mãi'


**Luồng xử lý tổng quát:**

Bước 1. Hàm nhận vào hai tham số:
   - Tháng
   - Năm

Bước 2. Hệ thống truy xuất danh sách tất cả các phòng hiện có.

Bước 3. Thực hiện kết nối với dữ liệu đặt phòng để:
   - Xác định các lượt đặt phòng trong tháng và năm tương ứng
   - Đếm số lần mỗi phòng được đặt trong khoảng thời gian này

Bước 4. Đưa dữ liệu vào bảng tạm gồm:
   - Mã phòng
   - Loại phòng
   - Số lần được đặt
   - Trạng thái đánh giá ban đầu

Bước 5. Dựa trên số lần được đặt, hệ thống tiến hành phân loại:
   - Nếu số lần đặt từ 3 trở lên → gán nhãn "Phòng Hot"
   - Nếu số lần đặt từ 1 đến 2 → gán nhãn "Bình thường"
   - Nếu không có lượt đặt nào → gán nhãn "Ế - Cần khuyến mãi"

Bước 6. Sau khi hoàn tất việc phân loại, hệ thống trả về bảng kết quả thống kê gồm:
   - Mã phòng
   - Loại phòng
   - Số lần được đặt
   - Đánh giá hiệu quả hoạt động

  
```sql
CREATE FUNCTION fn_BaoCaoHoatDongPhong (
    @Thang INT,
    @Nam INT
)
RETURNS @BangThongKe TABLE (
    MaPhong VARCHAR(10),
    LoaiPhong NVARCHAR(50),
    SoLanDuocDat INT,
    DanhGiaHieuQua NVARCHAR(50)
)
AS
BEGIN
    -- Bước 1: Đẩy dữ liệu cơ bản (mã phòng, loại phòng, đếm số lần đặt) vào biến bảng
    INSERT INTO @BangThongKe (MaPhong, LoaiPhong, SoLanDuocDat, DanhGiaHieuQua)
    SELECT p.MaPhong, p.LoaiPhong, COUNT(d.MaDatPhong), N'Chưa xét'
    FROM [Phong] p
    LEFT JOIN [DatPhong] d ON p.MaPhong = d.MaPhong 
         AND MONTH(d.ThoiGianNhan) = @Thang 
         AND YEAR(d.ThoiGianNhan) = @Nam
    GROUP BY p.MaPhong, p.LoaiPhong;

    -- Bước 2: Sử dụng các lệnh UPDATE riêng biệt (Logic phức tạp nhiều bước) để đánh giá
    UPDATE @BangThongKe 
    SET DanhGiaHieuQua = N'Phòng Hot' 
    WHERE SoLanDuocDat >= 3;

    UPDATE @BangThongKe 
    SET DanhGiaHieuQua = N'Bình thường' 
    WHERE SoLanDuocDat > 0 AND SoLanDuocDat < 3;

    UPDATE @BangThongKe 
    SET DanhGiaHieuQua = N'Ế - Cần khuyến mãi' 
    WHERE SoLanDuocDat = 0;

    -- Bước 3: Trả về kết quả
    RETURN;
END;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/24d83ebc-6c45-4600-b4fd-c2a2de880ebf" />


<p align="center">Tạo hàm BaoCaoHoatDongPhong</p>


```sql
-- Lấy báo cáo hiệu quả kinh doanh phòng của tháng 10 năm 2023
SELECT * FROM dbo.fn_BaoCaoHoatDongPhong(10, 2023)
ORDER BY SoLanDuocDat DESC;

```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/aa251a8d-9478-4dc1-8781-2c5025a3dee2" />

<p align="center">Lấy báo cáo hiệu quả kinh doanh phòng của tháng 10 năm 2023</p>


### Phần 3: Xây dựng Store Procedure

1. Stored Procedure hệ thống (System Stored Procedures)


Trong SQL Server, hệ quản trị cung cấp nhiều **Stored Procedure hệ thống (System Stored Procedures)** nhằm hỗ trợ quản trị, truy vấn thông tin và thao tác với cơ sở dữ liệu.

**Các nhóm Stored Procedure phổ biến:**

**Nhóm quản lý cơ sở dữ liệu (Database Management)**
   - Dùng để tạo, sửa, xóa hoặc cấu hình database
   - Ví dụ: tạo database, kiểm tra trạng thái, sao lưu dữ liệu

**Nhóm quản lý bảng và đối tượng (Object Management)**
   - Thao tác với bảng, view, index, constraint
   - Hỗ trợ xem cấu trúc hoặc thay đổi đối tượng trong database

**Nhóm bảo mật (Security Management)**
   - Quản lý user, login, quyền truy cập
   - Phân quyền và kiểm soát truy cập dữ liệu

**Nhóm thông tin hệ thống (System Information)**
   - Cung cấp thông tin về cấu trúc database, bảng, cột, index
   - Giúp kiểm tra metadata của hệ thống

**Nhóm hiệu năng và giám sát (Performance & Monitoring)**
   - Theo dõi hoạt động hệ thống
   - Kiểm tra truy vấn, cache, tài nguyên sử dụng

---

**Đặc điểm chung của System Stored Procedures:**
- Thường có tiền tố `sp_`
- Được tích hợp sẵn trong SQL Server
- Có thể gọi trực tiếp mà không cần tự định nghĩa

---

**Ví dụ một số Stored Procedure phổ biến:**
- `sp_help`: Xem thông tin chi tiết của bảng hoặc đối tượng
- `sp_tables`: Liệt kê danh sách các bảng
- `sp_columns`: Xem thông tin các cột trong bảng
- `sp_rename`: Đổi tên đối tượng

# Một vài System SP và cách dùng:

sp_helptext (Trình soi code ẩn):

Công dụng: Khi bạn có quá nhiều Trigger, View, hoặc các Function/SP cũ do người khác viết và bạn không biết bên trong họ code gì, SP này sẽ in ra toàn bộ mã nguồn (source code) của đối tượng đó.

```sql
EXEC sp_helptext 'fn_TinhTongTienPhong'; -- Trả về source code của function đã viết ở bài trước
```

<img width="1918" height="1079" alt="image" src="https://github.com/user-attachments/assets/1ef04a78-060d-4aee-9780-20a85a73a243" />

<p align="center">Trả về source code của function đã viết ở bài trước</p>


sp_spaceused (Kế toán dung lượng):

Công dụng: Báo cáo nhanh xem một bảng dữ liệu hoặc toàn bộ Database đang chiếm bao nhiêu MB trên ổ cứng, và có chính xác bao nhiêu dòng dữ liệu (nhanh hơn nhiều so với việc dùng COUNT(*) trên bảng hàng triệu dòng).

```sql
EXEC sp_spaceused 'KhachHang'; -- Kiểm tra xem bảng Khách Hàng đang nặng bao nhiêu KB
```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/5df30a53-a665-43af-9db4-c7e67259ebd7" />


<p align="center">Kiểm tra dung lượng bảng Khách Hàng</p>

# Viết 01 Store Procedure đơn giản để thực hiện lệnh INSERT hoặc UPDATE dữ liệu, có kiểm tra điều kiện logic
- SP Thực hiện INSERT có kiểm tra logic

- Ý tưởng (Scenario): "Đặt phòng Thần tốc (Flash Booking)"

- Tình huống: Khách hàng đến quầy lễ tân lúc nửa đêm, đang rất mệt và muốn nhận phòng ngay lập tức theo một hạng phòng cụ thể (VD: Standard). Lễ tân không có thời gian dò tìm từng phòng.

- Yêu cầu SP: Lễ tân chỉ cần nhập Mã Khách Hàng và Loại Phòng. SP sẽ tự động rà soát xem có phòng nào thuộc loại đó đang trống không. Nếu có, tự động INSERT một giao dịch nhận phòng ngay tại thời điểm hiện tại (GETDATE()), với tiền cọc là 0. Nếu hết phòng trống hạng đó, in ra thông báo xin lỗi.


```sql
CREATE PROCEDURE sp_DatPhongThanToc
    @MaKhachHang INT,
    @LoaiPhong NVARCHAR(50)
AS
BEGIN
    DECLARE @MaPhongTrong VARCHAR(10);

    -- Tìm 1 phòng thuộc loại khách yêu cầu, và hiện tại không có ai đang ở (ThoiGianTra không bị NULL)
    -- Hoặc phòng chưa từng được ai đặt
    SELECT TOP 1 @MaPhongTrong = p.MaPhong
    FROM [Phong] p
    WHERE p.LoaiPhong = @LoaiPhong
      AND p.MaPhong NOT IN (
          SELECT MaPhong FROM [DatPhong] WHERE ThoiGianTra IS NULL
      );

    -- Kiểm tra logic: Nếu tìm thấy phòng trống thì INSERT, không thì báo lỗi
    IF @MaPhongTrong IS NOT NULL
    BEGIN
        INSERT INTO [DatPhong] (MaKhachHang, MaPhong, ThoiGianNhan, ThoiGianTra, TienDatCoc)
        VALUES (@MaKhachHang, @MaPhongTrong, GETDATE(), NULL, 0);

        PRINT N'Thành công! Đã tự động xếp khách vào phòng: ' + @MaPhongTrong;
    END
    ELSE
    BEGIN
        PRINT N'Rất tiếc! Hiện tại đã hết phòng trống cho hạng phòng: ' + @LoaiPhong;
    END
END;
GO

```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/c91d056f-9c0f-4887-b45a-d4f331633079" />


```sql
-- Khách hàng ID số 5 muốn đặt gấp 1 phòng Standard
EXEC sp_DatPhongThanToc @MaKhachHang = 5, @LoaiPhong = N'Standard (Tiêu chuẩn)';
```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/f8acef63-8c6e-47db-b6d7-3e9e1daf4c36" />


# Viết 01 Store Procedure có sử dụng tham số OUTPUT để trả về một giá trị tính toán

- SP có tham số OUTPUT

- Ý tưởng (Scenario): "Vòng quay may mắn: Tính tỷ lệ giảm giá cho khách"

- Tình huống: Khách sạn đang có chương trình tri ân. Khi khách thanh toán, lễ tân chạy hệ thống Vòng quay may mắn.

- Yêu cầu SP: Dựa vào DiemDanhGia (từ 1 đến 5 sao) của khách hàng. Nếu khách 5 sao (VIP): random giảm từ 20% - 40%. Nếu khách 4 sao: random giảm 5% - 15%. Khách < 4 sao: không giảm. SP không trả về dạng bảng, mà gán tỷ lệ % giảm giá này vào một tham số OUTPUT để phần mềm (C#, Java...) có thể lấy ra hiển thị lên UI.



```sql
CREATE PROCEDURE sp_QuayThuongGiamGia
    @MaKhachHang INT,
    @PhanTramGiamGia INT OUTPUT -- Biến Output để trả kết quả ra ngoài
AS
BEGIN
    DECLARE @Diem INT;
    
    -- Lấy điểm đánh giá của khách
    SELECT @Diem = DiemDanhGia FROM [KhachHang] WHERE MaKhachHang = @MaKhachHang;

    -- Kiểm tra logic và random % giảm giá
    IF @Diem = 5
    BEGIN
        -- Random từ 20 đến 40
        SET @PhanTramGiamGia = ABS(CHECKSUM(NEWID())) % 21 + 20; 
    END
    ELSE IF @Diem = 4
    BEGIN
        -- Random từ 5 đến 15
        SET @PhanTramGiamGia = ABS(CHECKSUM(NEWID())) % 11 + 5;
    END
    ELSE
    BEGIN
        -- Dưới 4 sao không được giảm
        SET @PhanTramGiamGia = 0;
    END
END;
GO

```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/a6f62282-13d9-4b23-bd1b-a5b2873aa410" />



```sql
-- Để chạy SP có OUTPUT, ta phải khai báo một biến để hứng kết quả
DECLARE @KetQuaGiamGia INT;

-- Gọi hàm và truyền biến hứng vào (Nhớ ghi chữ OUTPUT)
EXEC sp_QuayThuongGiamGia 
     @MaKhachHang = 10, 
     @PhanTramGiamGia = @KetQuaGiamGia OUTPUT;

-- In kết quả ra màn hình
PRINT N'Khách hàng này được giảm: ' + CAST(@KetQuaGiamGia AS VARCHAR) + N'% tổng hóa đơn.';

```


<img width="1916" height="1079" alt="image" src="https://github.com/user-attachments/assets/3e7dd7cd-4506-404d-87f9-2049fa5e844a" />


# Viết 01 Store Procedure trả về một tập kết quả (Result set) từ lệnh SELECT sau khi đã join nhiều bảng. 
- SP trả về Result Set (Lệnh SELECT JOIN nhiều bảng)

- Ý tưởng (Scenario): "Truy quét biệt đội sống ảo"

- Tình huống: Gần đây có tình trạng một số khách hàng thuê phòng hạng sang trọng (Suite hoặc Deluxe) nhưng chỉ thuê... trong vài tiếng hoặc tối đa 1 ngày, chủ yếu chỉ để chụp ảnh check-in sống ảo rồi trả phòng ngay. Khách sạn cần lên danh sách nhóm đối tượng này để có chiến lược quảng cáo (upsell) các gói chụp ảnh Studio riêng thay vì cho thuê phòng lưu trú.

- Yêu cầu SP: Trả về danh sách (Result set) gồm Tên Khách, SĐT, Loại Phòng, Số Ngày Ở của những khách thuê phòng Suite hoặc Deluxe mà thời gian ở <= 1 ngày. Cần JOIN 3 bảng để lấy đủ thông tin.


```sql
CREATE PROCEDURE sp_BaoCaoKhachHangSongAo
AS
BEGIN
    -- Trả về trực tiếp một câu lệnh SELECT (Result Set)
    SELECT 
        kh.HoTenKhachHang AS [Họ Tên Khách],
        kh.SoDienThoai AS [Số Điện Thoại],
        p.LoaiPhong AS [Hạng Phòng Đặt],
        dp.ThoiGianNhan AS [Lúc Nhận],
        dp.ThoiGianTra AS [Lúc Trả],
        -- Tính số giờ đã ở
        DATEDIFF(HOUR, dp.ThoiGianNhan, dp.ThoiGianTra) AS [Tổng Số Giờ Ở]
    FROM [DatPhong] dp
    JOIN [KhachHang] kh ON dp.MaKhachHang = kh.MaKhachHang
    JOIN [Phong] p ON dp.MaPhong = p.MaPhong
    WHERE 
        -- Chỉ lọc phòng hạng sang
        (p.LoaiPhong LIKE N'%Suite%' OR p.LoaiPhong LIKE N'%Deluxe%')
        -- Chỉ tính những giao dịch đã trả phòng
        AND dp.ThoiGianTra IS NOT NULL
        -- Thời gian lưu trú từ lúc nhận đến lúc trả <= 24 tiếng (1 ngày)
        AND DATEDIFF(HOUR, dp.ThoiGianNhan, dp.ThoiGianTra) <= 24
    ORDER BY [Tổng Số Giờ Ở] ASC;
END;
GO


```


<img width="1917" height="1079" alt="image" src="https://github.com/user-attachments/assets/02c14a3f-62f6-42a1-b780-60e2dd660bea" />



```sql
EXEC sp_BaoCaoKhachHangSongAo;

```



<img width="1913" height="1079" alt="image" src="https://github.com/user-attachments/assets/2a091521-566e-4e57-99b7-947559dc5206" />

