Below is a **professional interview-preparation guide for Elasticsearch + Kibana**. Elasticsearch is a distributed search, analytics, and vector database built on Apache Lucene, used to index, search, store, and analyze data in near real time. Kibana is the UI layer used to query, analyze, visualize, dashboard, alert, and manage Elasticsearch data. ([Elastic][1])

---

# Big Picture: Elasticsearch + Kibana

```text
Application / Log Source
        |
        |  JSON documents / logs / events
        v
Elasticsearch
  - Indexing
  - Searching
  - Aggregations
  - Sharding / Replication
  - Near real-time search
        |
        v
Kibana
  - Discover
  - Dashboards
  - Visualizations
  - Dev Tools
  - Alerts
  - Observability
```

Example use cases:

```text
E-commerce search       -> Search products by name, category, price
Application logs        -> Search error logs, trace issues
Monitoring dashboards   -> CPU, memory, API latency
Security analysis       -> Suspicious login detection
Business analytics      -> Sales, revenue, user activity
```

---

# Top 50 Elasticsearch + Kibana Interview Questions

## 1. What is Elasticsearch?

**Answer:**
Elasticsearch is a distributed search and analytics engine. It stores data as JSON documents and allows fast search, filtering, sorting, and aggregation.

Example:

```json
{
  "id": 101,
  "name": "iPhone 15",
  "category": "mobile",
  "price": 79999
}
```

Instead of searching this data using SQL like:

```sql
SELECT * FROM products WHERE name LIKE '%iphone%';
```

Elasticsearch uses search queries optimized for full-text search.

---

## 2. What is Kibana?

**Answer:**
Kibana is the visualization and management UI for Elasticsearch. You use Kibana to:

```text
1. Search documents using Discover
2. Create dashboards
3. Create charts and graphs
4. Run Elasticsearch APIs from Dev Tools
5. Configure data views
6. Create alerts
7. Monitor logs, metrics, and traces
```

Kibana works on top of Elasticsearch data. Without Elasticsearch data, Kibana has nothing meaningful to visualize. Kibana supports Discover, dashboards, alerts, maps, ML-based anomaly detection, and Dev Tools for API testing. ([Elastic][2])

---

## 3. What is the difference between Elasticsearch and Kibana?

**Answer:**

| Elasticsearch                  | Kibana                                  |
| ------------------------------ | --------------------------------------- |
| Search and analytics engine    | UI for Elasticsearch                    |
| Stores JSON documents          | Displays and visualizes data            |
| Provides REST APIs             | Uses Elasticsearch APIs internally      |
| Handles indexing and searching | Handles dashboards, Discover, Dev Tools |
| Backend engine                 | Frontend/management layer               |

Simple answer:

```text
Elasticsearch stores and searches the data.
Kibana helps humans explore and visualize that data.
```

---

## 4. What is ELK Stack?

**Answer:**
ELK traditionally means:

```text
E -> Elasticsearch
L -> Logstash
K -> Kibana
```

Now people often say **Elastic Stack**, which may include:

```text
Elasticsearch
Kibana
Logstash
Beats
Elastic Agent
APM
Fleet
```

Common flow:

```text
Application logs -> Logstash/Filebeat -> Elasticsearch -> Kibana Dashboard
```

---

## 5. What is an index in Elasticsearch?

**Answer:**
An index is a logical container of documents. It is similar to a table in a relational database, but not exactly the same.

Example:

```text
products index
orders index
users index
application-logs index
```

Example document inside `products` index:

```json
{
  "name": "Samsung TV",
  "price": 45000,
  "brand": "Samsung"
}
```

---

## 6. What is a document in Elasticsearch?

**Answer:**
A document is a JSON object stored inside an index.

Example:

```json
{
  "userId": 1,
  "name": "Danish",
  "role": "Java Developer",
  "experience": 7
}
```

In RDBMS terms:

```text
Index    -> Table-like concept
Document -> Row-like concept
Field    -> Column-like concept
```

But Elasticsearch is document-oriented, not relational.

