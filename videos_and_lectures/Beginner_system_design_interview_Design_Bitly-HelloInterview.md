# "Beginner System Design Interview: Design Bitly w/ a Ex-Meta Staff Engineer"

**Video citation:** Beginner System Design Interview: Design Bitly w/ a Ex-Meta Staff Engineer. <www.youtube.com>. Retrieved March 8, 2025, from [https://www.youtube.com/watch?v=iUU4O1sWtJA](https://www.youtube.com/watch?v=iUU4O1sWtJA)

---

Note: I'll be using the space character as a thousands separator, since that's the internationally recommended way. I'll also use the explicit number when the video author mentions "billions", given that the meaning of "billion" changes from country to country.

---

An article with the contents of this video can be found [here](https://www.hellointerview.com/learn/system-design/problem-breakdowns/bitly).

---

We first need to understand the kind of system the interviwer is asking to design. In this case, a url shortener, which is a service that allows users to create short urls from long urls. E.g.: <www.mylongurl.com/long/is/annoying> can be shortened to <www.bit.ly/GHJ23>.

The order in which we should tackle the interview is to consider: requirements, core entities, api or interface, data flow (not needed in this case), high-level design, deep dives.

When doing any math, it's recommended to use exponents to make it easier.

## Requirements

### Functional requirements

Are "users can" or "users will" statements.

- Users can create short urls from original urls  
  - Support an optional custom alias
  - Support an optional expiration time
- Users can be redirected to the original url by visiting the short url

### Non-functional requirements

Instead of being features, they're the qualities of the system in order to ensure a good user experience.

- Low latency redirection (around 200 ms, which is what the human perceives as real time)
- Ensure uniqueness of short urls
- Scalable to 100 million daily active users and 1 000 000 000 urls
- Availability 99.99% of the time

Cap theorem: when considering consistency, availability and partition tolerance, you can only ever have 2 or those 3 things. In distributed systems, there's always the need for partition tolerance, in order to horizontally scale servers. So you need to choose either consistency or availability. To evaluate the need for "read after write" consistency, we have to ask: does every single "read" of my system have to read the latest "write"? (e.g.: a banking application, ticket booking, etc.). In a url shortener there's no need for a user to be redirected immediately after a short url is created (probably users won't even know the short url has been created in such a short time).

Also: We'll need to validate the long url. And there will be a significant imbalance between read and write operations.

## Core entities

- Original Url
- Short Url
- User

## The API

It's usually a one-to-one mapping with functional requirements (although sometimes you'll have 2 or 3 endpoints for the same requirement, or maybe no endpoint for a requirement).

- POST to /urls endpoint that takes in the long URL and optional custom alias and expiration date, and returns the shortened URL.
- GET {shorturl} endpoint that takes in the short code and redirects the user to the original long URL.

## High-level design

These should satisfy the core functional requirements. You should go to each of the endpoints and design the system necessary to satisfy it.

Assuming a classic design pattern, the layers here would be:

![Client <-> Primary server <-> Database](https://www.plantuml.com/plantuml/dpng/BOv12i9034NtESMd-zv0fA0745mM5sadQc0wXKmKlBrZd5KUUI-GgJgRlxPCMUuEts7wU3gIcgokUo6QJrcb-0rSiGRIV37Ts3wuYht5GjL0IkoyS1KCvqx3pwQ5w7-CmpWUUmtgCtQjcQIazimN)

### POST to /urls endpoint (create a short url)

To obtain a short url from a long url, the client uses a POST to /urls endpoint, and the primary server creates the short url and saves it to the database, then returns it to the user.

`Url table`:

- shortUrl/customAlias?
- longUrl
- expirationTime?
- creationTime
- userId

`User table`:

- userId
- email
- passwordHash

If the user gives us a custom alias, the server first checks the database to know if the custom alias exists. If not, it adds a new row where the short url is the custom alias.

### GET {shorturl} endpoint (redirect to original url)

The client will GET a short url and the primary server will query the database based on the short url and return the long url to the user with a 302 redirect status code, which is temporary redirect, instead of the 301 redirect which is permanent and would mean it will be cached in a way that it avoids going to our servers to get the long url (and that way it prevents us from adding analytics, logging or some kind of internal tracking if we wanted to).

If there's an expiration time, it also needs to be taken into account when redirecting the user to the original url. If the current time is greater than expiration time, return an error instead of a 302.

## Deep dives

In this section, we expand the design to support the non-functional requirements. And we will go into details about how the primary server will be implemented.

So we aim to make the url shortening:

- fast
- unique
- short (the slug should be around 5-7 characters, unless the interviewer specifies a number)

### Short url uniqueness

1. Prefix of the long url: take the first N characters of the long url and append it to our shortener's base url. Obviously it will lead to many collisions and it's a terrible solution.

2. Random number generator. Since we need to support 1 000 000 000 urls, the random number should be at least between 1 and 1 000 000 000, or we could also say it as being between 1 and 10^9. This means the number will be 10 characters, so it exceeds our slug limit of 5 to 7. So, in order to compact that number, we can encode it using base62 (characters 0-9, A-Z, a-z). Assuming a slug length of 6 and a base62 encoding, 62^6 = 56 800 235 584 possible urls. Now, we need to know the chance of collision when calculating, 1 000 000 000 times, a number between 0 and 62^6. This is a case of the "birthday problem", where the chances are actually quite higher than what we intuitively think, so applying the "birthday problem" formula we get that there is a change for 800 000 collisions. In order to address this, we can check for collision first, which introduces an extra "read" to the database (1- we generate the random number, 2- encode it using base62, 3- read the database to see if the short url already exists, 4- if it doesn't exist, then create the new short url).

3. Hash the long url using md5, sha256, etc.: after getting the hash, we still need to compact this into a 6-character slug, so we encode with base62. Since hash functions are deterministic (the same long url will return the same hashed result), the chance for collision is the same as with a random number generator so we also need an extra database read.

4. Counter: avoids the extra database read. We should still encode into base62, but the counter starts from 0 and it increases with each generated url so it avoids collisions. Getting a counter is fast, it guarantees uniqueness and the slug will be short. Flaw: predictability, as anyone could incrementally check our urls and scrap to find all urls we've generated and their mapped long urls (we could include a warning to users or implement some rate limit to prevent scraping). To avoid it: use a bijective function library, like [sqids.org](https://sqids.org/), which takes a number and generates an ID.

### Low latency on redirects

![Client <-> Primary server <-> Database](https://www.plantuml.com/plantuml/dpng/BOv12i9034NtESMd-zv0fA0745mM5sadQc0wXKmKlBrZd5KUUI-GgJgRlxPCMUuEts7wU3gIcgokUo6QJrcb-0rSiGRIV37Ts3wuYht5GjL0IkoyS1KCvqx3pwQ5w7-CmpWUUmtgCtQjcQIazimN)

The usual path is: the client makes a request from the primary server, the primary server looks up the short url in the database, the database returns the long url and the long url is returned to the client.

The database lookup is the most expensive part, time-wise. Without any indexing, we could end up with 1 000 000 000 rows in the `Url` table, and lookups could end up having to read all the rows in the worst case, which is too expensive.

- Index: a primary key ensures an index is built on that column (this index is a pointer in memory, that leads to a disk location), so it will make things faster as lookups will happen in memory (usually in some B-tree structure) and then only go to disk once, to find the long url. We can add a hashed index of the short url, making the lookup an `O(1)` operation but this isn't necessary, given that B-trees are usually quite optimized.

- Cache: avoids going to the disk altogether. We can use any in-memory cache, like Redis. It allows to go to the cache first and if the short url is not there, then we read the database and update the cache. It will work with a "least recently used" (LRU) eviction policy, which removes the least used items from a cache to make room for new items. Caches like this are usually implemented as key-value pairs, where the key is the short url and the value is the long url.

![Primary server lookups are done in both the DB and cache](https://www.plantuml.com/plantuml/dpng/JOqn2iCm34LtdK9apmrA2QG7A7HCEoYssXWS6sIvqDiNMnaowV7-vsigIg9rZn3vhQWP9NmsHH-4dOQS09Ov1axwGYheEi9obx2J_F31ycLv9dIv9cNffT8bU59QgJ0EjuE6blhrbCpaDjEk4O0zmCiuNeyT1Blch7OoRM_YvEiU_m00)

- Another option would be to cache in a CDN to make it faster to users depending on their geographic location. The problem here is that it will hit the CDN directly, avoiding our primary server. This can cause the same as the 302 vs 301 status code problem, as it would always go to the CDN. So instead of involving a CDN we could just go ahead and return a 301 status code to avoid hitting our server every time.

### Scale to support 100 million daily active users and 1 000 000 000 urls

We should go left to right in our system and see where are the bottlenecks.

1. Primary server:

Assuming each one of the 100 million (or 10^8) active users makes 1 redirect in average, and that there's around 100 000 (or 10^5) seconds in a day. So: `10^8 / 10^5 = 10^3`. 10^3 = 1000, so we have 1000 requests per second. Since it probably won't be evenly distributed, we'll have peaks where we might want to handle 10 000 or 100 000 requests per second. A T3 medium instance in EC2 can handle around 1000 concurrent requests, so one instance won't be good, since we need 10k or 100k requests at peak time. 2 options:

- Vertical scaling: making the primary server bigger (use something bigger than a T3 medium).
- Horizontal scaling: adding more small instances.

We need to consider there will be much more GET requests than POST requests, so we could evolve into a microservices architecture, with 2 separate servers, each one scaling horizontally: a read service and a write service. We'll also need an API gateway as an entry point. The read service will read both from the cache and the database, while the write service will write to the database only.

![We include a gateway, a read service that connects to the cache and the db, and a write service that connects to the db](https://www.plantuml.com/plantuml/dpng/LP312i8m38RlUOgm-zv0P391l8aN3yL3riQjC3TeeyEtTssSkaluylqf2QsYK937lOUURu8oOl1j9-XyO2j-701CtNiUv8eKqMO4ipiTyK324xsoRvN1d9aSHWvFRpadOHPWBi4Bh_oK33WIQYWo5liF5IbqpLzIa-rKsqG0UWHkodAxB4PO85bTcegs-kxNksOzhhVa-XseU73pjxm1)

We could scale the read service horizontally and have less servers for writing.

As a downside, having separate microservices makes it harder to maintain and might be an overkill. We don't need separate microservices in order to scale read operations separately from write operations, and modern cloud services handle this for you automatically, since you can configure it to start a new instance if a specific CPU or memory limit is reached. A load balancer will handle the routing to the different instances.

Potential problem: if we implement the counter logic to create the short urls, and we have more than 1 instances of our write service, we need to make the counter global, since it would be very hard to keep instances in sync to avoid number collisions. So we could add a Redis server to hold our global counter. Since Redis is single thread, there won't be concurrency issues. But this adds a new read operation every time we create a new short url, so to avoid that we could say that each write service requests 1000 numbers from the counter and keeps them in memory for future use, and when it runs out of numbers, it will request another 1000, thus reducing the number of times it should read from the counter service.

2. Database:

How much space the Url table needs:

- shortUrl: around 8 bytes
- optional custom alias: around 100 bytes
- longUrl: around 100 bytes
- expirationTime: 8 bytes
- creationTime: 8 bytes
Rounding it up: 500 bytes (assuming we might want to add more stuff).

500 bytes * 1 000 000 000 rows = 500 GB.

This means we could have a single instance of our database, since modern SSDs have terabytes of space. If we had the need of adding more database instances and we had N instances, in order to decide where to store each short url we'd do the short url modulo N.

3. High availability:

- If the read cache goes down it will just make things slower for a while (since we can still read everything from the database) but it won't be a problem.
- If the server holding the global counter goes down, it will be a problem, so we need redundancy. We can also store snapshots periodically to disk. Redis has a high availability mode so in case we have a separate server for the counter, we can use this.
- Database: we could have some redundancy as well and also take snapshots every hour or so and store them in S3. If the main db goes down, we can point to the replica for a while.
