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

- Trigger & Nghiên cứu logic: Viết Trigger xử lý nghiệp vụ và thực hiện thí nghiệm về "Trigger vòng lặp" để rút ra nhận xét.

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
## Phần 2: Xây dựng Function

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
## Phần 3: Xây dựng Store Procedure

**1**. Tìm hiểu về System Stored Procedure (SP có sẵn)

Trong SQL Server, các System SP là các thủ tục được hệ thống xây dựng sẵn để hỗ trợ quản trị và truy xuất siêu dữ liệu (metadata). Chúng thường bắt đầu bằng tiền tố sp_.

Một số System SP đặc sắc:

- sp_help: Dùng để xem thông tin chi tiết về một đối tượng (bảng, view, index...). Nó trả về kiểu dữ liệu của các cột, khóa chính, khóa ngoại.
  - Cách dùng: EXEC sp_help 'Tour';

- sp_helpdb: Cung cấp thông tin về dung lượng, đường dẫn tệp tin và trạng thái của các cơ sở dữ liệu trên Server.
  - Cách dùng: EXEC sp_helpdb 'QuanLyTourDuLich_K235480206061';


- sp_columns: Trả về danh sách chi tiết tất cả các cột trong một bảng nhất định.
  - Cách dùng: EXEC sp_columns 'DangKyTour';


- sp_rename: Dùng để đổi tên một đối tượng (như bảng hoặc cột) mà không cần phải xóa đi tạo lại.
  - Cách dùng: EXEC sp_rename 'OldName', 'NewName';


**2**. Thực hành xây dựng Stored Procedure
SP Thực hiện INSERT có kiểm tra điều kiện logic

Yêu cầu: Thêm một bản đăng ký tour mới. Phải kiểm tra: số người đi không được vượt quá 40 (giới hạn của một xe khách) và mã Tour phải tồn tại.
```
CREATE PROCEDURE [sp_InsertDangKyTour]
    @TenKhach NVARCHAR(100),
    @MaT VARCHAR(10),
    @SoNguoi INT
AS
BEGIN
    -- 1. Kiểm tra mã Tour có tồn tại không
    IF NOT EXISTS (SELECT 1 FROM [Tour] WHERE [MaTour] = @MaT)
    BEGIN
        PRINT N'Lỗi: Mã Tour không tồn tại trên hệ thống.';
        RETURN;
    END

    -- 2. Kiểm tra số lượng người (Giới hạn xe 40 chỗ)
    IF @SoNguoi > 40 OR @SoNguoi <= 0
    BEGIN
        PRINT N'Lỗi: Số người đi không hợp lệ (Phải từ 1 đến 40).';
        RETURN;
    END

    -- 3. Thực hiện Insert nếu thỏa mãn điều kiện
    INSERT INTO [DangKyTour] (HoTenKhach, MaTour, NgayDangKy, SoNguoiDi)
    VALUES (@TenKhach, @MaT, GETDATE(), @SoNguoi);
    
    PRINT N'Đăng ký tour thành công cho khách: ' + @TenKhach;
END;
GO

-- KHAI THÁC:
EXEC [sp_InsertDangKyTour] N'Phạm Văn Bình', 'T001', 5;
```
Luồng xử lý dữ liệu:

Hàm: sp_InsertDangKyTour

- Bước 1 - Tiếp nhận Tham số: Server nhận các giá trị đầu vào (Tên khách, Mã tour, Số người) từ ứng dụng hoặc người dùng.

- Bước 2 - Kiểm tra tồn tại (Existence Check): Server truy cập bảng Tour để xác minh mã tour truyền vào có tồn tại hay không. Nếu không, luồng xử lý bị ngắt và trả về thông báo lỗi.

- Bước 3 - Kiểm tra ràng buộc nghiệp vụ (Business Rule Check): Hệ thống so sánh số lượng người đăng ký với giới hạn xe (40 chỗ). Nếu vi phạm, luồng xử lý dừng lại ngay lập tức (lệnh RETURN).

- Bước 4 - Ghi dữ liệu (Commit): Khi các điều kiện đều thỏa mãn, lệnh INSERT được thực thi để đưa dữ liệu vào bảng vật lý DangKyTour.

- Bước 5 - Phản hồi: Server gửi thông báo "Thành công" về phía Client.


