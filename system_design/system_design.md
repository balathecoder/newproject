# System Design
## 1. Database Replication
![image](https://github.com/user-attachments/assets/e6b6fb41-aba8-4d1e-9dba-9ae103319099)
### What is SPOF - Single Point Of Failure
when one component in a system stops working leads to the entire system stops working is called single point of failure.

In above diagram, the component which will lead to single point of failure,
- when load balancer fails, entire system fails.
- when web server fails, traffic still can go to other web server and continue to serve.
- when database system fails, entire system fails.
### Database Replication
Database replication is a process of copying database data from one database to one or more databases.
### Use Database Replication to Avoid SPOF
In above diagram, add Database replication with master and slave databases so that when one database instance fails, the other instance will become active.
In master slave concept, write operation will take place at master database and read operation will take place in slave databases.
This also provides redundancy.
With this setup, if one database instance fails, the system still function with other databases. Let say, if master database fails, the slave database will be promoted as master database and all the write operation will go to new master database. 
Similarly, if read operation fails, all read operation will be temporarily goes to master database. In any system, the number of select statements executed is much higher that insert/update/delete statements 
that means number of read is much higher than the write operation.
If user base or records continue to grow, you can simply add more instance to improve performance.
![image](https://github.com/user-attachments/assets/a3d5d52f-9611-426d-b67f-0342bf301d2d)

## 2. Horizontal vs Vertical Scaling
![image](https://github.com/user-attachments/assets/88bf9ac2-f105-4c02-a657-540fc8ee085b)
The users access the system using the web browser or mobile app. Here the website, database and cache everything runs under one system. When system becomes more popular, you will get more users coming to your system. In that case
one system is not enough to support web traffic. 

In this as a first step, separate your data tier from web tier so there is a separate database server and separate web server as shown below
![image](https://github.com/user-attachments/assets/6f19f96d-5766-4175-aedf-e3ce7897e09d)
With this, the advantage is we can scale the web tier and data tier independently. Let say if more number of users coming to your system, then increase web server to handle the load.
When more number of data processing with database, then increase instances in data tier.

There are two types of scaling,
### Vertical scaling - here you add more CPU, more RAM on the same server.
before vertical scaling
![image](https://github.com/user-attachments/assets/6577a81a-fa60-4c22-aff2-663feb3a81a8)
after vertical scaling
![image](https://github.com/user-attachments/assets/1af8cd8c-7958-4249-84d3-41c0f4304c4d)

Pros :
 Simplicity - It's relatively simple to add more RAM or CPUs to a server.
Cons :
 Impossible to add infinite memory & CPU to one server
 No failover or redundancy. Meaning you have only one web server, if that server crashes or goes down, your entire system goes down.
### Horizontal scaling - 
In horizontal scaling, you can add more server to handle the load and improve performance.
![image](https://github.com/user-attachments/assets/37cc6089-2b74-408b-944d-669c56229887)

![image](https://github.com/user-attachments/assets/4ea239e2-4af6-475b-aa10-3ebd9e4dca81)

Here, the load balancer can manage the load like half the request can go to server1 and half the request can go to server2.
Pros : 
 Provides failover & redundancy. Meaning, if your one server goes down, users are still able to access your system using the second server.
Cons :
 Cost of new servers. Buying a new server is more expensive than upgrading your RAM or Hard disk drive HDD or SDD in your existing server.
 Configuration & Maintenance of the servers. Once you have added the server, you have to do the maintenance for it, like patching and other things.
 
## 3. Content Delivery Network - CDN

![image](https://github.com/user-attachments/assets/fe4bd1ed-14ac-483e-86f8-0f78004b2127)

### Cache & Readh Through Cache
it is a temporary storage which is much faster that your database. It is used to store frequently accessed in cache memory rather than querying your database everytime.

The cache tier sits between web tier and data tier. so when the request comes in, web server will check to see if the requested data is present, if yes requested data will be served from cache. If the request is not available is not available, then request will be sent to database.
This is called as read through cache. 

When to implement cache - when data is read more frequently than modify then you should implement cache. For example, the newspaper scenario, once newspaper is published, the users will be access
newspaper for read and there wont be any change in its content. so in that case, cache will be useful. Storing content in cache will be with expiration time, once data is expired it will be removed, fetched again from database.

![image](https://github.com/user-attachments/assets/c1ded21e-79b4-456a-8e87-c9f7d095c061)

### CDN - Content Delivery Network
It's basically a third party service. servers located all over the world and allow you to store your static information like html/css/images/videos for your website.
Let say you have webserver in Japan and you have 2 users. with 1 from Japan and another from Germany, for user1 the website will be loaded faster than user2
![image](https://github.com/user-attachments/assets/a3c0f5f1-4eae-4c14-bd30-84fa2f543e30)


### Use Cache and CDN to improve load time

if you Content Distribution Network, then your static website content like html, css, javascipt, images, videos will be cached in a server nearby and your user will see a improved load time. It's a third party server, they charge based on data coming in and data going out.

![image](https://github.com/user-attachments/assets/eddf8aa1-3514-4f6c-b20a-9b657ef00c09)

## Stateful vs Stateless Architecture
Stateful - storing the user session/state who is interacting with application.
Stateless - Not storing.

## Non Functional Requirements
For NFR, the key requirements are,
* Performance(Low latency)
* Availability (System should be highly availability with 99.9999% availability)
* Scalability (The system should be highly scalable. During peak load, the system should be able to scale autotically to handle such loads)
* Reliability (How consistent system performance on a regular basis including fault tolerant. In case of ecommerce, when system fails during payment, it should refund the money for failure in placing the order)

NFR defines how well the system performs when the system grows and matching the user expectation. Let say if search results taking lot of time to load, then it creates negative impact to user and they might go away from our system. One more scenario, when system load increases during Olympic time, the number of users for Twitter will increase, so system should be able to handle such high loads.

## Capacity Estimations
1. Determine no of servers and databases
- Daily Active Users
- Monthly Active Users
- Throughput
- Bandwidth
- Storage
let say if requirement is 10M requests/hour and our system handles 1M request/hour, then we should have those many instances to handle the load.
2. Cost Management
We should make sure the system provisioned with required number of servers to manage cost effectively.
3. Decide type and specifications of all hardware
Let say 10M queries /Hour, if SQL handles 50k queries, NoSQL handles 1M queries, then we should go with NoSQL.


