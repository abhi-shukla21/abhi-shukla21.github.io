## Requirements and Goals of the System

##### **Functional Requirements:**

- Generate a short unique URL for a given URL.
- Opening the short URL should redirect to original link
- Options of picking custom short url
- Links should expire after a standard time. User should be able to specify it.

##### 

##### **Non-functional Requirements:**

- The system should be highly available. If services are down, URL redirection will fail.
- Redirection should happen in real-time with minimal latency.
- Shortened links should not be guessable.



##### **Extended Requirements:**

- Analytics, i.e, how many redirections happened?
- Services should be accessible through REST APIs



## Capacity Estimation and Constraints:

**Traffic estimates:** Assuming we have 500M new URLs per month, with 100:1 read/write ratio, we can expect 50B redirections during same period. Consequently, this would lead to 20K read requests per sec
$$
500 million / (30 days * 24 hrs * 3600 sec) = ~200 URLs/s
$$

$$
100 * 200 URLs/s = 20K/s
$$

**Storage Estimates:** 

Assuming we store every URL for 5 years, we'll be storing 30 billion objects at peak. Assuming, each url takes 500 bytes of space, we will need 15TB  of total storage.
$$
500 million * 5 yrs * 12 mnths = 30 billion
$$

$$
30 billion * 500 bytes = 15 TB
$$

**Bandwidth estimates:** 

For write requests, since we expect 20 URLs per sec, total data incoming will be 100Kbps.

For read requests, 20k * 500 bytes  = ~10 MBps



**Memory estimates:** If we want to cache some hot URLs, following 80-20 rule. Since we're getting 20K requests per sec, we will get 1.7 billion requests per day.
$$
20K * 3600sec * 24hrs = ~1.7billion
$$
To cache 20% of these requests, we will need 170GB of memory
$$
0.2 * 1.7billion * 500 bytes = 170GB
$$

## System APIs

Following are the APIs for creating and deleting a shortened url:

```
createURL(api_dev_key, original_url, custome_alias=None, user_name=None, expire_date=None)
```

returns the shortened URL in case of success, otherwise the error code.

```
deleteURL(api_dev_key, url_key)
```



## Database Design

- We need to store billions of records
- Each object we store is small
- There are no relationships between records- other than which user created the URL
- Our service is read-heavy



### Database Schema:

```166
URL										User
Hash:varchar(16) - PK					UserId: int - PK
OriginalURL: varchar (512)				Name: varchar(20)
CreationDate: datetime					Email: varchar(32)
ExpirationDate: datetime				CreationDate: datetime
UserId: int								LastLogin: datetime
```

Since we anticipate storing billions of rows, and we don't need to use relationships b/w objects, a NoSQL store makes better choice. It's also easier to scale.



## Basic System Design and Algorithm

- We can use a Key Generation System (KGS) to pregenerate keys and have them handy to be used.