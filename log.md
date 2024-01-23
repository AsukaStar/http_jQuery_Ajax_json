# 日志

## 日志门面 SLF4J
### 简介：
主要是为了给Java日志访问提供一套标准、规范的API框架。其主要意义是提供接口，具体的实现可以交给其他日志框架。
### SLF4J桥接技术：
我们会遇到某些组件依赖于SLF4J之外的日志API，例如log4j、JUL、JCL，是在SLF4J之前就出现了，不遵循SLF4J的API标准，为了处理这种情况，SLF4J的桥接技术，会对log4j、JUL、JCL API的调用进行重定向行为，就好像是对SLF4J进行操作一样。
SLF4J桥接技术允许你使用log4j的代码，但在执行时将其转换为SLF4J框架的标准。
### 适配器
Log4j适配器（Log4j-to-SLF4J适配器）是一种特殊类型的桥接，它允许你在代码中使用SLF4J的API，但在运行时将日志请求转发给底层的Log4j实现。

### SLF4J的使用
1.在maven里引入相关的依赖
        <!--slf4j日志门面 核心依赖-->
        <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-api -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.32</version>
        </dependency>
2.通过SLF4J的API编写代码
        Logger logger = LoggerFactory.getLogger(SLF4JTest.class);

        logger.info("========= INFO信息 test slf4j =========");

### SLF4J的日志级别
1.error ：日志错误信息
2.warn ：日志警告信息
3.info ：日志关键信息（默认级别）
4.debug ：日志详细信息
5.trace ：日志跟踪信息

### SLF4J打印动态日志信息
    @Test
    public void test02(){

        Logger logger = LoggerFactory.getLogger(SLF4JTest.class);
        String name = "giteasy";
        int age = 18;
        logger.info("my name is {}, I am {} years old.",new Object[]{name,age});
        logger.info("my name is {}, I am {} years old.",name,age);//常用


    }
### SLF4J打印异常信息
 	@Test
    public void test03(){

        Logger logger = LoggerFactory.getLogger(SLF4JTest.class);
        try {
            Class.forName("abc");
        } catch (ClassNotFoundException e) {
            //打印堆栈跟踪信息
            //e.printStackTrace();
            logger.info("错误信息：",e);
        }
    }

### 通过适配器绑定其他日志的实现
SLF4J日志门面，共有三种情况对日志进行绑定
https://www.slf4j.org/manual.html
1.在没有绑定任何日志实现的基础之上，是不能实现任何功能的
注意：slf4j-simple是虽然是slf4j官方提供的，使用的时候，也是需要导入依赖，自动绑定到slf4j门面上，如果不导入，slf4j核心依赖是不提供任何实现的。

2.logback和simple（包括nop）都是slf4j门面时间线之后出现的日志实现框架，遵循SLF4J的API设计，我们只需要导入日志依赖，即可使用。
注意：nop虽然也划分到了实现中，但是他是指不实现日志记录，不记录日志

3.log4j和JUL：都是SLF4J门面时间线之前出现的日志框架，不遵循SLF4J的API设计，通过适配器桥接的技术，完成与日志门面的衔接。

### 集成logback
导入SLF4J的依赖跟logback的依赖即可。







## 框架技术Logback
### Logback简介
Logback 是由 log4j 创始人设计的又一个开源日志组件。
Logback 当前分成三个模块： logback-core,logback-classic和logback-access。
logback-core 是其它两个模块的基础模块。
logback-classic 是 log4j的一个改良版本。此外 logback-classic 完整实现SLF4J API。使你可以很方便地更换成其它日志系统如 log4j或JDK14 Logging。
logback-access 访问模块与 Servlet 容器集成提供通过 Http 来访问日志的功能。

### Logback中的组件
1.Logger：日志记录器：主要用来存放日志对象，也可以定义日志类型和级别。
2.Appender：用于指定日志输出的目的地，目的地可以是控制台、文件、数据库等。
3.Layout：负责把事件转换为字符串，格式化的日志信息的输出。在Logback中Layout对象被封装在encoder中，也就是说我们未来使用的 encoder 其实就是 Layout。

