# Maven构建配置

## 资源处理

### 1. 资源目录配置
```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

### 2. 资源过滤
#### 开启过滤
- 在resource配置中设置filtering为true
- 支持变量替换和表达式计算

#### 过滤示例
```properties
# application.properties
app.name=${project.name}
app.version=${project.version}
db.url=${db.url}
```

```xml
<properties>
    <db.url>jdbc:mysql://localhost:3306/mydb</db.url>
</properties>
```

### 3. 资源打包
#### 包含/排除文件
```xml
<resource>
    <directory>src/main/resources</directory>
    <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
    </includes>
    <excludes>
        <exclude>**/*.bak</exclude>
    </excludes>
</resource>
```

## 编译配置

### 1. 编译器配置
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 2. 编译参数配置
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <compilerArgs>
            <arg>-parameters</arg>
            <arg>-Xlint:unchecked</arg>
        </compilerArgs>
    </configuration>
</plugin>
```

### 3. 多源码目录
```xml
<build>
    <sourceDirectory>src/main/java</sourceDirectory>
    <testSourceDirectory>src/test/java</testSourceDirectory>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
        </resource>
    </resources>
</build>
```

## 打包配置

### 1. 打包类型
```xml
<packaging>jar</packaging>  <!-- jar/war/pom/ear -->
```

### 2. JAR包配置
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.2.0</version>
    <configuration>
        <archive>
            <manifest>
                <addClasspath>true</addClasspath>
                <classpathPrefix>lib/</classpathPrefix>
                <mainClass>com.example.MainClass</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

### 3. WAR包配置
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.3.1</version>
    <configuration>
        <webResources>
            <resource>
                <directory>src/main/webapp</directory>
            </resource>
        </webResources>
        <warName>${project.artifactId}</warName>
    </configuration>
</plugin>
```

### 4. 依赖打包
#### 使用maven-assembly-plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.3.0</version>
    <configuration>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
        <archive>
            <manifest>
                <mainClass>com.example.MainClass</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

## 部署配置

### 1. 部署到远程仓库
```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <name>Releases Repository</name>
        <url>http://repository.company.com/releases</url>
    </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <name>Snapshots Repository</name>
        <url>http://repository.company.com/snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

### 2. 部署到Web容器
```xml
<plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.2</version>
    <configuration>
        <url>http://localhost:8080/manager/text</url>
        <server>tomcat-server</server>
        <path>/myapp</path>
    </configuration>
</plugin>
```

## 构建生命周期配置

### 1. 绑定插件到生命周期
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-source-plugin</artifactId>
    <executions>
        <execution>
            <id>attach-sources</id>
            <phase>verify</phase>
            <goals>
                <goal>jar-no-fork</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### 2. 跳过生命周期阶段
```xml
<properties>
    <maven.test.skip>true</maven.test.skip>
    <maven.javadoc.skip>true</maven.javadoc.skip>
</properties>
```

## 最佳实践

### 1. 资源处理
- 合理使用资源过滤
- 注意编码设置
- 避免敏感信息硬编码

### 2. 编译配置
- 统一JDK版本
- 设置适当的编译参数
- 保持编码一致性

### 3. 打包策略
- 选择合适的打包类型
- 处理好依赖关系
- 配置manifest信息

### 4. 部署规范
- 使用正确的仓库地址
- 配置认证信息
- 做好版本管理

## 常见问题

### 1. 资源文件找不到
- 检查资源目录配置
- 验证文件位置
- 确认过滤设置

### 2. 编译错误
- 检查JDK版本
- 验证依赖完整性
- 确认编码设置

### 3. 打包问题
- 检查打包类型
- 验证依赖配置
- 确认插件版本