# Caching in the "Alexandrite" System

## 1. Which Parts of the System Should Be Cached?

Based on the analysis of the "Alexandrite" system and its C4 diagram, the following areas have been identified as benefiting most from caching:

- **Reading the order list:** Frequently performed by both customers (via Shop API) and operators (via CRM and MES APIs). Since the order list is not updated every second, caching will reduce database load and improve response times for users.
- **Order cost calculation (MES API):** A complex and resource-intensive operation, especially when a customer uploads detailed 3D models for calculations. Caching calculation results for repetitive orders (or similar models) can significantly reduce system load.

---

## 2. Motivation

### Problems Solved by Caching:
- **High Database Load:**  
  As "Alexandrite" grows, the number of orders increases monthly. Database queries for reading order lists and statuses create high load, potentially slowing the system and reducing performance.

- **Slow Response Times for Non-Instantaneous Updates:**  
  Operations like reading order statuses or calculating costs are performed frequently, but the data rarely changes. Caching these operations will speed up the system for both customers and operators.

### System Elements to Cache:
- **Order list retrieval (Shop API, CRM API, MES API):**  
  Caching will reduce database queries and speed up order-related requests.
- **Order cost calculation (MES API):**  
  Caching results for identical or similar orders will save computation time and reduce resource usage.

---

## 3. Proposed Solution

### Server-Side Caching:
Server-side caching is preferred because it provides centralized control and management over cached data, which is crucial for a high-volume system with frequently changing data.

### Caching Pattern: Cache-Aside

Cache-Aside is chosen for its flexibility and controlled data management:

#### Cache-Aside:
- **How it Works:** Data is first checked in the cache. If available, it is returned directly. If not, data is fetched from the database and then stored in the cache for future requests.
- **Advantages:** Caches only frequently accessed data, reducing database load and speeding up requests.
- **Disadvantages:** May serve stale data if the cache invalidation strategy is not properly implemented.

#### Why Other Patterns Are Not Suitable:
1. **Write-Through:**  
   - Automatically updates the cache whenever the data changes, which can be excessive for a system with infrequent updates.
   - Can lead to unnecessary cache overhead.
2. **Refresh-Ahead:**  
   - Updates data in the cache proactively before it is requested, which is inefficient for systems with irregular request patterns.

---

## 4. Cache Invalidation Strategy

The "Alexandrite" system will use a combination of key-based invalidation and time-based invalidation (TTL):

1. **Key-Based Invalidation:**
   - When an order status changes or the order list is updated, the corresponding cache entries are immediately **invalidated**.
   - Ensures the cache holds accurate data and prevents stale data from being served to clients.

2. **Time-To-Live (TTL) Invalidation:**
   - For frequently accessed data (e.g., order lists or statuses), set a TTL of 5-10 minutes.
   - Allows periodic updates even if changes in the system are infrequent.

---

### Why Other Strategies Are Not Suitable:
- **Programmatic Invalidation:**  
  Difficult to implement as it requires tracking all operations that might affect cached data.
- **Full Cache Invalidation:**  
  Inefficient as it frequently clears the cache, negating caching benefits.

---

## Sequence Diagram for Caching Operations

![Sequence Diagram](sequence_diagram.png)

1. **Cache Miss:**  
   - User requests data from the API.  
   - API checks the cache; if data is not present, the request goes to the database.  
   - Data is retrieved from the database and stored in the cache for future use.  

2. **Cache Hit:**  
   - User requests data.  
   - API retrieves the data directly from the cache, skipping the database.  

---

## Benefits of Caching in the "Alexandrite" System:
- **Improved Performance:** Reduced load on the database and faster response times for users.
- **Optimized Resource Usage:** Caching computation-heavy operations like order cost calculations decreases overall system load.
- **Scalability:** Supports growing order volumes and user requests without overloading the database.
