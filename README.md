import javax.swing.*; import
java.awt.*;
import java.awt.event.ActionEvent; import
java.awt.event.ActionListener; import
java.sql.*;
public class HospitalManagementSystem {
private Connection connection;
public HospitalManagementSystem() {
// Initialize the database connection try
{ connection =
DriverManager.getConnection("jdbc:mysql://localhost:3306/hospitaldata", "root",
"1234");
} catch (SQLException e) {
e.printStackTrace();
JOptionPane.showMessageDialog(null, "Failed to connect to the database.");
}
createAndShowGUI();
}
private void createAndShowGUI() {
// Create the main frame
JFrame frame = new JFrame("Hospital Management System");
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); frame.setSize(400,
400);
// Create a panel for patient management
JPanel panel = new JPanel();
frame.add(panel);
// Add components to the panel
panel.setLayout(new FlowLayout(FlowLayout.CENTER, 10, 10));
JLabel nameLabel = new JLabel("Patient Name:");
JTextField nameField = new JTextField(20);
JLabel phoneLabel = new JLabel("Phone Number:");
JTextField phoneField = new JTextField(20);

11

2
JLabel reasonLabel = new JLabel("Reason for Visit:");
JTextField reasonField = new JTextField(20);
JLabel admitDateLabel = new JLabel("Admit Date (YYYY-MM-DD):");
JTextField admitDateField = new JTextField(20);
JButton addButton = new JButton("Add Patient");
JButton listButton = new JButton("List Patients");
panel.add(nameLabel);
panel.add(nameField);
panel.add(phoneLabel);
panel.add(phoneField);
panel.add(reasonLabel);
panel.add(reasonField);
panel.add(admitDateLabel);
panel.add(admitDateField);
panel.add(addButton); panel.add(listButton);
// Add action listeners
addButton.addActionListener(new ActionListener() {
@Override
public void actionPerformed(ActionEvent e) {
String name = nameField.getText();
String phone = phoneField.getText();
String reason = reasonField.getText(); String
admitDate = admitDateField.getText(); if
(!name.isEmpty()) {
addPatient(name, phone, reason, admitDate);
nameField.setText(""); // Clear the input fields after adding
phoneField.setText(""); reasonField.setText("");
admitDateField.setText("");
} else {
JOptionPane.showMessageDialog(null, "Please enter a patient name.");
}
}
});
listButton.addActionListener(new ActionListener() {
@Override

12

2
public void actionPerformed(ActionEvent e) { listPatients();
}
});
frame.setVisible(true);
}
private void addPatient(String name, String phone, String reason, String
admitDate) {
try {
String insertQuery = "INSERT INTO patients (name, phone_number,
reason_for_visit, admit_date) VALUES (?, ?, ?, ?)"; PreparedStatement
preparedStatement = connection.prepareStatement(insertQuery);
preparedStatement.setString(1, name); preparedStatement.setString(2,
phone); preparedStatement.setString(3, reason);
preparedStatement.setString(4, admitDate);
preparedStatement.executeUpdate();
JOptionPane.showMessageDialog(null, "Patient added successfully!");
} catch (SQLException e) { e.printStackTrace();
JOptionPane.showMessageDialog(null, "Error adding patient.");
}
}
private void listPatients() { try
{
String selectQuery = "SELECT * FROM patients";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(selectQuery); StringBuilder
patients = new StringBuilder(); while (resultSet.next()) { int id =
resultSet.getInt("id");
String name = resultSet.getString("name");
String phone = resultSet.getString("phone_number");
String reason = resultSet.getString("reason_for_visit");
String admitDate = resultSet.getString("admit_date");
patients.append("ID: ").append(id).append(", Name: ").append(name).append(",
Phone: ").append(phone).append(", Reason: ").append(reason).append(", Admit
Date: ").append(admitDate).append("\n");
}

13

2
if (patients.length() > 0) {
JOptionPane.showMessageDialog(null, patients.toString(), "Patients",
JOptionPane.INFORMATION_MESSAGE);
} else {
JOptionPane.showMessageDialog(null, "No patients found.", "Patients",
JOptionPane.INFORMATION_MESSAGE);
}
} catch (SQLException e) {
e.printStackTrace();
JOptionPane.showMessageDialog(null, "Error listing patients.");
}
}
public static void main(String[] args) {
SwingUtilities.invokeLater(new Runnable() { public
void run() {
new HospitalManagementSystem();
}
});
}
}
