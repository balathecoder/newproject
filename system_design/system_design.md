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

The database replication will be used mainly for High availability and load balancing. While sharding is used for horizontal scaling when data volume grows what a single server cant handle or to handle massive amount of data and traffic by using multiple servers.

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

### Database Sharding
Database is a technique involves dividing a large database into smaller, more manageable parts called shards. Each shard is stored on a separate database server or node, allowing for horizontal scalability and improved performance. This is mainly used for write efficiency.
 
In essence, sharding enables the database to handle large amount of data and traffic by distributing the load and resources across multiple servers for improved performance.

Sharding is to improve write efficiency on database. Based on id or geographical location, gender, creator the sharding can be applied.
https://www.geeksforgeeks.org/difference-between-database-sharding-and-replication/
#### When to use Sharding
* Large Datasets : When your database grows significantly and a single server can no longer handle the load.
* Horizontal scaling : To scale by adding more servers or nodes as data grows.
* High write load : When you have lot of write operations and want to distribute the load across multiple servers.
* Example : Ecommerce system with extensive product catalogs, social media platforms, or multi tenant applications.

#### When to use Replication
* High Availability : To ensure continuous access to data even if a server fails.
* Fault tolerance : To protect against server failures by maintaing multiple copies of data.
* Read scalability : To distribute read traffic and reduce load on the primary server.
* Data durability : To ensure data is not lost when server failure.
* Example : Web application, online banking system, or other application where data integrity and availability are critical.
  
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


## API Performance
### 1. Payload compression
Network latency will be high if payload data that we are sending in large so we can send it compressed data using gzip middleware.
```
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

import gzip

app = FastAPI()

@app.post("/compressed")
async def handle_compressed_request(request: Request):
    if "Content-Encoding" in request.headers and request.headers['Content-Encoding'] == "gzip":
        try:
            decompressed_data = gzip.decompress(await request.body())
            payload_size = len(decompressed_data)

            print('decompressed data :',decompressed_data.decode('utf-8'))
            print('payload size : ', payload_size, "bytes")
            return JSONResponse(content={"message":"Success!"})
        except Exception as e:
            return JSONResponse(content={"message":f"Error : {str(e)}"})
    else:
        return JSONResponse(content={"message":"Content Encoding must be gzip"})
```
Run the server,
```
$ uvicorn payload_compression:app --reload
```
For quick testing, create a simple json and compress it with gzip,
```
$ echo '{"key":"value"}' >data.json
$ gzip data.json
```

Run the curl command,
```
$ curl -X POST -H "Content-Type: application/json" -H "Content-Encoding: gzip" --data-binary "@data.json.gz" http://127.0.0.1:8000/compressed
{"message":"Success!"}
```
### Pagination
Paginatino with fastapi
```
from fastapi import FastAPI
from pydantic import BaseModel, Field

from fastapi_pagination import Page, add_pagination, paginate, Params
app = FastAPI()
print("app created")
add_pagination(app)
print("pagination added")

class UserOut(BaseModel):
    name: str = Field(..., example="Steve")
    surname: str = Field(..., example="Rogers")
    class Config:
        json_schema_extra = {
            "example": {
                "name": "Steve",
                "surname": "Rogers"
            }
        }

users = [
    UserOut(name="Steve", surname="Rogers"),
    UserOut(name="Tony", surname="Stark"),
    UserOut(name="Natasha", surname="Romanoff"),
    UserOut(name="Bruce", surname="Banner"),
    UserOut(name="Clint", surname="Barton"),
    UserOut(name="Thor", surname="Odinson"),
    UserOut(name="Wanda", surname="Maximoff"),
    UserOut(name="Vision", surname="Vision"),
    UserOut(name="Sam", surname="Wilson"),
    UserOut(name="Bucky", surname="Barnes")
]
print('start /users')
@app.get("/users", response_model=Page[UserOut])
async def get_users(params: Params = Params()):
    return paginate(users, params)
```

Note : orm_mode in FastAPI, when used with Pydantic models, enables seamless integration with Object-Relational Mappers (ORMs) like SQLAlchemy. This setting allows Pydantic models to accept ORM objects directly, instead of just dictionaries.
from pydantic import BaseModel
class IPSchema(BaseModel):
   ip: str
   class Config:
       orm_mode: True
       
from sqlalchemy import Column, Integer, String
from database import Base
class IPModel(Base)
### Caching

### Connection Pooling
Connection pooling primarily enhances the responses of your FastAPI application by managing pool of database connections that can be reused among multiple user requests.
The max_connections to a mysql database is 150 + 1 for administrator. It depends on resources such as RAM, file handles and network sockets. More connections generally requires more memory usage and that will degrade performance.


Without using ORM if we want to use database connection in fastapi application, asyncpg can be used.

https://www.sheshbabu.com/posts/fastapi-without-orm-getting-started-with-asyncpg/
https://www.youtube.com/watch?v=gaOFk3bqM_4
