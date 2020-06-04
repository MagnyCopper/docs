# 发布至Github Packages

## 流程说明

1. 在Github创建一个仓库，本文中以`REPOSITORY`为例;
2. 修改本机的Maven配置文件，添加Github Packages相关配置，根据不同的情况该文件可能存在于以下位置：`${maven.home}/conf/settings.xml`或`${user.home}/.m2/settings.xml`；
    ```xml

    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                            http://maven.apache.org/xsd/settings-1.0.0.xsd">

        <activeProfiles>
            <activeProfile>github</activeProfile>
        </activeProfiles>

        <profiles>
            <profile>
                <id>github</id>
                <repositories>
                    <repository>
                        <id>central</id>
                        <url>https://repo1.maven.org/maven2</url>
                        <releases>
                            <enabled>true</enabled>
                        </releases>
                        <snapshots>
                            <enabled>true</enabled>
                        </snapshots>
                    </repository>
                    <repository>
                        <id>github</id>
                        <name>GitHub OWNER Apache Maven Packages</name>
                        <url>https://maven.pkg.github.com/OWNER/REPOSITORY</url>
                    </repository>
                </repositories>
            </profile>
        </profiles>

        <servers>
            <server>
                <id>github</id>
                <username>USERNAME</username>
                <password>TOKEN</password>
            </server>
        </servers>
    </settings>
    ```

3. 修改工程中的`pom.xml`文件，在其中添加Github Packages的仓库配置，需要修改`url`标签中的内容为自己的仓库地址；

    ```xml
    <distributionManagement>
    <repository>
        <id>github</id>
        <name>GitHub OWNER Apache Maven Packages</name>
        <url>https://maven.pkg.github.com/OWNER/REPOSITORY</url>
    </repository>
    </distributionManagement>
    ```
    修改后的`pom.xml`文件如下：
    
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

        <distributionManagement>
            <repository>
                <id>github</id>
                <name>GitHub OWNER Apache Maven Packages</name>
                <url>https://maven.pkg.github.com/magnycopper/repository</url>
            </repository>
        </distributionManagement>
    </project>
    ```
4. 执行maven发布命令：`./mvnw deploy -Dregistry=https://maven.pkg.github.com/GITHUB_USERNAME -Dtoken=GH_TOKEN`；