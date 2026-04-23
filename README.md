# BÁO CÁO BÀI TẬP SỐ 2: HỆ QUẢN TRỊ CSDL SQL SERVER 2025
## Thông tin sinh viên:
**Họ và tên:** Đàm Ngọc Sơn

**Lớp:** K59.KMT.K01

**MSSV:** K23548016061

---
**Yêu cầu đề bài:**

Tên đề tài: Quản lý tour du lịch

Bài tập yêu cầu sinh viên vận dụng kiến thức về SQL Server để thiết kế và lập trình các đối tượng nâng cao, bao gồm:

- Thiết kế DB: Tạo Database theo đúng cú pháp [TênDựÁn]_[MãSV], thiết kế ít nhất 3 bảng có quan hệ, áp dụng naming convention BướuLạcĐà (PascalCase) và các ràng buộc dữ liệu (PK, FK, Check).

- Lập trình Function: Thực hiện 03 loại hàm: Scalar, Inline Table-Valued, và Multi-statement Table-Valued.

- Lập trình Stored Procedure: Viết các SP xử lý logic (Insert/Update), SP có tham số đầu ra (Output) và SP truy vấn kết hợp nhiều bảng.

- rigger & Nghiên cứu logic: Viết Trigger xử lý nghiệp vụ và thực hiện thí nghiệm về "Trigger vòng lặp" để rút ra nhận xét.

- Cursor & Tối ưu hóa: Sử dụng Cursor để duyệt dữ liệu và tìm giải pháp thay thế bằng câu lệnh SQL thuần (Set-based) để so sánh hiệu năng.

- Minh chứng: Toàn bộ quá trình phải được chụp ảnh màn hình, chú thích rõ ràng và đẩy lên GitHub cá nhân.
**Giới thiệu về hệ thống:**
  
Mục tiêu hệ thống

- Hệ thống được xây dựng nhằm hỗ trợ các đơn vị lữ hành quản lý danh mục tour, thông tin khách hàng và các giao dịch đặt tour một cách khoa học. Thay vì quản lý thủ công, hệ thống sử dụng SQL Server để đảm bảo tính toàn vẹn dữ liệu và hỗ trợ tính toán tự động các nghiệp vụ phức tạp.

Cấu trúc dữ liệu chính

Hệ thống tập trung vào 3 thực thể cốt lõi:

- Loại Tour (LoaiTour): Phân loại các tour theo tính chất (Du lịch nghỉ dưỡng, Du lịch khám phá, Tour quốc tế, Tour nội địa).

- Danh mục Tour (Tour): Lưu trữ thông tin chi tiết về từng hành trình bao gồm tên tour, giá tiền, ngày khởi hành và liên kết với loại tour tương ứng.

- Khách hàng (KhachHang): Quản lý thông tin định danh của khách, email liên lạc và hệ thống điểm tích lũy để phân hạng thành viên (VIP/Normal).

Các nghiệp vụ logic xử lý trong bài
- Để minh họa cho các kỹ thuật lập trình SQL, hệ thống sẽ thực hiện các logic sau:

- Tự động hóa: Khi khách hàng đặt tour, hệ thống tự động tính điểm tích lũy dựa trên giá trị tour.

- Tính toán: Sử dụng Function để tính toán giá tour sau thuế hoặc các chương trình khuyến mãi theo mùa.

- Kiểm soát: Sử dụng Stored Procedure để đảm bảo không có email trùng lặp khi đăng ký khách hàng và kiểm tra tính hợp lệ của ngày khởi hành.

- Báo cáo: Kết xuất danh sách khách hàng và các tour họ đã tham gia thông qua các hàm trả về bảng.

---
## Phần 1: Khởi tạo bảng
**1**. Tạo database cơ sở dữ liệu
</p> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/224830ad-b985-48db-8023-ca74ab5a7b5e" />
  <i>Hình 1: Giao diện khởi tạo Database QuanLyTourDL với mã sinh viên K235480106061</i>
</p>

