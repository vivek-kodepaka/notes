### simple jdbc

1. Class.forName("com.mysql.cj.jdbc.Driver");   -- load and register to DriverManager
2. Connection  conn = DriverManager.getConnection(url,username,password); -- get connections for driver
3. Statement statement = connection.createStatement();   -- statment obj to execute query
4. ResultSet rs = statement.executeQuery(query);
    - executeQuery()  - select - ResultSet
    - executeUpate()  - insert/update/selete  - Int
    - execute()       - any  - boolean
5. conn.close()

```java
 while (rs.next()) {
                int id = rs.getInt(1);
                String name = rs.getString(2);
                Date date = rs.getDate("dob");
                int batchid = rs.getInt("batchId");


                System.out.println(id + "\t" + name + "\t\t" + date + "\t\t"+batchid);
            }
```
6.  PreparedStatement preparedStatement = connection.prepareStatement(query);
















------------------------------------------------------------------------------------------

### spring -jdbc

- ```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
 <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
```

