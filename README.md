# Bài kiểm tra số 2: Hệ Quản Trị Cơ Sở Dữ Liệu (SQL Server)
## PHẦN MỞ ĐẦU
**Thông tin sinh viên:**

- Họ và tên: Từ Văn Hải

- Mã sinh viên: K235480106022

- Lớp: K59KMT.K01
  
- Khoa: Điện tử
  
- Môn học: Hệ Quản Trị Cơ Sở Dữ Liệu (SQL Server)

**Yêu Cầu Đầu Bài**


Đề tài: Quản lý khách sạn

Thực hiện xây dựng một hệ thống quản lý khách sạn hoàn chỉnh trên SQL Server, đáp ứng các tiêu chí cụ thể sau đây:

- Toàn bộ quá trình thực hiện phải được ghi lại thông qua các screenshot minh họa, mỗi bức ảnh đều đi kèm với câu lệnh SQL tương ứng và chú thích rõ ràng về chức năng, mục đích xử lý, cũng như kết quả đạt được.
  
- Bài tập được nộp dưới dạng repository GitHub (public), bao gồm hai thành phần chính: tệp README.md chứa toàn bộ nội dung, hình ảnh minh họa và giải thích chi tiết, tệp baikiemtra2.sql chứa toàn bộ mã SQL.

  
Đánh giá dựa trên ba yếu tố quan trọng:
- Logic xử lý dữ liệu — các câu lệnh có giải quyết đúng bài toán không
  
- Quy tắc đặt tên — các bảng, cột, hàm có tuân theo chuẩn bướu Lạc Đà không
  
- Commit history — quá trình phát triển có rõ ràng, có thể theo dõi được không. Deadline nộp bài là cuối kỳ, sinh viên cần hoàn thành và push lên GitHub trước ngày hết hạn.

**Giới Thiệu Về Hệ Thống Quản Lý Khách Sạn**


Xây dựng một hệ thống quản lý khách sạn (QuanLyKhachSan) từ nền tảng SQL Server, bao gồm các chức năng chủ yếu như quản lý thông tin khách hàng, quản lý dữ liệu phòng, và quản lý các lượt đặt phòng. Mỗi khách hàng đều có hồ sơ lưu trữ với các thông tin cá nhân, số điện thoại liên hệ, ngày sinh và điểm đánh giá; mỗi phòng trong khách sạn được phân loại theo loại phòng (Standard, Superior, Deluxe, Suite), có diện tích riêng biệt và giá thuê theo ngày; các lượt đặt phòng lưu giữ mối quan hệ giữa khách hàng và phòng, cùng với thời gian nhận-trả phòng và tiền cọc.

Toàn bộ bài làm được chia thành 5 phần chính theo thứ tự tăng độ phức tạp. 

- Thiết Kế Cơ Sở Dữ Liệu — khởi tạo các bảng KhachHang, Phong, DatPhong với các ràng buộc toàn vẹn (Primary Key, Foreign Key, Check Constraint) và chèn dữ liệu mẫu. 
- Xây Dựng Function — tạo các hàm tính toán như tính số ngày ở, tính doanh thu, tìm phòng trống; những hàm này giúp tái sử dụng logic và làm sạch mã. 
- Xây Dựng Stored Procedure — tạo các thủ tục lưu trữ để xử lý các nghiệp vụ phức tạp như đặt phòng mới, tính hóa đơn, báo cáo doanh thu theo tháng.
- Trigger Xử Lý Nghiệp Vụ — định nghĩa các trigger tự động kích hoạt khi dữ liệu thay đổi (chẳng hạn, tự động cập nhật trạng thái phòng khi có đặt phòng mới, hoặc ghi log thay đổi). 
- Dùng Cursor và Xử Lý Dữ Liệu — sử dụng cursor để duyệt từng bản ghi và thực hiện các xử lý tuần tự, đồng thời so sánh hiệu năng giữa phương pháp cursor và phương pháp set-based, từ đó rút ra kết luận về tối ưu hóa.

Trong quá trình thực hiện, sẽ thiết kế cơ sở dữ liệu với tên chuẩn: QuanLyKhachSan_K235480106022. Mỗi phần lệnh SQL viết ra sẽ đi kèm một ảnh screenshot chứa mã lệnh và kết quả thực thi, với các chú thích chi tiết: ảnh dùng để minh họa bước nào, câu lệnh giải quyết vấn đề gì, kết quả thể hiện thông tin gì. Thông qua bài tập này sẽ nắm vững các khái niệm cơ bản và nâng cao của SQL Server, từ DDL (Data Definition Language) đến DML (Data Manipulation Language), từ những truy vấn đơn giản đến những xử lý phức tạp bằng Function, Procedure, Trigger, và Cursor, từ đó tích lũy kỹ năng thiết kế và quản trị cơ sở dữ liệu chuyên nghiệp.
### Phần 1: Khởi tạo database và bảng
- Tạo cơ sở dữ liệu

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/194df676-d05b-4d49-94e8-45e2e8d67e96" />


<p align="center">Tạo cơ sở dữ liệu QuanLyKhachSan_K235480106022</p>
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

- Chèn dữ liệu vào các bảng

