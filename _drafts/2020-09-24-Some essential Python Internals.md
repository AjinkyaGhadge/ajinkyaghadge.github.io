# Paper Review: S4: Distributed Stream Computing Platform

## Introduction

S4 is a general purpose, real-time, distributed, decentralized, partially fault-tolerant, scalable, event driven, pluggable platform that allows programmers to easily implement applications for processing continuous unbound streams of data.

In case of static data model, different queries are run on top of already stored data, to obtain results, on the contrary, in case of streaming data model, same queries are run but the data keeps on streaming in, and we obtain the results.

Traditional approaches don't scale to streaming problem.

Examples of streaming applications:
1. Network monitoring
2. Web personalization and session monitoring
3. Online machine learning
4. Online Parameter Optimization
5. Integrated time and space sensitive information, eg. News, weather alerts, sales, traffic


## Advangages

1. Graph oriented model useful to visualize how dataflows through the system
2. Parallelism is handled by the platform
3. Asynchronous processing of stream, data is processed as it arrives
4. Bottlenecks due to storage can be avoided since the computation is done on the fly
1. Actors programming model is highly suitable for stream processing
2. Runs on commodity hardware
3. High frequency data access is mostly to local ram, which is a great optimization

## Topics difficult to understand

1. Actors programming model

## Disadvantages

1. System lacks dynamic load balancing and robust live PE migration
2. Uses zookeper for node configuration and cluster setup, and limitations of zookeeper like lack of rack aware replication, only 3 or 5 zookeeper nodes, etc are inherited
3. The PE model doesn't stop cyclical dataflow, which in several applications may make it difficult to detect cycles in the dataflow.