<P> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fc66734d-bc6c-4416-a057-6a48e0b3934e" />
<i>Hình 8: Đăng ký tour thành công cho khách</i></P>


**2**. Stored Procedure trả về giá trị qua tham số OUTPUT

Yêu cầu: Xây dựng thủ tục tính toán tổng doanh thu của một "Loại Tour" nhất định (ví dụ: Tour Biển). Kết quả không hiển thị ra màn hình ngay mà phải được gán vào một biến đầu ra (Output) để các module khác trong phần mềm có thể tiếp tục xử lý hoặc tính thuế.

```
ALTER PROCEDURE [sp_GetDoanhThuTheoLoai]
    @MaLoai INT,
    @TongTien MONEY OUTPUT
AS
BEGIN
    SELECT @TongTien = SUM(D.SoNguoiDi * T.GiaTour)
    FROM [DangKyTour] D
    JOIN [Tour] T ON D.MaTour = T.MaTour
    WHERE T.MaLoai = @MaLoai;

    SET @TongTien = ISNULL(@TongTien, 0);
END;
GO

-- KHAI THÁC
DECLARE @BienHungResult MONEY;
DECLARE @MaLoaiCoThat INT;
SELECT TOP 1 @MaLoaiCoThat = T.MaLoai 
FROM DangKyTour D JOIN Tour T ON D.MaTour = T.MaTour;
EXEC [sp_GetDoanhThuTheoLoai] @MaLoaiCoThat, @BienHungResult OUTPUT;

SELECT 
    N'Mã loại đang kiểm tra: ' + CAST(@MaLoaiCoThat AS NVARCHAR(10)) AS [ThongTin],
    FORMAT(@BienHungResult, 'N0') + ' VND' AS [TongDoanhThuThucTe];

```
Luồng xử lý dữ liệu:

Hàm: sp_GetDoanhThuTheoLoai

- Bước 1 - Khởi tạo vùng nhớ: Phía người gọi khai báo một biến cục bộ để chuẩn bị nhận giá trị trả về từ Server.
- Bước 2 - Liên kết dữ liệu (JOIN): Server thực hiện kết nối bảng DangKyTour và Tour thông qua mã tour chung để lấy được bộ dữ liệu gồm: Số người đi và Đơn giá tour.
- Bước 3 - Tổng hợp (Aggregation): Sử dụng hàm SUM để tính tổng kết quả của biểu thức (Số người $\times$ Đơn giá) cho toàn bộ danh sách đã lọc theo @MaLoai.
- Bước 4 - Ghi đè vào biến Output: Giá trị sau cùng được gán trực tiếp vào biến tham số có từ khóa OUTPUT.
- Bước 5 - Trả vùng nhớ: Khi thủ tục kết thúc, giá trị này được "bắn" ngược lại về biến đã khai báo ở Bước 1 của Client.

<p><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/92aca1e4-ea01-400f-8555-6156e92935f1" />
 <i>Hình 9: Xem tổng doanh thu thực tế </i></p>

**3**. Stored Procedure trả về tập kết quả (Result Set) từ lệnh JOIN

Yêu cầu: Xây dựng một thủ tục báo cáo tổng hợp danh sách khách hàng thuộc nhóm "Khách đoàn VIP" (những khách đăng ký từ 5 người trở lên). Báo cáo cần trích xuất dữ liệu từ 3 bảng: DangKyTour, Tour, và LoaiTour để hiển thị đầy đủ thông tin về hành trình và loại hình du lịch mà khách đã chọn.
```
ALTER PROCEDURE [sp_BaoCaoKhachDoanVIP] 
AS
BEGIN
   
    SELECT 
        D.HoTenKhach AS [Tên Khách Hàng],
        T.TenTour AS [Tên Hành Trình],
        L.TenLoai AS [Loại Hình Tour],
        D.SoNguoiDi AS [Số Lượng Khách],
        FORMAT(D.SoNguoiDi * T.GiaTour, 'N0') + ' VND' AS [Tổng Giá Trị]
    FROM [DangKyTour] D
    INNER JOIN [Tour] T ON D.MaTour = T.MaTour
    INNER JOIN [LoaiTour] L ON T.MaLoai = L.MaLoai
    WHERE D.SoNguoiDi >= 5  
    ORDER BY D.SoNguoiDi DESC; 
END;
GO
EXEC [sp_BaoCaoKhachDoanVIP];
```
Luồng xử lý dữ liệu:

