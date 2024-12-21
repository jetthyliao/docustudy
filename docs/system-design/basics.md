# Scaling

**Scalability**: a systems ability to cope with increase load

- Scalability issue = system is fast for a single user, but slow under heavy load

**Scalable**: when resources increase in a system, the increased performance from the resources should be proportional to the resources added. 

- Adding additional database for redundancy (but not for performance) is an example of scaling.

**Vertical Scaling**: add more resources to a single server/machine so it can handle more load

- Achieved by adding more ram, faster processor, or more disk space
- Will eventually reach a physical or technological limitation (making it a bad long-term solution)

**Horizontal Scaling**: use multiple servers to build out a distributed topology. 

- Utilize multiple cheap server/machines to handle more tasks/requests
- **Issue:** how do we distribute requests to multiple servers? (system design will solve this)

# Speed 

**Performance**: a systems ability to perform a specific task

- A performance issue = system is slow for a single user

**Latency**: time to perform some action or to produce some result.

**Throughput**: number of such actions or results per unit of time. 

**Latency VS Throughput**: generally should aim for **maximal throughput** with **acceptable latency**