**2**. Bảng [LoaiTour]
- Đây là bảng chứa các loại hình tour (Biển, Núi, Tâm linh...).
```
CREATE TABLE [LoaiTour] (
    [MaLoai] INT IDENTITY(1,1) NOT NULL,
    [TenLoai] NVARCHAR(100) NOT NULL,
    CONSTRAINT [PK_LoaiTour] PRIMARY KEY ([MaLoai])
);
```
- PK: MaLoai là khóa chính, tự động tăng (IDENTITY) để tránh trùng lặp.
- Kiểu dữ liệu: NVARCHAR để lưu tên loại bằng tiếng Việt có dấu.

<p> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/03977a66-af4a-4d34-a9cf-9c3072f6208b" />
<i>Hình 2: Tạo bảng [LoaiTour]</i>
</p>

**3**. Bảng [Tour]

- Bảng này lưu các gói tour cụ thể, có liên kết với bảng LoaiTour.
```
CREATE TABLE [Tour] (
    [MaTour] VARCHAR(10) NOT NULL,
    [TenTour] NVARCHAR(200) NOT NULL,
    [GiaTour] MONEY NOT NULL,
    [MaLoai] INT NOT NULL,
    CONSTRAINT [PK_Tour] PRIMARY KEY ([MaTour]),
    CONSTRAINT [FK_Tour_LoaiTour] FOREIGN KEY ([MaLoai]) REFERENCES [LoaiTour]([MaLoai]),
    CONSTRAINT [CK_GiaTour] CHECK ([GiaTour] > 0)
);
```
- PK: MaTour dùng kiểu VARCHAR (Ví dụ: T001, T002) giúp quản lý mã tour dễ nhận diện hơn.

- FK: MaLoai là khóa ngoại, bắt buộc mỗi Tour phải thuộc về một Loại Tour nhất định.

- CK: [GiaTour] > 0 đảm bảo không có tour nào được nhập giá bằng 0 hoặc âm.
<p>
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2e8ffa8c-b7ee-4620-a832-ffc3d648b148" />
<i>Hình 3: Tạo bảng [Tour]
</i>
</p>

**4**. Bảng DangKyTour


- Bảng này ghi lại khách hàng nào đăng ký đi tour nào.

```
CREATE TABLE [DangKyTour] (
    [MaDangKy] INT IDENTITY(1,1) NOT NULL,
    [HoTenKhach] NVARCHAR(100) NOT NULL,
    [MaTour] VARCHAR(10) NOT NULL,
    [NgayDangKy] DATETIME DEFAULT GETDATE(),
    [SoNguoiDi] INT DEFAULT 1,
    CONSTRAINT [PK_DangKyTour] PRIMARY KEY ([MaDangKy]),
    CONSTRAINT [FK_DangKyTour_Tour] FOREIGN KEY ([MaTour]) REFERENCES [Tour]([MaTour]),
    CONSTRAINT [CK_SoNguoi] CHECK ([SoNguoiDi] >= 1)
);
```

- PK: MaDangKy là khóa chính tự tăng cho mỗi lượt giao dịch.

- FK: MaTour liên kết khách hàng với hành trình mà họ lựa chọn.

- CK: SoNguoiDi >= 1 đảm bảo số lượng người tham gia phải thực tế.


<p><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8937fae5-08dc-4e65-a4a5-4e0a316585aa" />
</i>Hình 4: Tạo bảng [DangKyTour]</p>

