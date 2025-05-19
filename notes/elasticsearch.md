# ElasticSearch Notes

ElasticSearch is a powerful search engine that can:

1. Store data  
2. Search data  
3. Analyze massive volumes of data  

---

## 1. Basic Concepts

### Index  
- As a **verb**, "to index" means to store data, similar to `INSERT` in MySQL.  
- As a **noun**, an **index** is like a **database** in MySQL — it's a logical namespace that contains documents.

### Type (deprecated in newer versions)  
- A **type** is similar to a **table** in MySQL.
- In recent versions of ElasticSearch, the concept of "type" has been deprecated, and an index is expected to hold documents of a single type.

### Document  
- A **document** is a unit of data stored in an index.  
- It is stored in JSON format.  
- Each document represents a single record (e.g., a blog post, user profile, etc.).

### Inverted Index  
- ElasticSearch uses an **inverted index** for efficient full-text search.  
- **Tokenization**: Sentences are broken into words (tokens) during indexing (also known as "analyzing" or "text analysis").  
- During a search query, ElasticSearch retrieves all documents containing relevant tokens and calculates a **relevance score** for each result.

## 2. Tools and Clients

### Kibana
- **Kibana** is the visualization tool for ElasticSearch.
- It is mainly used for **visualizing data** and **testing queries**.
- If you don’t use Kibana, you can also send HTTP requests directly to ElasticSearch using tools like **Postman**.

### ElasticSearch Client & Java REST Clients
- In a **Spring Boot** project, you can include ElasticSearch and its client via `pom.xml`.
- The **ElasticSearch client** acts as a bridge that allows your Spring Boot application to interact with an ElasticSearch cluster.
- It enables operations such as creating indices, indexing documents, searching, and aggregations.

---

## 3. Search Flow (in Java using the ES client)

1. **Create a Search Request**  
   ```java
   SearchRequest searchRequest = new SearchRequest();
   ```

2. **Execute the Search**

Use the client to send the search request and receive a response.

3. **Analyze the Result**

Examine the structure of the search result (response object).

4. **Retrieve All Matched Data**

The search hits contain all matched documents in JSON format.

```java
SearchHits hits = response.getHits();
```

5. **Parse Data into Class Objects**

Convert each hit into a string or your domain class.

```java
for (SearchHit hit : hits) {
    String json = hit.getSourceAsString();
    // Deserialize JSON to object
}
```

6. Access Aggregation Results (if any)

If the search includes aggregations, access them like this:

```java
Aggregations aggs = response.getAggregations();
```
