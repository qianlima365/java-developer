# Maven插件开发

## 插件基础

### 1. 什么是Maven插件
- Maven的核心是一个插件执行框架
- 所有的任务都是通过插件完成
- 插件是Maven的核心执行单元

### 2. 插件类型

#### Build插件
- 在构建过程中执行
- 在pom.xml的`<build/>`中配置
- 例如：compiler、surefire等

#### Reporting插件
- 在网站生成过程中执行
- 在pom.xml的`<reporting/>`中配置
- 例如：javadoc、checkstyle等

## 插件开发

### 1. 项目创建

#### Maven插件项目结构
```
src/main/java
  └── com/example/plugin
      └── MyMojo.java
src/test/java
  └── com/example/plugin
      └── MyMojoTest.java
pom.xml
```

#### pom.xml配置
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>example-maven-plugin</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>maven-plugin</packaging>
    
    <dependencies>
        <dependency>
            <groupId>org.apache.maven</groupId>
            <artifactId>maven-plugin-api</artifactId>
            <version>3.8.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.maven.plugin-tools</groupId>
            <artifactId>maven-plugin-annotations</artifactId>
            <version>3.6.4</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```

### 2. 开发Mojo
Mojo是Maven中的执行单元，相当于一个插件目标。

#### 基本Mojo示例
```java
package com.example.plugin;

import org.apache.maven.plugin.AbstractMojo;
import org.apache.maven.plugin.MojoExecutionException;
import org.apache.maven.plugins.annotations.Mojo;

@Mojo(name = "hello")
public class MyMojo extends AbstractMojo {
    public void execute() throws MojoExecutionException {
        getLog().info("Hello, Maven Plugin!");
    }
}
```

### 3. 插件参数

#### 参数定义
```java
@Parameter(property = "message", defaultValue = "Hello, Maven!")
private String message;

@Parameter(required = true)
private File outputDirectory;
```

#### 参数使用
```xml
<plugin>
    <groupId>com.example</groupId>
    <artifactId>example-maven-plugin</artifactId>
    <version>1.0-SNAPSHOT</version>
    <configuration>
        <message>Custom message</message>
        <outputDirectory>${project.build.directory}/output</outputDirectory>
    </configuration>
</plugin>
```

## 插件测试

### 1. 单元测试
```java
import org.junit.Test;
import static org.junit.Assert.*;

public class MyMojoTest {
    @Test
    public void testMojo() {
        MyMojo mojo = new MyMojo();
        // 设置参数
        // 执行测试
    }
}
```

### 2. 集成测试
使用maven-invoker-plugin进行集成测试：
```xml
<plugin>
    <artifactId>maven-invoker-plugin</artifactId>
    <version>3.2.2</version>
    <configuration>
        <debug>true</debug>
        <cloneProjectsTo>${project.build.directory}/it</cloneProjectsTo>
        <pomIncludes>
            <pomInclude>*/pom.xml</pomInclude>
        </pomIncludes>
        <postBuildHookScript>verify</postBuildHookScript>
        <localRepositoryPath>${project.build.directory}/local-repo</localRepositoryPath>
        <settingsFile>src/it/settings.xml</settingsFile>
        <goals>
            <goal>clean</goal>
            <goal>verify</goal>
        </goals>
    </configuration>
</plugin>
```

## 插件发布

### 1. 插件部署
```xml
<distributionManagement>
    <repository>
        <id>company-releases</id>
        <url>http://repository.company.com/releases</url>
    </repository>
    <snapshotRepository>
        <id>company-snapshots</id>
        <url>http://repository.company.com/snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

### 2. 插件文档
使用maven-plugin-plugin生成插件文档：
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-plugin-plugin</artifactId>
    <version>3.6.4</version>
</plugin>
```

## 最佳实践

### 1. 命名规范
- 插件artifactId以maven-plugin结尾
- Mojo类名以Mojo结尾
- goal名称使用简单的动词或动词短语

### 2. 开发建议
- 保持插件功能单一
- 提供详细的文档
- 合理使用日志级别
- 处理异常情况
- 提供合理的默认值

### 3. 测试规范
- 编写完整的单元测试
- 包含集成测试
- 测试各种参数组合
- 测试异常情况

## 常见问题

### 1. 插件无法执行
- 检查插件配置
- 验证插件依赖
- 确认插件目标存在

### 2. 参数问题
- 检查参数名称
- 验证参数类型
- 确认必需参数已提供

### 3. 调试方法
- 使用-X参数开启调试输出
- 使用IDE调试
- 添加详细日志