# Brainwave_Matrix_Intern
SQL/Database Management
CREATE DATABASE LibraryManagement;
GO
USE LibraryManagement;
GO
--create Books Table
CREATE TABLE Books (
	BookID INT PRIMARY KEY IDENTITY(1,1),
    Title NVARCHAR(100) NOT NULL,
    Author NVARCHAR(100),
    ISBN NVARCHAR(13) UNIQUE,
    Genre NVARCHAR(50),
    PublishedYear INT,
    CopiesAvailable INT
);
--Create Members Table
CREATE TABLE Members(
    MemberID INT PRIMARY KEY IDENTITY(1,1),
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    Email NVARCHAR(100) UNIQUE,
    PhoneNumber NVARCHAR(15),
    Address NVARCHAR(255),
    JoinDate DATE
);
--Create Loans Table
CREATE TABLE Loans(
	LoanID INT PRIMARY KEY IDENTITY (1,1),
	MemberID INT FOREIGN KEY REFERENCES Members(MemberID),
	BookID INT FOREIGN KEY REFERENCES Books(BookID),
    LoanDate DATE,
    ReturnDate DATE,
    Returned BIT DEFAULT 0
);
--Create Penalty Table
CREATE TABLE Penalties (
    PenaltyID INT PRIMARY KEY IDENTITY(1,1),
    MemberID INT FOREIGN KEY REFERENCES Members(MemberID),
    LoanID INT FOREIGN KEY REFERENCES Loans(LoanID),
    PenaltyAmount DECIMAL(10, 2),
    DueDate DATE,
    ReturnDate DATE,
    DaysLate INT,
    Paid BIT DEFAULT 0
);

--Insert Data
--Insert Books
INSERT INTO Books (Title, Author, ISBN, Genre, PublishedYear, copies available)
Values
	('The Great Gatsby', 'F. Scott Fitzgerald', '9780743273565', 'Fiction', 1925, 5),
	('1984', 'George Orwell', '9780451524935', 'Dystopian', 1949, 3),
	('To Kill a Mockingbird', 'Harper Lee', '9780060935467', 'Fiction', 1960, 4),
	('Pride and Prejudice', 'Jane Austen', '9780141439518', 'Romance', 1813, 4),
	('The Catcher in the Rye', 'J.D. Salinger', '9780316769488', 'Fiction', 1951, 6),
	('Moby-Dick', 'Herman Melville', '9781503280786', 'Adventure', 1851, 3),
	('The Hobbit', 'J.R.R. Tolkien', '9780547928227', 'Fantasy', 1937, 5),
	('Brave New World', 'Aldous Huxley', '9780060850524', 'Dystopian', 1932, 4),
	('The Kite Runner', 'Khaled Hosseini', '9781594631931', 'Drama', 2003, 7),
	('The Road', 'Cormac McCarthy', '9780307387899', 'Post-Apocalyptic', 2006, 4);

--Insert Members
INSERT INTO Members (FirstName, LastName, Email, PhoneNumber, Address, JoinDate)
VALUES
	('John', 'Doe', 'johndoe@example.com', '1234567890', '123 Elm Street', '2024-01-10'),
	('Jane', 'Smith', 'janesmith@example.com', '0987654321', '456 Maple Avenue', '2024-02-05'),
	('Blessing', 'Cane', 'blessingcane@example.com', '0915438742', '223 Oak Avenue', '2024-03-09'),
	('Andrew', 'Peace', 'andrewpeace@example.com', '0804921675', '023 Kite Street', '2023-05-06');

--Borrowing a Book (Insert into Loans and update book availability)
--Loan a book to a member
INSERT INTO Loans (MemberID, BookID, LoanDate, ReturnDate)
VALUES (1, 2, GETDATE(), DATEADD(day, 14, GETDATE()));

--Update book availability (To decrease copies available)
UPDATE Books
	SET CopiesAvailable = CopiesAvailable - 1
	WHERE BookID = 2;

--Returning a Book (Update Loans and increase book availability)
-- Mark book as returned
UPDATE Loans
	SET Returned = 1, ReturnDate = GETDATE()
	WHERE LoanID = 1;

-- Update book availability (increase copies available)
UPDATE Books
	SET CopiesAvailable = CopiesAvailable + 1
	WHERE BookID = 2;

--Search for Books by Title or Author
-- Search by title
SELECT * FROM Books
 WHERE Title LIKE '%1984%';

-- Search by author
SELECT * FROM Books
WHERE Author LIKE '%Orwell%';

--Check Member Borrowing History
SELECT Loans.LoanID, Books.Title, Loans.LoanDate, Loans.ReturnDate, Loans.Returned
FROM Loans
JOIN Books ON Loans.BookID = Books.BookID
WHERE Loans.MemberID = 1;

