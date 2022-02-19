# Elasticache 
- To get managed Redis/Memcached
- In-memory databses with really high-performance, low latency
- Using elasticache involves heavy application code changes
- Must have an invalidation strategy to ensure only the most recent data is used

### Redis vs Memcached
#### Redis
- Multi-AZ with auto-failover
- Read replicas to scale and have high availability
- Data durability using AOF persistance
- Back-up & restore features
#### Memcached
- Multi-node for partitioning of data (sharding)
- No high availability (replication)
- Non persistent
- No back-up and restore
- Multi-threaded architecture

### Cache Security
#### All caches in Elasticache:
- Do not support IAM authentication
- IAM policies on Elasticache are only used for AWS API-level security
#### Redis AUTH
- Can set a 'password/token' when you create a Redis cluster
- Extra level of security for your cache (on top of the security group)
- Supports SSL in-flight encryption
#### Memcached
- Supports SASL-based authentication

### Redis (cluster mode disabled)
- One primary node, up to 5 replicas
- Asynchronous replication
- The primary node is used for read & write
- Other nodes are read-only
- One shard, all nodes have all the data
- Guard against data if node failure
- Multi-AZ enabled by default for failover
- Helpful to scale read performance

### Redis (cluster mode enabled)
- Data is partitioned across shards (help to scale writes)
- Each shard has a primary and up to 5 replica nodes
- Multi-AZ capacity
- Up to 500 nodes per cluster
- 500 shards with single master
- 250 shards with 1 master & 1 replica

### Cache implementation considerations
- Data may be out of date (eventually consistent)
- Which caching design is most appropriate?
#### Is caching effective for that data?
- Pattern: data changing slowly, few keys are frequently needed
- Anti-patterns: data changing rapidly, all large key space frequently needed
#### Is data structured well for caching?
- eg. key value caching, or caching of aggregation results

### Lazy-loading/cache-aside/Lazy population
#### Pros
- Only requested data is cached (cache not filled with unused data)
- Node failures are not fatal (just increased latency to warm the cache)
#### Cons
- Cache miss penalty that results in 3 round trips, noticable delay for that request
- Stale data: data that can be updated in the database and outdated in the cache

### Write-through
- Add or delete data when the database is updated
#### Pros
- Data in cache is never stale, reads are quick
- Write penalty vs read penalty (each write requires 2 calls)
#### Cons
- Missing data until it is added/updated in the database (migration is to also implement Lazy Loading strategy)
- Cache churn - a lot of data in the cache will never be accessed

### Cache evictions and TTL (time-to-live)
#### Cache evictions can occur in 3 ways:
- You delete the item explicity
- Item is eveicted because the memoery if full and it is not recently used (LRV)
- You set a time-to-live (TTL)
#### TTL
- Is helpful for: leaderboard, comments, activity streams
- Can range from a few seconds to hours to days
- If too many evictions happen due to memory, you should scale up or out.