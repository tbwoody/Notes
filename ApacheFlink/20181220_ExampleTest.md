As we already know, Apache Flink provides many examples. The source code are under the folder "flink-examples". 
In this article, we will build the Flink project and run one of the examples. 
This article is original from [Local Setup Tutorial.](https://ci.apache.org/projects/flink/flink-docs-master/tutorials/local_setup.html)

**1. Build Apache Flink Project**    
I assume that you alreay download the flink project. And before compiling the project, please make sure you have the right Java version. Currently Apache Flink works only with Java 8.x.

If you are using IntelliJ IDEA, you can build this project simply by clicking the button "Build -> Build Project".  
You can also build Flink by following CLI commands:    

```
$ cd flink
$ mvn clean package -DskipTests # this will take up to 10 minutes
$ cd build-target               # this is where Flink is installed to
```
  
Once the project is compiled successfully, the Flink is installed to "build-target" folder.
In this article, we will focus on two sub folders: build-target->bin and build-target->examples.

**2. Start a Local Flink Cluster**    
To start a local Flink cluster, we run the following bash file on console:

```
$ ./bin/start-cluster.sh  # Start Flink
```

In the console, if you see the following log, it means the cluster has started successfully.

```
Starting cluster.
Starting standalonesession daemon on host MY-COMPUTER-NAME.
```

Test from Internet Browser:    
Once a local Flink cluster is started successfully, you can test "Dispatcher’s web frontend" from any Internet browser.
You just need hit the default URL: http://localhost:8081. 
If everything is up and running, The web frontend should report a single available TaskManager instance.
![Dispatcher’s web frontend](image/jobmanager-1.png)

**3. Read the Code**



**4. Run the Example**    

**5. Stop the Local Flink Cluster**   
To stop a local Flink cluster, we run the following bash file on console:
```
$ ./bin/stop-cluster.sh  # Stop Flink
```



