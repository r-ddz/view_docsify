## 什么是依赖范围

> maven的依赖范围包括： compile，provide，runtime，test，system。

- compile：默认的依赖范围，编译、测试和运行时都生效。由于运行时需要，所以依赖会被打包。
  
- provided：只有在当 JDK 或者一个容器已提供该依赖之后才使用， provided 依赖在编译和测试时需要，在运行时不需要，比如：servlet api 被 tomcat 容器提供。由于运行时不需要，所以依赖不会被打包。

- runtime：在运行和测试系统时需要，但在编译时不需要。例如：jdbc的驱动包。由于运行时需要，所以依赖会被打包。

- system：范围与 provided 类似，但是你必须显式的提供一个对于本地系统中 JAR 文件的路径，需要指定 systemPath 磁盘路径，system依赖不推荐使用。

- test：在编译和运行时都不需要，只在测试编译和测试运行时需要。例如：Junit。由于运行时不需要，所以依赖不会被打包。

| 依赖范围      | 编译    | 测试   | 运行时 | 打入jar包 |
| ---           | :---: | :---: | :---: | :---: |
| compile       |   √   |   √   |   √   |   √   |
| provided      |   √   |   √   |   ×   |   ×   |
| runtime       |   ×   |   √   |   √   |   √   |
| test          |   ×   |   √   |   ×   |   ×   |
| system        |   √   |   √   |   ×   |   √   |

## 什么是依赖传递

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.2.4.RELEASE</version>
    </dependency>
</dependencies>
```

先添加 springmvc 的核心依赖的坐标，会发现出现除了 spring-webmvc 以外的其他 jar。因为我们的项目依赖 spring-webmv.jar，而 spring-webmv.jar 会依赖 spring-beans.jar 等等，所以 spring-beans.jar 这些 jar 包也出现在了我们的 maven 工程中，这种现象我们称为依赖传递。

## 依赖传递对依赖范围的影响

> 是因为依赖会有依赖范围，依赖范围对传递依赖也有影响，例如有 A、B、C，A依赖B、B依赖C，C可能是A的传递依赖。

例如：A对B有 compile 依赖，B对C有 runtime 依赖，那么根据表格所示A对C有 runtime 依赖。

依赖传递对依赖范围的影响如下图所示：

| 依赖传递表      | B依赖C     | compile   | provided  | runtime   | test |
| ---           | :---:     | :---:     | :---:     | :---:     | :---: |
| A依赖B         | A依赖C     |           |           |           |      |
| compile       |           | compile   | -         | runtime   | -     |
| provided      |           | provided  | provided  | provided  | -     |
| runtime       |           | runtime   | -         | runtime   | -     |
| test          |           | test      | -         | test      | -     |

1. A依赖B，B依赖C；
2. 横轴是B依赖C的范围类型；
3. 纵轴是A依赖B的范围类型；
4. 中间区域体现的是依赖传递，导致A依赖C的范围类型；

## 依赖配置样例

```xml
<dependencies>
    <dependency>                                <!-- 引入一个依赖 -->
        <groupId>junit</groupId>                <!-- 项目名称 -->
        <artifactId>junit</artifactId>          <!-- 模块名称 -->
        <version>4.9</version>                  <!-- 版本 -->
        <scope>test</scope>                     <!-- 依赖范围：这里配置 test 只在测试编译和测试运行时生效 -->
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.2.RELEASE</version>
        <scope>compile</scope>                  <!-- compile 是默认的依赖范围，编译、测试和运行时都生效 -->
    </dependency>
    <dependency>                                <!-- 没有配置 scope 会默认为 compile -->
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>5.0.1.RELEASE</version>
    </dependency>
</dependencies>
```

## 依赖冲突的解决

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
    </dependencies>
```

- 我们会发现这两个 jar 包同时都依赖了 spring-beans
- spring-webmvc 依赖 spirng-beans-4.2.4
- spring-context 依赖 spring-beans-5.0.2
- 但是发现 spirng-beans-4.2.4 加入到工程中
- 而我们希望 spring-beans-5.0.2 加入工程。
  
这就造成了依赖冲突。解决依赖冲突有以下原则：

1. 依赖调解（路径最近者优先；第一声明者优先）
2. 排除依赖
3. 锁定版本

## maven的依赖调解
> Maven 引入的传递性依赖机制，大大简化和方便了依赖声明，但是两条依赖路径上有两个版本的相同依赖就会造成依赖重复，maven 自动按照下边的原则调解：

1.路径最近者优先。<br>
> 例：项目 A 有这样的依赖关系：<br>
> A -> B -> C -> X(1.0)<br>
> A -> D -> X(2.0)<br>
> 分析：该例中 X(1.0) 的路径长度为 3，而 X(2.0) 的路径长度为 2，因此，后者会被解析使用。<br>

```xml
    <!-- maven配置样例 - 依赖调解：路径最近者优先 -->
    <!-- spring-contex 和 spring-webmvc 都会传递过来 spirng-beans -->
    <!-- 这里 spring-beans 本身的依赖路径是最近的，因此 spirng-beans 的版本是自己本身的依赖版本 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.0.1.RELEASE</version>
        </dependency>
    </dependencies>
```

2.第一声明者优先。<br>
> 例：依赖调解的第一原则不能解决所有问题，比如这样的依赖关系：<br>
> A -> B -> Y(1.0)<br>
> A -> C -> Y(2.0)<br>
> 分析：Y(1.0) 和 Y(2.0) 的依赖路径长度是一样的，都为 2，在 Maven 2.0.8 及之前的版本中这是不确定的，但是从 2.0.9 开始，为了尽可能避免构件的不确定性，Maven 定义了依赖调解的第二原则：第一声明者优先。在依赖路径相等的前提下，在 POM 中依赖声明的顺序决定了谁会被解析使用，顺序最靠前的那么依赖优胜。该例中，如果 B 的依赖在 C 之前，那么 Y(1.0) 就会被解析使用。<br>

```xml
    <!-- maven配置样例 - 依赖调解：第一声明者优先原则 -->
    <!-- spring-contex 和 spring-webmvc 都会传递过来 spirng-beans -->
    <!-- 这里 spring-webmvc 优先，因此 spirng-beans 的版本也是 4.2.4.RELEASE -->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
    </dependencies>
```

## maven的排除依赖

上边的问题也可以通过排除依赖方法辅助依赖调解，如下：
<br>
比如在依赖 spring-webmvc 的设置中添加排除依赖，排除 spring-beans，
<br>
下边的配置表示：依赖 spring-webmvc，但排除 spring-webmvc 所依赖的 spring-beans。

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.2.4.RELEASE</version>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-beans</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

## maven的锁定版本

> 面对众多的依赖，有一种方法不用考虑依赖路径、声明优化等因素可以采用直接锁定版本的方法确定依赖构件的版本，版本锁定后则不考虑依赖的声明顺序或依赖的路径，以锁定的版本的为准添加到工程中，此方法在企业开发中常用。

```xml
    <!-- 锁定版本 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>5.0.2.RELEASE</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>5.0.2.RELEASE</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <!-- 引入的依赖可以不需要指定版本了 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </dependency>
    </dependencies>
```

> 还可以把版本号提取出来，使用<properties>标签设置成变量。

```xml
    <!-- 版本号提取出来，设置成变量 -->
    <properties>
        <spring.version>5.0.2.RELEASE</spring.version>
    </properties>
    <!-- 锁定版本 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <!-- 引入的依赖可以不需要指定版本了 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </dependency>
    </dependencies>
```