---

## 7. What is a field in Elasticsearch?

**Answer:**
A field is one property inside a document.

Example:

```json
{
  "name": "MacBook Pro",
  "price": 180000,
  "available": true
}
```

Here:

```text
name      -> field
price     -> field
available -> field
```

Each field has a type such as `text`, `keyword`, `integer`, `date`, `boolean`, `object`, etc. Elasticsearch field types define how data is stored and searched. For example, `text` is analyzed for full-text search, while `keyword` is used as exact value for filtering, sorting, and aggregation. ([Elastic][3])

---

## 8. What is a shard?

**Answer:**
A shard is a smaller physical part of an index. Elasticsearch splits an index into shards so data can be distributed across nodes.

Example:

```text
products index
  -> shard 0
  -> shard 1
  -> shard 2
```

Why shards are useful:

```text
1. Distribute data across nodes
2. Improve scalability
3. Improve parallel search
4. Handle large data volumes
```

Primary shard count is configured at index creation time, and the default primary shard count is `1` in current Elasticsearch docs. ([Elastic][4])

---

## 9. What is a replica shard?

**Answer:**
A replica shard is a copy of a primary shard.

Example:

```text
Primary shard 0  -> Replica shard 0
Primary shard 1  -> Replica shard 1
```

Benefits:

```text
1. High availability
2. Fault tolerance
3. Better read performance
```

If one node goes down, Elasticsearch can use replica shards to continue serving data.

Replica count is a dynamic index setting. The default number of replicas is `1`, and setting replicas to `0` can risk availability or data loss during failures. ([Elastic][4])

---

## 10. What is a node?

**Answer:**
A node is a single running instance of Elasticsearch.

Example:

```text
Node 1
Node 2
Node 3
```

A group of nodes forms a cluster.

Different node roles:

```text
Master-eligible node -> Manages cluster metadata
Data node            -> Stores data and handles search/indexing
Ingest node          -> Runs ingest pipelines
Coordinating node    -> Routes requests
ML node              -> Machine learning jobs
```

---

## 11. What is an Elasticsearch cluster?

**Answer:**
A cluster is a group of Elasticsearch nodes working together.

Example:

```text
Cluster: production-search-cluster

Node 1 -> data
Node 2 -> data
Node 3 -> master
```

Benefits:

```text
1. Horizontal scalability
2. High availability
3. Load distribution
4. Fault tolerance
```

---

## 12. What is near real-time search?

**Answer:**
Elasticsearch is called **near real-time** because newly indexed documents are not always searchable immediately. Usually they become searchable after a refresh cycle.

Example:

```text
Document indexed at 10:00:00
Available for search around 10:00:01
```

Interview answer:

```text
Elasticsearch does not guarantee immediate visibility like a normal database commit.
It refreshes segments periodically, so documents become searchable after a small delay.
```

---

## 13. What is mapping in Elasticsearch?

**Answer:**
Mapping defines the structure and data types of fields in an index.

Example:

```json
PUT products
{
  "mappings": {
    "properties": {
      "name": {
        "type": "text"
      },
      "brand": {
        "type": "keyword"
      },
      "price": {
        "type": "double"
      },
      "createdAt": {
        "type": "date"
      }
    }
  }
}
```

Mapping is similar to schema in SQL, but Elasticsearch can also create mappings dynamically.

---

## 14. What is dynamic mapping?

**Answer:**
Dynamic mapping means Elasticsearch automatically detects field types when you insert documents.

Example:

```json
{
  "name": "Laptop",
  "price": 50000,
  "available": true
}
```

Elasticsearch may infer:

```text
name      -> text + keyword
price     -> long
available -> boolean
```

But in production, explicit mapping is better because wrong field types can create search and aggregation issues.

---

## 15. Difference between `text` and `keyword` field?

**Answer:**

| `text`                              | `keyword`                     |
| ----------------------------------- | ----------------------------- |
| Used for full-text search           | Used for exact match          |
| Analyzed/tokenized                  | Not analyzed                  |
| Good for descriptions               | Good for ID, status, category |
| Example: product name, blog content | Example: email, city, status  |

