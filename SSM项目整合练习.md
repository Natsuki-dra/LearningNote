# SSM项目整合练习

------------项目名称SSMTemplate

## 1 配置maven项目

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.natsuki</groupId>
    <artifactId>SSMTemplate</artifactId>
    <version>1.0-SNAPSHOT</version>


    <packaging>jar</packaging>
    <dependencies>
<!--        依赖:
                junit,jdbc,连接池，servlet,jsp,mybatis,mybatis-spring,spring-->
<!--        Junit-->
        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>
<!--        JDBC-->
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
<!--        连接池：c3p0-->
        <!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>
<!--        servlet-->
        <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/javax.servlet.jsp/jsp-api -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
            <scope>provided</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
<!--Mybatis-->
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.5</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.5</version>
        </dependency>
<!--        Spring-->
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>




    </dependencies>
    <!--        静态资源导出问题-->
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
</project>
```

## 2 配置mybatis配置文件

**mybatis-config.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

        <typeAliases>
        <package name="com.natsuki.pojo"/>
    </typeAliases>

    <mappers>
        <mapper resource="com/natsuki/mapper/IBookMapper.xml"/>
    </mappers>
</configuration>
```

mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.natsuki.mapper.IBookMapper">


    <select id="queryAllBooks" resultType="Books">
        select * from books
    </select>

    <select id="queryBookById" parameterType="int" resultType="Books">
        select * from books where bookID = #{bookID}
    </select>

    <insert id="addBook" parameterType="Books">
        insert into `books` (bookName,bookCounts,detail)
        values(#{bookName},#{bookCounts},#{detail})
    </insert>

    <update id="updateBook" parameterType="Books">
        update books
        set bookName = #{bookName} ,
        bookCounts = #{bookCounts} ,
        detail = #{detail}
        where bookID = #{bookID}
    </update>

    <delete id="deleteBook" parameterType="int">
        delete from `books` where bookID = #{bookID}
    </delete>


</mapper>
```



## 3 配置Spring配置文件

**applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--mapper层-->
    <import resource="spring-mapper.xml"/>
<!--service层-->
    <import resource="spring-service.xml"/>
<!--    view层-->
    <import resource="spring-mvc.xml"/>
    

</beans>
```

spring-mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">


<!--    1.关联数据库配置文件-->
    <context:property-placeholder location="classpath:database.properties"/>

<!--    2.连接池
        dbcp:半自动化操作，不能自动连接
        c3p0：自动化操作（自动加载配置文件，并且可以自动设置到对象中
        -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

<!--        c3p0私有属性-->
<!--        连接池线程数-->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="5"/>
<!--        关闭连接后不自动commit-->
        <property name="autoCommitOnClose" value="false"/>
<!--        获取连接超时时间-->
        <property name="checkoutTimeout" value="10000"/>
<!--        获取连接失败重试次数-->
        <property name="acquireRetryAttempts" value="2"/>
    </bean>

<!--    3.sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

<!--    配置dao接口扫描包,动态实现了dao接口可以注入到Spring容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<!--    注入sqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
<!--    要扫描的dao包-->
        <property name="basePackage" value="com.natsuki.mapper"/>
    </bean>

</beans>
```

spring-service.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

<!--    1.扫描service包-->
    <context:component-scan base-package="com.natsuki.service"/>

<!--    2.将所有业务类注入spring，通过配置或注解-->
    <bean id="bookServiceImpl" class="com.natsuki.service.BookServiceImpl">
        <property name="bookMapper" ref="IBookMapper"/>
    </bean>

<!--    3.声明式事务配置-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<!--        注入数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

<!--    4.aop事务支持-->

</beans>
```

spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">


<!--    1.注解驱动-->
    <mvc:annotation-driven/>
<!--    2.静态资源过滤-->
    <mvc:default-servlet-handler/>
<!--    3.扫描包-->
    <context:component-scan base-package="com.natsuki.controller"/>
<!--    4.视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

database.properties

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
#
jdbc.url=jdbc:mysql://localhost:9118/ssmbuild?useSSL=true&useUniCode=true&charactorEncoding=utf8&serverTimezone=Asia/Shanghai
jdbc.username=root
jdbc.password=Wyl196672
```



