# Troubleshooting Note

- [Troubleshooting Note](#troubleshooting-note)
	- [Tools](#tools)
		- [Maven](#maven)
		- [Eclipse](#eclipse)
	- [Tech](#tech)
		- [Java](#java)
		- [Java Web](#java-web)
		- [Spring Boot](#spring-boot)
		- [Hive](#hive)
		- [Spark](#spark)
	- [Software](#software)
		- [Talend](#talend)
		- [Ubuntu](#ubuntu)

---

## Tools

### Maven

**Problem 1**

When cloning the project from GitHub to Intellij IDEA, mvn install failed due to java outOfMemory exception.

**Reason**: mvn packaging runs out of memory. 

**Solution**: 

- Modify "intellij-2019.1.3-win\bin\idea.exe.vmoptions" as follows:

```
-server
-Xms1024m
-Xmx2048m
-XX:MaxPermSize=1024m
-XX:ReservedCodeCacheSize=225m
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-ea
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
```

- Set a Windows env variable
	- name: MAVEN_OPS
	- value: -Xmx1024m -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m
- Set "testFailureIgnore" to true from false inside pom.xml file of the project as follows.

	````
	<!-- enable scalatest -->
	<plugin>
		<groupId>org.scalatest</groupId>
		<artifactId>scalatest-maven-plugin</artifactId>
		<version>${scalaTstMvnPlgin}</version>
		<configuration>
			<reportsDirectory>${project.build.directory}/surefire-reports</reportsDirectory>
			<junitxml>.</junitxml>
			<filereports>ScalaTestReport.txt</filereports>
			<!--htmlreporters>${project.build.directory}/html/scalatest</htmlreporters-->
			<testFailureIgnore>true</testFailureIgnore>
		</configuration>
		<executions>
			<execution>
				<id>test</id>
				<goals>
					<goal>test</goal>
				</goals>
			</execution>
		</executions>
	</plugin>
	````

**Problem 2**

maven项目pom.xml文件第一行报错。

**Solution**: https://www.jianshu.com/p/80f91bd900d9

### Eclipse

**Problem 1**

When creating the first maven project in Eclipse on Ubuntu, cannot download dependencies. 

**Reason**: Have not downloaded maven and configured it in Eclipse.  

**Solution**: Download from http://mirror.ventraip.net.au/apache/maven/maven-3/. Eclipse -> preferences -> maven -> installations -> add; Eclipse -> preferences -> maven -> user settings -> user settings  

Reference: https://jingyan.baidu.com/article/6dad5075209b37a122e36e66.html 

**Problem 2**

On Ubuntu, install hadoop plugin in eclipse：download hadoop-eclipse-plugin-2.7.1.jar, then put it in dropins folder under eclipse directory.  

**Problem 3**

In Eclipse, after running project, got an error message "Could not find or load main class". 

**Reason**: The project name is the same as package name.

**Solution**: Change one.

---

## Tech

### Java

**Problem 1**

Intellij IDEA prompts "Try-with-resources are not supported at language level '5'".

**Solution**: Add the following in pom.xml. (https://blog.csdn.net/qq_37502106/article/details/86771122)

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

### Java Web

**Problem 1**

Java web app project, Eclipse, error in the jsp file, The superclass “javax.servlet.http.HttpServlet” was not found on the Java Build Path.  

**Solution**: Project properties → Java Build Path → Add Library → Select "Server Runtime" from the list → Next → Select "Apache Tomcat" → Finish. https://stackoverflow.com/questions/22756153/the-superclass-javax-servlet-http-httpservlet-was-not-found-on-the-java-build  

### Spring Boot

**Problem 1**

SpringBoot启动类报错: 

```
The type org.springframework.context.ConfigurableApplicationContext cannot be resolved. It is indirectly referenced from required .class files
```

**Solution**: https://blog.csdn.net/anqi114179/article/details/79825145

### Hive

**Problem 1**

When executing HiveQL, error message: ParseException missing KW_END at ')' near '<EOF>'.

**Reason**: SQL "case when then else end" statement misses "end".

### Spark

**Problem 1**

When running "hello world" example once after setting up the project, got such an error.

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 10582
...
```

**Solution**: Add such a dependency in pom.xml.

```
<dependency>
	<groupId>com.thoughtworks.paranamer</groupId>
	<artifactId>paranamer</artifactId>
	<version>2.8</version>
</dependency>
```

**Problem 2**

When running the below code:

```scala
val dfWithEventTimeQuery3 = dfWithEventTime.groupBy(window(col("Event_Time"), "10 minutes", "10 seconds"))
      .count()
      .writeStream
      .queryName("events_per_window_sliding")
      .format("memory")
      .outputMode("complete")
      .start()
```

got error: 

```
ERROR CodeGenerator: failed to compile: org.codehaus.janino.InternalCompilerException: Compiling "GeneratedClass": Code of method "expand_doConsume$(Lorg/apache/spark/sql/catalyst/expressions/GeneratedClass$GeneratedIteratorForCodegenStage1;JZ)V" of class "org.apache.spark.sql.catalyst.expressions.GeneratedClass$GeneratedIteratorForCodegenStage1" grows beyond 64 KB
```

**Reason**: The problem is that when Java programs generated using Catalyst from programs using DataFrame and Dataset are compiled into Java bytecode, the size of byte code of one method must not be 64 KB or more, This conflicts with the limitation of the Java class file, which is an exception that occurs.

---

## Software

### Talend 

**Problem 1**

When I am following along with the Talend Tutorial video on condition based filtering, I receive the following error when I run my job: 

```
Exception in component tMap_1 (conditionBasedFiltering) 
java.lang.NullPointerException 
  at talenddemo.conditionbasedfiltering_0_1.conditionBasedFiltering.tFileInputDelimited_1Process(conditionBasedFiltering.java:1627) 
  at talenddemo.conditionbasedfiltering_0_1.conditionBasedFiltering.runJobInTOS(conditionBasedFiltering.java:2352) 
  at talenddemo.conditionbasedfiltering_0_1.conditionBasedFiltering.main(conditionBasedFiltering.java:2201)  
```

**Solution**: tMap expression filter 加一个判断 && row_name != null. 
 
### Ubuntu

**Problem 1**

In Ubuntu, cannot paste files into a folder.  

**Reason**: without write privilege.  

**Solution**: sudo chown -R 系统用户名 ./name of the folder 

**Problem 2**

In Ubuntu, cannot open terminal after installing python3.6 and change default python3 to python3.6.  

**Solution**: https://blog.csdn.net/Threeyearsago/article/details/80276579 
 