Example:

```json
{
  "title": "Spring Boot Interview Questions",
  "status": "PUBLISHED"
}
```

Recommended mapping:

```json
"title": { "type": "text" },
"status": { "type": "keyword" }
```

Use `text` when searching words inside a sentence. Use `keyword` when filtering, sorting, or aggregating exact values. This matches Elastic’s distinction that `text` is analyzed for full-text search and `keyword` is stored as-is for filtering/sorting. ([Elastic][3])

---

## 16. What is an analyzer?

**Answer:**
An analyzer converts text into searchable tokens.

Example:

```text
Input: "Spring Boot Developer"
Tokens: ["spring", "boot", "developer"]
```

Analyzer usually has:

```text
Character filters
Tokenizer
Token filters
```

Example:

```json
"name": {
  "type": "text",
  "analyzer": "standard"
}
```

Analyzer matters for full-text search. If analyzer is wrong, search results may be poor.

---

## 17. What is inverted index?

**Answer:**
An inverted index is the internal data structure used by Elasticsearch/Lucene for fast search.

Normal document:

```text
Doc 1: Java Spring Boot
Doc 2: Java Microservices
Doc 3: Python Django
```

Inverted index:

```text
Java          -> Doc 1, Doc 2
Spring        -> Doc 1
Boot          -> Doc 1
Microservices -> Doc 2
Python        -> Doc 3
```

This is why search is fast. Instead of scanning every document, Elasticsearch directly finds matching document IDs.

---

## 18. What is Query DSL?

**Answer:**
Query DSL is Elasticsearch’s JSON-based query language.

Example:

```json
GET products/_search
{
  "query": {
    "match": {
      "name": "iphone"
    }
  }
}
```

Query DSL supports:

```text
match
term
range
bool
exists
wildcard
prefix
nested
aggregation queries
```

---

## 19. Difference between `match` and `term` query?

**Answer:**

| `match` query             | `term` query                                |
| ------------------------- | ------------------------------------------- |
| Used for full-text search | Used for exact match                        |
| Analyzes input            | Does not analyze input                      |
| Good for `text` fields    | Good for `keyword`, numeric, boolean fields |

Example `match`:

```json
GET products/_search
{
  "query": {
    "match": {
      "name": "spring boot"
    }
  }
}
```

Example `term`:

```json
GET products/_search
{
  "query": {
    "term": {
      "status": "ACTIVE"
    }
  }
}
```

Interview line:

```text
Use match for analyzed text.
Use term for exact values.
```

---

## 20. What is bool query?

**Answer:**
A bool query combines multiple conditions.

Example:

```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "name": "laptop" } }
      ],
      "filter": [
        { "term": { "brand": "Dell" } },
        { "range": { "price": { "lte": 80000 } } }
      ],
      "must_not": [
        { "term": { "status": "OUT_OF_STOCK" } }
      ]
    }
  }
}
```

Common clauses:

```text
must      -> condition should match and affects score
filter    -> condition should match but does not affect score
should    -> optional matching, improves score
must_not  -> must not match
```

Elastic docs explain that `filter` and `must_not` run in filter context, where scoring is ignored and clauses can be considered for caching. ([Elastic][5])

---

## 21. Difference between query context and filter context?

**Answer:**

| Query context                    | Filter context            |
| -------------------------------- | ------------------------- |
| Calculates relevance score       | Does not calculate score  |
| Used for full-text search        | Used for yes/no filtering |
| Slower than filter               | Faster and cache-friendly |
| Example: search “java developer” | Example: status = ACTIVE  |

Example:

```json
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "description": "java developer"
        }
      },
      "filter": {
        "term": {
          "location": "Pune"
        }
      }
    }
  }
}
```

Use `filter` for fixed conditions like status, category, date range, tenant ID, etc. Filter context avoids score calculation and is more resource-efficient for exact yes/no conditions. ([Elastic][6])

---

