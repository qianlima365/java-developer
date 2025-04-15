# Maven高级主题

## 多模块项目

### 1. 项目结构
```
parent-project/
  ├── pom.xml
  ├── common/
  │   ├── pom.xml
  │   └── src/
  ├── service/
  │   ├── pom.xml
  │   └── src/
  └── web/
      ├── pom.xml
      └── src/
```

### 2. 父POM配置
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    
    <modules>
        <module>common</module>
        <module>service</module>
        <module>web</module>
    </modules>
    
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
        <spring.version>5.3.20</spring.version>
    </properties>
    
    <dependencyManagement>
        <dependencies>
            <!-- 统一管理依赖版本 -->
        </dependencies>
    </dependencyManagement>
</project>
```

### 3. 子模块配置
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    
    <artifactId>common</artifactId>
    
    <dependencies>
        <!-- 模块特定依赖 -->
    </dependencies>
</project>
```

### 4. 模块间依赖
```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>common</artifactId>
    <version>${project.version}</version>
</dependency>
```

## 私服搭建

### 1. Nexus安装配置

#### 下载安装
```bash
# 下载Nexus
wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz

# 解压
tar -zxvf latest-unix.tar.gz

# 启动
./nexus run
```

#### 基本配置
- 访问地址：http://localhost:8081
- 默认用户：admin
- 配置文件：etc/nexus-default.properties

### 2. 仓库管理

#### 仓库类型
- hosted：本地仓库
- proxy：代理仓库
- group：仓库组

#### 创建本地仓库
1. 登录Nexus管理界面
2. 选择Create Repository
3. 选择maven2(hosted)
4. 配置仓库信息

### 3. 客户端配置

#### settings.xml配置
```xml
<servers>
    <server>
        <id>nexus-releases</id>
        <username>admin</username>
        <password>admin123</password>
    </server>
</servers>

<mirrors>
    <mirror>
        <id>nexus</id>
        <mirrorOf>*</mirrorOf>
        <url>http://localhost:8081/repository/maven-public/</url>
    </mirror>
</mirrors>
```

#### 项目配置
```xml
<distributionManagement>
    <repository>
        <id>nexus-releases</id>
        <url>http://localhost:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
        <id>nexus-snapshots</id>
        <url>http://localhost:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

## 持续集成

### 1. Jenkins集成

#### 安装Jenkins
```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
```

#### 配置Maven
1. 安装Maven插件
2. 配置Maven工具
3. 配置全局工具配置

### 2. 自动化构建

#### 创建Jenkins任务
1. 新建Maven项目
2. 配置Git仓库
3. 配置构建触发器
4. 配置构建步骤

#### Jenkinsfile配置
```groovy
pipeline {
    agent any
    
    tools {
        maven 'Maven 3.8.5'
        jdk 'JDK 8'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/example/project.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'mvn deploy'
            }
        }
    }
}
```

### 3. 发布管理

#### 配置发布策略
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-release-plugin</artifactId>
    <version>3.0.0-M4</version>
    <configuration>
        <tagNameFormat>v@{project.version}</tagNameFormat>
        <autoVersionSubmodules>true</autoVersionSubmodules>
    </configuration>
</plugin>
```

#### 执行发布
```bash
mvn release:prepare
mvn release:perform
```

## 最佳实践

### 1. 多模块项目
- 合理划分模块
- 统一版本管理
- 避免循环依赖

### 2. 私服管理
- 定期清理
- 备份重要数据
- 控制访问权限

### 3. 持续集成
- 自动化测试
- 代码质量检查
- 自动化部署

## 常见问题

### 1. 模块依赖问题
- 检查版本一致性
- 验证依赖关系
- 解决循环依赖

### 2. 私服访问问题
- 检查网络连接
- 验证认证信息
- 确认权限设置

### 3. 构建失败
- 检查配置正确性
- 查看详细日志
- 验证环境变量