--List All Available Books
SELECT * FROM Books
WHERE CopiesAvailable > 0;

--List Overdue Books
SELECT Loans.LoanID, Members.FirstName, Members.LastName, Books.Title, Loans.LoanDate, Loans.ReturnDate
FROM Loans
JOIN Members ON Loans.MemberID = Members.MemberID
JOIN Books ON Loans.BookID = Books.BookID
WHERE Loans.ReturnDate < GETDATE() AND Loans.Returned = 0;

--To Maintain the System
--Update Book Details
UPDATE Books
SET Genre = 'Classic Fiction'
WHERE BookID = 1;

--Add New Copies to a Book
UPDATE Books
SET CopiesAvailable = CopiesAvailable + 3
WHERE ISBN = '9780743273565';

--Advanced Queries
--Top 5 Most Borrowed Books
SELECT Top 5 Books.Title, COUNT(Loans.BookID) AS BorrowCount
FROM Loans
JOIN Books ON Loans.BookID = Books.BookID
GROUP BY Books.Title
ORDER BY BorrowCount DESC;

--Members with Most Loans
SELECT Members.FirstName, Members.LastName, COUNT(Loans.MemberID) AS LoanCount
FROM Loans
JOIN Members ON Loans.MemberID = Members.MemberID
GROUP BY Members.FirstName, Members.LastName
ORDER BY LoanCount DESC;

--Penalty for Late Return (Assuming that books returned after its ReturnDate has a penalty of $3 per day late applied
INSERT INTO Penalties (MemberID, LoanID, PenaltyAmount, DueDate, ReturnDate, DaysLate)
SELECT 
    L.MemberID,
    L.LoanID,
    DATEDIFF(day, L.ReturnDate, GETDATE()) * 2 AS PenaltyAmount,
    L.ReturnDate AS DueDate,
    GETDATE() AS ReturnDate,
    DATEDIFF(day, L.ReturnDate, GETDATE()) AS DaysLate
FROM Loans L
WHERE L.Returned = 1 AND GETDATE() > L.ReturnDate
AND NOT EXISTS (SELECT 1 FROM Penalties P WHERE P.LoanID = L.LoanID);

--Create a Reservation Table
CREATE TABLE Reservations (
    ReservationID INT PRIMARY KEY IDENTITY(1,1),
    MemberID INT FOREIGN KEY REFERENCES Members(MemberID),
    BookID INT FOREIGN KEY REFERENCES Books(BookID),
    ReservationDate DATE,
    ReservationStatus NVARCHAR(20) DEFAULT 'Active' -- 'Active', 'Cancelled', 'Fulfilled'
);
--Reserve a Book when it is unavailable
DECLARE @MemberID INT = 1;
DECLARE @BookID INT = 101;
INSERT INTO Reservations (MemberID, BookID, ReservationDate)
SELECT M.MemberID, B.BookID, GETDATE()
FROM Members M, Books B
WHERE M.MemberID = @MemberID AND B.BookID = @BookID
AND B.CopiesAvailable = 0;

--OR use this to reserve books directly
INSERT INTO Reservations (MemberID, BookID, ReservationDate)
SELECT 1, 101, GETDATE()
FROM Books B
WHERE B.BookID = 101
AND B.CopiesAvailable = 1

--Update Reservation Status to Fulfilled when new books are available
UPDATE Reservations
SET ReservationStatus = 'Fulfilled'
WHERE BookID = 1
AND MemberID = 101
AND ReservationStatus = 'Active';

-- Reduce copies available by 1 as it is now reserved
UPDATE Books
SET CopiesAvailable = CopiesAvailable - 1
WHERE BookID = 1;

--Tracking Books Review: Create a Table for Books Review assuming a rating between 1 and 5
CREATE TABLE BookReviews (
    ReviewID INT PRIMARY KEY IDENTITY(1,1),
    MemberID INT FOREIGN KEY REFERENCES Members(MemberID),
    BookID INT FOREIGN KEY REFERENCES Books(BookID),
    Rating INT CHECK (Rating BETWEEN 1 AND 5), 
    ReviewText NVARCHAR(1000),
    ReviewDate DATE
);

--Add a New Review for a Book
DECLARE @Rating INT=5;
DECLARE @ReviewText NVARCHAR(1000);
SET @ReviewText = 'Insightful';

INSERT INTO BookReviews (MemberID, BookID, Rating, ReviewText, ReviewDate)
VALUES (2, 10, 5, 'Insightful', GETDATE());

-SELECT * FROM Books WHERE BookID = 10;

--View Reviews for a Specific Book
SELECT M.FirstName, M.LastName, R.Rating, R.ReviewText, R.ReviewDate
FROM BookReviews R
JOIN Members M ON R.MemberID = M.MemberID
WHERE R.BookID = 2
ORDER BY R.ReviewDate DESC;