**5**. Chèn dữ liệu mẫu vào các bảng
```
-- CHÈN LOẠI TOUR
INSERT INTO [LoaiTour] (TenLoai) 
VALUES (N'Du lịch Biển'), (N'Khám phá Núi'), (N'Nghỉ dưỡng Cao cấp'), (N'Du lịch Văn hóa');
GO

-- CHÈN 20 TOUR
DECLARE @TourNames TABLE (ID INT IDENTITY(1,1), Ten NVARCHAR(200));
INSERT INTO @TourNames (Ten) VALUES 
(N'Vịnh Hạ Long'), (N'Đỉnh Fansipan'), (N'Phố cổ Hội An'), (N'Bãi biển Nha Trang'), (N'Đảo Ngọc Phú Quốc'), 
(N'Cao nguyên Đà Lạt'), (N'Miền Tây sông nước'), (N'Địa đầu Hà Giang'), (N'Cố đô Huế'), (N'Sapa mờ sương'), 
(N'Đảo Côn Đảo'), (N'Tây Nguyên hùng vĩ'), (N'Hang Sơn Đoòng'), (N'Mũi Né cát trắng'), (N'Đảo Lý Sơn'), 
(N'Rừng Cúc Phương'), (N'Chùa Tam Chúc'), (N'Chợ nổi Cái Răng'), (N'Bán đảo Sơn Trà'), (N'Thác Bản Giốc');

DECLARE @j INT = 1;
WHILE @j <= 20
BEGIN
    DECLARE @MLID INT = (SELECT TOP 1 MaLoai FROM LoaiTour ORDER BY NEWID());
    INSERT INTO [Tour] (MaTour, TenTour, GiaTour, MaLoai)
    VALUES (
        'T' + RIGHT('00' + CAST(@j AS VARCHAR), 3), 
        (SELECT Ten FROM @TourNames WHERE ID = @j), 
        (ABS(CHECKSUM(NEWID())) % 10 + 2) * 500000, 
        @MLID
    );
    SET @j = @j + 1;
END;
GO

-- CHÈN 100 KHÁCH HÀNG ĐĂNG KÝ TOUR
DECLARE @i INT = 1;
-- Tạo bảng tạm chứa kho tên để bốc ngẫu nhiên
DECLARE @Hos TABLE (ID INT IDENTITY(1,1), V NVARCHAR(20));
DECLARE @Dems TABLE (ID INT IDENTITY(1,1), V NVARCHAR(20));
DECLARE @Tens TABLE (ID INT IDENTITY(1,1), V NVARCHAR(20));

INSERT INTO @Hos VALUES (N'Nguyễn'), (N'Trần'), (N'Lê'), (N'Phạm'), (N'Hoàng'), (N'Vũ'), (N'Phan'), (N'Đặng'), (N'Bùi'), (N'Lý');
INSERT INTO @Dems VALUES (N'Minh'), (N'Thanh'), (N'Ngọc'), (N'Văn'), (N'Thị'), (N'Hữu'), (N'Anh'), (N'Đức'), (N'Xuân'), (N'Hải');
INSERT INTO @Tens VALUES (N'Sơn'), (N'Tùng'), (N'Hà'), (N'Tuấn'), (N'Linh'), (N'Dũng'), (N'An'), (N'Bình'), (N'Hùng'), (N'Trang');

WHILE @i <= 100
BEGIN
    DECLARE @FullTen NVARCHAR(100) = 
        (SELECT TOP 1 V FROM @Hos ORDER BY NEWID()) + ' ' +
        (SELECT TOP 1 V FROM @Dems ORDER BY NEWID()) + ' ' +
        (SELECT TOP 1 V FROM @Tens ORDER BY NEWID());

    DECLARE @MT_Rand VARCHAR(10) = (SELECT TOP 1 MaTour FROM [Tour] ORDER BY NEWID());

    INSERT INTO [DangKyTour] (HoTenKhach, MaTour, NgayDangKy, SoNguoiDi)
    VALUES (@FullTen, @MT_Rand, DATEADD(DAY, -(ABS(CHECKSUM(NEWID())) % 60), GETDATE()), (ABS(CHECKSUM(NEWID())) % 5 + 1));

    SET @i = @i + 1;
END;
GO

SELECT * FROM [LoaiTour];
SELECT * FROM [Tour];
SELECT * FROM [DangKyTour];
```
<p> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/0a502bcd-c656-410c-9f8f-dd6d805a256f" />
<i>Hình 4: Chèn dữ liệu mẫu vào các bảng đã tạo
</i>
</p>

