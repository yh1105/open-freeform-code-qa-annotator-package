
# Post \#71737495 [Link](https://stackoverflow.com/questions/71737495/)

## Getting the oracle database host name at runtime

**Vote**: 6 (473/702) **Views**: 809 (676/702) 

**Internal ID** \#2-5-361

Created at 2022-04-04 12:46:45

Tags: `java` `spring` `oracle` `spring-boot`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

There is a Springboot application which connects to the Oracle data. The URL for the database is configured as
```
spring.datasource.url=jdbc:oracle:thin:@(DESCRIPTION=\
  (LOAD_BALANCE=OFF)(FAILOVER=ON)\
  (ADDRESS=(PROTOCOL=TCP)(HOST=domainName1.com) (PORT=1521))\
  (ADDRESS=(PROTOCOL=TCP)(HOST=domainName2.com)(PORT=1521))\
  (CONNECT_DATA=(SERVICE_NAME=xyz)))
```

This URL is configured so that when one host is down then the application connects to second database.
The URL to the database is printed in the application healthcheck as shown below
```
Hello 
version    : 4.0.0
build      : 2022-03-3 
datasource :    oracle.jdbc.OracleDriver
db url     :    jdbc:oracle:thin:@(DESCRIPTION=(LOAD_BALANCE=OFF)(FAILOVER=ON)(ADDRESS=(PROTOCOL=TCP)(HOST=domainName1.com) (PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=domainName2.com)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=xyz)))
db status  :    ok
```

My question is how can I get just the host name of the database being used, that means how can I get the host currently being used (domainName1 or domainName2) by the application and display it on healthcheck? For example as shown below.
```
Hello 
version    : 4.0.0
build      : 2022-03-3
datasource :    oracle.jdbc.OracleDriver
db url     :    jdbc:oracle:thin:@domainName1.com: 1521/coldv1
db status  :    ok (LVZ count = 379)
```

The java code I used for this healthcheck is as shown below.
```
@GetMapping(path = "/healthcheck",
            produces = MediaType.APPLICATION_JSON_VALUE
    )
    public String getServiceStatus(){
        String[] activeProfiles = environment.getActiveProfiles();
        final BeanWrapper accessor = PropertyAccessorFactory.forBeanPropertyAccess(dataSource);
        final String driverClassName = String.valueOf(accessor.getPropertyValue("driverClassName"));
        String url = null;

        if(activeProfiles[1].equals("external_tomcat")) {
            url = String.valueOf(accessor.getPropertyValue("url"));
        }else{
            try {
                String[] dataSourceProperties = nameService.getDataSource();
                url = dataSourceProperties[0];
            } catch (SQLException ex) {
//                throwables.printStackTrace();
                log.error(ex.getMessage(), ex);
            }
        }

        String version = buildProperties.getVersion();
        String buildTimestamp = String.valueOf(buildProperties.getTime());
        BigDecimal count = nameService.getCount("Table_name_of_database");

        StringBuilder result = new StringBuilder("Hello")
                .append("\nversion    :\t")
                .append(version)
                .append("\nbuild      :\t")
                .append(buildTimestamp)
                .append("\ndatasource :\t")
                .append(driverClassName)
                .append("\ndb url     :\t")
                .append(url)
                .append("\ndb status  :\tok  ")
                .append(count.intValue())
                .append(")");
        return result.toString();
    }
```


## NameService.java


```
@Override
    public String[] getDataSource() throws SQLException {
        return getDataSourceProperties();
    }


   

    public String[] getDataSourceProperties() {
        String[] dataSourceProperties = new String[2];
        HikariDataSource dataSource = getDataSourceFromHibernateEntityManager();
        if(dataSource.getJdbcUrl() != null){
            dataSourceProperties[0] = dataSource.getJdbcUrl();
            dataSourceProperties[1] = dataSource.getDataSourceClassName();
        }
        return dataSourceProperties;
    }
```



----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-04-12 11:13:01

------------

@Olivier and @ik_zelf both answers works for me.
And the way I implemented in spring boot application is using the following code snippet.
```
public String getHostNameFromUrl() {
        String sql = "SELECT host_name FROM v$instance";
//        String sql = "SELECT sys_context('USERENV','SERVER_HOST') server_host FROM dual"; this also works
        Query query = entityManager.createNativeQuery(sql);
        return query.getSingleResult().toString();
}
```

and later in the controller I call this method to display in the healthcheck.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-04-07 07:16:07

------------

A solution is to execute an SQL query to retrieve the server host name, for example:
```
SELECT host_name FROM v$instance
```

See [this question](https://stackoverflow.com/questions/2366962/find-the-server-name-for-an-oracle-database) for more details.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-04-07 09:14:56

------------

The database instance has this data available so you should ask the database instance.
It is tempting to use v$instance but that needs an extra select privilege.
```
SELECT sys_context('USERENV','SERVER_HOST') server_host
 FROM dual;
```

Does not need extra privileges.


------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-04-11 08:53:42

------------

Use
```
dataSource.getConnection().getMetaData().getURL();
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-04-07 18:44:11

------------

If you are not able to get it from the database as in the other 2 answers, then inject the spring property into a component using @Value:
```
@Value("spring.datasource.url")
String datasource;
```

Then parse out the values from the string.


------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-04-08 15:50:10

------------

Have you considered a more native approach?
What I mean is why don't you decorate the Datasource implementation.
You can use `org.springframework.beans.factory.config.BeanPostProcessor` and its variants to achieve this, and an object that wraps your datasource and decorates/override the `Datasource#getConnection` to notify or save the value or the property you want somewhere.


------------
    
    