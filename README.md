# bt3
﻿IF EXISTS (SELECT * FROM sys.databases WHERE Name='BookLibrary')
DROP DATABASE BookLibrary
GO

GO
CREATE DATABASE BookLibrary
GO
USE BookLibrary

CREATE TABLE Book(
	BookCode int PRIMARY KEY IDENTITY,
	BookTitle varchar(100) NOT NULL,
	Author varchar(50) NOT NULL,
	Edition int,
	BookPrice money,
	Copies int
)

CREATE TABLE Member(
	MemberCode int PRIMARY KEY IDENTITY,
	MemberName varchar(50) NOT NULL,
	MemberAddress varchar(100) NOT NULL,
	PhoneNumber int
)

CREATE TABLE IssueDetails(
	BookCode int,
	MemberCode int,
	IssueDate date,
	ReturnDate date
	CONSTRAINT fk_IssueDetails_M FOREIGN KEY(MemberCode) REFERENCES Member(MemberCode),
	CONSTRAINT fk_IssueDetails_B FOREIGN KEY(BookCode) REFERENCES Book(BookCode)
)
--xoa bang
DROP TABLE IssueDetails

DROP TABLE Book

--Xóa bỏ các Ràng buộc Khóa ngoại của bảng IssueDetails
ALTER TABLE IssueDetails
DROP CONSTRAINT fk_IssueDetails_M,fk_IssueDetails_B

--Xóa bỏ Ràng buộc Khóa chính của bảng Member và Book

ALTER TABLE book
DROP constraint PK__Book__0A5FFCC68F86D9D2

exec sp_help book

exec sp_help Member

ALTER TABLE Member
DROP constraint PK__Member__84CA6376D13A2C42

--Thêm mới Ràng buộc Khóa chính cho bảng Member và Book

ALTER TABLE Book
ADD PRIMARY KEY (BookCode);

ALTER TABLE Member
ADD PRIMARY KEY (MemberCode);

--Thêm mới các Ràng buộc Khóa ngoại cho bảng IssueDetails
ALTER TABLE IssueDetails
ADD CONSTRAINT fk_IssueDetails_M
FOREIGN KEY (MemberCode) REFERENCES Member(MemberCode);

ALTER TABLE IssueDetails
ADD CONSTRAINT fk_IssueDetails_B
FOREIGN KEY (BookCode) REFERENCES Book(BookCode);

--Bổ sung thêm Ràng buộc giá bán sách > 0 và < 200
ALTER TABLE Book 
ADD CONSTRAINT CK_Book CHECK(BookPrice>0 and BookPrice<200)
GO

--Bổ sung thêm Ràng buộc duy nhất cho PhoneNumber của bảng Member
ALTER TABLE Member
ADD CONSTRAINT CK_PhoneNumber UNIQUE(PhoneNumber)
GO

ALTER TABLE Member DROP COLUMN PhoneNumber;

ALTER TABLE Member
ADD PhoneNumber int UNIQUE NOT NULL;

--Bổ sung thêm ràng buộc NOT NULL cho BookCode, MemberCode trong bảng IssueDetails

UPDATE IssueDetails SET BookCode =0 WHERE BookCode IS NULL

ALTER TABLE IssueDetails ALTER COLUMN BookCode INTEGER NOT NULL

UPDATE IssueDetails SET MemberCode =0 WHERE MemberCode IS NULL

ALTER TABLE IssueDetails ALTER COLUMN MemberCode INTEGER NOT NULL


--Tạo khóa chính gồm 2 cột BookCode, MemberCode cho bảng IssueDetails
ALTER TABLE IssueDetails
ADD PRIMARY KEY (MemberCode,BookCode);
