#mysql #connectionpool

You can check how many connections are currently being used and how many are available in the MySQL connection pool using a few different methods. Here's how to do it:

### 1. **Querying the MySQL Status**:
You can query MySQL's internal status to see the current connection information.

Run the following SQL command to check the status of connections:

```sql
SHOW STATUS LIKE 'Threads_connected';
```

This will show you how many active connections (threads) are currently connected to the MySQL server.

You can also get information on the maximum allowed connections:

```sql
SHOW VARIABLES LIKE 'max_connections';
```

This will tell you the maximum number of connections allowed by MySQL.

### 2. **Using `SHOW PROCESSLIST`**:
This command provides details about the active connections. It shows a list of currently running threads connected to the MySQL server:

```sql
SHOW PROCESSLIST;
```

To see all connections (including idle ones), use:

```sql
SHOW FULL PROCESSLIST;
```

This will give you details about each connection, such as the user, host, database, command, and current state.

### 3. **Checking the InnoDB Status**:
If you're using the InnoDB engine (which is typical for MySQL databases), you can use the following command to get detailed information about the status of InnoDB, including active connections:

```sql
SHOW ENGINE INNODB STATUS;
```

### 4. **Using MySQL Workbench**:
If you're using MySQL Workbench or another GUI tool, you can monitor connections visually:
- Go to **Server > Server Status** in MySQL Workbench.
- You will find information on how many connections are in use and other related metrics.

### 5. **Using Information Schema**:
You can also query the `INFORMATION_SCHEMA.PROCESSLIST` table to get detailed information about current sessions:

```sql
SELECT * FROM INFORMATION_SCHEMA.PROCESSLIST;
```

This table shows similar information to `SHOW PROCESSLIST` but provides additional filtering and querying capabilities.

### 6. **Monitor HikariCP Connections in Spring Boot**:
If you're using HikariCP in Spring Boot (which is the default connection pool), you can also monitor the connection pool usage within your Spring Boot application.

Add the following actuator dependency in your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Then, expose metrics by adding these lines to your `application.properties` or `application.yml`:

```properties
management.endpoint.metrics.enabled=true
management.endpoints.web.exposure.include=metrics
management.metrics.export.simple.enabled=true
```

After starting your application, you can check the connection pool details via the `/actuator/metrics/hikaricp.connections` endpoint.

This endpoint will show you details like:
- `hikaricp.connections.active`
- `hikaricp.connections.idle`
- `hikaricp.connections.max`
- `hikaricp.connections.min`

These metrics will help you track connection pool usage in real-time.