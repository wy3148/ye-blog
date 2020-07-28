---
title: simple jdbc example to connect mysql
date: 2020-07-28 21:10:34
tags: java
categories: java
---


# java maven for jdbc


```java
    <dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.21</version>
</dependency>

```


```java
        Connection conn = null;

        try {
            conn = DriverManager.getConnection("jdbc:mysql://localhost/hearing?" +
                            "user=root&password=pass1234&serverTimezone=UTC");

            String user ="test@gmail.com";
            PreparedStatement st = conn.prepareStatement("SELECT * FROM Customer WHERE Email = ?");
            st.setString(1,user);
            ResultSet ret = st.executeQuery();
            while(ret.next()) {
            	//here we can get the column by name
                System.out.println(ret.getString("FirstName"));
                System.out.println(ret.getString("Created"));
            }
        } catch (SQLException ex) {
            // handle any errors
            System.out.println("SQLException: " + ex.getMessage());
            System.out.println("SQLState: " + ex.getSQLState());
            System.out.println("VendorError: " + ex.getErrorCode());
        }
```

