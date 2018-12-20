As we already know, Apache Flink provides many examples. The source code are under the folder "flink-examples". 
In this article, we will build the Flink project and run one of the examples. 
This article is original from [Local Setup Tutorial](https://ci.apache.org/projects/flink/flink-docs-master/tutorials/local_setup.html)

**1. Build Apache Flink Project**    
I assume that you alreay download the flink project. And before building the project, please make sure you have the right Java version.
Currently Apache Flink works only with Java 8.x.

```
$ cd flink
$ mvn clean package -DskipTests # this will take up to 10 minutes
$ cd build-target               # this is where Flink is installed to
```

**2. Start a Local Flink Cluster**    

**3. Read the Code**

**4. Run the Example**    

