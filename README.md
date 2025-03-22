# SQL
This is a SQL project on HR analytical database.

# Here is the SQL query ---

-- HR Analytics Database Schema

-- Creating the Departments Table
CREATE TABLE Departments (
    department_id INT PRIMARY KEY AUTO_INCREMENT,
    department_name VARCHAR(100) NOT NULL UNIQUE  -- Added UNIQUE constraint
);

-- Creating the Employees Table
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    department_id INT,
    hire_date DATE NOT NULL,  -- Added NOT NULL as hire_date should be mandatory
    salary DECIMAL(10,2) CHECK (salary >= 0),  -- Added check constraint
    attrition_status BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (department_id) REFERENCES Departments(department_id)
    ON DELETE SET NULL  -- Added cascade rule
);

-- Creating the Salaries Table
CREATE TABLE Salaries (
    salary_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_id INT NOT NULL,  -- Added NOT NULL
    salary DECIMAL(10,2) CHECK (salary >= 0),
    bonus DECIMAL(10,2) CHECK (bonus >= 0),
    effective_date DATE,  -- Added to track salary history
    FOREIGN KEY (employee_id) REFERENCES Employees(employee_id)
    ON DELETE CASCADE  -- Added cascade rule
);

-- Creating the Attendance Table
CREATE TABLE Attendance (
    attendance_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_id INT NOT NULL,
    attendance_date DATE NOT NULL,
    status ENUM('Present', 'Absent', 'On Leave') DEFAULT 'Present',
    FOREIGN KEY (employee_id) REFERENCES Employees(employee_id)
    ON DELETE CASCADE
);

-- Creating the Performance Table
CREATE TABLE Performance (
    performance_id INT PRIMARY KEY AUTO_INCREMENT,
    employee_id INT NOT NULL,
    review_date DATE NOT NULL,
    rating INT CHECK (rating BETWEEN 1 AND 5),
    comments TEXT,
    FOREIGN KEY (employee_id) REFERENCES Employees(employee_id)
    ON DELETE CASCADE
);

-- Sample Data Insertion
INSERT INTO Departments (department_name) VALUES 
    ('HR'), 
    ('IT'), 
    ('Finance'), 
    ('Marketing');

INSERT INTO Employees (first_name, last_name, department_id, hire_date, salary, attrition_status) 
VALUES 
    ('John', 'Doe', 2, '2020-03-15', 70000.00, FALSE), 
    ('Jane', 'Smith', 1, '2019-06-20', 60000.00, FALSE), 
    ('Bob', 'Johnson', 3, '2021-07-10', 80000.00, TRUE);

-- Complex Query: Get Employee Count per Department
SELECT d.department_name, COUNT(e.employee_id) AS employee_count
FROM Departments d
LEFT JOIN Employees e ON d.department_id = e.department_id
GROUP BY d.department_name;

-- Stored Procedure: Get Employees by Department
DELIMITER //
CREATE PROCEDURE GetEmployeesByDepartment(IN dept_id INT)
BEGIN
    SELECT * FROM Employees 
    WHERE department_id = dept_id 
    AND attrition_status = FALSE;  -- Added to show only active employees
END //
DELIMITER ;
