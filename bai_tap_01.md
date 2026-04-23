# sql_task1
Task 1
- Họ và tên: Từ Văn Hải
- Lớp: K59.KMT.K01
- MSSV: K235480106022
1. Download và cài đặt SQL Server 2025
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/1e8d4c71-5455-41df-8251-ec841d5311f0" />


2. Cấu hình cho SQL Server làm việc ở cổng động (Dynamic Port), TCP (MSSV:K235480106022):
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/fbbe4886-0b3b-4aea-b2c0-bc74e30b9e57" />


3. Kiểm tra xem service SQL Server bằng Command Prompt

```powershell
netstat -ano | findstr LISTENING
```
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/0682c2e0-11c0-4e85-a52b-474bcd7b6f7d" />

4. Cài đặt SQL Server Management Studio
<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/e8b9bd64-46a0-416f-8c25-5c28d6922ac8" />

5. Chạy phần mềm ssms để Đăng nhập vào SQL Server bằng 2 cách: Windows Authentication và SQL Server Authentication.

*Chạy bằng mode Windows Authentication*


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/0197773b-63a0-492c-b134-4fb870b6b2e1" />


*Chạy bằng mode SQL Server Authentication*


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/4fc46929-deb6-4436-8802-aaeb2dbc63ca" />

6. Đường dẫn 2 file đã tạo ra khi tạo ra database
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/6218980a-8535-4dda-873a-b14800d08734" />

7. Tạo bảng dữ liệu với khoá chính (Primary Key) là trường masv
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/e3a1f459-5db4-40bc-a89f-fa3b06dcb368" />

8. Import dữ liệu từ file csv mẫu vào trong bảng vừa tạo
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/50141304-102c-4a41-b132-76762b713dab" />


9. Kiểm tra xem số dòng của bảng dữ liệu sau khi import: 12020 dòng
```sql
SELECT COUNT(*) AS SoDong FROM tnut;
```

<img width="1918" height="1079" alt="image" src="https://github.com/user-attachments/assets/a2c57f71-7888-4ac7-8c69-aa3c98d0ac73" />

10. Thêm 1 row vào bảng với dữ liệu là thông tin cá nhân.
```sql
INSERT INTO tnut (masv, hotensv, malop, ngaysinh, noisinh, diachi) 
VALUES ('K235480106022', N'Từ Văn Hải','K59KMT','02/09/2005', N'Thái Nguyên', N'Thái Nguyên');
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/5081e865-c803-43d4-8ee5-3f21e410a3c2" />

11. Cập nhật(update) trường noisinh thành 'Sao Hoả' cho những dòng có noisinh và diachi đều là NULL
```sql
UPDATE tnut
SET noisinh = N'Sao Hoả'
WHERE noisinh = 'NULL' AND diachi = 'NULL';
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/06f2a4b6-20d6-4619-8cfa-f24f0b8109bd" />

12. Tạo bảng SaoHoa gồm những sinh viên có nơi sinh ở 'Sao Hoả'
```sql
SELECT * 
INTO SaoHoa
FROM tnut
WHERE noisinh = N'Sao Hoả';
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/108afa15-3596-4ca4-9910-17ce0d759421" />

13. Xoá (delete) trong bảng SaoHoa những sinh viên cùng họ với em (Họ: Từ)
```sql
DELETE FROM SaoHoa 
WHERE hotensv LIKE N'Từ%';
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/3c53e5c8-3895-4f56-ab10-2d67229654ea" />

14. Xuất toàn bộ kết quả của các bước 6,7,8,9,10,11,12,13 ra file dulieu.sql


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/cd870ea1-d453-496b-a488-d06a0da97c75" />

15. Xoá cơ sở dữ liệu đã tạo, kiểm tra tại path


*Xóa cơ sở dữ liệu*


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/d101b822-0e90-461e-a9ae-9a8d5b46a68a" />


*Kiểm tra lại đường dẫn các file sau khi xóa*
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/b698e128-3bd5-40be-acb1-b35aaaac5b22" />

16. Mở file dulieu.sql của bước 14, chạy toàn bộ các lệnh kết quả được tạo ra tương đương với các bước 6, 7, 8, 9, 10, 11, 12, 13.


*Kiểm tra database từ file dulieu.sql giữ nguyên cấu trúc cũ*


<img width="1914" height="1079" alt="image" src="https://github.com/user-attachments/assets/7b81d803-07e9-4cf6-a416-4ab9dfab14e8" />


*Kiểm tra dữ liệu các bảng khi tạo lại*


<img width="1913" height="1079" alt="image" src="https://github.com/user-attachments/assets/5d3e4808-e858-4e5a-bdc5-2e7d5b2a1155" />

17. Upload file dulieu.sql lên github repository


<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/f17aaedb-6e0c-4a6d-9121-696040ecd4fd" />















