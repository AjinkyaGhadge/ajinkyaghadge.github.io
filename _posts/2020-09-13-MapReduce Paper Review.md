# Paper Review: MapReduce

It is best to read the GFS review before going through the MapReduce review. 

## Motivation

Assume you are a developer at google music. And you are given a task to find out the top trending songs on google music. Consider several songs are spread over GFS. Fundamentally, the problem reduces to finding out how many times each of the song was played. A naive way to do this, would be read all the files using the GFS client, and process everything on the client. The issues of naive approach:
1. Needs high network bandwidth.
2. Single client application will find it difficult to process so much data.

MapReduce approach: Programming model to do the processing, i.e counting of all the songs to the chunk servers. 

## Map + Reduce

*Map is the processing part of data. In this case, counting the number of times each of the song is played on the chunk servers.*

*Reduce is the process of summation of all data from individual servers.*

Process: 

1. Client tells the master to run a mapreduce task.
2. Master starts worker process on each chunk server.
3. Each chunk server, creates a new local file with the output and only shares the path of this file with the master. 
4. Results are kept in the files locally on each of these files.
5. Master starts the reducer process on some of the nodes and reduces aggregates the results.
6. Finally the result by sharing the path to the file containing the results.


## MapReduce fault tolerance

1. Worker sends heartbeats. On absence of hearbeat, master assigns same task to different server. GFS can create the replica.

## Performance gains
![Data Transfer rate over time](/assets/images/perf.png)

