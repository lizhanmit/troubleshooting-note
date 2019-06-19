# Troubleshooting Note

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

---

### Java Web

**Problem 1**

Java web app project, Eclipse, error in the jsp file, The superclass “javax.servlet.http.HttpServlet” was not found on the Java Build Path.  

**Soultion**: Project properties → Java Build Path → Add Library → Select "Server Runtime" from the list → Next → Select "Apache Tomcat" → Finish. https://stackoverflow.com/questions/22756153/the-superclass-javax-servlet-http-httpservlet-was-not-found-on-the-java-build  

---

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

### Eclipse

**Problem 1**

When creating the first maven project in Eclipse on Ubuntu, cannot download dependencies. 

**Reason**: Have not downloaded maven and configured it in Eclipse.  

**Solution**: Download from http://mirror.ventraip.net.au/apache/maven/maven-3/. Eclipse -> preferences -> maven -> installations -> add; Eclipse -> preferences -> maven -> user settings -> user settings  

Reference: https://jingyan.baidu.com/article/6dad5075209b37a122e36e66.html 

**Problem 2**

On Ubuntu, install hadoop plugin in eclipse：download hadoop-eclipse-plugin-2.7.1.jar, then put it in dropins folder under eclipse directory.  

---

### Ubuntu

**Problem 1**

In Ubuntu, cannot paste files into a folder.  

**Reason**: without write privilege.  

**Solution**: ﻿sudo chown -R 系统用户名 ./name of the folder 

**Problem 2**

In Ubuntu, cannot open terminal after installing python3.6 and change default python3 to python3.6.  

**Solution**: https://blog.csdn.net/Threeyearsago/article/details/80276579 
 
---

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

 