Hàm: sp_BaoCaoKhachDoanVIP
- Bước 1 - Phối hợp dữ liệu (Multi-Join Operation): Server thực hiện lệnh kết nối đồng thời 3 bảng gốc: DangKyTour (Giao dịch), Tour (Hành trình) và LoaiTour (Danh mục) để tập hợp đầy đủ thông tin định danh.

- Bước 2 - Lọc dữ liệu theo tiêu chuẩn (Filtering): Hệ thống áp dụng mệnh đề WHERE để lọc ra các bản ghi có SoNguoiDi >= 5. Các dữ liệu không thỏa mãn sẽ bị loại bỏ khỏi bộ nhớ đệm.

- Bước 3 - Biến đổi hiển thị (Data Transformation): Thực hiện phép nhân đơn giá trực tiếp trên từng dòng và sử dụng hàm FORMAT() để chuyển đổi số tiền sang dạng chuỗi có ký tự VND và dấu phân cách hàng nghìn.

- Bước 4 - Sắp xếp thứ tự (Sorting): Server sắp xếp lại tập dữ liệu theo thứ tự giảm dần của số lượng khách để làm nổi bật các khách hàng quan trọng nhất (VIP).

- Bước 5 - Xuất luồng kết quả (Output Stream): Trả về một tập kết quả (Result Set) dạng bảng hiển thị trực tiếp cho người dùng cuối.


<p> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/02788970-5107-4d58-b0e4-194ea945a804" />
<i>Hình 10: Danh sách khách đoàn VIP </i></p>


---
## Phần 4: Trigger và Xử lý logic nghiệp vụ

**1**. Trigger tự động hóa nghiệp vụ thực tế

Ý tưởng (Scenario): "Tích lũy tổng chi tiêu của khách hàng và tự động phân loại Khách VIP khi đạt doanh thu lớn."

Tình huống thực tế:
- Để quản lý doanh thu từ từng khách hàng, mỗi khi có một lượt đăng ký tour mới, hệ thống cần tự động cộng dồn số tiền đó vào hồ sơ của khách. Nếu tổng số tiền khách đã chi trả đạt trên 50.000.000 VND, hệ thống sẽ tự động nâng cấp khách đó lên hạng 'VIP' để hưởng chiết khấu cho các lần sau.

Logic giải quyết:

- Bước 1: Tạo thêm hai cột TongChiTieu (mặc định là 0) và HangThanhVien (mặc định là 'Thường') vào bảng DangKyTour (hoặc bảng khách hàng nếu có). Để đơn giản và khớp code, ta sẽ cập nhật trực tiếp dựa trên HoTenKhach.
- Bước 2: Viết Trigger gắn trên bảng DangKyTour (Bảng A) cho sự kiện INSERT.
- Bước 3: Khi chèn một bản ghi mới, Trigger sẽ lấy (Số người $\times$ Giá tour) cộng vào TongChiTieu. Nếu TongChiTieu > 50.000.000, Trigger tự động UPDATE cột HangThanhVien thành 'VIP'.

```
ALTER TABLE [DangKyTour] ADD [TongChiTieuKhach] MONEY DEFAULT 0;
ALTER TABLE [DangKyTour] ADD [HangThanhVien] NVARCHAR(50) DEFAULT N'Thường';
GO

-- =============================================
-- Author:      Đàm Ngọc Sơn
-- Create date: 2026-04-24
-- Description: Tích lũy doanh thu và tự động nâng cấp VIP cho khách hàng
-- =============================================
CREATE TRIGGER [trg_TichLuyDoanhThu]
ON [DangKyTour]
AFTER INSERT
AS
BEGIN
    DECLARE @TenK NVARCHAR(100);
    DECLARE @SoTienVuaGiaoDich MONEY;

 
    SELECT @TenK = i.HoTenKhach, @SoTienVuaGiaoDich = (i.SoNguoiDi * T.GiaTour)
    FROM inserted i
    JOIN [Tour] T ON i.MaTour = T.MaTour;

    
    UPDATE [DangKyTour]
    SET [TongChiTieuKhach] = [TongChiTieuKhach] + @SoTienVuaGiaoDich
    WHERE [HoTenKhach] = @TenK;

    
    UPDATE [DangKyTour]
    SET [HangThanhVien] = N'VIP'
    WHERE [HoTenKhach] = @TenK AND [TongChiTieuKhach] >= 50000000;

    PRINT N'HỆ THỐNG: Đã cập nhật tích lũy và kiểm tra hạng VIP cho khách: ' + @TenK;
END;
GO


INSERT INTO [DangKyTour] (HoTenKhach, MaTour, NgayDangKy, SoNguoiDi)
VALUES (N'Đàm Ngọc Sơn', 'T001', GETDATE(), 10);
```
- Bước 1 - Tiếp nhận sự kiện: Khi có lệnh INSERT vào bảng DangKyTour, Server kích hoạt Trigger và nạp dữ liệu vào bảng tạm inserted.

