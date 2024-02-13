# login
import java.sql.*;

public class LoginRegistrationSystem {
    // JDBC URL, username, and password of MySQL server
    static final String JDBC_URL = "jdbc:mysql://localhost:3306/your_database";
    static final String DB_USER = "your_username";
    static final String DB_PASSWORD = "your_password";

    public static void main(String[] args) {
        // Assuming you have a 'users' table with columns 'username' and 'password'
        createTable();

        // Example usage:
        // Register a new user
        registerUser("john_doe", "password123");

        // Attempt to log in
        boolean loginResult = loginUser("john_doe", "password123");
        if (loginResult) {
            System.out.println("Login successful!");
        } else {
            System.out.println("Login failed. Invalid credentials.");
        }
    }

    static void createTable() {
        try (Connection connection = DriverManager.getConnection(JDBC_URL, DB_USER, DB_PASSWORD);
             Statement statement = connection.createStatement()) {

            String createTableQuery = "CREATE TABLE IF NOT EXISTS users (" +
                    "id INT PRIMARY KEY AUTO_INCREMENT," +
                    "username VARCHAR(255) NOT NULL," +
                    "password VARCHAR(255) NOT NULL)";
            statement.executeUpdate(createTableQuery);

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    static void registerUser(String username, String password) {
        try (Connection connection = DriverManager.getConnection(JDBC_URL, DB_USER, DB_PASSWORD);
             PreparedStatement preparedStatement = connection.prepareStatement(
                     "INSERT INTO users (username, password) VALUES (?, ?)")) {

            preparedStatement.setString(1, username);
            preparedStatement.setString(2, password);
            preparedStatement.executeUpdate();

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    static boolean loginUser(String username, String password) {
        try (Connection connection = DriverManager.getConnection(JDBC_URL, DB_USER, DB_PASSWORD);
             PreparedStatement preparedStatement = connection.prepareStatement(
                     "SELECT * FROM users WHERE username = ? AND password = ?")) {

            preparedStatement.setString(1, username);
            preparedStatement.setString(2, password);
            ResultSet resultSet = preparedStatement.executeQuery();

            return resultSet.next();

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return false;
    }
}
