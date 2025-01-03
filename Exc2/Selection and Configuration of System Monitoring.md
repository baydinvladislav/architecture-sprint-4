### Selection and Configuration of System Monitoring

#### 1. **Motivation:**
   System monitoring for "Alexandrite" is essential to enhance reliability, resilience, and rapid response to potential issues. With the increase in order volume, load, and processing delays, the company has faced client complaints and contract losses. Implementing monitoring will help administrators and developers quickly gain insights into system health and pinpoint the causes of failures, enabling timely decision-making. For example:
   - a. **Identify performance issues promptly**, such as slow calculations, message delays between CRM and MES, and API overloads.<br>
   - b. **Respond quickly to incidents**, improving service for external clients (API) and internal users (operators, vendors).<br>
   - c. **Analyze and optimize system performance**, eliminating bottlenecks, enhancing system interaction, and reducing order losses.<br>
   - d. **Maintain system stability** during a linear growth of orders, which is crucial for future scalability.<br>

#### 2. **Monitoring Approach:**
   - a. **For APIs and services: RED (Rate, Errors, Duration).**  
      This will effectively track overall API performance and identify bottlenecks.<br>
      - i. **Rate:** API request processing speed.<br>
      - ii. **Errors:** Percentage of API errors (internal and external errors).<br>
      - iii. **Duration:** Request processing time.<br>
   - b. **For databases and message queues: USE (Utilization, Saturation, Errors).**  
      This approach prevents overloads and delays in queues and databases.<br>
      - i. **Utilization:** Resource usage (CPU, memory, disk usage).<br>
      - ii. **Saturation:** Levels of queue and database saturation.<br>
      - iii. **Errors:** Number of errors during database or queue operations (e.g., RabbitMQ).<br>
   - c. **For the entire system: “The Four Golden Signals” (Latency, Traffic, Errors, Saturation).**<br>
      - i. **Latency:** System response time (e.g., order cost calculation, query execution time).<br>
      - ii. **Traffic:** Volume of data transferred across the system (network load).<br>
      - iii. **Errors:** Percentage of errors across system components (backend, RabbitMQ, API).<br>
      - iv. **Saturation:** Resource usage and saturation (queues, databases, CPU utilization).<br>

#### 3. **System Monitoring Metrics:**

   - a. **API (RED Approach):**

      | Metric Name                  | Purpose                                                             | Labels                                         |
      |------------------------------|----------------------------------------------------------------------|-----------------------------------------------|
      | Rate (Request Speed)         | Evaluates API load and determines when scaling is required.          | Request type (GET, POST), response status (2xx, 4xx, 5xx) |
      | Errors (API Errors)          | Tracks API issues (e.g., 500 or 404 errors).                         | Error code (500, 404, 503), request type       |
      | Duration (Request Time)      | Assesses API performance under high load.                            | Request type, client type (external or internal operator) |

   - b. **Message Queues (USE Approach):**

      | Metric Name                          | Purpose                                                             | Labels                |
      |--------------------------------------|----------------------------------------------------------------------|-----------------------|
      | Utilization (RabbitMQ Queue Load)    | Tracks queue usage to prevent overflows.                            | Queue name            |
      | Saturation (Queue Saturation)        | Measures proximity to overload, crucial for preventing data loss.    | Queue name            |
      | Errors (Message Handling Errors)     | Tracks issues in message delivery between CRM and MES.               | Queue name, message type |

   - c. **Database (USE Approach):**

      | Metric Name                                   | Purpose                                                             | Labels                                  |
      |-----------------------------------------------|----------------------------------------------------------------------|-----------------------------------------|
      | Utilization (PostgreSQL CPU and Disk Usage)   | Tracks database resource usage to assess scaling needs.              | Operation type (INSERT, SELECT), table name |
      | Saturation (Active Connections)              | Identifies issues when connection count approaches limits.            | Database name                         |
      | Errors (Database Operation Errors)           | Tracks critical errors (e.g., locks, transaction failures).           | Operation type, table name            |

   - d. **System-wide Metrics (“The Four Golden Signals”):**

      | Metric Name                 | Purpose                                                             | Labels                |
      |-----------------------------|----------------------------------------------------------------------|-----------------------|
      | Latency (System Delays)     | Tracks response time for core operations (e.g., order cost calculation). | Service, operation     |
      | Traffic (Network Traffic)   | Evaluates network usage, crucial during order and client growth.       | Service, traffic type  |
      | Errors (System Error Rate)  | Monitors the overall error rate across services.                      | Service, error type   |
      | Saturation (Resource Usage) | Tracks resource saturation (CPU, memory) to prevent performance degradation. | Server node, service  |

#### 4. **Action Plan:**
   - a. **Deploy Monitoring System:**<br>
      - i. Set up a time-series database for storing metrics (e.g., Prometheus).<br>
      - ii. Configure Grafana dashboards for metric visualization.<br>
   - b. **Configure API and Database Metrics Collection:**<br>
      - i. Add API and RabbitMQ metrics (using tools like Prometheus Exporter).<br>
      - ii. Enable PostgreSQL performance metrics using `pg_stat_statements`.<br>
   - c. **Set Up Alerts and Notifications:**<br>
      - i. Create Grafana alerts for critical metrics (e.g., errors, resource overloads, latency breaches).<br>
      - ii. Configure notifications via Slack, email, or other channels.<br>
   - d. **Automate Scaling on Threshold Breaches:**<br>
      - i. Set up automatic scaling for APIs and queues when saturation thresholds are exceeded.<br>

#### 5. **Saturation Thresholds:**
   - a. **API Load (Rate):**<br>
      - i. **Threshold:** More than 1000 requests per second.<br>
      - ii. **Action:** Automatically scale API instances.<br>
   - b. **API Delay (Duration):**<br>
      - i. **Threshold:** More than 1 second request processing time.<br>
      - ii. **Action:** Trigger alert and create a developer ticket.<br>
   - c. **RabbitMQ Queue Saturation:**<br>
      - i. **Threshold:** Queue utilization exceeds 80%.<br>
      - ii. **Action:** Add new queues or increase limits for existing ones.<br>
   - d. **Database Load (CPU Utilization):**<br>
      - i. **Threshold:** CPU usage exceeds 85% for more than 5 minutes.<br>
      - ii. **Action:** Add database instances.<br>
