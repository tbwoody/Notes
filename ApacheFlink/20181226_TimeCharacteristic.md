In Apache Flink, there are three types of Time for the time-dependent order and operations that depend on time (such as time windows).
They are defined in TimeCharacteristic.java. Here are the details:

**1. Processing Time**    
	 Processing time for operators means that the operator uses the system clock of the machine
	 to determine the current time of the data stream. Processing-time windows trigger based
	 on wall-clock time and include whatever elements happen to have arrived at the operator at
	 that point in time.

	 <p>Using processing time for window operations results in general in quite non-deterministic
	 results, because the contents of the windows depends on the speed in which elements arrive.
	 It is, however, the cheapest method of forming windows and the method that introduces the
	 least latency.

**2. Ingestrion Time**    

**3. Event Time**    
