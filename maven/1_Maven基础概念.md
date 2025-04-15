# Maven基础概念

## 什么是Maven
Maven是一个项目管理和构建自动化工具。它可以帮助开发者管理项目的构建、报告和文档等任务。

### Maven的主要功能
- 项目构建
- 依赖管理
- 项目信息管理
- 项目报告
- 项目发布

## Maven的核心概念

### 1. POM (Project Object Model)
POM是Maven项目的核心配置文件，采用XML格式，名称为`pom.xml`。它包含了项目的基本信息、构建配置、依赖关系等。

基本结构示例：
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <dependencies>
        <!-- 项目依赖配置 -->
    </dependencies>
</project>
```

### 2. 坐标系统
Maven使用三个主要元素来唯一标识一个项目或依赖：
- groupId：组织或项目的唯一标识
- artifactId：项目的唯一标识
- version：项目的版本号

### 3. 依赖管理
- 依赖配置
- 依赖范围（scope）
  - compile：默认范围，编译和运行时都需要
  - provided：编译时需要，运行时由容器提供
  - runtime：运行时需要
  - test：测试时需要
  - system：系统依赖范围
  - import：导入依赖范围

### 4. 仓库
Maven仓库用于存储项目的依赖和插件：

- 本地仓库：默认位于用户目录下的.m2/repository
- 远程仓库
  - 中央仓库：Maven默认的远程仓库
  - 私服：组织内部的Maven仓库
  - 其他公共仓库

### 5. 生命周期
Maven构建生命周期的主要阶段：

1. clean：清理项目
   - pre-clean
   - clean
   - post-clean

2. default：构建项目
   - validate
   - compile
   - test
   - package
   - verify
   - install
   - deploy

3. site：生成项目站点
   - pre-site
   - site
   - post-site
   - site-deploy

## 配置文件

### settings.xml
主要配置内容：
- 本地仓库路径
- 远程仓库服务器
- 认证信息
- 代理设置
- 镜像设置

### 配置文件位置
- 全局配置：${Maven_HOME}/conf/settings.xml
- 用户配置：${user.home}/.m2/settings.xml

## 最佳实践

### 1. 版本号命名规范
- SNAPSHOT：开发版本
- RELEASE：发布版本
- 版本号格式：主版本号.次版本号.修订号

### 2. 依赖管理建议
- 使用依赖的最新稳定版本
- 及时清理无用依赖
- 注意依赖冲突处理
- 合理使用依赖范围

### 3. 项目结构规范
```
src/main/java：源代码
src/main/resources：资源文件
src/test/java：测试代码
src/test/resources：测试资源
target：编译输出目录
pom.xml：项目配置文件
```

## 常见问题解决

1. 依赖下载失败
   - 检查网络连接
   - 验证仓库配置
   - 尝试使用镜像仓库

2. 依赖冲突
   - 使用`mvn dependency:tree`查看依赖树
   - 通过`<exclusions>`排除冲突依赖
   - 显式声明版本号

3. 编译错误
   - 检查Java版本配置
   - 验证依赖完整性
   - 清理项目（mvn clean）后重新编译