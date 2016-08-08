Spring Boot, Spring Data JPA and Embedded h2
---------------------------------------------------

In almost all of my projects that involves external resources, I try my best to enable the application to fully run without dependencies.
It's useful to provide a fully working backing 
This sample project shows how a spring-boot application can be setup with an embedded SQL database over JPA.
The focus of this project is to show how to configure an embedded database with Spring Boot, however the source code also contains a RestController and a Spring Data Repository.



Other sample projects with embedded databases 
---------------------------------------------------
* [sample-spring-boot-data-mongodb-embedded](https://github.com/alexturcot/sample-spring-boot-data-mongodb-embedded)
* [sample-spring-boot-data-mongodb-embedded-multiple](https://github.com/alexturcot/sample-spring-boot-data-mongodb-embedded-multiple)
* [sample-spring-boot-data-solr-embedded](https://github.com/alexturcot/sample-spring-boot-data-solr-embedded)
* [sample-spring-boot-data-redis-embedded](https://github.com/alexturcot/sample-spring-boot-data-redis-embedded)
* [sample-spring-boot-data-keyvalue-embedded](https://github.com/alexturcot/sample-spring-boot-data-keyvalue-embedded)
* [sample-spring-boot-data-jpa-embedded](https://github.com/alexturcot/sample-spring-boot-data-jpa-embedded)


Step by step
---------------------------------------------------

**Maven dependencies**
To load an embedded database with Spring Boot, all you really need is to add its maven dependency into your pom. The rest will be taken care of.
In my case I chose h2, so I added the following dependency:
```
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
</dependency>
```

**Spring Boot configuration**
```
spring.datasource.url=jdbc:h2:mem:TEST;MVCC=true;DB_CLOSE_DELAY=-1;MODE=Oracle
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.platform=h2

spring.datasource.initialize=true
#datasource.schema=
#datasource.data
spring.h2.console.enabled=true
spring.jpa.hibernate.ddl-auto=none
```

* **spring.datasource.***: sets up an in-memory H2 database;
* **spring.datasource.initialize**: tells spring-boot to initialize the database with scripts;
* **datasource.schema**: the schema sql script to load. By default it is schema-${platform}.sql then schema.sql;
* **datasource.data**: the data sql script. By default, it is data-${platform}.sql then data.sql;
* **spring.h2.console.enabled**: allow us to access the memory database from a web interface;
* **spring.jpa.hibernate.ddl-auto**: hibernates also tries to initialize the database. 
When it detects an embedded database, it sets ddl-auto to create-drop and initialize the database with entities annotated with @Table (and also looks for imports.sql). 
This may lead to creating the same table twice. I prefer to stick with Spring Boot magic, so I set this to none

**That's it**

Assuming you have a Spring Boot entry point, launch it:
```
@SpringBootApplication
public class Launcher {
    
    public static void main(String[] args){
        new SpringApplicationBuilder() //
        .sources(Launcher.class)//
        .run(args);
    }
}
```
Then, you can access h2's console at: [localhost:8080/h2-console](http://localhost:8080/h2-console)
Simply type in the url ```jdbc:h2:mem:TEST;MVCC=true;DB_CLOSE_DELAY=-1;MODE=Oracle``` in **JDBC URL** field  

It should work, however the database is empty.

Now simply add sql script in src/main/resources.
**schema-h2.sql**:
```
CREATE TABLE MYTABLE
(
    ID NUMBER(19) NOT NULL,
    VAL VARCHAR2(50) NOT NULL,
);
```
**data-h2.sql**:
```
INSERT INTO MODEL (ID, VAL) VALUES (1, 'TEST');
```
If you go back to the h2-console, you should see your data. All that is left to do, is to create your JPARepsitory like you would normally do with Spring Data, spring boot we'll wire it up with a datasource pointing to the embedded database.

Get the code - do it
------------------------
Clone the repository:

    $ git clone https://github.com/alexturcot/sample-spring-boot-data-jpa-embedded.git