## 22. What is relevance score?

**Answer:**
Relevance score tells how well a document matches a query.

Example:

```text
Search: "spring boot microservices"
```

Document A:

```text
Spring Boot Microservices Guide
```

Document B:

```text
Java Programming Basics
```

Document A gets a higher score.

Score is stored in:

```json
"_score": 3.45
```

Higher score means better match.

---

## 23. What is aggregation in Elasticsearch?

**Answer:**
Aggregation is used for analytics and summary calculations.

Examples:

```text
Average product price
Count orders by status
Maximum salary
Sales by city
Logs count by error type
```

Example:

```json
GET orders/_search
{
  "size": 0,
  "aggs": {
    "orders_by_status": {
      "terms": {
        "field": "status"
      }
    }
  }
}
```

Elastic docs define aggregations as summaries of data for metrics, statistics, and analytics, and organize them into categories. ([Elastic][7])

---

## 24. Types of aggregations?

**Answer:**

Main types:

```text
1. Metric aggregation
2. Bucket aggregation
3. Pipeline aggregation
```

Example metric aggregation:

```json
"avg_price": {
  "avg": {
    "field": "price"
  }
}
```

Example bucket aggregation:

```json
"by_category": {
  "terms": {
    "field": "category"
  }
}
```

Simple explanation:

```text
Metric aggregation calculates values.
Bucket aggregation groups documents.
Pipeline aggregation works on output of other aggregations.
```

---

## 25. What is `size: 0` in aggregation query?

**Answer:**
`size: 0` means we do not want actual documents in response. We only want aggregation result.

Example:

```json
GET products/_search
{
  "size": 0,
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```

This reduces response size and improves performance when only analytics result is required.

---

## 26. How does pagination work in Elasticsearch?

**Answer:**
Basic pagination uses `from` and `size`.

Example:

```json
GET products/_search
{
  "from": 0,
  "size": 10,
  "query": {
    "match_all": {}
  }
}
```

For page 2:

```json
{
  "from": 10,
  "size": 10
}
```

But deep pagination is expensive because Elasticsearch has to collect and sort many documents. For large pagination, prefer:

```text
search_after
scroll API for batch processing
point in time + search_after
```

Interview line:

```text
from + size is okay for small pagination.
For deep pagination, use search_after.
```

---

## 27. What is sorting in Elasticsearch?

**Answer:**
Sorting orders search results by a field.

Example:

```json
GET products/_search
{
  "query": {
    "match": {
      "name": "phone"
    }
  },
  "sort": [
    {
      "price": {
        "order": "asc"
      }
    }
  ]
}
```

Important point:

```text
Sort on keyword, numeric, date fields.
Avoid sorting on analyzed text fields.
```

---

## 28. What is highlighting?

**Answer:**
Highlighting shows matched words in search result.

Example query:

```json
GET posts/_search
{
  "query": {
    "match": {
      "content": "spring boot security"
    }
  },
  "highlight": {
    "fields": {
      "content": {}
    }
  }
}
```

Response may contain:

```html
<em>Spring Boot</em> Security
```

Use case:

```text
Search result page where matched words are highlighted.
```

---

## 29. What is alias in Elasticsearch?

**Answer:**
An alias is an alternate name for one or more indices.

Example:

```text
products-v1
products-v2

Alias:
products
```

Application queries:

```text
products
```

Internally alias may point to:

```text
products-v2
```

Benefits:

```text
1. Zero-downtime reindexing
2. Versioned indices
3. Easier index switching
4. Read/write separation
```

---

## 30. What is reindexing?

**Answer:**
Reindexing means copying data from one index to another.

Use cases:

```text
1. Change mapping
2. Change analyzer
3. Move data to new index version
4. Clean old fields
```

Example:

```json
POST _reindex
{
  "source": {
    "index": "products-v1"
  },
  "dest": {
    "index": "products-v2"
  }
}
```

Important:

```text
Some mapping changes cannot be applied directly to existing fields.
So we create a new index and reindex data.
```

---

