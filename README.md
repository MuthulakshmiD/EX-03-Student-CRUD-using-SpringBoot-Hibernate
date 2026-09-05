## EXP 03 - Entity Student and Build CRUD Operations using Spring Boot Hibernate Configuration
Name: Muthulakshmi D
Register Number: 212223040122
## AIM:

To develop a Spring Boot application that performs CRUD (Create, Read, Update, Delete) operations on a Student entity using Spring Data JPA (Hibernate) and an in-memory H2 database.

## ALGORITHM:

Create a Spring Boot Project.

Add dependencies:

Spring Web MVC
Spring Data JPA
H2 Database
Spring Boot DevTools

Configure application.properties.

Define the H2 database connection.

Enable Hibernate automatic table creation using JPA configuration.

Create the Student Entity Class.

Annotate the class with @Entity.
Define fields using @Id and @GeneratedValue.

Create StudentRepository.

Extend JpaRepository<Student, Long> to perform CRUD operations.

Create StudentController.

Handle HTTP requests for CRUD operations.

Implement the following REST API endpoints:

POST /students → Add a new student
GET /students → Get all students
GET /students/{id} → Get a student by ID
PUT /students/{id} → Update a student
DELETE /students/{id} → Delete a student

Run the application and verify the CRUD operations using Postman and the H2 Web Console.

## PROGRAM CODE
pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>4.1.1</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>student</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>student</name>
    <description>Student CRUD Application</description>

    <properties>
        <java.version>21</java.version>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <maven.compiler.release>21</maven.compiler.release>
    </properties>

    <dependencies>

        <!-- Spring Boot H2 Console -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-h2console</artifactId>
        </dependency>

        <!-- Spring Data JPA -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- Spring Web MVC -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webmvc</artifactId>
        </dependency>

        <!-- Spring Boot DevTools -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <!-- H2 Database -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- Spring Data JPA Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Spring Web MVC Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webmvc-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```
application.properties
```
spring.application.name=student

# H2 Database
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# H2 Console
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```
Student.java
```
package com.example.student;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
    private String course;

    public Student() {
    }

    public Student(String name, String email, String course) {
        this.name = name;
        this.email = email;
        this.course = course;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getCourse() {
        return course;
    }

    public void setCourse(String course) {
        this.course = course;
    }
}
```
StudentRepository.java
```
package com.example.student;

import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Long> {
}
```
StudentController.java
```
package com.example.student;

import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/students")
public class StudentController {

    private final StudentRepository studentRepository;

    public StudentController(StudentRepository studentRepository) {
        this.studentRepository = studentRepository;
    }

    // CREATE
    @PostMapping
    public Student addStudent(@RequestBody Student student) {
        return studentRepository.save(student);
    }

    // READ - Get all students
    @GetMapping
    public List<Student> getAllStudents() {
        return studentRepository.findAll();
    }

    // READ - Get student by ID
    @GetMapping("/{id}")
    public Student getStudentById(@PathVariable Long id) {
        return studentRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Student not found"));
    }

    // UPDATE
    @PutMapping("/{id}")
    public Student updateStudent(
            @PathVariable Long id,
            @RequestBody Student studentDetails) {

        Student student = studentRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Student not found"));

        student.setName(studentDetails.getName());
        student.setEmail(studentDetails.getEmail());
        student.setCourse(studentDetails.getCourse());

        return studentRepository.save(student);
    }

    // DELETE
    @DeleteMapping("/{id}")
    public String deleteStudent(@PathVariable Long id) {

        studentRepository.deleteById(id);

        return "Student with ID " + id + " deleted successfully!";
    }
}
```
StudentApplication.java
package com.example.student;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class StudentApplication {

    public static void main(String[] args) {
        SpringApplication.run(StudentApplication.class, args);
    }
}
```
## OUTPUT:
POST
<img width="1917" height="1078" alt="Screenshot 2026-09-05 154238" src="https://github.com/user-attachments/assets/6d964be5-9ac2-40dc-b88c-bdf3cc762364" />


POST http://localhost:8080/students


JSON Body:
```
{
    "name": "Anitha",
    "email": "anitha@gmail.com",
    "course": "Computer Science"
}
```


H2 CONSOLE

Open the H2 Console in a browser:

http://localhost:8080/h2-console


Use the following database details:

JDBC URL: jdbc:h2:mem:testdb
User Name: sa
Password:


After connecting, execute:

SELECT * FROM STUDENT;


<img width="1917" height="1027" alt="Screenshot 2026-09-05 154252" src="https://github.com/user-attachments/assets/e9b2ba07-cdfd-431b-90f9-1197bc3da48c" />


RESULT

The Spring Boot application for performing CRUD operations on the Student entity using Spring Data JPA (Hibernate) and an in-memory H2 database was successfully developed, executed, and verified using REST API requests and the H2 Web Console.
