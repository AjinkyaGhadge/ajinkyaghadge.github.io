# Paper Review: Dynamo Amazon's highly avaliable key-value store

## Summary:
Amazon’s Dynamo is a distributed key-value store that focuses on providing high availability at the cost of some consistency. It uses consistent hashing, gossip policies, Merkle trees, and sloppy quorums to accomplish the aforementioned. This paper shows how principles such as consistent hashing, quorums, gossip policies, vector clocks can be incorporated into an effective tool that is truly distributed and scalable. The paper, certainly improved my understanding of distributed systems. It is quite insightful as to how an elegant tool is built by elegantly applying several existing algorithms.

## Advantages:
1. The use of consistent hashing to achieve better partitioning as well, further having virtual nodes which have more databases within them, is an excellent way to balance the entire system. 
2. Use of Vector clocks with conflict resolution on reads allows for high availability of writes. This is a significant advantage as it allows the system to eventually get consistent, without having any primary reference node. The concept of quorums is very elegantly used here.
3. Handling of temporary failures is done using hinted handoff, which is a very good way of maintaining redundancy without having to rejig the entire distributed system.
4. Handling of permanent failures using Merkle trees saves in order of magnitudes time for achieving consistency as only hashes of root nodes can be compared and only specific branches need to be moved.
5. Although the Gossip based protocol has very little probability of not working due to different nodes having different versions of memberships, the paper claims that it achieves the desired goal of membership and failure detection, in a truly distributed fashion.

## Limitations:
1. Gossip protocol may not scale well ~thousands of nodes.
2. Applications that are new may need a redesign before using Dynamo as it exposes data consistency and reconciliation logic issues to the developers. This is a design consideration during the initial stages of the development for picking the right conflict resolution mechanisms to meet the business case.

## Credits
1. https://levelup.gitconnected.com/distributed-systems-physical-logical-and-vector-clocks-7ca989f5f780
2. https://www.youtube.com/watch?v=w96lLsbI1q8&t=1320s&ab_channel=PapersWeLoveLondon
3. https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf