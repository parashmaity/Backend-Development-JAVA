# JDBC & Database Connectivity

## 83. What is JDBC, and how does it work?

JDBC (Java Database Connectivity) is an API that allows Java applications to interact with relational databases. It provides a standard set of interfaces and classes to establish a connection, execute queries, and process results.

### How JDBC Works:
1. Load the database driver (`Class.forName("com.mysql.cj.jdbc.Driver")`).
2. Establish a connection using `DriverManager.getConnection(url, user, password)`.
3. Create a `Statement` or `PreparedStatement` to execute queries.
4. Process results using `ResultSet`.
5. Close connections to free resources.

## 84. What are the different types of JDBC drivers?

JDBC drivers are categorized into four types:

1. **Type 1: JDBC-ODBC Bridge Driver**
    - Uses ODBC (legacy support).
    - Example: `sun.jdbc.odbc.JdbcOdbcDriver`.
    - Slow performance; deprecated.
2. **Type 2: Native-API Driver**
    - Uses vendor-specific libraries.
    - Requires database client software.
    - Example: Oracle OCI driver.
3. **Type 3: Network Protocol Driver (Middleware Driver)**
    - Uses a middleware server to translate JDBC calls.
    - More flexible but slower.
4. **Type 4: Thin Driver (Pure Java Driver)**
    - Directly communicates with the database via TCP/IP.
    - Most commonly used.
    - Example: MySQL Connector/J (`com.mysql.cj.jdbc.Driver`).

**Best Practice:** Use Type 4 drivers because they are platform-independent and provide the best performance.

## 85. What is the difference between Statement and PreparedStatement?

| Feature          | Statement  | PreparedStatement  |
|-----------------|------------|--------------------|
| SQL Execution   | Hardcoded query | Precompiled query |
| Performance     | Slower due to repeated parsing | Faster due to query caching |
| SQL Injection  | Prone to SQL injection | Prevents SQL injection |
| Parameters      | Requires manual string concatenation | Uses `?` placeholders |

### Example:
```java
// Using Statement (not recommended)
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM users WHERE id=" + userId);

// Using PreparedStatement (recommended)
PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
pstmt.setInt(1, userId);
ResultSet rs = pstmt.executeQuery();
```

## 86. How do you manage database connections in Java?

Managing connections properly is crucial for performance. Best practices include:
- Using Connection Pooling (e.g., HikariCP, Apache DBCP).
- Closing connections after use (`conn.close()`).
- Using try-with-resources to auto-close connections.
- Reusing database connections rather than creating new ones for every request.

### Example using try-with-resources:
```java
try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS);
     PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM employees")) {
    ResultSet rs = pstmt.executeQuery();
    while (rs.next()) {
        System.out.println(rs.getString("name"));
    }
} catch (SQLException e) {
    e.printStackTrace();
}
```

## 87. What is connection pooling, and why is it used?

Connection pooling is a mechanism that reuses database connections instead of creating new ones every time a request is made. It improves performance by reducing the overhead of connection creation.

### Benefits:
- Reduces database load.
- Improves application response time.
- Avoids connection exhaustion.

### Popular Connection Pooling Libraries:
- HikariCP (recommended)
- Apache Commons DBCP
- C3P0

### Example using HikariCP:
```java
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
config.setUsername("user");
config.setPassword("password");
config.setMaximumPoolSize(10);

HikariDataSource ds = new HikariDataSource(config);
try (Connection conn = ds.getConnection()) {
    // Use connection
}
```

## 88. Explain the ACID properties in the context of JDBC transactions.

ACID properties ensure data integrity in database transactions:

1. **Atomicity** → A transaction is either fully completed or fully rolled back.
2. **Consistency** → Database remains in a valid state before and after the transaction.
3. **Isolation** → Concurrent transactions do not interfere with each other.
4. **Durability** → Once a transaction is committed, it is permanently saved.

### Example of Transaction Management in JDBC:
```java
try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS)) {
    conn.setAutoCommit(false); // Disable auto-commit

    PreparedStatement pstmt = conn.prepareStatement("INSERT INTO accounts VALUES (?, ?)");
    pstmt.setInt(1, 101);
    pstmt.setDouble(2, 5000);
    pstmt.executeUpdate();

    conn.commit(); // Commit transaction
} catch (SQLException e) {
    conn.rollback(); // Rollback on failure
    e.printStackTrace();
}
```

## 89. How do you handle SQL exceptions in Java?

Use proper exception handling techniques:
- Catch and log exceptions properly.
- Use specific exception messages (`SQLException.getSQLState()`, `.getErrorCode()`).
- Retry failed transactions when needed.

### Example:
```java
try {
    Connection conn = DriverManager.getConnection(DB_URL, USER, PASS);
    PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
    pstmt.setInt(1, 1);
    ResultSet rs = pstmt.executeQuery();
} catch (SQLException e) {
    System.err.println("Error Code: " + e.getErrorCode());
    System.err.println("SQL State: " + e.getSQLState());
    e.printStackTrace();
}
```

## 90. What is the role of the ResultSet interface?

`ResultSet` stores and processes query results returned from the database.

### Key Methods:
- `next()` → Moves cursor forward.
- `getInt("column"), getString("column")` → Fetch column values.
- `close()` → Releases database resources.

### Example:
```java
ResultSet rs = stmt.executeQuery("SELECT name FROM users");
while (rs.next()) {
    System.out.println(rs.getString("name"));
}
rs.close();
```

## 91. What is the difference between executeQuery() and executeUpdate()?

| Method         | Purpose |
|---------------|---------|
| `executeQuery()` | Used for SELECT statements. Returns a `ResultSet`. |
| `executeUpdate()` | Used for INSERT, UPDATE, DELETE. Returns row count. |

### Example:
```java
// executeQuery() - Fetch data
ResultSet rs = stmt.executeQuery("SELECT * FROM users");

// executeUpdate() - Modify data
int rowsAffected = stmt.executeUpdate("UPDATE users SET name='John' WHERE id=1");
```

## 92. How do you perform batch processing in JDBC?

Batch processing executes multiple SQL statements in a single database round trip, improving performance.

### Example:
```java
try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS)) {
    conn.setAutoCommit(false);

    PreparedStatement pstmt = conn.prepareStatement("INSERT INTO employees (id, name) VALUES (?, ?)");
    for (int i = 1; i <= 5; i++) {
        pstmt.setInt(1, i);
        pstmt.setString(2, "Employee " + i);
        pstmt.addBatch();
    }

    int[] updateCounts = pstmt.executeBatch();
    conn.commit();
} catch (SQLException e) {
    e.printStackTrace();
}
```

