# CAP Theorem

CAP Theorem: in a distributed system, only two of the following three can be provided.

- **Consistency:** every read receives the most recent write or an error.
- **Availability:** every request receives a response, without guarantee that it contains the most recent version of the information.
- **Partition Tolerance:** the system continues to operate despite arbitrary partitioning due to network failures.
    - A *partition* is when a network fails to deliver some message by *losing* them (eventual delivery or delay of a message is not a partition)
    - *Total partition* is a period when no messages are delivered between two sets of node. A failed server/machine is not a total partition

**Networks are not reliable** failures will occur. Partitions must be tolerated in a distributed system. This means, in the real world, when designing a distributed system there are two options in relation to the CAP theorem: 

1. Consistency + Partition Tolerance
2. Availability + Partition Tolerance

## Consistency + Partition Tolerance

Consistency/Partition (CP): prioritizes returning consistent data or returning an error if its not available. Below are some potential features a system could have to make it CP tolerant. 

- Timeout error: waits for response from partitioned nodes
- Turn off inconsistent nodes (return error based on why node is inconsistent)

### Consistency Patterns

Within CP tolerant systems, the strictness of how consistent data has to be can vary. There are three different **consistency patterns** that helps determine how tolerant the CP system should be

1. Weak Consistency: After a write, reads **may or may not** see it

2. Eventual Consistency: After a write, reads **will eventually** see it

3. Strong Consistency: After a write, reads **will** see it

???- note "Example - Mongo DB" 

    MongoDB uses a single leader based architecture with multiple replicas. This means that data gets written to a main source and gets replicated to the replicas. 

    Reading will reference this master database which means we will always get the correct data (strong consistency). 

    The trade-off is when messages are lost, there is no way for the primary database to get it back, meaning we sacrificed availability. 

    However, there are ways to configure MongoDB such that you sacrifice some consistency (evnetual consistency) to get some more availability. 


## Availability + Partition Tolerance

Availability/Partition (AP): prioritizes always returning data regardless if the data is stale or up-to-date. Below are some potential features a system could have to make it AP tolerant.

- Return the most recent version data available (could be stale data)
- Systems could accept writes, but they may be processed at a later time (when partition is resolved)

### Availability Patterns

Like CP tolerant systems, the strictness on availability can vary as well. There are two main **availability patterns** that can be implemented in an AP tolerant system. 

1. Fail-Over: design pattern that utilizes multiple nodes to prioritize maintaining uptime. There are two main approaches to fail-over design:
    - **Active-Passive** (master-slave fail-over): 
        - Two servers, active and passive
        - Only active server handles traffic/requests
        - Heartbeats are sent between active/passive server
        - If heartbeat is interrupted (active server dies), the passive server takes over the active's IP address and resumes service
        - Length of downtime determined by whether the passive server is already running *hot* (in standby) or *cold* (needs to be booted up)
    - **Active-Active** (master-master fail-over)
        - Both servers are managing traffic, spreading load between them
        - If public-facing, DNS needs to know public IPs of both server
        - If internal-facing, application logic needs to know about both servers

2. Replication: having redundant systems with the same copy of the data. If one fails, it is replaced by a copy
    - topic further discussed in scaling database section

???- note "Example - Cassandra DB" 

    Cassandra has multiple database nodes that are connected in a ring (ring represents a data center). Each of these nodes have the ability to get read/written to. 

    This means if one node goes down (or a partition occurs), another node can pick it up (high availibility). 

    In the backend the nodes coordinate (gossiping) to coordinate the data they store. This is a trade-off lost of consistency since the nodes can have varying data at a given time. 

    Again, like MongoDB, Cassandra can be configure to be more consistent by sacrificing some availbilty. 


## Quantifying Availability 

Availability is often quantified by uptime (or downtime) as a percentage of time the service is available. Availability is measured by the number of 9s in the percentage.

99.9% (Three 9s)

|Duration           |Acceptable Downtime|
|-------------------|-------------------|
|Downtime Per Year  |8h 45m 57s         |
|Downtime Per Month |43m 49.7s          |
|Downtime Per Week  |10m 4.8s           |
|Downtime Per Day   |1m 26.4s           |

99.99% (Four 9s)

|Duration           |Acceptable Downtime|
|-------------------|-------------------|
|Downtime Per Year  |52m 35.7s          |
|Downtime Per Month |4m 23s             |
|Downtime Per Week  |1m 5s              |
|Downtime Per Day   |8.6s               |


## Calculating Availability 

Calculating availability of in sequence components

- Availability (Total) = Availability (Server 1) * Availability (Server 2)
- Overall decrease if in sequence

Calculating availability of in parallel components

- Availability (Total) = 1 - (1 - Availability (Server 1)) * (1 - Availability (Server 2))
- Overall increase if in parallel

## Resources

[:material-video-outline: CAP Example Video](https://www.youtube.com/watch?v=k-Yaq8AHlFA)

[:material-file-document-outline: CAP](https://www.educative.io/blog/what-is-cap-theorem#captheoremexplained)

[:material-file-document-outline: MongoDB vs CassandraDB](https://stackoverflow.com/questions/50645244/why-mongodb-is-consistent-not-available-and-cassandra-is-available-not-consisten)