## 31. What is ingest pipeline?

**Answer:**
An ingest pipeline transforms documents before indexing.

Example use cases:

```text
1. Convert field value
2. Remove unwanted field
3. Rename field
4. Parse logs
5. Add metadata
```

Example:

```json
PUT _ingest/pipeline/product_pipeline
{
  "processors": [
    {
      "set": {
        "field": "source",
        "value": "spring-boot-app"
      }
    }
  ]
}
```

Index using pipeline:

```json
POST products/_doc?pipeline=product_pipeline
{
  "name": "Laptop",
  "price": 70000
}
```

Elastic docs describe ingest pipelines as sequential processors that transform incoming documents before Elasticsearch adds them to an index or data stream. ([Elastic][8])

---

## 32. What is ILM in Elasticsearch?

**Answer:**
ILM means **Index Lifecycle Management**. It automates index management for time-based data like logs and metrics.

Common phases:

```text
Hot    -> Active writing/searching
Warm   -> Less frequent search
Cold   -> Rare search
Frozen -> Very rare search
Delete -> Remove old data
```

Example use case:

```text
Keep application logs for 30 days, then delete automatically.
```

ILM helps with rollover, retention, deletion, performance, reliability, and storage cost optimization for time-based indices. ([Elastic][9])

---

## 33. What is rollover?

**Answer:**
Rollover creates a new index when the current index becomes too large or too old.

Example:

```text
logs-000001 -> old write index
logs-000002 -> new write index
```

Rollover conditions:

```text
max_age: 1d
max_size: 50gb
max_docs: 10000000
```

Rollover is commonly used with ILM and data streams. Elastic docs describe rollover as switching the write target to keep indices manageable. ([Elastic][10])

---

## 34. What is a data stream?

**Answer:**
A data stream is used for append-only time-series data.

Use cases:

```text
logs
metrics
traces
security events
```

Instead of manually managing daily indices, data streams manage backing indices internally.

Example:

```text
logs-app-default
  -> .ds-logs-app-default-2026.06.22-000001
  -> .ds-logs-app-default-2026.06.23-000002
```

---

## 35. What is Kibana Discover?

**Answer:**
Discover is a Kibana feature used to search and inspect raw documents.

Use Discover for:

```text
1. Searching logs
2. Filtering by time
3. Viewing document fields
4. Debugging application issues
5. Saving searches
```

Example:

```text
Find all logs where level = ERROR in last 15 minutes
```

Kibana’s Discover uses data views to know which Elasticsearch indices, data streams, or aliases to query.

---

## 36. What is a Kibana data view?

**Answer:**
A data view tells Kibana which Elasticsearch data to use.

Example:

```text
logstash-*
filebeat-*
products
orders-*
```

If your indices are:

```text
app-logs-2026.06.20
app-logs-2026.06.21
app-logs-2026.06.22
```

Then data view can be:

```text
app-logs-*
```

Elastic docs explain that data views can match indices, data streams, and aliases; wildcards like `filebeat-*` can match multiple sources. You can also choose a timestamp field for global time filtering in dashboards. ([Elastic][11])

---

## 37. What is Kibana Lens?

**Answer:**
Kibana Lens is a drag-and-drop visualization tool.

Use it to create:

```text
Bar chart
Line chart
Pie chart
Metric chart
Table
Area chart
```

Example:

```text
Show API error count by service name for the last 24 hours.
```

You select:

```text
Data view: app-logs-*
X-axis: timestamp
Y-axis: count
Breakdown: serviceName
Filter: level = ERROR
```

---

## 38. What is Kibana Dashboard?

**Answer:**
A dashboard is a collection of visualizations.

Example production dashboard:

```text
API latency
Error count
Request count
Top failing endpoints
CPU usage
Memory usage
Database errors
```

Use case for Java backend:

```text
Monitor Spring Boot microservices using logs, metrics, and traces.
```

---

## 39. What is Kibana Dev Tools?

**Answer:**
Dev Tools is a console in Kibana used to execute Elasticsearch REST APIs.

