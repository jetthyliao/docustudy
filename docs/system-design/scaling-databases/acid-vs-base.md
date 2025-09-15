# Transaction Models

The [CAP Theorem](../cap.md) states it is impossible to achieve both **consistency** and **availability** in a partition tolerant distributed system. Therefore, when it comes to database transaction models, there are *two primary models*, one that prioritizes consistency and one for availability. 

## ACID Model

The ACID model ensures that transactions are always consistent. Due to the emphasis on consistency, ACID model databases are usually used for databases with strong constraints such as **relational** databases. Although NoSQL database can be somewhat ACID compliant, the ACID model goes against the NoSQL approach to database design. 

ACID is an acronym, where each word represents a quality of the ACID model.

### Atomicity: 

Atomic: each transaction is either properly carried out or the process halts and the database reverts back to the state before the transaction started. Ensures all data in the database is valid.

- Basic: read and writes either finish completely, or abandoned altogether.
- Important for handling crashes or outages.

### Consistency 

Consistent: a processed transaction will never endanger the data integrity of the database.

- Basic: Does not violate integrity of the database.
- Ensures if something being added will compromise the database structure, it will get aborted
- Helps maintain schema if there is one
- This could also be used as maintaining constraints. EX. Bank Withdrawal database shouldn't allow negative account balances. 

### Isolation

Isolated: transactions cannot compromise the integrity of other transactions by interacting with them while they are still in progress.

- Basic: read or writes on database will not be impacted by other read or writes of separate transactions on the same database
- Optimistic transaction schema: assumes two transactions will most likely not effect each other and lets both run. However, if conflict occur, it will abort both and make both retry again. 
- Pessimistic transaction schema: locks down resources and forms a queue for all transactions. This means less abort and retries, but possibly longer wait times for transactions. 

### Durability

Durable: the data related to the completed transaction will persist even in the cases of network or power outages. If a transaction fails, it will not impact the manipulated data.

- Basic: changes in database made will be permanent in the database
- Ensures data will not be corrupted by outages, crashed, etc...
- Achieved through changelogs that are referenced when databases are restarted

## BASE Model

The BASE model prioritizes availability. It was created to reflect the flexible and fluid properties of NoSQL databases. 

Like ACID, BASE is also an acronym that is self describing of the model itself. 

### Basically Available (BA)

Basically available: rather than enforcing immediate consistency, BASE-modelled NoSQL databases will ensure availability of data by spreading and replicating it across the nodes of the database cluster. 

- There will be a response to every requests (even if response is a system failure, or stale data)

### Soft State

Soft state: due to lack of immediate consistency, data values may change over time. Soft state breaks away from the concept of databases enforcing its own consistency and delegates that responsibility to developers. 

- Soft state is due to the eventual consistency model, where the database doesn't care if transactions are consistent

### Eventually Consistent

Eventually Consistent: even though it doesn't enforce immediate consistency, it will eventually achieve it. Until it does, data reads are still possible (although may be stale)

- In the case of NoSQL, copies of the database are on multiple servers. If a write transaction just occurred it may not be available on all servers (needs to clone to all server). However, it eventually will be cloned, and thus consistent.

## Resources

[ACID Explained](https://www.bmc.com/blogs/acid-atomic-consistent-isolated-durable/)

[ACID VS BASE](https://phoenixnap.com/kb/acid-vs-base)


