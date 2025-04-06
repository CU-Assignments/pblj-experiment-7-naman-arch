[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/_-JrKZbN)
// ============================ // Program 1: Product CRUD App // ============================

import java.sql.*; import java.util.Scanner;

public class ProductCRUDApp { private static final String DB_URL = "jdbc:mysql://localhost:3306/your_database"; private static final String DB_USER = "root"; private static final String DB_PASSWORD = "password";

public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);

    try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD)) {
        conn.setAutoCommit(false);
        while (true) {
            System.out.println("\n1. Create Product\n2. Read Products\n3. Update Product\n4. Delete Product\n5. Exit");
            int choice = scanner.nextInt();

            switch (choice) {
                case 1 -> createProduct(conn, scanner);
                case 2 -> readProducts(conn);
                case 3 -> updateProduct(conn, scanner);
                case 4 -> deleteProduct(conn, scanner);
                case 5 -> {
                    conn.close();
                    return;
                }
                default -> System.out.println("Invalid choice");
            }
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
}

private static void createProduct(Connection conn, Scanner scanner) {
    try {
        System.out.print("Enter Product Name: ");
        String name = scanner.next();
        System.out.print("Enter Price: ");
        double price = scanner.nextDouble();
        System.out.print("Enter Quantity: ");
        int qty = scanner.nextInt();

        String sql = "INSERT INTO Product (ProductName, Price, Quantity) VALUES (?, ?, ?)";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setString(1, name);
        stmt.setDouble(2, price);
        stmt.setInt(3, qty);
        stmt.executeUpdate();
        conn.commit();
        System.out.println("Product added successfully.");
    } catch (SQLException e) {
        try { conn.rollback(); } catch (SQLException ex) { ex.printStackTrace(); }
        e.printStackTrace();
    }
}

private static void readProducts(Connection conn) throws SQLException {
    Statement stmt = conn.createStatement();
    ResultSet rs = stmt.executeQuery("SELECT * FROM Product");

    while (rs.next()) {
        System.out.println("ID: " + rs.getInt("ProductID") + ", Name: " + rs.getString("ProductName") +
                ", Price: " + rs.getDouble("Price") + ", Quantity: " + rs.getInt("Quantity"));
    }
}

private static void updateProduct(Connection conn, Scanner scanner) {
    try {
        System.out.print("Enter Product ID to update: ");
        int id = scanner.nextInt();
        System.out.print("Enter new Price: ");
        double price = scanner.nextDouble();
        System.out.print("Enter new Quantity: ");
        int qty = scanner.nextInt();

        String sql = "UPDATE Product SET Price = ?, Quantity = ? WHERE ProductID = ?";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setDouble(1, price);
        stmt.setInt(2, qty);
        stmt.setInt(3, id);
        stmt.executeUpdate();
        conn.commit();
        System.out.println("Product updated successfully.");
    } catch (SQLException e) {
        try { conn.rollback(); } catch (SQLException ex) { ex.printStackTrace(); }
        e.printStackTrace();
    }
}

private static void deleteProduct(Connection conn, Scanner scanner) {
    try {
        System.out.print("Enter Product ID to delete: ");
        int id = scanner.nextInt();

        String sql = "DELETE FROM Product WHERE ProductID = ?";
        PreparedStatement stmt = conn.prepareStatement(sql);
        stmt.setInt(1, id);
        stmt.executeUpdate();
        conn.commit();
        System.out.println("Product deleted successfully.");
    } catch (SQLException e) {
        try { conn.rollback(); } catch (SQLException ex) { ex.printStackTrace(); }
        e.printStackTrace();
    }
}

}

// ============================================= // Program 2: Student Management App using MVC // =============================================

// Student.java (Model) public class Student { private int studentID; private String name; private String department; private double marks;

// Constructors, Getters, and Setters
public Student(int studentID, String name, String department, double marks) {
    this.studentID = studentID;
    this.name = name;
    this.department = department;
    this.marks = marks;
}

