Base Mybatis Generator Plugin
======================================

[![github stars](https://img.shields.io/github/stars/liaomengge/base-mybatis-generator-plugin.svg)](https://github.com/liaomengge/base-mybatis-generator-plugin/stargazers)
[![github forks](https://img.shields.io/github/forks/liaomengge/base-mybatis-generator-plugin.svg)](https://github.com/liaomengge/base-mybatis-generator-plugin/network)
[![maven center](https://img.shields.io/maven-central/v/com.github.liaomengge/base-mybatis-generator-plugin.svg)](https://search.maven.org/search?q=g:com.github.liaomengge%20AND%20a:base-mybatis-generator-plugin)
[![sonatype nexus snapshots](https://img.shields.io/nexus/s/com.github.liaomengge/base-mybatis-generator-plugin?label=sonatype-nexus-snapshots&server=https%3A%2F%2Foss.sonatype.org%2F)](https://oss.sonatype.org/content/repositories/snapshots/com/github/liaomengge/base-mybatis-generator-plugin/)
[![github license](https://img.shields.io/github/license/liaomengge/base-mybatis-generator-plugin.svg)](https://github.com/liaomengge/base-mybatis-generator-plugin/blob/master/LICENSE)

1. ##### 概述

   mybatis generator plugin主要用于gradle项目生成mybatis文件的插件，不需要额外引入第三方其他配置，支持mysql，pg等，已引入[base-mybatis-plugin](https://github.com/liaomengge/base-mybatis-plugin)中的插件

2. ##### 使用

   1. ###### Add Gradle Plugin

      > ```groovy
      > apply plugin: 'com.github.liaomengge.MybatisGenerator'
      > 
      > buildscript {
      >   repositories {
      >     maven { url "https://plugins.gradle.org/m2/" }
      >     maven { url 'https://maven.aliyun.com/repository/public' }
      >     maven { url 'https://maven.aliyun.com/repository/spring' }
      >     maven { url 'https://maven.aliyun.com/repository/spring-plugin' }
      >     maven { url 'https://oss.sonatype.org/content/repositories/snapshots/' }
      >   }
      >   dependencies {
      >     classpath "com.github.liaomengge:mybatis-generator-plugin:1.1.0"
      >   }
      > }
      > ```

   2. ###### Add Configuration

      > ```groovy
      > configurations {
      >     mybatisGenerator
      > }
      > 
      > mybatisGenerator {
      >     verbose = false
      >     configFile = 'src/main/resources/generatorConfig.xml'
      >     // optional, here is the override dependencies for the plugin
      >     dependencies {
      >         mybatisGenerator 'org.mybatis.generator:mybatis-generator-core:1.4.0'
      >         mybatisGenerator 'mysql:mysql-connector-java:5.1.47'
      >         mybatisGenerator 'org.postgresql:postgresql:42.2.8'
      >         mybatisGenerator  // Here add your mariadb dependencies or else
      >     }
      > }
      > ```

   3. ###### Integrate `generatorConfig.xml`

      > ```xml
      > <?xml version="1.0" encoding="UTF-8"?>
      > <!DOCTYPE generatorConfiguration
      >         PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
      >         "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
      > <generatorConfiguration>
      > 
      >     <!--
      >     context:生成一组对象的环境
      >     id:必选，上下文id，用于在生成错误时提示
      >     defaultModelType:指定生成对象的样式
      >         1，conditional：类似hierarchical；
      >         2，flat：所有内容（主键，blob）等全部生成在一个对象中；
      >         3，hierarchical：主键生成一个XXKey对象(key class)，Blob等单独生成一个对象，其他简单属性在一个对象		         						(record class)
      >     targetRuntime:
      >         1，MyBatis3：默认的值，生成基于MyBatis3.x以上版本的内容，包括XXXBySample；
      >         2，MyBatis3Simple：类似MyBatis3，只是不生成XXXBySample；
      >     introspectedColumnImpl：类全限定名，用于扩展MBG
      >     -->
      >     <context id="Mysql" targetRuntime="MyBatis3" defaultModelType="flat">
      > 
      >         <!-- 自动识别数据库关键字，默认false，如果设置为true，根据SqlReservedWords中定义的关键字列表；
      >         一般保留默认值，遇到数据库关键字（Java关键字），使用columnOverride覆盖
      >         -->
      >         <property name="autoDelimitKeywords" value="true"/>
      >         <!-- 生成的Java文件的编码 -->
      >         <property name="javaFileEncoding" value="UTF-8"/>
      > 
      >         <!-- 格式化java代码 -->
      >         <property name="javaFormatter" value="org.mybatis.generator.api.dom.DefaultJavaFormatter"/>
      >         <!-- 格式化XML代码 -->
      >         <property name="xmlFormatter" value="org.mybatis.generator.api.dom.DefaultXmlFormatter"/>
      > 
      >         <!-- beginningDelimiter和endingDelimiter：指明数据库的用于标记数据库对象名的符号，比如ORACLE就是双引					号，MYSQL默认是`反引号；-->
      >         <property name="beginningDelimiter" value="`"/>
      >         <property name="endingDelimiter" value="`"/>
      > 
      >         <!-- 插件 -->
      >         <plugin type="com.github.liaomengge.mybatis.plugins.LombokPlugin"/>
      >         <plugin type="com.github.liaomengge.mybatis.plugins.ServicePlugin">
      >             <property name="targetPackage" value="com.github.liaomengge.template.service"/>
      >             <property name="targetProject" value="src/test/java/"/>
      >         </plugin>
      >         <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
      >             <property name="mappers"    										value="com.github.liaomengge.service.base_framework.mysql.mapper.BaseMapper"/>
      >         </plugin>
      > 				
      > 				<!-- 去掉注释 -->
      >         <commentGenerator>
      >             <property name="suppressAllComments" value="true"/>
      >         </commentGenerator>
      > 
      >         <!-- 必须要有的，使用这个配置链接数据库 -->
      >         <jdbcConnection driverClass="com.mysql.jdbc.Driver"
      >                         connectionURL="jdbc:mysql://127.0.0.1:3306/demo?useUnicode=true&amp;zeroDateTimeBehavior=convertToNull&amp;tinyInt1isBit=false"
      >                         userId="test"
      >                         password="test">
      >         </jdbcConnection>
      > 
      >         <!-- 类型解析器 -->
      >         <javaTypeResolver type="com.github.liaomengge.mybatis.types.JavaTypeResolverMysqlImpl"/>
      > 
      >         <!-- java模型创建器，是必须要的元素
      >         targetPackage：生成的类要放的包，真实的包受enableSubPackages属性控制；
      >         targetProject：目标项目，指定一个存在的目录下，生成的内容会放到指定目录中，如果目录不存在，MBG不会自动建目录
      >         -->
      >         <javaModelGenerator targetPackage="com.github.liaomengge.template.entity"
      >                             targetProject="src/test/java/">
      >             <!-- 设置是否在getter方法中，对String类型字段调用trim()方法 -->
      >             <property name="trimStrings" value="true"/>
      >         </javaModelGenerator>
      > 
      >         <!-- 生成SQL map的XML文件生成器 -->
      >         <sqlMapGenerator targetPackage="mybatis" targetProject="src/test/resources/"/>
      > 
      >         <!-- 对于mybatis来说，即生成Mapper接口，注意，如果没有配置该元素，那么默认不会生成Mapper接口
      >         targetPackage/targetProject:同javaModelGenerator
      >         type：选择怎么生成mapper接口（在MyBatis3/MyBatis3Simple下）：
      >             1，ANNOTATEDMAPPER：会生成使用Mapper接口+Annotation的方式创建（SQL生成在annotation中），不会生成 						对应的XML；
      >             2，MIXEDMAPPER：使用混合配置，会生成Mapper接口，并适当添加合适的Annotation，但是XML会生成在XML中；
      >             3，XMLMAPPER：会生成Mapper接口，接口完全依赖XML；
      >         注意，如果context是MyBatis3Simple：只支持ANNOTATEDMAPPER和XMLMAPPER
      >         -->
      >         <javaClientGenerator targetPackage="com.github.liaomengge.template.mapper"
      >                              targetProject="src/test/java/" type="XMLMAPPER"/>
      > 
      >         <table tableName="test_demo" domainObjectName="TestDemoEntity"
      > 							 sqlProviderName="true"
      >                enableInsert = "false"
      >                enableSelectByExample="false"
      >                enableUpdateByPrimaryKey="false"
      >                enableDeleteByPrimaryKey="false"
      >                enableDeleteByExample="false"
      >                enableCountByExample="false"
      >                enableUpdateByExample="false">
      >             <generatedKey column="id" sqlStatement="MYSQL" identity="true"/>
      >             <columnOverride column="created_time" property="createdTime"
      >                             typeHandler="org.apache.ibatis.type.LocalDateTimeTypeHandler"
      >                             jdbcType="OTHER" javaType="java.time.LocalDateTime"/>
      >             <columnOverride column="updated_time" property="updatedTime"
      >                             typeHandler="org.apache.ibatis.type.LocalDateTimeTypeHandler"
      >                             jdbcType="OTHER" javaType="java.time.LocalDateTime"/>
      >         </table>
      > 
      >     </context>
      > 
      > </generatorConfiguration>
      > ```

   4. ###### Generator File

      > `gradle mbg`生产mybatis文件

   ##### 附

   - 参考文档：https://github.com/kimichen13/mybatis-generator-plugin