```sql
-- 1. CHÈN 20 PHÒNG VÀO BẢNG [Phong]
DECLARE @iPhong INT = 1;
DECLARE @Tang VARCHAR(1);
DECLARE @MaPhong VARCHAR(10);
DECLARE @LoaiPhong NVARCHAR(50);
DECLARE @DienTich FLOAT;
DECLARE @GiaPhong MONEY;
DECLARE @RanPhong INT;

WHILE @iPhong <= 20
BEGIN
    -- Tính toán số tầng (từ tầng 1 đến tầng 4)
    SET @Tang = CAST((@iPhong - 1) / 5 + 1 AS VARCHAR);
    -- Tạo mã phòng: 101, 102, ... 404, 405
    SET @MaPhong = 'P' + @Tang + RIGHT('0' + CAST(((@iPhong - 1) % 5) + 1 AS VARCHAR), 2);
    -- Random loại phòng, diện tích và giá
    SET @RanPhong = ABS(CHECKSUM(NEWID())) % 4 + 1;
    SET @LoaiPhong = CHOOSE(@RanPhong, N'Standard (Tiêu chuẩn)', N'Superior (Cao cấp)', N'Deluxe (Sang trọng)', N'Suite (Hạng sang)');
    IF @LoaiPhong = N'Standard (Tiêu chuẩn)'
    BEGIN
        SET @DienTich = ROUND(20 + RAND() * 10, 1);
        SET @GiaPhong = 500000;
    END
    ELSE IF @LoaiPhong = N'Superior (Cao cấp)'
    BEGIN
        SET @DienTich = ROUND(30 + RAND() * 10, 1);
        SET @GiaPhong = 800000;
    END
    ELSE IF @LoaiPhong = N'Deluxe (Sang trọng)'
    BEGIN
        SET @DienTich = ROUND(40 + RAND() * 15, 1);
        SET @GiaPhong = 1500000;
    END
    ELSE
    BEGIN
        SET @DienTich = ROUND(60 + RAND() * 20, 1);
        SET @GiaPhong = 3000000;
    END

    INSERT INTO [Phong] (MaPhong, LoaiPhong, DienTich, GiaPhongTheoNgay)
    VALUES (@MaPhong, @LoaiPhong, @DienTich, @GiaPhong);

    SET @iPhong = @iPhong + 1;
END;
GO

-- 2. CHÈN 100 KHÁCH HÀNG VÀO BẢNG [KhachHang]
DECLARE @iKhach INT = 1;
DECLARE @Ho NVARCHAR(50);
DECLARE @Dem NVARCHAR(50);
DECLARE @Ten NVARCHAR(50);
DECLARE @HoTen NVARCHAR(100);
DECLARE @NgaySinh DATE;
DECLARE @Sdt VARCHAR(15);
DECLARE @Diem INT;
DECLARE @RanHo INT;
DECLARE @RanDem INT;
DECLARE @RanTen INT;
DECLARE @RanSdt INT;
DECLARE @SdtPrefix VARCHAR(2);

WHILE @iKhach <= 100
BEGIN
    SET @RanHo = ABS(CHECKSUM(NEWID())) % 10 + 1;
    SET @RanDem = ABS(CHECKSUM(NEWID())) % 10 + 1;
    SET @RanTen = ABS(CHECKSUM(NEWID())) % 15 + 1;

    SET @Ho = CHOOSE(@RanHo, N'Nguyễn', N'Trần', N'Lê', N'Phạm', N'Hoàng', N'Huỳnh', N'Phan', N'Vũ', N'Võ', N'Đặng');
    SET @Dem = CHOOSE(@RanDem, N'Văn', N'Thị', N'Hữu', N'Minh', N'Ngọc', N'Thanh', N'Đức', N'Xuân', N'Hải', N'Hoài');
    SET @Ten = CHOOSE(@RanTen, N'An', N'Bình', N'Cường', N'Dung', N'Hương', N'Khoa', N'Linh', N'Nam', N'Nghĩa', N'Oanh', N'Phong', N'Quỳnh', N'Sơn', N'Trang', N'Tuấn');
    
    SET @HoTen = @Ho + ' ' + @Dem + ' ' + @Ten;

    -- Random Ngày Sinh (từ 1960 đến 2005)
    SET @NgaySinh = DATEADD(DAY, -(ABS(CHECKSUM(NEWID())) % (45 * 365)), '2005-01-01');

    -- Random Số điện thoại (Bắt đầu bằng 09, 08, 03... + 8 số random)
    SET @RanSdt = ABS(CHECKSUM(NEWID())) % 3 + 1;
    SET @SdtPrefix = CHOOSE(@RanSdt, '09', '08', '03');
    SET @Sdt = @SdtPrefix + RIGHT('00000000' + CAST(ABS(CHECKSUM(NEWID())) % 100000000 AS VARCHAR), 8);

    -- Random Điểm đánh giá (từ 3 đến 5, ít khi có khách chấm 1, 2)
    SET @Diem = ABS(CHECKSUM(NEWID())) % 3 + 3;

    INSERT INTO [KhachHang] (HoTenKhachHang, NgaySinh, SoDienThoai, DiemDanhGia)
    VALUES (@HoTen, @NgaySinh, @Sdt, @Diem);

    SET @iKhach = @iKhach + 1;
END;
GO

-- 3. CHÈN 100 LƯỢT ĐẶT PHÒNG VÀO BẢNG [DatPhong]
DECLARE @iDatPhong INT = 1;
DECLARE @MaKH INT;
DECLARE @MaP VARCHAR(10);
DECLARE @NgayNhan DATETIME;
DECLARE @NgayTra DATETIME;
DECLARE @SoNgayO INT;
DECLARE @TienCoc MONEY;
DECLARE @RanCoc INT;

WHILE @iDatPhong <= 100
BEGIN
    -- Lấy ngẫu nhiên 1 khách hàng đã được thêm thành công (Đảm bảo không lỗi Khóa Ngoại)
    SELECT TOP 1 @MaKH = MaKhachHang FROM [KhachHang] ORDER BY NEWID();

    -- Lấy ngẫu nhiên 1 phòng (có 20 phòng)
    -- Thủ thuật: ORDER BY NEWID() để lấy ngẫu nhiên 1 record
    SELECT TOP 1 @MaP = MaPhong FROM [Phong] ORDER BY NEWID();

    -- Random ngày nhận phòng trong năm 2023
    SET @NgayNhan = DATEADD(DAY, ABS(CHECKSUM(NEWID())) % 365, '2023-01-01');
    -- Thêm giờ ngẫu nhiên từ 10h sáng đến 18h tối
    SET @NgayNhan = DATEADD(HOUR, ABS(CHECKSUM(NEWID())) % 9 + 10, @NgayNhan);

    -- Random số ngày ở (từ 1 đến 7 ngày)
    SET @SoNgayO = ABS(CHECKSUM(NEWID())) % 7 + 1;

    -- Tính ngày trả = Ngày nhận + Số ngày ở
    SET @NgayTra = DATEADD(DAY, @SoNgayO, @NgayNhan);
    -- Thêm giờ trả ngẫu nhiên (từ 8h sáng đến 12h trưa)
    SET @NgayTra = DATEADD(HOUR, (ABS(CHECKSUM(NEWID())) % 5 + 8) - DATEPART(HOUR, @NgayTra), @NgayTra);

    -- Có khoảng 10% trường hợp khách đang ở, chưa trả phòng (ThoiGianTra = NULL)
    IF (ABS(CHECKSUM(NEWID())) % 100) < 10
    BEGIN
        SET @NgayTra = NULL;
    END

    -- Tiền cọc: random từ 0, 500k, hoặc 1 triệu
    SET @RanCoc = ABS(CHECKSUM(NEWID())) % 3 + 1;
    SET @TienCoc = CHOOSE(@RanCoc, 0, 500000, 1000000);

    INSERT INTO [DatPhong] (MaKhachHang, MaPhong, ThoiGianNhan, ThoiGianTra, TienDatCoc)
    VALUES (@MaKH, @MaP, @NgayNhan, @NgayTra, @TienCoc);

    SET @iDatPhong = @iDatPhong + 1;
END;
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/7d8a726e-b100-4953-872f-4b18c8e74c9a" />
<p align="center">Chèn dữ liệu mẫu vào các bảng</p>

### Phần 2: Xây dựng Function

#### Các loại hàm có sẵn (Built-in Functions)

SQL Server cung cấp hàng trăm hàm có sẵn để xử lý dữ liệu, được chia thành các nhóm chính:

- Hàm chuỗi (String Functions): LEN(), SUBSTRING(), REPLACE(), UPPER(), LOWER()...

- Hàm ngày tháng (Date/Time Functions): GETDATE(), DATEDIFF(), DATEADD(), YEAR(), MONTH()...

- Hàm toán học (Mathematical Functions): ABS(), ROUND(), CEILING(), FLOOR()...

- Hàm tổng hợp (Aggregate Functions): SUM(), COUNT(), MAX(), MIN(), AVG()...

- Hàm chuyển đổi kiểu (Conversion Functions): CAST(), CONVERT()...

- Hàm hệ thống (System Functions): ISNULL(), COALESCE(), NEWID(), CHOOSE()...

#### Tìm hiểu chi tiết 1 số Built-in Functions

- NEWID(): Tạo ra một mã định danh duy nhất (GUID) dùng khi cần sắp xếp dữ liệu ngẫu nhiên.
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

#### Hàm do người dùng tự viết (User-Defined Functions - UDFs)


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

#### Viết các Function

##### Viết 1 Scalar Function (Hàm trả về 1 giá trị)


Viết hàm tính tổng tiền phòng cho một lần đặt phòng cụ thể. Tổng tiền = (Số ngày ở) * (Giá phòng). Nếu khách thuê và trả phòng trong cùng 1 ngày thì tính là 1 ngày. Nếu khách chưa trả phòng (ThoiGianTra bị NULL) thì tính số ngày từ lúc nhận đến thời điểm hiện tại.

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

##### Viết 1 Inline Table-Valued Function (ITVF)
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

##### Viết 1 Multi-statement Table-Valued Function (MSTVF)
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

#### Stored Procedure hệ thống (System Stored Procedures)


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

##### Viết 01 Store Procedure đơn giản để thực hiện lệnh INSERT hoặc UPDATE dữ liệu, có kiểm tra điều kiện logic
- SP Thực hiện INSERT có kiểm tra logic

- Ý tưởng: "Đặt phòng Thần tốc (Flash Booking)"

- Tình huống: Khách hàng đến quầy lễ tân lúc nửa đêm, đang rất mệt và muốn nhận phòng ngay lập tức theo một hạng phòng cụ thể (VD: Standard). Lễ tân không có thời gian dò tìm từng phòng.

- Yêu cầu SP: Lễ tân chỉ cần nhập Mã Khách Hàng và Loại Phòng. SP sẽ tự động rà soát xem có phòng nào thuộc loại đó đang trống không. Nếu có, tự động INSERT một giao dịch nhận phòng ngay tại thời điểm hiện tại (GETDATE()), với tiền cọc là 0. Nếu hết phòng trống hạng đó, in ra thông báo xin lỗi.


### Phân tích logic Stored Procedure sp_DatPhongThanToc

**Luồng xử lý tổng quát:**

Bước 1. Procedure nhận vào hai tham số:
   - Mã khách hàng
   - Loại phòng mong muốn

Bước 2. Hệ thống tiến hành tìm kiếm một phòng phù hợp:
   - Thuộc đúng loại phòng khách yêu cầu
   - Không nằm trong danh sách các phòng đang được sử dụng (chưa trả phòng)
   - Bao gồm cả các phòng chưa từng được đặt trước đó

Bước 3. Sau khi tìm kiếm:
   - Nếu tồn tại phòng trống:
     - Gán phòng đó cho khách hàng
     - Tạo một bản ghi đặt phòng mới với:
       - Thời gian nhận là thời điểm hiện tại
       - Thời gian trả chưa xác định
       - Tiền đặt cọc ban đầu

   - Nếu không tìm thấy phòng phù hợp:
     - Thông báo không còn phòng trống cho loại phòng yêu cầu

Bước 4. Kết quả của procedure:
   - Trả về thông báo tương ứng với trạng thái thành công hoặc thất bại

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

<p align="center">Tạo Procedure sp_DatPhongThanToc(Đặt phòng Thần tốc)</p>




```sql
-- Khách hàng ID số 5 muốn đặt gấp 1 phòng Standard
EXEC sp_DatPhongThanToc @MaKhachHang = 5, @LoaiPhong = N'Standard (Tiêu chuẩn)';
```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/f8acef63-8c6e-47db-b6d7-3e9e1daf4c36" />

<p align="center">Đặt phòng gấp cho khách hàng ID số 5, phòng Standard</p>


##### Viết 01 Store Procedure có sử dụng tham số OUTPUT để trả về một giá trị tính toán

- SP có tham số OUTPUT

- Ý tưởng (Scenario): "Vòng quay may mắn: Tính tỷ lệ giảm giá cho khách"

- Tình huống: Khách sạn đang có chương trình tri ân. Khi khách thanh toán, lễ tân chạy hệ thống Vòng quay may mắn.

- Yêu cầu SP: Dựa vào DiemDanhGia (từ 1 đến 5 sao) của khách hàng. Nếu khách 5 sao (VIP): random giảm từ 20% - 40%. Nếu khách 4 sao: random giảm 5% - 15%. Khách < 4 sao: không giảm. SP không trả về dạng bảng, mà gán tỷ lệ % giảm giá này vào một tham số OUTPUT để phần mềm (C#, Java...) có thể lấy ra hiển thị lên UI.


### Phân tích logic Stored Procedure sp_QuayThuongGiamGia


**Luồng xử lý tổng quát:**

Bước 1. Procedure nhận vào:
   - Mã khách hàng
   - Một biến đầu ra để lưu phần trăm giảm giá

Bước 2. Hệ thống truy xuất điểm đánh giá của khách hàng từ dữ liệu lưu trữ.

Bước 3. Dựa trên điểm đánh giá, hệ thống phân loại và xử lý:
   - Nếu khách đạt mức điểm cao nhất:
     - Sinh ngẫu nhiên một giá trị giảm giá trong khoảng cao
   - Nếu khách đạt mức điểm khá:
     - Sinh ngẫu nhiên một giá trị giảm giá trong khoảng trung bình
   - Nếu điểm thấp hơn:
     - Không áp dụng giảm giá

Bước 4. Giá trị phần trăm giảm giá được gán vào biến đầu ra.

Bước 5. Kết quả cuối cùng:
   - Trả về phần trăm giảm giá tương ứng cho khách hàng thông qua biến output


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

<p align="center">Tạo Procedure sp_QuayThuongGiamGia(Vòng quay may mắn: Tính tỷ lệ giảm giá cho khách)</p>



```sql
-- Để chạy SP có OUTPUT, ta phải khai báo một biến để hứng kết quả
DECLARE @KetQuaGiamGia INT;

