## Demo of Spring Boot 2.7.0 with actuator endpoints and using maven shade 3.3.0 leading to broken jar

### tl;dr
Spring Boot 2.7.0 with actuator endpoints which is shaded using maven shade plugin leads to 404 on actuator endpoints

### How to reproduce
Build and run this project using `mvn clean package && java -jar target/demo-0.0.1-SNAPSHOT.jar`

#### Test Requests
`curl http://localhost:8080/test/ping` returns http 200 `pong` -> works

`curl http://localhost:8080/actuator/health` return http 200 `{"status":"UP"}` -> **does not work, returns 404**

### Findings so far to "resolve" the issue

* Downgrading to Spring 2.6.8
* Building the jar with spring-boot-maven-plugin without shading
* Running the project directly in IntelliJ


## Final solution
See https://github.com/spring-projects/spring-boot/issues/31316, credits to @philwebb
```
<transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
  <resource>META-INF/spring.handlers</resource>
  <resource>META-INF/spring.schemas</resource>
  <resource>META-INF/spring/org.springframework.boot.actuate.autoconfigure.web.ManagementContextConfiguration.imports</resource>
  <resource>META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports</resource>
</transformer>
```
