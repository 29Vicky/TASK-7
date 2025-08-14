# TASK-7
CREATE DATABASE LIST;
USE LIST;
-- Create Authors table
CREATE TABLE Authors (
    AuthorID INT PRIMARY KEY,
    Name VARCHAR(100),
    Country VARCHAR(50)
);

-- Create Books table
CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(100),
    Genre VARCHAR(50),
    AuthorID INT,
    PublishedYear INT,
    FOREIGN KEY (AuthorID) REFERENCES Authors(AuthorID)
);

-- Create Members table
CREATE TABLE Members (
    MemberID INT PRIMARY KEY,
    Name VARCHAR(100),
    Email VARCHAR(100)
);

-- Create Borrow table
CREATE TABLE Borrow (
    BorrowID INT PRIMARY KEY,
    BookID INT,
    MemberID INT,
    BorrowDate DATE,
    ReturnDate DATE,
    FOREIGN KEY (BookID) REFERENCES Books(BookID),
    FOREIGN KEY (MemberID) REFERENCES Members(MemberID)
);

-- Insert sample data
INSERT INTO Authors VALUES
(1, 'J.K. Rowling', 'UK'),
(2, 'Chetan Bhagat', 'India'),
(3, 'R.K. Narayan', 'India'),
(4, 'George R.R. Martin', 'USA');

INSERT INTO Books VALUES
(1, 'Harry Potter', 'Fantasy', 1, 1997),
(2, 'Five Point Someone', 'Fiction', 2, 2004),
(3, 'Malgudi Days', 'Short Stories', 3, 1943),
(4, 'A Game of Thrones', 'Fantasy', 4, 1996),
(5, 'Half Girlfriend', 'Romance', 2, 2014);

INSERT INTO Members VALUES
(1, 'Alice', 'alice@example.com'),
(2, 'Bob', 'bob@example.com'),
(3, 'Charlie', 'charlie@example.com');

INSERT INTO Borrow VALUES
(1, 1, 1, '2024-01-10', '2024-01-20'),
(2, 2, 1, '2024-02-15', NULL),
(3, 3, 2, '2024-02-20', '2024-02-28'),
(4, 1, 2, '2024-03-01', NULL),
(5, 5, 1, '2024-03-10', NULL);

-- ======================
-- 1. View: All books with their author name and genre
CREATE VIEW view_books_authors AS
SELECT B.BookID, B.Title, B.Genre, B.PublishedYear, A.Name AS AuthorName, A.Country
FROM Books B
JOIN Authors A ON B.AuthorID = A.AuthorID;

-- Test the view
SELECT * FROM view_books_authors;

-- ======================
-- 2. View: Members who borrowed books (with borrow details)
CREATE VIEW view_member_borrow_details AS
SELECT M.MemberID, M.Name AS MemberName, M.Email, B.Title AS BookTitle, BR.BorrowDate, BR.ReturnDate
FROM Members M
JOIN Borrow BR ON M.MemberID = BR.MemberID
JOIN Books B ON BR.BookID = B.BookID;

-- Test the view
SELECT * FROM view_member_borrow_details;

-- ======================
-- 3. View: Count of total books borrowed by each member
CREATE VIEW view_member_borrow_count AS
SELECT M.MemberID, M.Name AS MemberName, COUNT(BR.BorrowID) AS TotalBorrowed
FROM Members M
LEFT JOIN Borrow BR ON M.MemberID = BR.MemberID
GROUP BY M.MemberID, M.Name;

-- Test the view
SELECT * FROM view_member_borrow_count;

-- ======================
-- 4. View: Overdue books (not returned after 30 days)
CREATE VIEW view_overdue_books AS
SELECT M.Name AS MemberName, B.Title AS BookTitle, BR.BorrowDate, DATEDIFF(CURDATE(), BR.BorrowDate) AS DaysBorrowed
FROM Borrow BR
JOIN Members M ON BR.MemberID = M.MemberID
JOIN Books B ON BR.BookID = B.BookID
WHERE BR.ReturnDate IS NULL
  AND DATEDIFF(CURDATE(), BR.BorrowDate) > 30;

-- Test the view
SELECT * FROM view_overdue_books;

-- ======================
-- 5. View: Authors with total books they have written
CREATE VIEW view_author_book_count AS
SELECT A.AuthorID, A.Name AS AuthorName, COUNT(B.BookID) AS TotalBooks
FROM Authors A
LEFT JOIN Books B ON A.AuthorID = B.AuthorID
GROUP BY A.AuthorID, A.Name;

-- Test the view
SELECT * FROM view_author_book_count;