-- Gọi hàm và truyền biến hứng vào 
EXEC sp_QuayThuongGiamGia 
     @MaKhachHang = 10, 
     @PhanTramGiamGia = @KetQuaGiamGia OUTPUT;

-- In kết quả ra màn hình
PRINT N'Khách hàng này được giảm: ' + CAST(@KetQuaGiamGia AS VARCHAR) + N'% tổng hóa đơn.';

```


<img width="1916" height="1079" alt="image" src="https://github.com/user-attachments/assets/3e7dd7cd-4506-404d-87f9-2049fa5e844a" />
<p align="center">Vòng quay may mắn: giảm giá cho khách</p>

##### Viết 01 Store Procedure trả về một tập kết quả (Result set) từ lệnh SELECT sau khi đã join nhiều bảng. 
- SP trả về Result Set (Lệnh SELECT JOIN nhiều bảng)

- Ý tưởng (Scenario): "Truy quét biệt đội sống ảo"

- Tình huống: Gần đây có tình trạng một số khách hàng thuê phòng hạng sang trọng (Suite hoặc Deluxe) nhưng chỉ thuê... trong vài tiếng hoặc tối đa 1 ngày, chủ yếu chỉ để chụp ảnh check-in sống ảo rồi trả phòng ngay. Khách sạn cần lên danh sách nhóm đối tượng này để có chiến lược quảng cáo (upsell) các gói chụp ảnh Studio riêng thay vì cho thuê phòng lưu trú.

- Yêu cầu SP: Trả về danh sách (Result set) gồm Tên Khách, SĐT, Loại Phòng, Số Ngày Ở của những khách thuê phòng Suite hoặc Deluxe mà thời gian ở <= 1 ngày. Cần JOIN 3 bảng để lấy đủ thông tin.



### Phân tích logic Stored Procedure sp_BaoCaoKhachHangSongAo

**Luồng xử lý tổng quát:**

Bước 1. Hệ thống truy xuất dữ liệu từ các bảng:
   - Bảng đặt phòng (chứa thông tin giao dịch)
   - Bảng khách hàng (thông tin cá nhân)
   - Bảng phòng (loại phòng)

Bước 2. Thực hiện liên kết giữa các bảng thông qua:
   - Mã khách hàng
   - Mã phòng

Bước 3. Lọc dữ liệu theo các điều kiện:
   - Chỉ chọn các phòng thuộc nhóm cao cấp (ví dụ: Suite hoặc Deluxe)
   - Chỉ lấy các giao dịch đã hoàn tất (đã có thời gian trả phòng)
   - Chỉ lấy các trường hợp có thời gian lưu trú không quá 24 giờ

Bước 4. Tính toán thời gian lưu trú:
   - Xác định số giờ khách đã ở dựa trên thời gian nhận và trả phòng

Bước 5. Sắp xếp kết quả:
   - Theo số giờ lưu trú tăng dần

Bước 6. Trả về danh sách kết quả bao gồm:
   - Họ tên khách hàng
   - Số điện thoại
   - Hạng phòng đã đặt
   - Thời gian nhận phòng
   - Thời gian trả phòng
   - Tổng số giờ lưu trú




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


<p align="center">Tạo Procedure sp_BaoCaoKhachHangSongAo(Truy quét biệt đội sống ảo)</p>



```sql
EXEC sp_BaoCaoKhachHangSongAo;