---
# Phần 2: Xây dựng Function

**1**. Các loại Built-in Function trong SQL Server

  SQL Server cung cấp một hệ thống hàm có sẵn cực kỳ đồ sộ, được chia thành các nhóm chính sau:

- Hàm chuỗi (String Functions): Xử lý văn bản như LEN, SUBSTRING, REPLACE, UPPER, LOWER.

- Hàm ngày tháng (Date and Time Functions): Xử lý thời gian như GETDATE, DATEADD, DATEDIFF, YEAR, MONTH.

- Hàm toán học (Mathematical Functions): Tính toán số học như ROUND, ABS, CEILING, FLOOR.

- Hàm chuyển đổi (Conversion Functions): Chuyển đổi kiểu dữ liệu như CAST, CONVERT, TRY_PARSE.

- Hàm tổng hợp (Aggregate Functions): Tính toán trên tập hợp dữ liệu như SUM, AVG, COUNT, MAX, MIN.

- Hàm hệ thống (System Functions): Lấy thông tin về server và database như HOST_NAME, DB_NAME, USER_NAME.

**2**. Hàm do người dùng tự viết (User-Defined Functions - UDF)

Mục đích sử dụng:
- Hàm tự viết được xây dựng nhằm đóng gói các quy tắc nghiệp vụ (Business Logic) riêng biệt của từng bài toán quản lý. Việc này giúp mã nguồn gọn gàng, tăng khả năng tái sử dụng và đảm bảo tính nhất quán của dữ liệu khi thực hiện các phép tính phức tạp.

Phân loại và thời điểm sử dụng:
- Scalar Function (Hàm vô hướng): Trả về một giá trị duy nhất. Sử dụng khi cần tính toán một kết quả cụ thể cho từng dòng dữ liệu (ví dụ: tính thuế, tính điểm thưởng).

- Inline Table-Valued Function (Hàm bảng dòng đơn): Trả về một tập kết quả (bảng). Sử dụng khi cần lọc dữ liệu từ nhiều bảng theo tham số đầu vào, có tốc độ thực thi cao như một View có tham số.

- Multi-statement Table-Valued Function (Hàm bảng đa dòng): Trả về một bảng nhưng có cấu trúc phức tạp, chứa nhiều câu lệnh logic và biến bảng bên trong. Sử dụng cho các bài toán thống kê hoặc phân loại dữ liệu qua nhiều bước xử lý.

Tại sao cần tự viết hàm khi đã có nhiều hàm hệ thống?
- Dù các hàm hệ thống rất đa dạng, nhưng chúng chỉ giải quyết các bài toán kỹ thuật chung. Các hàm tự viết đóng vai trò giải quyết các nghiệp vụ đặc thù của doanh nghiệp (ví dụ: công thức tính chiết khấu cho khách đoàn, quy tắc phân hạng thành viên dựa trên doanh thu). Ngoài ra, việc tự viết hàm giúp che giấu sự phức tạp của code, người dùng chỉ cần gọi tên hàm thay vì viết lại toàn bộ logic.

**3**. Thực hành xây dựng các loại hàm

Viết Scalar Function:

Yêu cầu: Tính tổng số tiền mà một khách hàng phải trả cho một lượt đăng ký tour (Số lượng người đi $\times$ Giá tour gốc).
```
CREATE FUNCTION [fn_TinhTongTienDangKy](@MaDangKy INT)
RETURNS MONEY
AS
BEGIN
    DECLARE @TongTien MONEY;
    -- Truy vấn lấy đơn giá từ bảng Tour nhân với số người từ bảng DangKyTour
    SELECT @TongTien = D.[SoNguoiDi] * T.[GiaTour]
    FROM [DangKyTour] D
    JOIN [Tour] T ON D.[MaTour] = T.[MaTour]
    WHERE D.[MaDangKy] = @MaDangKy;
    
    RETURN ISNULL(@TongTien, 0);
END;
GO
-- Khai thác hàm:
SELECT [MaDangKy], [HoTenKhach], dbo.[fn_TinhTongTienDangKy]([MaDangKy]) AS [TongThanhToan]
FROM [DangKyTour];
```