- Bước 2 - Tính toán tài chính: Trigger thực hiện phép JOIN giữa bảng tạm và bảng Tour để xác định tổng giá trị của giao dịch vừa phát sinh.

- Bước 3 - Cập nhật cộng dồn: Hệ thống thực hiện lệnh UPDATE để tích lũy số tiền mới vào cột TongChiTieuKhach. Đây là quá trình tự động cập nhật dữ liệu nội bảng dựa trên danh tính khách hàng.

- Bước 4 - Phân loại hạng mục: Server kiểm tra điều kiện ngân sách. Nếu đạt ngưỡng 50 triệu, lệnh UPDATE thứ hai sẽ thay đổi nhãn HangThanhVien từ 'Thường' sang 'VIP'.

- Bước 5 - Kết thúc và thông báo: Server đóng giao dịch và in thông báo xác nhận việc cập nhật hạng thành viên đã hoàn tất.


<P> <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e2a5bc61-6060-47d1-ac61-c1c97eca40a6" />
<I>Hình 11: Kiểm tra và cập nhật thông tin VIP cho khách hàng</I></P>


**2**. Thử nghiệm Trigger vòng lặp (Recursive Trigger)

Ý tưởng: Thiết lập hai Trigger cập nhật chéo giữa bảng DangKyTour (Bảng A) và bảng Tour (Bảng B) để tạo ra một vòng lặp vô tận.

Thực hiện thiết lập:

- Trigger 1 (Trên bảng DangKyTour - A): Khi có một lượt đăng ký mới, tự động cập nhật lại tên Tour ở bảng B.

- Trigger 2 (Trên bảng Tour - B): Khi tên Tour ở bảng B bị cập nhật, tự động cập nhật lại số lượng người đi ở bảng A.

<P><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/81c02403-d9a8-4579-aa69-329cb8398a60" />
<I>Hình 12: Sau khi thiết lập hai Trigger cập nhật chéo giữa bảng DangKyTour (Bảng A) và bảng Tour (Bảng B)</I>
</P>

Nguyên nhân: Đây là hiện tượng Đệ quy gián tiếp (Indirect Recursion).

Lệnh Update ở bảng A kích hoạt trg_A_to_B.

- trg_A_to_B thực hiện Update bảng B, làm kích hoạt trg_B_to_A.

- trg_B_to_A lại quay lại Update bảng A, làm trg_A_to_B chạy tiếp...

Cơ chế bảo vệ: SQL Server không để vòng lặp này chạy mãi mãi vì sẽ làm treo Server (cạn kiệt CPU và RAM). Hệ thống có một bộ đếm tầng lặp, mặc định giới hạn là 32 cấp. Khi vòng lặp đạt đến lần thứ 32, hệ thống tự động ngắt kết nối và báo lỗi để bảo vệ cơ sở dữ liệu.


Về mặt kỹ thuật: Việc thiết kế Trigger cập nhật chéo lẫn nhau giữa các bảng liên quan là một sai lầm nghiêm trọng trong kiến trúc Database.

Về mặt quản trị: Trigger là công cụ mạnh mẽ nhưng là "con dao hai lưỡi". Nếu không kiểm soát được các tầng tác động (Nesting level), nó sẽ gây ra các lỗi hệ thống khó kiểm soát và làm giảm hiệu năng cực lớn.

Giải pháp: Để tránh tình trạng này:

- Hạn chế dùng Trigger cập nhật chéo.