```



<img width="1913" height="1079" alt="image" src="https://github.com/user-attachments/assets/2a091521-566e-4e57-99b7-947559dc5206" />
<p align="center">Liệt kê danh sách khách hàng sống ảo</p>


### Phần 4: Trigger và Xử lý logic nghiệp vụ

##### YÊU CẦU 1: Viết 01 Trigger tự động cập nhật bảng B khi bảng A thay đổi

Ý tưởng (Scenario): "Đưa vào Sổ Đen (Blacklist) khi khách ăn cắp vặt hoặc quậy phá"

Tình huống thực tế: Trong khách sạn, đôi khi xảy ra tình trạng khách thuê phòng làm hỏng đồ đạc nghiêm trọng, quậy phá, hoặc trốn không thanh toán. Khi nhân viên lễ tân đánh giá khách hàng này điểm 1 sao (DiemDanhGia = 1), hệ thống cần tự động cảnh báo và cấm khách hàng này đặt phòng trong tương lai.

Logic giải quyết: 
Bước 1.  Tạo thêm một cột TrangThai trong bảng [KhachHang] (Mặc định là 'Bình thường').
Bước 2.  Viết Trigger gắn trên bảng [KhachHang] (Bảng A) sự kiện UPDATE.
Bước 3.  Khi có lệnh UPDATE điểm đánh giá của khách xuống 1 sao, Trigger sẽ tự động DELETE tất cả các lượt đặt phòng trong tương lai (nếu có) của khách này trong bảng [DatPhong] (Bảng B) để dọn đường cho khách khác, đồng thời cập nhật trạng thái khách thành 'Blacklist'.



### Phân tích logic Trigger trg_KhachHang_Blacklist

**Luồng xử lý tổng quát:**

Bước 1. Trigger được kích hoạt khi có thao tác cập nhật dữ liệu trên bảng khách hàng.

Bước 2. Hệ thống kiểm tra xem cột điểm đánh giá có bị thay đổi hay không.

Bước 3. Nếu có thay đổi, hệ thống xác định danh sách các khách hàng vừa được cập nhật có điểm đánh giá bằng 1.

Bước 4. Với các khách hàng này, hệ thống thực hiện:
   - Xác định các lượt đặt phòng trong tương lai (chưa đến thời gian nhận phòng)
   - Xóa các lượt đặt phòng đó để giải phóng phòng cho khách khác

Bước 5. Sau khi xử lý dữ liệu đặt phòng, hệ thống tiếp tục:
   - Cập nhật trạng thái của các khách hàng này thành trạng thái bị cấm (Blacklist)

Bước 6. Hoàn tất quá trình, đảm bảo:
   - Khách hàng có điểm đánh giá thấp không còn các đặt phòng trong tương lai
   - Trạng thái của khách được cập nhật để phục vụ các xử lý tiếp theo trong hệ thống



```sql
-- Bước 1: Thêm cột TrangThai vào bảng KhachHang
ALTER TABLE [KhachHang] ADD [TrangThai] NVARCHAR(50) DEFAULT N'Bình thường';
GO

