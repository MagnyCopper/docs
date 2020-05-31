# 创建Spring Boot Starter

创建自定义的Spring Boot Starter，这里以创建MinIO的Spring Boot Starter为例。

## 命名规范

官方的starter遵循`spring-boot-starter-*`的命名模式，如`spring-boot-starter-data-jpa`。第三方的starter通常以自己的项目名称开始，例如:名为`thirdpartyproject`的第三方入门项目通常被命名为`thirdpartyproject-spring-boot-starter`;

## Bean的加载条件

- `@ConditionalOnProperty`:根据配置文件中的属性或值判断是否加载该bean；
- `@ConditionalOnBean`和`@ConditionalOnMissingBean`:根据bean是否存在于应用上下文(applicationcontext)中判断是否加载该bean；
- `@ConditionalOnClass`和`@ConditionalOnMissingClass`:根据bean是否存在于类路径(classpath)中判断是否加载该bean；
- `@ConditionalOnExpression`:根据传入的表达式结果判断是否加载该bean；
- `@ConditionalOnWebApplication`和`@ConditionalOnNotWebApplication`:根据是为web应用判断是否加载该bean；


## 流程说明

1. 使用IDE创建一个spring boot工程，选择模块时注意勾选`Spring Configuration Processor`模块；
2. 修改`pom.xml`文件，删除无用部分；

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.3.0.RELEASE</version>
            <relativePath/> <!-- lookup parent from repository -->
        </parent>
        <groupId>com.github.magnycopper</groupId>
        <artifactId>minio-spring-boot-starter</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <name>minio-spring-boot-starter</name>
        <description>minio-spring-boot-starter</description>

        <properties>
            <java.version>1.8</java.version>
        </properties>

        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-configuration-processor</artifactId>
                <optional>true</optional>
            </dependency>
            <!-- https://mvnrepository.com/artifact/io.minio/minio -->
            <dependency>
                <groupId>io.minio</groupId>
                <artifactId>minio</artifactId>
                <version>7.0.2</version>
            </dependency>
        </dependencies>
    </project>
    ```
3. 删除spring boot的启动类、默认的配置文以及测试类；
4. 创建配置文件映射类`MinIOConfigurationProperties`，并添加相关注解及需要映射在配置的文件中的属性名称；

    ```java
    package com.github.magnycopper.minio.starter.configuration;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "minio.server")
    public class MinIOConfigurationProperties {

        /**
        * MinIO的接入点
        */
        private String endpoint;
        /**
        * 访问MinIO的AccessKey
        */
        private String accessKey;
        /**
        * 访问MinIO的SecretKey
        */
        private String secretKey;

        public String getEndpoint() {
            return endpoint;
        }

        public void setEndpoint(String endpoint) {
            this.endpoint = endpoint;
        }

        public String getAccessKey() {
            return accessKey;
        }

        public void setAccessKey(String accessKey) {
            this.accessKey = accessKey;
        }

        public String getSecretKey() {
            return secretKey;
        }

        public void setSecretKey(String secretKey) {
            this.secretKey = secretKey;
        }
    }
    ```
5. 创建自动配置类`MinIOAutoConfiguration`,添加相关注解并创建业务bean;

    ```java
    package com.github.magnycopper.minio.starter;

    import com.github.magnycopper.minio.starter.configuration.MinIOConfigurationProperties;
    import io.minio.MinioClient;
    import io.minio.errors.InvalidEndpointException;
    import io.minio.errors.InvalidPortException;
    import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    @EnableConfigurationProperties(MinIOConfigurationProperties.class)
    public class MinIOAutoConfiguration {

        private final MinIOConfigurationProperties minIOConfigurationProperties;

        public MinIOAutoConfiguration(MinIOConfigurationProperties minIOConfigurationProperties) {
            this.minIOConfigurationProperties = minIOConfigurationProperties;
        }

        @Bean
        @ConditionalOnClass(name = "io.minio.MinioClient")
        public MinioClient minioClient() throws InvalidPortException, InvalidEndpointException {
            MinioClient minioClient = new MinioClient(minIOConfigurationProperties.getEndpoint(), minIOConfigurationProperties.getAccessKey(), minIOConfigurationProperties.getSecretKey());
            return minioClient;
        }
    }
    ```
6. 创建自动配置识别文件`resources/META-INF/spring.factories`，并添加相关内容；

    ```properties
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.github.magnycopper.minio.starter.MinIOAutoConfiguration
    ```