- Sử dụng lệnh IF NOT UPDATE(Tên_Cột) để chặn đứng vòng lặp.

- Chỉ nên dùng Trigger cho các tác vụ ghi Log hoặc kiểm tra ràng buộc đơn giản.
---
## Phần 5: Cursor và Duyệt dữ liệu
**1**. Sử dụng Cursor để duyệt danh sách và xử lý bản ghi

Ý tưởng (Scenario): "Tự động gửi thông báo chi tiết và tính lại khuyến mãi cho từng khách đăng ký Tour."

Hệ thống sẽ duyệt qua danh sách đăng ký hôm nay. Với mỗi bản ghi, nếu số người đi trên 5 người, hệ thống sẽ in ra một thông báo đặc biệt và tặng thêm 10% giảm giá cho bản ghi đó.

```
-- =============================================
-- Author:      Đàm Ngọc Sơn
-- Description: Duyệt từng dòng đăng ký để xử lý khuyến mãi riêng biệt
-- =============================================
DECLARE @MaDK INT, @TenK NVARCHAR(100), @SoNguoi INT;


DECLARE cur_KhuyenMai CURSOR FOR 
SELECT MaDangKy, HoTenKhach, SoNguoiDi FROM [DangKyTour];


OPEN cur_KhuyenMai;


FETCH NEXT FROM cur_KhuyenMai INTO @MaDK, @TenK, @SoNguoi;


WHILE @@FETCH_STATUS = 0
BEGIN
    IF @SoNguoi >= 5
    BEGIN
        PRINT N'Xử lý khách đoàn: ' + @TenK + N' - Mã DK: ' + CAST(@MaDK AS VARCHAR) + N' (Tặng Voucher 10%)';
       
    END
    ELSE
    BEGIN
        PRINT N'Xử lý khách lẻ: ' + @TenK;
    END


    FETCH NEXT FROM cur_KhuyenMai INTO @MaDK, @TenK, @SoNguoi;
END;


CLOSE cur_KhuyenMai;
DEALLOCATE cur_KhuyenMai;
```


<p><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/95e9f18b-712d-4845-8336-271e2f9322bb" />
<i>Hình 13: Chạy lệnh thành công và in ra một thông báo đặc biệt và tặng thêm 10% giảm giá</i>
</p>
**2**. Giải quyết bài toán không dùng Cursor (Set-based)
Bài toán trên hoàn toàn có thể giải quyết nhanh bằng lệnh UPDATE hoặc SELECT kết hợp cấu trúc CASE WHEN.
Mã nguồn tối ưu SQL:
```
SELECT 
    HoTenKhach,
    CASE 
        WHEN SoNguoiDi >= 5 THEN N'Tặng Voucher 10%'
        ELSE N'Không khuyến mãi'
    END AS [GhiChuKhuyenMai]
FROM [DangKyTour];
```

<p><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/cb507cac-08b0-47fc-a880-ef29033f3516" />
<i>Hình 14: Chạy mã nguồn tối ưu</i>
</p>
So sánh tốc độ và nhận xét:
- Tốc độ: Khi chạy với dữ liệu lớn ), cách không dùng Cursor (Set-based) nhanh hơn gấp nhiều lần. Cursor tốn tài nguyên vì phải mở/đóng kết nối dữ liệu cho từng dòng một.


Bài toán đặc thù: Thực thi SQL động cho công tác quản trị


Ý tưởng bài toán: "Tự động sao lưu (Backup) tất cả các bảng trong Database ra các file vật lý riêng biệt hoặc Rebuild lại toàn bộ Index của các bảng để tối ưu tốc độ."

- Các lệnh SQL như SELECT, UPDATE, DELETE làm việc trên tập dữ liệu (rows).

- Các lệnh quản trị như ALTER INDEX... REBUILD hoặc DBCC CHECKDB chỉ nhận tham số là tên một đối tượng duy nhất. SQL không cho phép viết: ALTER INDEX ALL ON (SELECT Name FROM sys.tables) REBUILD. Điều này là sai cú pháp.

**3**. Cách giải quyết bằng Cursor (Duy nhất khả thi):
- Cursor sẽ đóng vai trò là "người điều phối". Nó đi nhặt từng cái tên bảng trong hệ thống, sau đó lắp ghép thành một câu lệnh SQL hoàn chỉnh và ra lệnh thực thi câu lệnh đó.

