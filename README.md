# SMM整合

本项目于新手练习整合SSM框架(Spring、Spring Mvc、Mybatis)



![image-20211203152323584](C:\Users\16348\AppData\Roaming\Typora\typora-user-images\image-20211203152323584.png)

# 重点提醒

## pom.xml

导入相应的jar包后，添加相应的处理静态资源build

```xml
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
```



## spring-dao.xml

```xml
<!-- 4.配置扫描Dao接口包，动态实现Dao接口注入到spring容器中 -->
    <!--解释 ：https://www.cnblogs.com/jpfss/p/7799806.html-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.biao.dao"/>
    </bean>
```

通过MapperScannerConfigurer后，spring会生成对应的mapper，通过对应的mapper可以快速的进行数据库操作

```xml
<bean id="BookServiceImpl" class="com.biao.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
</bean>
```

ref="bookMapper" 是Spring生成对应com.biao.dao下bookMapper接口的mapper，可以通过该mapper进行操作

**注意：dao包下 *.xml文件可以另外存放一起，如 resources目录下的目录中存放，修改好对应路径，不影响程序运行**



## applicationContext.xml

整合所有的spring配置

```xml
<import resource="spring-dao.xml"/>
<import resource="spring-service.xml"/>
<import resource="spring-mvc.xml"/>
```



## web.xml

```xml
<servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```



通过<param-value>classpath:applicationContext.xml</param-value>将对应的mvc、mybatis-spring、spring配置导入进来交给DispatcherServlet管理



