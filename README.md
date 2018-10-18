# mybatis-generator-core-update
修改mybatis-generator-core源码生成中文注释

## 说明

1、基于mybatis-generator-core-1.3.5，重写注释生成的接口CommentGenerator

2、打包成一个新的jar包，同时把mybatis-generator-core打到新的jar包中

3、在需要的项目pom.xml中添加反向生成插件
```
<build>
    <plugins>
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
            <version>1.3.5</version>
            <dependencies>
                <dependency>
                    <groupId> mysql</groupId>
                    <artifactId> mysql-connector-java</artifactId>
                    <version>5.1.45</version>
                </dependency>
                <!--使用自定义的mybatis-generator-core实现数据库里的中文注释-->
                <dependency>
                    <groupId>org.mybatis.generator</groupId>
                    <artifactId>mybatis-generator-core-update</artifactId>
                    <version>1.3.5-SNAPSHOT</version>
                </dependency>
            </dependencies>
            <executions>
                <execution>
                    <id>Generate MyBatis Artifacts</id>
                    <phase>package</phase>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <!--允许移动生成的文件 -->
                <verbose>true</verbose>
                <!-- 是否覆盖 -->
                <overwrite>true</overwrite>
                <!-- 自动生成的配置 -->
                <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
            </configuration>
        </plugin>
    </plugins>
</build>
```

4、添加生成的配置文件generatorConfig.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>

    <!--
    context:生成一组对象的环境
    id:必选，上下文id，用于在生成错误时提示
    defaultModelType:指定生成对象的样式
        1，conditional：特定情况下类似hierarchical，默认；
        2，flat：所有内容（主键，blob）等全部生成在一个对象中；
        3，hierarchical：主键生成一个XXKey对象(key class)，Blob等单独生成一个对象，其他简单属性在一个对象中(record class)
    targetRuntime:
        1，MyBatis3：默认的值，生成基于MyBatis3.x以上版本的内容，包括XXXBySample；
        2，MyBatis3Simple：类似MyBatis3，只是不生成XXXBySample；
    -->
    <context id="Mysql" defaultModelType="flat" targetRuntime="MyBatis3">

        <!-- 自定义，使用数据库的注释 -->
        <property name="javaFileEncoding" value="UTF-8"/>
        <commentGenerator type="org.mybatis.generator.MyCommentGenerator">
            <!--处理中文乱码问题-->
            <property name="javaFileEncoding" value="UTF-8"/>
            <!--阻止生成注释-->
            <property name="suppressAllComments" value="false"/>
            <!--阻止生成的注释包含时间戳-->
            <property name="suppressDate" value="false"/>
            <!-- 针对mysql数据库 -->
            <property name="useInformationSchema" value="true"/>
        </commentGenerator>
        <!--数据库链接地址账号密码-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://127.0.0.1:3306/meal" userId="root" password="root">
        </jdbcConnection>
        <!--是否强制DECIMAL和NUMERIC类型的字段转换为Java类型的java.math.BigDecimal,默认值为false-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!--生成Model类存放位置-->
        <javaModelGenerator targetPackage="com.suanya.model" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
            <!-- 设置是否在getter方法中，对String类型字段调用trim()方法 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!--生成映射文件存放位置-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        <!--生成Dao类存放位置-->
        <!-- 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
                type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
                type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
                type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
        -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.suanya.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        <!--生成对应表及类名-->

        <!--生成全部表-->
        <table tableName="%" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"
               enableDeleteByPrimaryKey="false">
            <generatedKey column="id" sqlStatement="MySql" identity="true"/>
        </table>

    </context>

</generatorConfiguration>
```