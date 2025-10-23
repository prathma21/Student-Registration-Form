# Student-Registration-Form
a Java Swing-based application that allows users to enter student details, validate inputs, and store them in a database.
// Import necessary Swing components for GUI
import javax.swing.*;
// Import layout and event handling classes
import java.awt.*;
import java.awt.event.*;
// Import JDBC classes for database connectivity
import java.sql.*;
// Import regex classes for email validation
import java.util.regex.*;

public class StudentRegistrationForm extends JFrame {
    // Declare input fields and button
    private JTextField nameField, ageField, emailField, courseField;
    private JButton submitButton;

    // Constructor to set up the GUI
    public StudentRegistrationForm() {
        setTitle("Student Registration Form"); // Set window title
        setSize(400, 300); // Set window size
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // Close app on exit
        setLocationRelativeTo(null); // Center the window
        setLayout(new GridLayout(5, 2, 10, 10)); // Use grid layout with spacing

        // Add labels and input fields to the form
        add(new JLabel("Name:"));
        nameField = new JTextField();
        add(nameField);

        add(new JLabel("Age:"));
        ageField = new JTextField();
        add(ageField);

        add(new JLabel("Email:"));
        emailField = new JTextField();
        add(emailField);

        add(new JLabel("Course:"));
        courseField = new JTextField();
        add(courseField);

        // Add submit button
        submitButton = new JButton("Register");
        add(submitButton);

        // Add action listener to handle button click
        submitButton.addActionListener(e -> registerStudent());

        // Make the window visible
        setVisible(true);
    }

    // Method to validate and register student details
    private void registerStudent() {
        // Get input values and trim whitespace
        String name = nameField.getText().trim();
        String ageText = ageField.getText().trim();
        String email = emailField.getText().trim();
        String course = courseField.getText().trim();

        // Check for empty fields
        if (name.isEmpty() || ageText.isEmpty() || email.isEmpty() || course.isEmpty()) {
            JOptionPane.showMessageDialog(this, "All fields are required.");
            return;
        }

        // Validate age as numeric
        if (!ageText.matches("\\d+")) {
            JOptionPane.showMessageDialog(this, "Age must be a number.");
            return;
        }

        // Validate email format using regex
        if (!Pattern.matches("^[\\w.-]+@[\\w.-]+\\.\\w{2,}$", email)) {
            JOptionPane.showMessageDialog(this, "Invalid email format.");
            return;
        }

        // Convert age to integer
        int age = Integer.parseInt(ageText);

        // Connect to MySQL database and insert student record
        try (Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/college", "root", "password")) {
            String query = "INSERT INTO students (name, age, email, course) VALUES (?, ?, ?, ?)";
            PreparedStatement ps = con.prepareStatement(query);
            ps.setString(1, name);
            ps.setInt(2, age);
            ps.setString(3, email);
            ps.setString(4, course);
            ps.executeUpdate();

            // Show success message and clear fields
            JOptionPane.showMessageDialog(this, "Student registered successfully!");
            nameField.setText("");
            ageField.setText("");
            emailField.setText("");
            courseField.setText("");
        } catch (SQLException ex) {
            // Show error message if database operation fails
            JOptionPane.showMessageDialog(this, "Database error: " + ex.getMessage());
        }
    }

    // Main method to launch the application
    public static void main(String[] args) {
        SwingUtilities.invokeLater(StudentRegistrationForm::new);
    }
}

//SQL COMMANDS
//CREATE DATABASE college;
//USE college;
//CREATE TABLE students (
//    id INT AUTO_INCREMENT PRIMARY KEY,
//    name VARCHAR(100),
//    age INT,
//    email VARCHAR(100),
//    course VARCHAR(100)
//);