-- Bước 2: Viết Trigger
CREATE TRIGGER trg_KhachHang_Blacklist
ON [KhachHang]
AFTER UPDATE
AS
BEGIN
    -- Chỉ kích hoạt logic nếu cột DiemDanhGia bị thay đổi
    IF UPDATE(DiemDanhGia)
    BEGIN
        -- Lấy ra mã của những khách hàng vừa bị đánh giá 1 sao (từ bảng ảo inserted)
        DECLARE @MaKH_BiPhath INT;
        
        -- Dùng con trỏ (Cursor) hoặc cấu trúc UPDATE/DELETE SET-BASED
        -- Ở đây dùng SET-BASED cho tối ưu: Xóa tất cả các đặt phòng trong tương lai của những người bị 1 sao
        DELETE FROM [DatPhong]
        WHERE MaKhachHang IN (SELECT MaKhachHang FROM inserted WHERE DiemDanhGia = 1)
          AND ThoiGianNhan > GETDATE(); -- Chỉ hủy các phòng chưa ở (trong tương lai)
          
        -- Cập nhật trạng thái của chính khách hàng đó thành Blacklist
        -- Lưu ý: Lệnh UPDATE này kích hoạt trên chính bảng đang gắn Trigger, nhưng vì ta đang 
        -- ở AFTER UPDATE, nếu không cẩn thận có thể dính đệ quy (sẽ giải thích ở Yêu cầu 2).
        UPDATE [KhachHang]
        SET TrangThai = N'Blacklist (Cấm cửa)'
        WHERE MaKhachHang IN (SELECT MaKhachHang FROM inserted WHERE DiemDanhGia = 1);
        
        PRINT N'TRIGGER KÍCH HOẠT: Đã đưa khách hàng có điểm 1 sao vào Blacklist và tự động hủy các phòng họ đã đặt trước trong tương lai.';
    END
END;
GO

```




<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/eb1765d8-e5a4-4470-887e-b706a211d9a6" />


<p align="center">Tạo Trigger trg_KhachHang_Blacklist</p>

VD:
Giả sử Khách hàng số 3 (đang có điểm bình thường) bất ngờ quậy phá. Lễ tân phạt điểm xuống 1.
Kiểm tra lại khách hàng số 3, bạn sẽ thấy cột TrangThai tự động đổi thành 'Blacklist (Cấm cửa)'


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/256a57e1-c1a5-4844-ab7e-f1ab4f845e83" />


<p align="center">Khách hàng số 3 thấy cột TrangThai tự động đổi thành 'Blacklist (Cấm cửa)'</p>



##### YÊU CẦU 2: Tình huống Trigger "Vòng Lặp" (A gọi B, B gọi A)

- Ý tưởng (Scenario): "Cuộc chiến Khuyến mãi - Điểm Tích Lũy và Hoàn Tiền"

- Logic:

  
Bảng A (KhachHang): Có thêm cột DiemTichLuy.

Bảng B (DatPhong): Có thêm cột TienHoanLai.

Trigger 1 (Trên Bảng DatPhong): Khi có một giao dịch nhận phòng (INSERT vào DatPhong), tự động thưởng cho Khách Hàng 10 DiemTichLuy (cập nhật bảng KhachHang).

Trigger 2 (Trên Bảng KhachHang): Khi DiemTichLuy của khách được cập nhật lên (đạt mốc mới), tự động quay lại bảng DatPhong cập nhật TienHoanLai = 50.000đ cho giao dịch gần nhất của họ.


### Phân tích logic hệ thống Trigger "Khuyến mãi - Điểm tích lũy và Hoàn tiền"

**Luồng xử lý tổng thể:**

Bước 1. Khi phát sinh một giao dịch đặt phòng mới:
   - Hệ thống ghi nhận thông tin đặt phòng vào dữ liệu

Bước 2. Trigger thứ nhất được kích hoạt:
   - Xác định khách hàng thực hiện giao dịch
   - Tự động cộng thêm điểm tích lũy cho khách hàng đó

Bước 3. Sau khi điểm tích lũy được cập nhật:
   - Trigger thứ hai được kích hoạt do có sự thay đổi dữ liệu ở bảng khách hàng

Bước 4. Trigger thứ hai thực hiện:
   - Kiểm tra việc tăng điểm tích lũy của khách hàng
   - Xác định giao dịch đặt phòng gần nhất của khách

Bước 5. Hệ thống tiến hành:
   - Cập nhật số tiền hoàn lại cho giao dịch gần nhất của khách hàng

Bước 6. Kết quả cuối cùng:
   - Khách hàng được cộng điểm sau mỗi lần đặt phòng
   - Khi điểm thay đổi, hệ thống tự động áp dụng hoàn tiền cho giao dịch phù hợp




Chuẩn bị dữ liệu:

```sql   
ALTER TABLE [KhachHang] ADD [DiemTichLuy] INT DEFAULT 0;
ALTER TABLE [DatPhong] ADD [TienHoanLai] MONEY DEFAULT 0;
```


```sql
-- Trigger 1 (Insert DatPhong -> Update KhachHang)
CREATE TRIGGER trg_DatPhong_ThemDiem
ON [DatPhong]
AFTER INSERT
AS
BEGIN
    PRINT N'[Trigger 1]: Đã thêm giao dịch Đặt Phòng, chuẩn bị cộng 10 điểm cho khách...';
    
    UPDATE [KhachHang]
    SET DiemTichLuy = DiemTichLuy + 10
    WHERE MaKhachHang IN (SELECT MaKhachHang FROM inserted);