Luồng xử lý dữ liệu:
Hàm: fn_TinhTongTienDangKy (Tính tổng tiền cho một mã đăng ký).

- Bước 1 - Tiếp nhận Input: Hàm nhận một tham số duy nhất là biến @MaDangKy từ câu lệnh gọi hàm.

- Bước 2 - Truy vấn tham chiếu: SQL Server thực hiện tìm kiếm bản ghi tương ứng trong bảng DangKyTour. Đồng thời, dựa trên mã tour, hệ thống thực hiện phép JOIN sang bảng Tour để lấy thông tin đơn giá (GiaTour).

- Bước 3 - Thực thi logic toán học: Sau khi có đủ hai thông số là SoNguoiDi và GiaTour, hàm thực hiện phép nhân ngay trong bộ nhớ.

- Bước 4 - Trả về kết quả (Output): Một giá trị kiểu số (MONEY) duy nhất được đẩy ra ngoài để hiển thị lên dòng tương ứng trong kết quả truy vấn.

<p> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8a6fd244-bcf6-4635-ab9d-a905feae2ee1" />
  <i>Hình 5: Tính tổng tiền cho một mã đăng ký </i>
</p>


Viết Inline Table-Valued Function:
Yêu cầu: Lọc danh sách các Tour có giá nằm trong một khoảng nhất định (Từ giá X đến giá Y) để phục vụ tính năng tìm kiếm nhanh trên Web.

```
CREATE FUNCTION [fn_LocTourTheoKhoangGia](@GiaTu MONEY, @GiaDen MONEY)
RETURNS TABLE
AS
RETURN (
    SELECT T.[MaTour], T.[TenTour], T.[GiaTour], L.[TenLoai]
    FROM [Tour] T
    JOIN [LoaiTour] L ON T.[MaLoai] = L.[MaLoai]
    WHERE T.[GiaTour] BETWEEN @GiaTu AND @GiaDen
);
GO
-- Khai thác hàm: Tìm các tour từ 2 triệu đến 5 triệu
SELECT * FROM dbo.[fn_LocTourTheoKhoangGia](2000000, 5000000);
```
Luồng xử lý dữ liệu:

Hàm: fn_LocTourTheoKhoangGia (Lọc tour theo khoảng giá).

- Bước 1 - Tiếp nhận tham số lọc: Nhận các giá trị giới hạn @GiaTu và @GiaDen từ phía người dùng.

- Bước 2 - Nhúng logic (Binding): Khác với các hàm khác, Inline TVF không có thân hàm phức tạp. SQL Server sẽ nhúng trực tiếp các tham số này vào trong cấu trúc câu lệnh SELECT đã định nghĩa sẵn.

- Bước 3 - Thực thi truy vấn đồng thời: SQL Server Optimizer coi hàm này như một hằng số truy vấn. Hệ thống thực hiện quét (Scan) bảng Tour và chỉ giữ lại các dòng thỏa mãn điều kiện lọc.

- Bước 4 - Trả về tập dữ liệu (Output): Trả về toàn bộ tập hợp các dòng dữ liệu (Table) thỏa mãn điều kiện mà không cần lưu trữ qua biến trung gian.
<p> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/81da8e7c-d8da-47f1-8b57-bef2de475222" />
<i>Hình 6: Lọc tour theo khoảng giá được chỉ định </i>
</p>

Viết Multi-statement Table-Valued Function:

