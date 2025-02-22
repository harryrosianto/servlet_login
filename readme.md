To create a Java Servlet for login using NetBeans with a Microsoft SQL Server database, follow these steps:

### Prerequisites
1. NetBeans IDE with Java EE support.
2. Microsoft SQL Server with the database `students_info` and table `dbo.student_login`.
3. JDBC Driver for SQL Server. You can download it from the Microsoft website.

### Steps

#### 1. Set Up the Database
Ensure you have the database and table set up. Here's an example SQL script for your table:


```sql
CREATE DATABASE students_info;
USE students_info;
CREATE TABLE dbo.student_login (
    username VARCHAR(50) PRIMARY KEY,
    userpassword VARCHAR(50)
);
```


#### 2. Create a New Project in NetBeans
1. Open NetBeans.
2. Go to `File` -> `New Project`.
3. Choose `Java Web` -> `Web Application` and click `Next`.
4. Name your project (e.g., `LoginServletApp`) and specify the project location.
5. Choose your server (e.g., Apache Tomcat) and Java EE version, then click `Finish`.


#### 3. Add JDBC Driver to Project
1. Right-click on your project in the `Projects` pane.
2. Go to `Properties` -> `Libraries`.
3. Click `Add Library` -> `New Library`.
4. Name the library (e.g., `SQLServerJDBC`).
5. Click `Add JAR/Folder` and add the JDBC driver JAR file you downloaded.


#### 4. Configure Database Connection
1. Create a `DBConnection` class to handle database connections.


```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;


public class DBConnection {
    private static final String URL = "jdbc:sqlserver://<server>:<port>;databaseName=students_info";
    private static final String USERNAME = "<your-username>";
    private static final String PASSWORD = "<your-password>";


    public static Connection getConnection() throws SQLException {
        try {
            Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return DriverManager.getConnection(URL, USERNAME, PASSWORD);
    }
}
```


Replace `<server>`, `<port>`, `<your-username>`, and `<your-password>` with your SQL Server details.


#### 5. Create the Servlet
1. Right-click on the `Source Packages` in the `Projects` pane.
2. Select `New` -> `Servlet`.
3. Name your servlet (e.g., `LoginServlet`) and specify the package (e.g., `com.example`).


#### 6. Implement the Servlet Code
Modify the servlet to handle login requests:

```java
package com.example;


import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;


@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String username = request.getParameter("username");
        String password = request.getParameter("password");


        try (Connection connection = DBConnection.getConnection()) {
            String sql = "SELECT * FROM dbo.student_login WHERE username = ? AND userpassword = ?";
            PreparedStatement statement = connection.prepareStatement(sql);
            statement.setString(1, username);
            statement.setString(2, password);
            ResultSet resultSet = statement.executeQuery();


            response.setContentType("text/html");
            PrintWriter out = response.getWriter();


            if (resultSet.next()) {
                out.println("Login successful! Welcome, " + username);
            } else {
                out.println("Invalid username or password.");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }


    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.getWriter().println("Login Servlet is running...");
    }
}
```


#### 7. Create the Login Form
1. Right-click on the `Web Pages` folder.
2. Select `New` -> `HTML`.
3. Name it `login.html`.


```html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <h2>Login</h2>
    <form action="LoginServlet" method="post">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username"><br><br>
        <label for="password">Password:</label>
        <input type="password" id="password" name="password"><br><br>
        <input type="submit" value="Login">
    </form>
</body>
</html>
```


#### 8. Run the Project
1. Right-click on your project.
2. Select `Run`.
3. Open the login page (e.g., `http://localhost:8080/LoginServletApp/login.html`).
4. Enter a username and password to test the login functionality.


By following these steps, you will have a Java Servlet application that connects to a Microsoft SQL Server database to handle user logins.