END;
GO

-- Trigger 2 (Update KhachHang -> Update DatPhong)
CREATE TRIGGER trg_KhachHang_HoanTien
ON [KhachHang]
AFTER UPDATE
AS
BEGIN
    IF UPDATE(DiemTichLuy)
    BEGIN
        PRINT N'[Trigger 2]: Phát hiện điểm tích lũy tăng, chuẩn bị hoàn 50k vào giao dịch mới nhất...';
        
        -- Cập nhật bảng DatPhong (hoàn 50k cho giao dịch mới nhất của khách vừa được tăng điểm)
        UPDATE [DatPhong]
        SET TienHoanLai = 50000
        WHERE MaDatPhong IN (
            SELECT TOP 1 d.MaDatPhong 
            FROM [DatPhong] d
            JOIN inserted i ON d.MaKhachHang = i.MaKhachHang
            ORDER BY d.MaDatPhong DESC
        );
    END
END;
GO

```


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/63f459dd-9785-4a01-b6ce-d4fd4e06133f" />


<p align="center">Tạo Trigger Cuộc chiến Khuyến mãi - Điểm Tích Lũy và Hoàn Tiền</p>




<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/940617cd-2154-4b78-a7e5-81ce6cca046b" />

<p align="center">Chèn 1 giao dịch mới kích hoạt Trigger 1</p>


Phân tích kết quả:
- Câu lệnh INSERT kích hoạt Trigger 1.

- Bên trong Trigger 1 có lệnh UPDATE KhachHang. Lệnh này được thi hành thành công.

- Hành động UPDATE KhachHang đó lại đóng vai trò là "mồi lửa" kích hoạt Trigger 2 (vì Trigger 2 đang lắng nghe sự kiện UPDATE trên KhachHang).

- Bên trong Trigger 2 thực hiện UPDATE DatPhong.

- Chuỗi sự kiện kết thúc tại đây (vì chúng ta không có Trigger bắt sự kiện UPDATE trên bảng DatPhong).

### Phần 5: Cursor và Duyệt dữ liệu


#### YÊU CẦU 1: Viết một đoạn script sử dụng CURSOR và không dùng CURSOR để duyệt qua danh sách của 1 câu lệnh SQL dạng SELECT, duyệt qua từng bản ghi, xử lý riêng từng bản ghi, so sánh

- Ý tưởng (Scenario): "Phân phát Quỹ đền bù rủi ro (Hết tiền thì dừng)"

- Tình huống: Trong năm qua, khách sạn bị phàn nàn nhiều. Giám đốc duyệt một "Quỹ đền bù" trị giá 3.000.000 VNĐ. Lễ tân cần duyệt danh sách các khách hàng có DiemDanhGia < 4 (từ thấp đến cao). Mỗi khách sẽ được tặng một Voucher trị giá 500.000 VNĐ (Cộng vào cột TienVoucher mới tạo). Quá trình trao tặng phải dừng lại ngay lập tức khi Quỹ đền bù cạn kiệt (không được chi âm).


Chuẩn bị dữ liệu:

```sql

-- Tạo thêm cột TienVoucher
ALTER TABLE [KhachHang] ADD [TienVoucher] MONEY DEFAULT 0;
GO
-- Cố tình tạo ra một số khách hàng đánh giá thấp để test
UPDATE [KhachHang] SET DiemDanhGia = 2 WHERE MaKhachHang IN (5, 10, 15, 20, 25, 30, 35, 40);

```

##### Sử dụng Cursor 

Cầm 3 triệu trên tay, gặp khách 1 đưa 500k (còn 2.5tr), gặp khách 2 đưa 500k... cứ thế đến khi hết tiền thì thoát vòng lặp.


### Phân tích logic sử dụng CURSOR "Phân phát Quỹ đền bù rủi ro"

**Luồng xử lý tổng quát:**

Bước 1. Khởi tạo các giá trị ban đầu:
   - Tổng quỹ đền bù
   - Số tiền cấp cho mỗi khách

Bước 2. Hệ thống truy xuất danh sách khách hàng:
   - Chỉ lấy những khách có điểm đánh giá thấp hơn mức yêu cầu
   - Sắp xếp theo mức độ không hài lòng (từ thấp đến cao)

Bước 3. Khởi tạo cơ chế duyệt tuần tự:
   - Lần lượt lấy từng khách hàng trong danh sách

Bước 4. Với mỗi khách hàng:
   - Kiểm tra số tiền còn lại trong quỹ

Bước 5. Nếu quỹ còn đủ:
   - Cộng tiền voucher cho khách hàng đó
   - Trừ số tiền tương ứng khỏi quỹ

Bước 6. Nếu quỹ không còn đủ:
   - Dừng ngay quá trình phân phát

Bước 7. Lặp lại quá trình:
   - Tiếp tục với khách hàng tiếp theo cho đến khi hết danh sách hoặc hết quỹ

Bước 8. Kết thúc:
   - Quỹ được phân bổ dần theo thứ tự ưu tiên
   - Đảm bảo không vượt quá ngân sách ban đầu


```sql   
SET STATISTICS TIME ON;
GO

