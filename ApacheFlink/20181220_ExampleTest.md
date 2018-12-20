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

![Web Frontend](/images/jobmanager-1.png) 
    
    
You can also verify that the system is running by checking the log files in the logs directory:    
```
$ tail log/flink-*-standalonesession-*.log
INFO ... - Rest endpoint listening at localhost:8081
INFO ... - http://localhost:8081 was granted leadership ...
INFO ... - Web frontend listening at http://localhost:8081.
INFO ... - Starting RPC endpoint for StandaloneResourceManager at akka://flink/user/resourcemanager .
INFO ... - Starting RPC endpoint for StandaloneDispatcher at akka://flink/user/dispatcher .
INFO ... - ResourceManager akka.tcp://flink@localhost:6123/user/resourcemanager was granted leadership ...
INFO ... - Starting the SlotManager.
INFO ... - Dispatcher akka.tcp://flink@localhost:6123/user/dispatcher was granted leadership ...
INFO ... - Recovering all persisted jobs.
INFO ... - Registering TaskManager ... under ... at the SlotManager.
```

**3. Read the Code**

This is the complete source code for this SocketWindowWordCount example (For Java). 

```
/*
 * Licensed to the Apache Software Foundation (ASF) under one
 * or more contributor license agreements.  See the NOTICE file
 * distributed with this work for additional information
 * regarding copyright ownership.  The ASF licenses this file
 * to you under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance
 * with the License.  You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.apache.flink.streaming.examples.socket;

import org.apache.flink.api.common.functions.FlatMapFunction;
import org.apache.flink.api.common.functions.ReduceFunction;
import org.apache.flink.api.java.utils.ParameterTool;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.windowing.time.Time;
import org.apache.flink.util.Collector;

/**
 * Implements a streaming windowed version of the "WordCount" program.
 *
 * <p>This program connects to a server socket and reads strings from the socket.
 * The easiest way to try this out is to open a text server (at port 12345)
 * using the <i>netcat</i> tool via
 * <pre>
 * nc -l 12345
 * </pre>
 * and run this example with the hostname and the port as arguments.
 */
@SuppressWarnings("serial")
public class SocketWindowWordCount {

	public static void main(String[] args) throws Exception {

		// the host and the port to connect to
		final String hostname;
		final int port;
		try {
			final ParameterTool params = ParameterTool.fromArgs(args);
			hostname = params.has("hostname") ? params.get("hostname") : "localhost";
			port = params.getInt("port");
		} catch (Exception e) {
			System.err.println("No port specified. Please run 'SocketWindowWordCount " +
				"--hostname <hostname> --port <port>', where hostname (localhost by default) " +
				"and port is the address of the text server");
			System.err.println("To start a simple text server, run 'netcat -l <port>' and " +
				"type the input text into the command line");
			return;
		}

		// get the execution environment
		final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

		// get input data by connecting to the socket
		DataStream<String> text = env.socketTextStream(hostname, port, "\n");

		// parse the data, group it, window it, and aggregate the counts
		DataStream<WordWithCount> windowCounts = text

				.flatMap(new FlatMapFunction<String, WordWithCount>() {
					@Override
					public void flatMap(String value, Collector<WordWithCount> out) {
						for (String word : value.split("\\s")) {
							out.collect(new WordWithCount(word, 1L));
						}
					}
				})

				.keyBy("word")
				.timeWindow(Time.seconds(5))

				.reduce(new ReduceFunction<WordWithCount>() {
					@Override
					public WordWithCount reduce(WordWithCount a, WordWithCount b) {
						return new WordWithCount(a.word, a.count + b.count);
					}
				});

		// print the results with a single thread, rather than in parallel
		windowCounts.print().setParallelism(1);

		env.execute("Socket Window WordCount");
	}

	// ------------------------------------------------------------------------

	/**
	 * Data type for words with count.
	 */
	public static class WordWithCount {

		public String word;
		public long count;

		public WordWithCount() {}

		public WordWithCount(String word, long count) {
			this.word = word;
			this.count = count;
		}

		@Override
		public String toString() {
			return word + " : " + count;
		}
	}
}

```


**4. Run the Example**    
Now, we are going to run this Flink application. It will read text from a socket and once every 5 seconds print the number of occurrences of each distinct word during the previous 5 seconds, i.e. a tumbling window of processing time, as long as words are floating in.

- First of all, we use netcat to start local server via:    
```
$ nc -l 9000
```

- Then we submit the Flink program via:    
```
$ ./bin/flink run examples/streaming/SocketWindowWordCount.jar --port 9000
Starting execution of program
```

The program connects to the socket and waits for input. You can check the web interface to verify that the job is running as expected:    

![Web Frontend](/images/jobmanager-2.png)  ![Web Frontend](/images/jobmanager-3.png) 


- Words are counted in time windows of 5 seconds (processing time, tumbling windows) and are printed to stdout. Monitor the TaskManager’s output file and write some text in nc (input is sent to Flink line by line after hitting ):    
```
$ nc -l 9000
it is working perfectly
```
The .out file will print the counts at the end of each time window as long as words are floating in, e.g.:    
```
$ tail -f log/flink-*-taskexecutor-*.out
it : 1
perfectly : 1
working : 1
is : 1
```

**5. Stop the Local Flink Cluster**   
To stop a local Flink cluster, we run the following bash file on console:
```
$ ./bin/stop-cluster.sh  # Stop Flink
```