```
-- =============================================
-- Author:      Đàm Ngọc Sơn
-- Description: Cursor duyệt danh sách bảng để bảo trì Index - SQL thuần không làm được trực tiếp
-- =============================================
DECLARE @TableName NVARCHAR(256);
DECLARE @SqlCmd NVARCHAR(MAX);

DECLARE cur_Maintenance CURSOR FOR 
SELECT name FROM sys.tables WHERE type = 'U';

OPEN cur_Maintenance;
FETCH NEXT FROM cur_Maintenance INTO @TableName;

WHILE @@FETCH_STATUS = 0
BEGIN
 
    SET @SqlCmd = 'ALTER INDEX ALL ON [' + @TableName + '] REBUILD;';
    
    PRINT N'Đang thực hiện bảo trì bảng: ' + @TableName;

    EXEC sp_executesql @SqlCmd;

    FETCH NEXT FROM cur_Maintenance INTO @TableName;
END;

CLOSE cur_Maintenance;
DEALLOCATE cur_Maintenance;
```

<p><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f3ee0830-062b-4c52-a79c-8c6f0e659523" />
<i>Hình 15: Thực hiện lệnh thành công , hệ thống bảo trì các bảng</i>
</p>
Nhận xét và Tổng kết:
Dựa trên kết quả thực hành và quan sát thực tế về tốc độ cũng như cách vận hành, em rút ra các điểm khác biệt cốt lõi giữa việc sử dụng Cursor và SQL thuần (Set-based) như sau:

1. Về phương thức tư duy và tiếp cận
   
- Sử dụng Cursor: Mang tư duy lập trình tuần tự (Procedural), tương tự như các ngôn ngữ C++, Python hay Java. Người lập trình phải ra lệnh cho hệ thống đi từng bước: "Mở bảng ra -> Lấy dòng 1 -> Làm việc A -> Lấy dòng tiếp theo".

- SQL Thuần (Set-based): Mang tư duy khai báo (Declarative). Ta chỉ cần mô tả cho SQL Server biết "Tôi muốn kết quả như thế này", còn việc lấy dữ liệu ở đâu, duyệt dòng nào trước dòng nào sau sẽ do hệ thống tự tính toán.

2. Về hiệu suất và tốc độ xử lý
   
- Sử dụng Cursor: Tốc độ Rất Chậm. Hệ thống phải lặp đi lặp lại các thao tác quản lý bộ nhớ như OPEN, FETCH, và CLOSE cho từng dòng dữ liệu. Với hàng vạn bản ghi, Cursor sẽ gây ra hiện tượng nghẽn cổ chai.

- SQL Thuần (Set-based): Tốc độ Rất Nhanh. Nhờ vào bộ tối ưu hóa truy vấn (Query Optimizer), SQL Server sẽ xử lý toàn bộ tập dữ liệu cùng lúc trong bộ nhớ, giúp giảm thiểu tối đa thời gian truy xuất.

3. Về khả năng thực thi và tính linh hoạt
   
- Sử dụng Cursor: Cực kỳ linh hoạt trong các tác vụ động. Nó có thể thực thi các lệnh hệ thống khác nhau, gọi các Stored Procedure khác nhau tùy thuộc vào giá trị của từng dòng dữ liệu mà nó duyệt qua.

- SQL Thuần (Set-based): Có hạn chế nhất định. Nó cực mạnh khi tính toán, biến đổi hoặc lọc dữ liệu trên các bảng có sẵn, nhưng rất khó (hoặc không thể) thực hiện các lệnh quản trị hệ thống phức tạp một cách tự động trên nhiều đối tượng khác nhau.

4. Về tính ứng dụng thực tế
   
- Sử dụng Cursor: Chủ yếu dùng trong công tác Quản trị hệ thống (DBA) như: Duyệt danh sách bảng để bảo trì, sao lưu dữ liệu động, hoặc xử lý những logic nghiệp vụ quá rắc rối mà SQL thuần không thể diễn đạt được.

- SQL Thuần (Set-based): Là "xương sống" của 99% các bài toán ứng dụng phần mềm. Mọi thao tác từ tính tiền tour, lọc danh sách khách đoàn đến thống kê doanh thu đều phải dùng cách này để đảm bảo hệ thống vận hành mượt mà.
---
