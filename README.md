# Student Management System
Spring Boot Student CRUD + H2 + HTML/JS frontend.

Requirements: Java 17, Maven 3.9+.

Run:
mvn clean test
mvn spring-boot:run

Open http://localhost:8080

Health: http://localhost:8080/actuator/health
H2 console: http://localhost:8080/h2-console
JDBC URL: jdbc:h2:file:./data/studentdb
User: sa
Password: empty

Build JAR:
mvn clean package

Docker:
docker build -t student-management:1.0 .
docker run -d --name student-management -p 8080:8080 student-management:1.0
