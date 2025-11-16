---
title: Spring Boot 3 入门指南
tags:
  - springboot3
  - java
  - 后端框架
createTime: 2025/11/16 19:13:00
permalink: /blog/68apz2dd/
---

# Spring Boot 3 入门指南

## Spring Boot 3 简介

Spring Boot 3 是基于 Spring Framework 6 的下一代企业级 Java 应用开发框架，提供了快速构建生产级应用的能力。

### 主要特性

- **Java 17+ 支持** - 要求 Java 17 或更高版本
- **GraalVM 原生镜像** - 支持编译为原生应用
- **改进的自动配置** - 更智能的配置机制
- **更好的 Jakarta EE 支持** - 从 javax 迁移到 jakarta
- **性能优化** - 启动更快，内存占用更少

## 环境要求

| 环境 | 最低要求 | 推荐配置 |
|------|---------|---------|
| Java | JDK 17 | JDK 21 LTS |
| Maven | 3.6+ | 3.9+ |
| Gradle | 7.x | 8.x |
| IDE | IntelliJ IDEA | IntelliJ IDEA Ultimate |

## 项目创建

### 使用 Spring Initializr（推荐）

1. 访问 [start.spring.io](https://start.spring.io/)
2. 选择配置：
   - Project: Maven 或 Gradle
   - Language: Java
   - Spring Boot: 3.2.x（最新稳定版）
   - Packaging: Jar
   - Java: 17 或 21
3. 添加依赖（根据需求选择）
4. 生成并下载项目

### 使用命令行

```bash
# 使用 curl 创建项目
curl https://start.spring.io/starter.zip \
  -d dependencies=web,data-jpa \
  -d javaVersion=17 \
  -d bootVersion=3.2.0 \
  -o my-springboot3-app.zip

# 解压并进入目录
unzip my-springboot3-app.zip
cd my-springboot3-app
```

### 使用 IDE 创建

**IntelliJ IDEA:**
1. File → New → Project
2. 选择 Spring Initializr
3. 配置项目信息
4. 选择依赖
5. 完成创建

## 项目结构

典型的 Spring Boot 3 项目结构：

```
my-springboot3-app/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/demo/
│   │   │       ├── DemoApplication.java     # 主启动类
│   │   │       ├── controller/               # 控制器层
│   │   │       ├── service/                 # 业务逻辑层
│   │   │       ├── repository/              # 数据访问层
│   │   │       └── entity/                  # 实体类
│   │   └── resources/
│   │       ├── application.properties       # 配置文件
│   │       ├── static/                      # 静态资源
│   │       └── templates/                   # 模板文件
│   └── test/                                # 测试代码
├── pom.xml                                 # Maven 配置
└── README.md
```

## 核心概念

### 主启动类

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### 配置文件

**application.properties:**
```properties
# 服务器配置
server.port=8080
server.servlet.context-path=/api

# 数据库配置
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA 配置
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect

# 日志配置
logging.level.com.example.demo=DEBUG
```

**application.yml（推荐）：**
```yaml
server:
  port: 8080
  servlet:
    context-path: /api

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
  
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL8Dialect

logging:
  level:
    com.example.demo: DEBUG
```

## Web 开发

### REST 控制器

```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @GetMapping
    public List<User> getUsers() {
        // 返回用户列表
        return userService.findAll();
    }
    
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        // 根据ID查询用户
        return userService.findById(id);
    }
    
    @PostMapping
    public User createUser(@RequestBody User user) {
        // 创建新用户
        return userService.save(user);
    }
    
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        // 更新用户信息
        user.setId(id);
        return userService.save(user);
    }
    
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        // 删除用户
        userService.deleteById(id);
    }
}
```

### 统一响应格式

```java
// 响应包装类
public class ApiResponse<T> {
    private boolean success;
    private String message;
    private T data;
    
    // 构造方法、getter、setter
    public static <T> ApiResponse<T> success(T data) {
        return new ApiResponse<>(true, "成功", data);
    }
    
    public static ApiResponse<?> error(String message) {
        return new ApiResponse<>(false, message, null);
    }
}

// 使用示例
@RestController
public class ApiController {
    
    @GetMapping("/data")
    public ApiResponse<List<String>> getData() {
        List<String> data = Arrays.asList("item1", "item2", "item3");
        return ApiResponse.success(data);
    }
}
```

## 数据访问

### JPA 实体类

```java
package com.example.demo.entity;

import jakarta.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "users")
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, unique = true)
    private String username;
    
    @Column(nullable = false)
    private String email;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    // 构造方法、getter、setter
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
}
```

### Repository 接口

```java
package com.example.demo.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import java.util.List;
import java.util.Optional;

public interface UserRepository extends JpaRepository<User, Long> {
    
    // 根据用户名查询
    Optional<User> findByUsername(String username);
    
    // 根据邮箱查询
    Optional<User> findByEmail(String email);
    
    // 自定义查询
    @Query("SELECT u FROM User u WHERE u.email LIKE %:domain")
    List<User> findByEmailDomain(@Param("domain") String domain);
    
    // 统计数量
    long countByUsernameContaining(String keyword);
}
```

### Service 层

```java
package com.example.demo.service;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;

@Service
@Transactional
public class UserService {
    
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public List<User> findAll() {
        return userRepository.findAll();
    }
    
    public Optional<User> findById(Long id) {
        return userRepository.findById(id);
    }
    
    public User save(User user) {
        return userRepository.save(user);
    }
    
    public void deleteById(Long id) {
        userRepository.deleteById(id);
    }
    
    public Optional<User> findByUsername(String username) {
        return userRepository.findByUsername(username);
    }
}
```

## 依赖管理

### 常用依赖

**pom.xml 示例：**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <dependencies>
        <!-- Web 开发 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <!-- 数据访问 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <!-- 数据库驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.33</version>
        </dependency>
        
        <!-- 安全认证 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        
        <!-- 测试 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <!-- 验证 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
    </dependencies>
</project>
```

## 配置管理

### 多环境配置

**application-dev.yml（开发环境）：**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb_dev
    username: dev_user
    password: dev_password
  
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: create-drop

logging:
  level:
    com.example.demo: DEBUG
```

**application-prod.yml（生产环境）：**
```yaml
spring:
  datasource:
    url: jdbc:mysql://prod-db:3306/mydb_prod
    username: prod_user
    password: ${DB_PASSWORD}
  
  jpa:
    show-sql: false
    hibernate:
      ddl-auto: validate

logging:
  level:
    com.example.demo: INFO
```

### 自定义配置

```java
// 配置类
@Configuration
public class AppConfig {
    
    @Bean
    @ConfigurationProperties(prefix = "app.custom")
    public CustomProperties customProperties() {
        return new CustomProperties();
    }
    
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

// 配置属性类
@ConfigurationProperties(prefix = "app.custom")
public class CustomProperties {
    private String apiKey;
    private int timeout = 30;
    private List<String> allowedHosts;
    
    // getter、setter
}

// 在配置文件中使用
app:
  custom:
    api-key: "your-api-key"
    timeout: 60
    allowed-hosts:
      - "example.com"
      - "api.example.com"
```

## 测试

### 单元测试

```java
package com.example.demo.service;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    
    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void shouldSaveUser() {
        // 准备测试数据
        User user = new User();
        user.setUsername("testuser");
        user.setEmail("test@example.com");
        
        // 模拟 Repository 行为
        when(userRepository.save(any(User.class))).thenReturn(user);
        
        // 执行测试
        User savedUser = userService.save(user);
        
        // 验证结果
        assertNotNull(savedUser);
        assertEquals("testuser", savedUser.getUsername());
        verify(userRepository, times(1)).save(user);
    }
}
```

### 集成测试

```java
package com.example.demo;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
class DemoApplicationTests {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Test
    void shouldReturnDefaultMessage() throws Exception {
        mockMvc.perform(get("/api/users"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$.success").value(true));
    }
}
```

## 部署和运行

### 打包应用

```bash
# 使用 Maven
mvn clean package

# 使用 Gradle
./gradlew build
```

### 运行应用

```bash
# 直接运行 JAR 文件
java -jar target/my-springboot3-app-0.0.1-SNAPSHOT.jar

# 使用 Maven 插件运行
mvn spring-boot:run

# 使用 Gradle 插件运行
./gradlew bootRun
```

### Docker 部署

**Dockerfile:**
```dockerfile
FROM openjdk:17-jdk-slim
VOLUME /tmp
COPY target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

**docker-compose.yml:**
```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
    depends_on:
      - mysql
    
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
    ports:
      - "3306:3306"
```

## 最佳实践

### 代码组织

1. **分层架构** - Controller、Service、Repository
2. **单一职责** - 每个类和方法只负责一个功能
3. **依赖注入** - 使用构造函数注入
4. **异常处理** - 统一的异常处理机制

### 性能优化

1. **连接池配置** - 使用 HikariCP
2. **缓存策略** - 使用 Redis 或 Caffeine
3. **异步处理** - 使用 @Async
4. **监控指标** - 使用 Spring Boot Actuator

## 下一步学习

1. **深入学习 Spring Security** - 安全认证和授权
2. **掌握微服务架构** - Spring Cloud
3. **学习消息队列** - RabbitMQ、Kafka
4. **实践容器化部署** - Docker、Kubernetes

### 推荐资源

- [Spring Boot 官方文档](https://spring.io/projects/spring-boot)
- [Spring 官方指南](https://spring.io/guides)
- [Baeldung Spring 教程](https://www.baeldung.com/)

**祝您 Spring Boot 3 学习之旅顺利！**