public int getStudentID() { return studentID; }
public String getName() { return name; }
public String getDepartment() { return department; }
public double getMarks() { return marks; }

public void setStudentID(int studentID) { this.studentID = studentID; }
public void setName(String name) { this.name = name; }
public void setDepartment(String department) { this.department = department; }
public void setMarks(double marks) { this.marks = marks; }

}

// StudentController.java (Controller) import java.sql.; import java.util.;

public class StudentController { private final Connection conn;

public StudentController() throws SQLException {
    conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/your_database", "root", "password");
}

public void addStudent(Student s) throws SQLException {
    String sql = "INSERT INTO Student (StudentID, Name, Department, Marks) VALUES (?, ?, ?, ?)";
    PreparedStatement stmt = conn.prepareStatement(sql);
    stmt.setInt(1, s.getStudentID());
    stmt.setString(2, s.getName());
    stmt.setString(3, s.getDepartment());
    stmt.setDouble(4, s.getMarks());
    stmt.executeUpdate();
}

public List<Student> getAllStudents() throws SQLException {
    List<Student> list = new ArrayList<>();
    Statement stmt = conn.createStatement();
    ResultSet rs = stmt.executeQuery("SELECT * FROM Student");
    while (rs.next()) {
        Student s = new Student(rs.getInt("StudentID"), rs.getString("Name"), rs.getString("Department"), rs.getDouble("Marks"));
        list.add(s);
    }
    return list;
}

public void updateStudent(Student s) throws SQLException {
    String sql = "UPDATE Student SET Name=?, Department=?, Marks=? WHERE StudentID=?";
    PreparedStatement stmt = conn.prepareStatement(sql);
    stmt.setString(1, s.getName());
    stmt.setString(2, s.getDepartment());
    stmt.setDouble(3, s.getMarks());
    stmt.setInt(4, s.getStudentID());
    stmt.executeUpdate();
}

public void deleteStudent(int id) throws SQLException {
    String sql = "DELETE FROM Student WHERE StudentID = ?";
    PreparedStatement stmt = conn.prepareStatement(sql);
    stmt.setInt(1, id);
    stmt.executeUpdate();
}

}

// StudentView.java (View) import java.util.*;

public class StudentView { public static void main(String[] args) { try { StudentController controller = new StudentController(); Scanner scanner = new Scanner(System.in);

while (true) {
            System.out.println("\n1. Add Student\n2. View Students\n3. Update Student\n4. Delete Student\n5. Exit");
            int choice = scanner.nextInt();

            switch (choice) {
                case 1 -> {
                    System.out.print("Enter ID: ");
                    int id = scanner.nextInt();
                    System.out.print("Enter Name: ");
                    String name = scanner.next();
                    System.out.print("Enter Department: ");
                    String dept = scanner.next();
                    System.out.print("Enter Marks: ");
                    double marks = scanner.nextDouble();
                    controller.addStudent(new Student(id, name, dept, marks));
                }
                case 2 -> {
                    List<Student> students = controller.getAllStudents();
                    for (Student s : students) {
                        System.out.println(s.getStudentID() + " | " + s.getName() + " | " + s.getDepartment() + " | " + s.getMarks());
                    }
                }
                case 3 -> {
                    System.out.print("Enter ID to update: ");
                    int id = scanner.nextInt();
                    System.out.print("Enter New Name: ");
                    String name = scanner.next();
                    System.out.print("Enter New Department: ");
                    String dept = scanner.next();
                    System.out.print("Enter New Marks: ");
                    double marks = scanner.nextDouble();
                    controller.updateStudent(new Student(id, name, dept, marks));
                }
                case 4 -> {
                    System.out.print("Enter ID to delete: ");
                    int id = scanner.nextInt();
                    controller.deleteStudent(id);
                }
                case 5 -> {
                    return;
                }
                default -> System.out.println("Invalid choice");
            }
        }

    } catch (Exception e) {
        e.printStackTrace();
    }
}

}