DECLARE @QuyDenBu MONEY = 3000000;
DECLARE @TienMoiNguoi MONEY = 500000;
DECLARE @MaKH INT;

-- Khai báo Cursor lấy danh sách khách không hài lòng, ưu tiên người đánh giá thấp nhất
DECLARE cur_DenBuKhachHang CURSOR FOR
    SELECT MaKhachHang FROM [KhachHang] WHERE DiemDanhGia < 4 ORDER BY DiemDanhGia ASC;

OPEN cur_DenBuKhachHang;
FETCH NEXT FROM cur_DenBuKhachHang INTO @MaKH;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @QuyDenBu >= @TienMoiNguoi
    BEGIN
        -- Cập nhật tiền cho khách
        UPDATE [KhachHang] SET TienVoucher = TienVoucher + @TienMoiNguoi WHERE MaKhachHang = @MaKH;
        -- Trừ quỹ
        SET @QuyDenBu = @QuyDenBu - @TienMoiNguoi;
    END
    ELSE
    BEGIN
        -- Hết tiền thì thoát vòng lặp ngay lập tức
        BREAK;
    END

    FETCH NEXT FROM cur_DenBuKhachHang INTO @MaKH;
END;

CLOSE cur_DenBuKhachHang;
DEALLOCATE cur_DenBuKhachHang;
GO
SET STATISTICS TIME OFF;

```

  

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/27c7f329-2ef8-4fbb-9f95-8c81ce738364" />

<p align="center">Khách có điểm đánh giá <4 lần lượt nhận TienVoucher</p>


##### Không dùng Cursor (dùng Set-Based SQL)

Trong SQL, ta có thể dùng Window Function SUM() OVER (...) để tính tổng lũy kế (Running Total). Nếu tổng tiền lũy kế từ người đầu tiên đến người hiện tại vẫn <= 3.000.000 thì người đó được nhận thưởng.


### Phân tích logic không dùng CURSOR (Set-based) "Phân phát Quỹ đền bù rủi ro"
**Luồng xử lý tổng quát:**

Bước 1. Hệ thống xác định danh sách khách hàng:
   - Chỉ lấy những khách có điểm đánh giá thấp hơn mức yêu cầu
   - Sắp xếp theo thứ tự ưu tiên (từ thấp đến cao)

Bước 2. Thực hiện tính toán lũy kế:
   - Với mỗi khách hàng, hệ thống tính tổng số tiền sẽ được phân phát nếu cấp thưởng từ người đầu tiên đến người đó

Bước 3. Xác định điều kiện nhận thưởng:
   - Những khách hàng có tổng tiền lũy kế chưa vượt quá quỹ cho phép sẽ được nhận voucher

Bước 4. Thực hiện cập nhật:
   - Cập nhật đồng loạt tiền voucher cho tất cả các khách hàng thỏa điều kiện

Bước 5. Kết thúc:
   - Quỹ được phân bổ đúng giới hạn
   - Không cần duyệt từng bản ghi riêng lẻ



```sql  
SET STATISTICS TIME ON;
GO

WITH KhachHang_LuyKe AS (
    SELECT 
        MaKhachHang,
        DiemDanhGia,
        -- Tính tổng tiền tích lũy tăng dần theo thứ tự ưu tiên
        SUM(500000) OVER (ORDER BY DiemDanhGia ASC, MaKhachHang ASC) AS TongTienLuyKe
    FROM [KhachHang]
    WHERE DiemDanhGia < 4
)
-- Cập nhật đồng loạt một phát (Set-based) cho những người nằm trong giới hạn quỹ
UPDATE k
SET k.TienVoucher = k.TienVoucher + 500000
FROM [KhachHang] k
JOIN KhachHang_LuyKe cte ON k.MaKhachHang = cte.MaKhachHang
WHERE cte.TongTienLuyKe <= 3000000;

GO
SET STATISTICS TIME OFF;

