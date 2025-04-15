# Maven依赖管理

## 依赖配置基础

### 1. 依赖声明
在`pom.xml`中使用`<dependencies>`元素声明项目依赖：
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.3.20</version>
    </dependency>
</dependencies>
```

### 2. 依赖范围（Scope）

#### compile（默认）
- 编译、测试、运行时都有效
- 会被打包到最终的构件中

#### provided
- 编译和测试时有效
- 运行时由容器提供
- 典型例子：servlet-api

#### runtime
- 测试和运行时有效
- 编译时无效
- 典型例子：JDBC驱动

#### test
- 仅在测试时有效
- 典型例子：JUnit

#### system
- 与provided类似
- 需要显式指定依赖文件路径
- 不推荐使用

#### import
- 只能用在`<dependencyManagement>`中
- 用于导入依赖管理配置

## 依赖管理进阶

### 1. 依赖传递
- Maven会自动引入直接依赖的间接依赖
- 可以通过依赖树查看完整依赖关系：
```bash
mvn dependency:tree
```

### 2. 依赖排除
使用`<exclusions>`排除不需要的传递依赖：
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.3.20</version>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### 3. 依赖管理
使用`<dependencyManagement>`统一管理依赖版本：
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>5.3.20</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 4. 依赖优化

#### 查找依赖冲突
```bash
mvn dependency:analyze
mvn dependency:tree -Dverbose -Dincludes=groupId:artifactId
```

#### 解决版本冲突
- 路径最近者优先
- 第一声明者优先
- 显式声明版本

## 依赖仓库

### 1. 仓库类型

#### 本地仓库
- 默认位置：`~/.m2/repository`
- 可在`settings.xml`中配置：
```xml
<localRepository>/path/to/local/repo</localRepository>
```

#### 远程仓库
- 中央仓库
- 私服（如Nexus、Artifactory）
- 其他公共仓库

### 2. 仓库配置

#### 配置远程仓库
```xml
<repositories>
    <repository>
        <id>company-repo</id>
        <url>http://repository.company.com/maven2</url>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

#### 配置镜像
```xml
<mirrors>
    <mirror>
        <id>aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Aliyun Maven Central Mirror</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
</mirrors>
```

## 最佳实践

### 1. 版本管理
- 使用属性定义版本号
- 使用依赖管理（BOM）
- 定期更新依赖版本

### 2. 依赖规范
- 显式声明所有直接依赖
- 不要过度依赖传递性依赖
- 及时清理无用依赖
- 使用适当的依赖范围

### 3. 私服使用
- 搭建私服管理内部构件
- 作为远程仓库代理
- 控制依赖使用范围

## 常见问题

### 1. 依赖下载失败
- 检查网络连接
- 验证仓库配置
- 检查依赖坐标是否正确
- 尝试清理本地仓库

### 2. 依赖冲突
- 使用依赖树分析冲突
- 合理使用依赖排除
- 统一管理依赖版本

### 3. 私服访问问题
- 检查认证配置
- 验证网络连接
- 确认仓库权限