Example:

```json
GET _cluster/health
```

```json
GET products/_search
{
  "query": {
    "match_all": {}
  }
}
```

It is very useful for testing queries, creating mappings, checking index health, and debugging Elasticsearch issues.

---

## 40. What are Kibana filters and KQL?

**Answer:**
KQL means **Kibana Query Language**. It is used in Kibana to filter data.

Examples:

```text
status: "ERROR"
service.name: "user-service"
response.status_code >= 500
message: "NullPointerException"
```

KQL is user-friendly for dashboard filtering and Discover search.

---

## 41. How do you monitor application logs using Elasticsearch and Kibana?

**Answer:**

Typical flow:

```text
Spring Boot App
   |
   | logs
   v
Filebeat / Logstash / Elastic Agent
   |
   v
Elasticsearch
   |
   v
Kibana Dashboard
```

Example log:

```json
{
  "timestamp": "2026-06-22T10:15:00",
  "level": "ERROR",
  "service": "payment-service",
  "message": "Payment failed",
  "traceId": "abc-123"
}
```

In Kibana, you can search:

```text
level: "ERROR" AND service: "payment-service"
```

Interview answer:

```text
We ship structured JSON logs to Elasticsearch and use Kibana Discover and dashboards to debug errors, monitor API health, and analyze service behavior.
```

---

## 42. What is cluster health?

**Answer:**
Cluster health tells the status of the Elasticsearch cluster.

Command:

```json
GET _cluster/health
```

Statuses:

```text
green  -> all primary and replica shards assigned
yellow -> all primary shards assigned, but some replicas missing
red    -> some primary shards are missing
```

Interview explanation:

```text
Green is healthy.
Yellow means data is available but high availability is affected.
Red means some data may be unavailable.
```

---

## 43. How do you check shards?

**Answer:**

Using Kibana Dev Tools:

```json
GET _cat/shards?v
```

Or for a specific index:

```json
GET _cat/shards/products?v
```

This shows:

```text
index
shard
primary/replica
state
docs
store
node
```

Elastic docs note that CAT APIs are meant for human consumption from command line or Kibana console, not for application use. ([Elastic][12])

---

## 44. What are common Elasticsearch performance issues?

**Answer:**

Common issues:

```text
1. Too many shards
2. Very large shards
3. Deep pagination
4. Heavy aggregations
5. Wrong mapping
6. Using wildcard queries on large data
7. Sorting on text fields
8. Insufficient heap memory
9. No ILM for logs
10. High-cardinality aggregations
```

Good practices:

```text
Use proper mapping.
Use keyword fields for exact filters.
Avoid deep pagination.
Use ILM for logs.
Keep shard size reasonable.
Use filters instead of must where scoring is not needed.
Monitor JVM heap and GC.
```

---

## 45. How is security handled in Elasticsearch and Kibana?

**Answer:**
Security includes:

```text
1. Authentication
2. Authorization
3. TLS encryption
4. Role-based access control
5. API keys
6. Audit logging
```

Example:

```text
User A can only view logs.
User B can manage dashboards.
Admin can manage users, roles, and cluster settings.
```

Elastic security features include authentication, authorization, TLS encryption, and related security capabilities. RBAC authorizes users by assigning privileges to roles and roles to users or groups. ([Elastic][13])

---

## 46. How do you connect Java/Spring Boot with Elasticsearch?

**Answer:**
Use the official Elasticsearch Java API Client or Spring Data Elasticsearch.

The official Java API Client provides strongly typed requests/responses, blocking and async APIs, fluent builders, and object-mapper integration. ([Elastic][14])

Maven dependency example:

```xml
<dependency>
    <groupId>co.elastic.clients</groupId>
    <artifactId>elasticsearch-java</artifactId>
    <version>8.x.x</version>
</dependency>
```

Simple Java client setup:

```java
RestClient restClient = RestClient.builder(
        new HttpHost("localhost", 9200)
).build();

ElasticsearchTransport transport = new RestClientTransport(
        restClient,
        new JacksonJsonpMapper()
);

ElasticsearchClient client = new ElasticsearchClient(transport);
```