### Logback配置文件
提供可以使用3种配置文件。
1.logback.groovy
2.logback-test.xml
3.logback.xml（使用）

### 日志的输出格式
%-10level                       级别，设置10个这符，左对齐
%d{yyyy-MM-dd HH:mm:ss.SSS}     日期时间
%c                              当前类全限定名
%M                              当前执行日志的方法
%L                              行号
%thread                         %thread	线程名称
%m或者%msg                      输出的日志信息
%n                              %n	换行


### 日志级别
trace->debug(默认)->info->warn->error


### 使用logback.xml配置文件
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>

    <!--
        <property name="" value=""></property>
        配置文件通用属性,通过${name}的形式取值
    -->
    
    <property name="pattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %c %M %L %m%n"></property>


    <!-- 控制台Appender -->
    <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
        <!--
            输出目标的配置，
            System.out：以黑色字体（默认）
            System.err：红色字体
        -->
        <target>
            System.err
        </target>
        <!-- 日志输出格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
    </appender>


    
    <!--
        日志记录器配置，可以配置多个Appender，进行多方向的日志输出
        root => rootLogger
        level:  表示日志级别
    -->
    <root level="ALL">
        <appender-ref ref="consoleAppender"/>
    </root>
 
</configuration>

### 输出日志到文件

<?xml version="1.0" encoding="UTF-8" ?>
<configuration>

   
    <property name="logDir" value="D://logback_log"></property>
    <property name="fileName" value="logback.log"></property>
    <property name="pattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %c %M %L %m%n"></property>


    <!--文件appender，默认是以追加日志的形式进行输出的-->
    <appender name="fileAppender" class="ch.qos.logback.core.FileAppender">
        <!--输出文件位置-->
        <file>${logDir}//${fileName}</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
    </appender>

    
    <root level="ALL">
        <appender-ref ref="fileAppender"/>
    </root>

</configuration>

### 输出日志到HTML
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>

   
    <property name="logDir" value="D://logback_log"></property>
    <property name="htmlFileName" value="logback.html"></property>
		<property name="htmlPattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS}%thread%-5level%c%M%L%m"></property>

    <!--HTML文件appender-->
    <appender name="htmlFileAppender" class="ch.qos.logback.core.FileAppender">
        <!--输出文件位置-->
        <file>${logDir}//${htmlFileName}</file>
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="ch.qos.logback.classic.html.HTMLLayout">
                <pattern>${htmlPattern}</pattern>
            </layout>
        </encoder>
    </appender>

    
    <root level="ALL">
        <!-- <appender-ref ref="fileAppender"/> -->
        <appender-ref ref="htmlFileAppender"/>
    </root>

</configuration>

### 日志文件拆分和归档
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>

   	<property name="logDir" value="D://logback_log"></property>
    <property name="pattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %c %M %L %m%n"></property>
    
  
    <!--可拆分归档的appender-->
    <appender name="rollFileAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
        <file>${logDir}/roll_logback.log</file>
        <!--指定拆分规则-->
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!--按照时间和压缩格式声明文件名，压缩格式gz-->
            <fileNamePattern>${logDir}/roll_logback.%d{yyyy-MM-dd}.log%i.gz</fileNamePattern>
            <!--按照文件大小进行拆分-->
            <maxFileSize>2KB</maxFileSize>
        </rollingPolicy>
    </appender>

    
    <root level="ALL">
        <!-- <appender-ref ref="fileAppender"/> -->
        <!-- <appender-ref ref="htmlFileAppender"/>  -->
      	<appender-ref ref="rollFileAppender"/>
    </root>

</configuration>