Yêu cầu: Thống kê chi tiết tình trạng tour. Hàm sẽ trả về bảng gồm tên tour và trạng thái dựa trên số lượng khách: Nếu > 10 khách là "Tour Hot", từ 1-10 khách là "Đang nhận khách", 0 khách là "Tour Trống".
```
CREATE FUNCTION [fn_ThongKeTrangThaiTour]()
RETURNS @ThongKeTable TABLE (
    MaTour VARCHAR(10), 
    TenTour NVARCHAR(200), 
    TongKhach INT, 
    TrangThai NVARCHAR(50)
)
AS
BEGIN
    -- Bước 1: Nạp dữ liệu cơ bản và tính tổng khách vào biến bảng
    INSERT INTO @ThongKeTable (MaTour, TenTour, TongKhach)
    SELECT T.[MaTour], T.[TenTour], ISNULL(SUM(D.[SoNguoiDi]), 0)
    FROM [Tour] T
    LEFT JOIN [DangKyTour] D ON T.[MaTour] = D.[MaTour]
    GROUP BY T.[MaTour], T.[TenTour];

    -- Bước 2: Xử lý logic phức tạp để cập nhật trạng thái (Multi-statement)
    UPDATE @ThongKeTable
    SET [TrangThai] = CASE 
        WHEN [TongKhach] > 10 THEN N'Tour Hot'
        WHEN [TongKhach] BETWEEN 1 AND 10 THEN N'Đang nhận khách'
        ELSE N'Tour Trống'
    END;

    RETURN;
END;
GO
-- Khai thác hàm:
SELECT * FROM dbo.[fn_ThongKeTrangThaiTour]();
```
Luồng xử lý dữ liệu:
Hàm: fn_ThongKeTrangThaiTour (Thống kê trạng thái tour).

- Bước 1 - Khởi tạo biến bảng (Table Variable): Hệ thống cấp phát một vùng nhớ tạm thời để tạo cấu trúc bảng @ThongKeTable theo định nghĩa của người dùng.

- Bước 2 - Thu thập và Nạp dữ liệu (Insert): Thực hiện truy vấn dữ liệu từ các bảng vật lý (Tour, DangKyTour), tính toán các giá trị tổng hợp (như SUM, COUNT) và chèn dữ liệu vào bảng tạm vừa khởi tạo.

- Bước 3 - Xử lý logic nội bộ (Processing): Đây là bước quan trọng nhất. Hệ thống chạy qua từng dòng dữ liệu trong bảng tạm, sử dụng các câu lệnh điều kiện (CASE WHEN, IF...ELSE) để cập nhật thêm thông tin mới (như cột TrangThai).

- Bước 4 - Kết xuất dữ liệu (Output): Sau khi hoàn tất các bước xử lý, bảng tạm đã được "làm giàu" thông tin sẽ được trả về dưới dạng một tập kết quả đầy đủ cho người dùng.
<p><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1fa112d4-7758-4ed6-a1a0-ac688233b81d" />
<i>Hình 7: Thống kê trạng thái tour</i>
</p>

---
# Phần 3: Xây dựng Store Procedure

**1**. Tìm hiểu về System Stored Procedure (SP có sẵn)

Trong SQL Server, các System SP là các thủ tục được hệ thống xây dựng sẵn để hỗ trợ quản trị và truy xuất siêu dữ liệu (metadata). Chúng thường bắt đầu bằng tiền tố sp_.

Một số System SP đặc sắc:

- sp_help: Dùng để xem thông tin chi tiết về một đối tượng (bảng, view, index...). Nó trả về kiểu dữ liệu của các cột, khóa chính, khóa ngoại.
  Cách dùng: EXEC sp_help 'Tour';

- sp_helpdb: Cung cấp thông tin về dung lượng, đường dẫn tệp tin và trạng thái của các cơ sở dữ liệu trên Server.
  Cách dùng: EXEC sp_helpdb 'QuanLyTourDuLich_K235480206061';


- sp_columns: Trả về danh sách chi tiết tất cả các cột trong một bảng nhất định.
  Cách dùng: EXEC sp_columns 'DangKyTour';


- sp_rename: Dùng để đổi tên một đối tượng (như bảng hoặc cột) mà không cần phải xóa đi tạo lại.
  Cách dùng: EXEC sp_rename 'OldName', 'NewName';
