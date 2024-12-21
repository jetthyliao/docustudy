# Other Scaling Techniques

## Denormalization

Denormalization: denormalize a previously normalized database in efforts to improve the read performance of a database.

- Add redundant data to multiple tables (avoids expensive joins)
- Done when performance/scalability is a main concern
- In most systems reads outweigh writes 100:1 or even 1000:1

Disadvantages of Denormalization

- Data is duplicated
- Constraints can help redundant copies of information stay in sync, which increases complexity of database design
- Denormalized database under heavy write load might perform worse than its normalized counterpart

## SQL Tuning (RDB Only)

SQL Tuning: is the process of enhancing SQL queries to speed up the performance of the server. This is a vast topic with a lot of different techniques/resources. This section will lightly go over some tips to speed up queries.

**Benchmark** and **Profile** are important to do to simulate and uncover bottlenecks.

- Benchmark: simulate high-load situations (tools usually exists for this)
- Profile: enable tools such as the *slow query log* to help track performance issues

General Optimizations:

- Tighten up schema:
    - MySQL dumps to disk in contiguous blocks for fast access
    - Use ``CHAR`` instead of ``VARCHAR`` for fixed-length fields
        - ``CHAR`` allows for fast, random access, whereas
        - ``VARCHAR`` must find the end of a string before moving onto next one
    - Use ``TEXT`` for large blocks of text (blog posts)
        - Allows for boolean searches
        - Use ``TEXT`` field results in storing a pointer on disk that is used to locate the text block
    - Use ``INT`` for numbers up to 2\^32 or 4 billion
    - Use ``DECIMAL`` for currency to avoid floating point representation errors
    - Avoid storing large ``BLOBS``, store the location of where to get object instead
    - ``VARCHAR(255)`` is the largest number of characters that can be counted in an 8 bit number, often maximizing the use of a byte in some RDBMS.
    - Set ``NOT NULL`` constraint where applicable (improves search performance)
- Use good [indices](https://www.youtube.com/watch?v=fsG1XaZEa78)
    - Columns that you are querying could be faster with indices
    - Indices are usually represented as self-balancing B-tree that keeps data stored and allow searches, sequential access, insertions, and deletions in logarithmic time
    - Placing an index can keep the data in memory, requiring more space
    - Writes could be slow since the index also needs to be updated
    - When loading large amounts of data, it might be faster to disable indices, load the data, then rebuild the indices
- Avoid expensive joins (denormalize)
- Partition table
- Tune the query cache