```



  
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/ddb0bc4c-2bfb-483c-85d0-9a2dc2ee5b1d" />


<p align="center">Không dùng cursor vẫn giải quyết được bài toán phát TienVoucher</p>
So sánh và nhận xét hiệu năng:



### Nhận xét và so sánh hiệu năng giữa CURSOR và Set-based


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/89344845-0f38-4f7d-ae29-c61cc44802ae" />


<p align="center">Hiệu năng khi dùng CURSOR</p>

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/17f1af1e-b87a-4906-9638-206c5a4ddb5c" />


<p align="center">Hiệu năng khi dùng Set-based (không dùng CURSOR)</p>


**Sử dụng CURSOR:**
- Thời gian xử lý thường chậm hơn, thể hiện qua CPU time và Elapsed time cao.
- Dữ liệu được xử lý theo từng dòng (row-by-row), không tận dụng được khả năng xử lý tập hợp của hệ quản trị.
- Mỗi lần lặp, hệ thống phải thực thi riêng lẻ các câu lệnh, dẫn đến phát sinh chi phí xử lý lặp lại.
- Gây ra hiện tượng chuyển đổi ngữ cảnh liên tục giữa các thao tác, làm giảm hiệu năng tổng thể.


**Sử dụng Set-based (không dùng CURSOR):**
- Thời gian xử lý rất nhanh, gần như không đáng kể với tập dữ liệu vừa và lớn.
- Dữ liệu được xử lý theo dạng tập hợp (set), đúng với cách tối ưu của SQL Server.
- Hệ thống chỉ cần biên dịch và tối ưu câu lệnh một lần, sau đó thực thi đồng loạt.
- Hiệu năng vượt trội, có thể nhanh hơn nhiều lần so với CURSOR khi xử lý dữ liệu lớn.

---

**Kết luận:**
- Nên ưu tiên sử dụng phương pháp Set-based trong hầu hết các trường hợp.
- CURSOR chỉ nên dùng khi bài toán yêu cầu xử lý tuần tự từng bản ghi mà không thể giải quyết bằng truy vấn tập hợp.



#### YÊU CẦU 2: XÂY DỰNG BÀI TOÁN MÀ CHỈ CURSOR MỚI GIẢI QUYẾT ĐƯỢC

Các câu lệnh SELECT, UPDATE truyền thống yêu cầu Tên Bảng, Tên Cột phải là hằng số (cố định). Chúng không thể tự động thay đổi tên Bảng, hoặc thực thi các lệnh quản trị động (Dynamic DDL) cho từng dòng dữ liệu được. Đây chính là "đất diễn" độc quyền của CURSOR.

Ý tưởng (Scenario): "Đóng gói dữ liệu (Archive) động theo từng Năm và Tháng"

Tình huống: Bảng [DatPhong] sau 5 năm hoạt động có hàng triệu dòng làm hệ thống chậm. Giám đốc IT yêu cầu: Hãy duyệt qua bảng DatPhong, xem có những Năm và Tháng nào. Với mỗi Tháng-Năm, hãy tự động tạo một bảng lưu trữ mới (VD: DatPhong_Archive_2023_10, DatPhong_Archive_2023_11) và copy dữ liệu của tháng đó sang, sau đó xóa ở bảng gốc.

Tại sao Set-based (SQL thường) bó tay? Không có một lệnh INSERT INTO thông thường nào có thể tự động biến đổi cái tên bảng đích [DatPhong_Archive_...] dựa theo giá trị Month, Year của từng dòng dữ liệu đang xử lý được. Bắt buộc phải dùng CURSOR kết hợp Dynamic SQL (Câu lệnh SQL dạng chuỗi động).

### Phân tích logic hệ thống CURSOR "Lưu trữ dữ liệu theo Tháng - Năm"

**Luồng xử lý tổng quát:**

Bước 1. Hệ thống xác định danh sách các mốc thời gian khác nhau:
   - Lấy ra các cặp tháng và năm tồn tại trong dữ liệu đặt phòng

Bước 2. Khởi tạo cơ chế duyệt tuần tự:
   - Lần lượt xử lý từng cặp tháng – năm

Bước 3. Với mỗi mốc thời gian:
   - Tạo tên bảng lưu trữ tương ứng theo định dạng tháng và năm

Bước 4. Hệ thống kiểm tra:
   - Nếu bảng lưu trữ tương ứng chưa tồn tại thì tiến hành tạo mới

Bước 5. Sau khi đảm bảo bảng đã tồn tại:
   - Trích xuất dữ liệu thuộc tháng – năm đang xét từ bảng chính
   - Chuyển toàn bộ dữ liệu này sang bảng lưu trữ tương ứng

Bước 6. Lặp lại quá trình:
   - Tiếp tục với các tháng – năm còn lại cho đến khi xử lý hết dữ liệu

Bước 7. Kết thúc:
   - Toàn bộ dữ liệu được phân tách và lưu trữ theo từng bảng riêng biệt ứng với từng tháng và năm



```sql
DECLARE @Thang INT;
DECLARE @Nam INT;
DECLARE @TenBangMoi NVARCHAR(100);
DECLARE @SqlString NVARCHAR(MAX);

-- Tìm ra các Tháng/Năm riêng biệt đang tồn tại trong hệ thống
DECLARE cur_Archive CURSOR FOR
    SELECT DISTINCT MONTH(ThoiGianNhan), YEAR(ThoiGianNhan) 
    FROM [DatPhong]
    WHERE ThoiGianNhan IS NOT NULL;

OPEN cur_Archive;
FETCH NEXT FROM cur_Archive INTO @Thang, @Nam;

WHILE @@FETCH_STATUS = 0
BEGIN
    -- Tạo tên bảng động. VD: DatPhong_Archive_2023_1
    SET @TenBangMoi = 'DatPhong_Archive_' + CAST(@Nam AS VARCHAR) + '_' + CAST(@Thang AS VARCHAR);
    
    -- Xây dựng chuỗi câu lệnh SQL (Dynamic SQL) để TẠO BẢNG và COPY dữ liệu
    SET @SqlString = N'
        -- 1. Tạo bảng mới nếu chưa có
        IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = ''' + @TenBangMoi + ''')
        BEGIN
            CREATE TABLE [' + @TenBangMoi + '] (
                MaDatPhong INT, MaKhachHang INT, MaPhong VARCHAR(10), ThoiGianNhan DATETIME
            );
        END
        
        -- 2. Chuyển dữ liệu sang bảng Archive
        INSERT INTO [' + @TenBangMoi + ']
        SELECT MaDatPhong, MaKhachHang, MaPhong, ThoiGianNhan
        FROM [DatPhong]
        WHERE MONTH(ThoiGianNhan) = ' + CAST(@Thang AS VARCHAR) + ' 
          AND YEAR(ThoiGianNhan) = ' + CAST(@Nam AS VARCHAR) + ';
    ';

    -- Thực thi đoạn mã động vừa lắp ghép
    PRINT N'Đang tạo và lưu trữ vào bảng: ' + @TenBangMoi;
    EXEC sp_executesql @SqlString;

    FETCH NEXT FROM cur_Archive INTO @Thang, @Nam;
END;

CLOSE cur_Archive;
DEALLOCATE cur_Archive;
GO
```


  
<img width="1918" height="1079" alt="image" src="https://github.com/user-attachments/assets/0c091a89-46cd-4573-be58-881a09a559cb" />


<p align="center">Dùng cursor đóng gói dữ liệu (Archive) động theo từng Năm và Tháng</p>



<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/a955a810-ffa5-44af-9c59-db064e66b7bc" />



<p align="center">Kiểm tra kết quả bảng sau khi xử lí</p>