---

## 47. How do you index a document using Java?

**Answer:**

Product class:

```java
public class Product {
    private String id;
    private String name;
    private String category;
    private double price;

    public Product() {}

    public Product(String id, String name, String category, double price) {
        this.id = id;
        this.name = name;
        this.category = category;
        this.price = price;
    }

    // getters and setters
}
```

Index document:

```java
Product product = new Product(
        "p1001",
        "Samsung Galaxy Phone",
        "mobile",
        45000
);

IndexResponse response = client.index(i -> i
        .index("products")
        .id(product.getId())
        .document(product)
);

System.out.println("Indexed with version: " + response.version());
```

Interview explanation:

```text
In Java, we create a POJO, then use ElasticsearchClient.index() to store it as a JSON document inside an index.
```

Elastic’s Java client supports fluent DSL-style request creation and object mapping for indexing application objects as JSON. ([Elastic][15])

---

## 48. How do you search documents using Java?

**Answer:**

```java
SearchResponse<Product> response = client.search(s -> s
        .index("products")
        .query(q -> q
                .match(m -> m
                        .field("name")
                        .query("phone")
                )
        ),
        Product.class
);

response.hits().hits().forEach(hit -> {
    Product product = hit.source();
    System.out.println(product.getName() + " - " + product.getPrice());
});
```

Interview explanation:

```text
We execute a search request on the products index, apply a match query on the name field, and map the response back to Product objects.
```

Elastic’s Java search documentation explains that indexed documents are available for near real-time search and that search responses contain hits with matched documents and total match information. ([Elastic][16])

---

## 49. How can Elasticsearch be used in a Spring Boot microservice?

**Answer:**

Example architecture:

```text
Product Service
   |
   | save product
   v
MySQL Database
   |
   | publish event
   v
Kafka
   |
   | consume event
   v
Search Service
   |
   | index product
   v
Elasticsearch
   |
   v
Kibana / Search API
```

Why not directly replace DB with Elasticsearch?

```text
Database is source of truth.
Elasticsearch is search/read-optimized store.
```

Example:

```text
MySQL stores product transactionally.
Elasticsearch helps fast search by name, category, filters, price range.
```

Good interview answer:

```text
In microservices, I would keep the relational database as the source of truth and use Elasticsearch as a search projection. Whenever product data changes, I would publish an event and update the Elasticsearch index asynchronously.
```

---

## 50. What are best practices for Elasticsearch in production?

**Answer:**

Important best practices:

```text
1. Define explicit mappings.
2. Use keyword for exact filters and aggregations.
3. Use text for full-text search.
4. Avoid too many shards.
5. Use replicas for high availability.
6. Use ILM for logs and time-series data.
7. Avoid deep pagination with from + size.
8. Use filters where scoring is not required.
9. Monitor cluster health, heap, CPU, disk.
10. Secure Elasticsearch with authentication, RBAC, and TLS.
11. Use aliases for zero-downtime reindexing.
12. Use bulk API for large indexing.
13. Avoid wildcard queries on huge datasets.
14. Use structured JSON logs.
15. Create Kibana dashboards for operational visibility.
```

Strong closing answer:

```text
Elasticsearch should be designed carefully. Wrong mappings, wrong shard count, uncontrolled logs, and expensive queries can create serious production issues.
```

---

# Common Java Backend Interview Scenario

**Question:**
How would you design product search for an e-commerce Spring Boot application?

**Answer:**

```text
1. Product Service stores product data in MySQL.
2. Whenever product is created/updated, publish event to Kafka.
3. Search Service consumes event.
4. Search Service indexes product into Elasticsearch.
5. Customer search API queries Elasticsearch.
6. Kibana dashboard monitors search latency, failed indexing, and error logs.
```

Example search query:

```json
GET products/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "running shoes"
          }
        }
      ],
      "filter": [
        {
          "term": {
            "brand": "Nike"
          }
        },
        {
          "range": {
            "price": {
              "gte": 2000,
              "lte": 8000
            }
          }
        }
      ]
    }
  },
  "sort": [
    {
      "price": {
        "order": "asc"
      }
    }
  ]
}
```

Professional explanation:

```text
I would use match query for full-text product name search and filter query for brand and price because brand and price do not need relevance scoring. This improves performance and keeps search results accurate.
```

---

# Quick Revision Sheet

```text
Elasticsearch -> Search and analytics engine
Kibana        -> UI for search, dashboard, visualization
Index         -> Collection of documents
Document      -> JSON record
Field         -> Property inside document
Shard         -> Physical partition of index
Replica       -> Copy of primary shard
Mapping       -> Field schema
Analyzer      -> Converts text into tokens
text          -> Full-text search
keyword       -> Exact match/filter/sort/aggregation
match query   -> Full-text search
term query    -> Exact match
bool query    -> Combine conditions
filter        -> No scoring, faster
aggregation   -> Analytics/grouping/calculation
ILM           -> Automatic index lifecycle management
Data view     -> Kibana view over indices/data streams/aliases
Discover      -> Explore raw documents
Dashboard     -> Visualize metrics/logs/search data
Dev Tools     -> Run Elasticsearch APIs
```

---

# Interview Tip for You

For your **Java Backend + Spring Boot + Microservices** profile, do not explain Elasticsearch only theoretically. Always connect it with a backend use case:

```text
In my Spring Boot application, I would use MySQL as the source of truth and Elasticsearch for fast search. I would index product/blog/order/searchable data into Elasticsearch and use Kibana to monitor logs, errors, API latency, and search behavior.
```

That answer sounds much more professional than just saying: “Elasticsearch is a search engine.”

[1]: https://www.elastic.co/docs/reference/elasticsearch "Elasticsearch | Elasticsearch Reference"
[2]: https://www.elastic.co/kibana "Kibana: Visualize, explore, and manage data in Elasticsearch | Elastic"
[3]: https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/field-data-types "Field data types | Elasticsearch Reference"
[4]: https://www.elastic.co/docs/reference/elasticsearch/index-settings/index-modules "General index settings | Elasticsearch Reference"
[5]: https://www.elastic.co/docs/reference/query-languages/query-dsl/query-dsl-bool-query?utm_source=chatgpt.com "Boolean query | Elasticsearch Reference"
[6]: https://www.elastic.co/docs/reference/query-languages/query-dsl/query-filter-context?utm_source=chatgpt.com "Query and filter context | Elasticsearch Reference"
[7]: https://www.elastic.co/docs/explore-analyze/query-filter/aggregations "Aggregations | Elastic Docs"
[8]: https://www.elastic.co/docs/manage-data/ingest/transform-enrich/ingest-pipelines?utm_source=chatgpt.com "Elasticsearch ingest pipelines"
[9]: https://www.elastic.co/docs/manage-data/lifecycle/index-lifecycle-management?utm_source=chatgpt.com "Index lifecycle management (ILM) in Elasticsearch"
[10]: https://www.elastic.co/docs/manage-data/lifecycle/index-lifecycle-management/rollover?utm_source=chatgpt.com "Index rollover and routing allocation in Elasticsearch"
[11]: https://www.elastic.co/docs/explore-analyze/find-and-organize/data-views "Data views | Elastic Docs"
[12]: https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-cat-shards "
  Get shard information
 \| Elasticsearch API documentation
"
[13]: https://www.elastic.co/docs/deploy-manage/security?utm_source=chatgpt.com "Security | Elastic Docs"
[14]: https://www.elastic.co/docs/reference/elasticsearch/clients/java "Java | Java"
[15]: https://www.elastic.co/guide/en/elasticsearch/client/java-api-client/8.19/indexing.html?utm_source=chatgpt.com "Indexing single documents | Elasticsearch Java API Client ..."
[16]: https://www.elastic.co/docs/reference/elasticsearch/clients/java/usage/searching "Searching for documents | Java"
