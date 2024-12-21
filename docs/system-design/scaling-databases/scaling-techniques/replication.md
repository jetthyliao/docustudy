# Replication

## Master-Slave Replication

In the master-slave replication design, we have a **master** server, and multiple slave servers.

- The **master** server does the following:
    - Serves both *reads* and *writes*
    - Replicates *writes* to one or more slaves
- The **slave** server does the following:
    - Serves only *reads*
    - Replicate to additional slaves (in a tree like fashion)
- If **master** goes offline, a slave is promoted to master, or a new master is provisioned

Disadvantages of Master-Slave:

- Additional logic is needed to promote slave to a master
- Includes [Disadvantages of Replication](#disadvantages-of-replication)

## Master-Master Replication

In the master-master replication design, we have multiple **master** servers. Both **master** servers read and write and coordinate with each other on writes. If either goes down, the system can continue to operate with both read and writes.

Disadvantages of Master-Master:

- Need a load balancer OR change application logic to determine where to write
- Most master-master systems are either loosely consistent (violates ACID) or have increased write latency due to synchronization
- Conflict resolution comes into play as more write nodes are added and latency increases
- Includes [Disadvantages of Replication](#disadvantages-of-replication)

## Replication Master-Master + Master-Slave

It is common to combine the two replication techiniques so that a distributed system can **handle higher load** and have **data redundancies**. Below is an example of a system that uses both techniques

!!! tip "Replication Diagram"
    
    <br>

    <figure markdown>
      ![Replication Diagram](../../../assets/images/system-design/replication.svg#only-dark)
      ![Replication Diagram](../../../assets/images/system-design/replication-light.svg#only-light)
    </figure>

    <br>

## Disadvantages of Replication

- Potential for loss of data if master fails before newly written data is replicated to other nodes
- Writes are replayed to the read replicas (if there are a lot of writes, system can get bogged down)
- Replication lag: the more read slaves, the more you have to replicate
- On some systems, writes are parallelized but reads are sequential
- Replication add mores hardware/complexity

