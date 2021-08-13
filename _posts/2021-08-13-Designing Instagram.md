## What is Instagram 

IG is a social n/w service that enables its users to upload and share their photos and videos with other users privately and publicly.

We plan to design a simple version of IG for this design problem, where a user can share photos and follow other  users. The 'News Feed' for the user will consist of top photos of all the people the user follows.



## Requirements and Goals of the System 

**Functional Requirements:**

1. Users should be able to upload/download/view photos
2. Users can perform searches based on photo/video titles.
3. Users can follow other users.
4. The system should generate and display a user's News Feed consisting of top photos from all the people user follows

**Non-functional Requirements:**

1. Our service needs to be highly available.
2. The acceptable latency of the system is 200ms for news feed generation
3. Consistency can tale a hit (in the interest of availability) if a user doesn't see a photo for a while, it should be fine.
4. The system should be highly reliable; any uploaded photo or video should never be lost.

**Not in scope**: Adding tags to photos, searching photos on tags, commenting on photos, tagging users to photos, who to follow, etc.



## Some Design Considerations

1. The system would be read-heavy so we will focus on building a system that  retrieve photos quickly.
2. Users can upload as many photos as they like, therefore efficient management of storage should be a crucial factor in designing this system.
3. Low latency is expected while viewing photos.
4. Data should be 100% reliable. If a user uploads a photo, the system will guarantee that it will never be lost.



## Capacity Estimation and Constraints

- Let's assume we have 500M total users, with 1M daily active users.
- 2M new photos every day, 23 new photos every sec.
- Avg photo size => 200KB
- Total space required for one day photos = 2M * 200KB => 400GB
- Total space required for 10 years = 400GB * 365 * 10 ~= 1425TB



## High Level System Design

At a high level, we need to support two scenarios, one to upload photos and the other to view/search photos. Our service would need some object storage servers to store the photos and some database servers to store metadata information about the photos.

![Instagram-HLD](/assets/Instagram-HLD.PNG)



## Database Scheme

We need to store data about users, their uploaded photos and the people they follow. The Photo table will store all the data related to a photo; we need to have an index on (PhotoID, CreationDate) since we need to fetch recent photos first.

```
Photo								User							UserFollow
PhotoID: int - PK					UserID: int - PK				UserID1: int - PK
UserID: int							Name: varchar(20)				UserID2: int - PK
PhotoPath: varchar(256)				Email:varchar(32)
PhotoLat: int						DoB: datetime
PhotoLong: int						CreationDate: datetime
UserLat: int						LastLogin: datetime
UserLong: int
CreationDate: datetime
```

A straightforward approach for storing the above schema would be to use an RDBM like MySQL since we require joins. But relational databases come with their own set of challenges especially when we need to scale them.

We can store photos in a distributed file storage such as HDFS or S3.

We can store the above schema in distributed key-value store to get the benefits offered by NoSQL. All the metadata can go to a table where the key is PhotoID and the value is an object containing PhotoLocation, UserLocation, CreationTimestamp etc.

If we go with NoSQL storage, we will need two more tables "UserPhoto" and "UserFollow" to store the what photos does the user own and what other users the user follows. We can use wide column datastores like Cassandra for these tables. The key would be UserId and the value would the list of PhotIDs/UserIDs that the user owns/follows.

Cassandra or key-values pairs in general maintain a certain number of replicas to offer reliability.  Also, deletes don't happen immediately. They keep the deleted data to support undeletation before the entry is removed from the system permanently.



## Data Size Estimation

Let's find out how much data goes into each table and how much space we will to store data of 10 years.

**User** Assuming every int and datetime is 4 bytes, every row of user table will occupy 68 bytes

​		UserID(4) + Name(20) + Email(38) + DoB(4) + CreationDate(4) + LastLogin(4) = 68 bytes.

Assuming we would need to store 500M users in a span of 10 years, we would need

​		68 bytes * 500M = 32GB of data

**Photo** Likewise, a Photo row will take 284 bytes of data.

So if 2M photos are getting uploaded everyday, we would need

​		284 bytes * 2M  = 0.5GB of storage for one day's photo.

For 10 years, 0.5 * 365 * 10 = 1.88TB of storage.

**UserFollow:** Each row in UserFollow table would take 8 bytes of data. So if we have 500M of users and each user follows 500 users,

8 * 500 * 500M = 1.82TB of data.



In total, we would need 1.82TB + 1.88TB + 32GB = ~3.7TB of data.



## Component Design 

Photo uploads (or writes) can be slow as they have to go through disk. While reads can be faster since they can be served from the cache.

Since uploads are slow, they can eat up the entire bandwidth of the system, leaving the read requests for starvation. Assuming a web service can handle a maximum of 500 concurrent connections, it can't handle more than 500 concurrent reads and writes. To address this issue, we should split the read and write service into two different services running on two different servers. This will make sure that write requests don't hog the system. 

Separating the two services will also help us scale them individually depending on the need.



![](/assets/instagram-component-design.PNG)