### 使用过滤器
使用过滤器，对日志进行更细粒度的控制
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>

   	<property name="logDir" value="D://logback_log"></property>
    <property name="pattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %c %M %L %m%n"></property>
    
   <!--使用过滤器,进行细粒度控制-->
    <appender name="consoleFilterAppender" class="ch.qos.logback.core.ConsoleAppender">
        <target>
            System.err
        </target>
        <!-- 日志输出格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>

        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <!--高于level中设置的级别，则打印日志-->
            <onMatch>ACCEPT</onMatch>
            <!--低于level中设置的级别，则屏蔽-->
            <onMismatch>DENY</onMismatch>
        </filter>

    </appender>
   
    
    <root level="ALL">
      	<appender-ref ref="consoleFilterAppender"/>
    </root>

</configuration>


### 异步日志
    <appender name="asyncAppender" class="ch.qos.logback.classic.AsyncAppender">
        <appender-ref ref="consoleAppender"/>
    </appender>
在rootLogger中引入导步日志
	<root level="ALL">
       <appender-ref ref="asyncAppender"/>
 </root>

 完整配置文件logback.xml
 <?xml version="1.0" encoding="UTF-8" ?>
<configuration>


    <property name="pattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %c %M %L %m%n"></property>


    <!-- 控制台Appender -->
    <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
        <target>
            System.err
        </target>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
    </appender>

    <!--异步日志Appender-->
    <appender name="asyncAppender" class="ch.qos.logback.classic.AsyncAppender">
        <appender-ref ref="consoleAppender"/>
    </appender>
    

    <root level="ALL">
        <appender-ref ref="asyncAppender"/>
    </root>
 
</configuration>


### 自定义logger
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>

   
    <property name="pattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %c %M %L %m%n"></property>


    <!-- 控制台Appender -->
    <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
        <target>
            System.err
        </target>
        <!-- 日志输出格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
    </appender>

 
    <!--
        自定义Logger
        additivity="false" 表示不继承rootLogger
      -->
    <logger name="cn.giteasy" level="warn" additivity="false">
        <appender-ref ref="consoleAppender"></appender-ref>
    </logger>

</configuration>

## spring boot日志实现
SpringBoot 默认就是使用SLF4J作为日志门面，Logback作为日志实现来记录日志。
从SpringBoog的依赖关系可以看到，log4j和jul都桥接到了slf4j，且slf4j的实现是logback，所以springboot的日志默认使用的是slf4j+logback的

### SpringBoot配置文件	
application.properties（yml）是springboot的核心配置文件（用来简化开发）
我们可以通过该配置文件，修改日志相关的配置


logging.level.cn.giteasy=trace
logging.pattern.console=%d{yyyy-MM-dd} %t [%level] - %m%n

#logging.file.path=D://springboot_log//

### 使用单独的日志配置文件
如果是需要配置日志拆分等相对高级的功能，那么application.properties就达不到我们的需求了
需要使用日志实现相应的配置文件

<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <property name="pattern" value="[%-5level] %d{yyyy-MM-dd HH:mm:ss.SSS} %c %M %L %thread %m%n"></property>
    <property name="logDir" value="D://springboot_logback_log"></property>

    <!--控制台日志-->
    <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
        <!--红色字体打印-->
        <target>
            System.err
        </target>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
    </appender>

    <!--可拆分归档的文件日志-->
    <appender name="rollFileAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--日志输出格式-->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${pattern}</pattern>
        </encoder>
        <!--文件位置-->
        <file>${logDir}//roll_logback.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!--按照时间和压缩格式声明文件名，压缩格式gz-->
            <fileNamePattern>${logDir}//roll_logback.%d{yyyy-MM-dd}.log%i.gz</fileNamePattern>
            <!-- 按照文件大小拆分，当日志文件达到这个大小时，将该文件以压缩格式归档 -->
            <maxFileSize>1KB</maxFileSize>
        </rollingPolicy>
    </appender>

    <!--自定义logger-->
    <logger name="cn.giteasy" level="info" additivity="false">
        <appender-ref ref="consoleAppender"/>
        <appender-ref ref="rollFileAppender"/>
    </logger>

</configuration>

