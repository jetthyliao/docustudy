# Partition

## Federation

Federation (functional partitioning) is a technique in which we split up a single database into multiple databases based on the characteristic of the data that goes into each individual database. 

???+ tip "Federation Diagram"

    <figure markdown>
      ![federation Diagram](../../../assets/images/system-design/federation.svg#only-dark)
      ![federation Diagram](../../../assets/images/system-design/federation-light.svg#only-light)
    </figure>

    The single bank database gets split up based on business logic. The databases themselves can act independently from others. 

Advantages of Federation:

- Less read and write traffic to each database and therefore less **replication lag**
- Smaller databases, results in more data that can fit in memory, which in turn results in more cache hits due to improved **cache locality**
- No centralized database (can write in parallel, increasing **throughput**)
- Horizontal scaling (adding more servers)

Disadvantages of Federation:

- Federation is not effective if your schema requires huge functions or tables
- Need to update application logic to determine which database to read/write to
- Joining data from two databases is more complex with a [server link SQL command](https://stackoverflow.com/questions/5145637/querying-data-by-joining-two-tables-in-two-database-on-different-servers)
- Federation adds more hardware and additional complexity

## Sharding

Sharding distributes data across different databases such that each database can only manage a subset of the data. Sounds similar to federation but they're different, read [shard VS federation](#sharding-vs-federation) for more clarification

???+ tip "Sharding Diagram"

    <figure markdown>
      ![sharding Diagram](../../../assets/images/system-design/sharding.svg#only-dark)
      ![sharding Diagram](../../../assets/images/system-design/sharding-light.svg#only-light)
    </figure>

    The single database has its data split up generically into multiple data servers. Each server maintians the same schema.


Advantages of Sharding: 

- Results in less read and write traffic
- Less replication, more cache hits
- Index size reduced, faster queries (on individual shards)
- If one shard goes down, other shards are operational
- Allow for parallel writes (throughput)

Disadvantages of Sharding:

- Need to update application logic (could result in complex SQL queries)
- Data distribution can become lopsided!
    - Example: one shard could have more active users (uneven server load)
    - Rebalancing the load adds additional complexity
    - Solution: [**consistent hashing**](#consistent-hashing)
- Joining data from multiple shards is more complex
- Sharding adds more hardware and additional complexity

## Consistent Hashing

Hashing is an important part of **sharding**. It works by passing a chunk of data to a **hashing function**, which provides a **hash value**. This **hash value** now represents a chunk (or shard) of data. 

Below are two ways where **shards** are distributed to servers. For both of these methods, what needs to be considered is **how does a failed/added server effect the distribution technique?**

**Trivial Solution**


???+ note "Trivial Sharding Hashing"

    <br>

    <figure markdown>
      ![sharding Diagram](../../../assets/images/system-design/sharding-trivial-hashing.svg#only-dark)
      ![sharding Diagram](../../../assets/images/system-design/sharding-trivial-hashing-light.svg#only-light)
    </figure>

    <br>

    1. Given data, split it up into **data chunks**
    2. For each data chunk, run it through a **hash function** to assign a **hash value** to each chunk
    3. Store **hash values** into a **hash array**. 

???+ note "Trivial Sharding Distribution"

    <br>

    <figure markdown>
      ![sharding Diagram](../../../assets/images/system-design/sharding-trivial-distribution.svg#only-dark)
      ![sharding Diagram](../../../assets/images/system-design/sharding-trivial-distribution-light.svg#only-light)
    </figure>

    <br>

    4. Trivial solution uses **modulo** to evenly assign chunks to N servers
    5. If number of server changes (failed server or new server added), need to redo modulo
    6. Redoing modulo results in a lot of reshuffled hash values (not very good)

**Consistent Hashing Solution** 

???+ note "Sharding Consistent Hashing"

    <br>

    <figure markdown>
      ![sharding Diagram](../../../assets/images/system-design/sharding-consistent-hashing.svg#only-dark)
      ![sharding Diagram](../../../assets/images/system-design/sharding-consistent-hashing-light.svg#only-light)
    </figure>

    <br>

    - Hashes all the data AND the servers we have available and stores it on a table
    - Connect the table from beginning to end so it forms a ring
    - Whenever a server is added/removed, we add/remove the corresponding entry on the hash table
    - Now, every data object behind that entry on the hash table will be shifted to the next visible server object on the hash table.
    - This ensures minimal shifting of data objects whenever servers are adjusted.

## Sharding VS Federation

I am unsure what the difference is but this is my interpretation.

- Federation: data gets split up based on the characteristics of the data. Since data is split based on how data is used, we can have each database contain different schemas. 
- Sharding: data gets split up generically. Since the data is split up generically, all databases that stores a shard needs to maintain the same schema. 





