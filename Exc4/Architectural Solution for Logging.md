# Logging and Data Analysis in the System

## 1. **List of Required Logs with INFO Level**
**Logs to collect and their sources:**
- **Internet Store (Shop API):**
  - **INFO:** Log order creation — timestamp, customer ID, order ID, and status.
  - **INFO:** Log 3D file uploads — timestamp, file size, file type, and processing status.
  - **ERROR:** Errors during order creation or file uploads.
- **CRM API:**
  - **INFO:** Log order status changes — timestamp, order ID, old and new statuses.
  - **INFO:** Log data sent to RabbitMQ — timestamp, order ID, message ID.
  - **ERROR:** Errors in data transmission to RabbitMQ.
- **MES API:**
  - **INFO:** Log order cost calculations — start and end timestamps, order ID, total cost, and status.
  - **INFO:** Log order status changes — timestamp, order ID, old and new statuses.
  - **ERROR:** Errors during cost calculations or status changes.
- **RabbitMQ:**
  - **INFO:** Log message transmission between CRM and MES — timestamp, message ID, status (success or failure).
  - **ERROR:** Errors during message processing or transmission.
- **Database (PostgreSQL):**
  - **INFO:** Log query execution — timestamp, query type (SELECT/INSERT/UPDATE), order ID, execution time, and status (success/failure).
  - **ERROR:** Log database access errors (e.g., timeouts, connection failures).

---

## 2. **Motivation**
Logging is essential to ensure system stability, monitor its state, and respond quickly to issues. Logging helps to:
- **Improve problem diagnosis and resolution:** Logs simplify identifying error sources and reduce resolution time.
- **Increase process transparency:** System logs allow tracking order states and other key operations.
- **Reduce downtime:** Logs enable faster response to failures and minimize system downtime.

**Key Metrics Influenced by Logging:**
- **Incident response time (technical):** Logs make it easier and faster to locate issues.
- **Order success rate (business):** Reducing errors improves successful order processing.
- **Execution time for key operations (technical):** Logs provide insights for performance optimization.
- **Error rate in message transmission (technical):** Quickly identify failures in RabbitMQ queues.
- **Customer satisfaction (business):** Faster issue resolution improves the user experience.

**Priority Systems for Logging and Tracing:**
- **Shop API and CRM API:** Highest priority, as they directly handle orders and client interactions.
- **MES API:** Critical for calculations and order processing, requiring comprehensive logging and tracing.

---

## 3. **Proposed Solution**
- **Technologies for Logging Implementation:**  
  Use the ELK stack (Elasticsearch, Logstash, Kibana):
  - **Elasticsearch:** Store logs.
  - **Logstash:** Process and route logs from different services.
  - **Kibana:** Visualize logs and create dashboards.
  - **Fluentd/Filebeat:** Collect and forward logs to Elasticsearch.

- **Log Security Policy:**  
  - **Sensitive Data:** Logs must exclude sensitive customer information (e.g., payment details). Any sensitive data (e.g., phone numbers, addresses) must be masked or excluded.
  - **Access Control:** Only specific roles (administrators, developers, operators) should have full access to logs through Kibana or Elasticsearch.

- **Log Retention Policy:**  
  - Logs will be stored in separate indices for each subsystem (Shop API, CRM API, MES API).
  - Logs will be retained for 30 days and automatically deleted or archived based on importance.
  - Index size limits will be adjusted based on system performance and volume.

---

## 4. **Transforming Log Collection into Log Analysis**
**Steps to Achieve This:**
- **Alerting Based on Logs:**  
  Configure Kibana alerts to detect anomalies, such as a spike in errors or unexpected increases in requests. Alerts will be sent to Slack or incident management systems (e.g., PagerDuty) for immediate response.
- **Anomaly Detection:**  
  Enable log anomaly monitoring (e.g., surges in order volume or errors) using Kibana functionality.

  **Example:**  
  If the system begins generating 10,000 orders per second, this may indicate a DDoS attack or a code issue.

---

## 5. **Criteria for Selecting a Logging Solution**
- **Scalability:**  
  The solution must support log collection from multiple components and scale with increased loads. Elasticsearch handles large data volumes effectively.
- **Search and Filtering:**  
  The solution should enable efficient log searching and filtering using key labels (e.g., order ID). Kibana provides a user-friendly interface for this.
- **Visualization:**  
  The solution should support creating dashboards and visualizing data. Kibana excels at visualization.
- **Security:**  
  Logging must include data protection measures. Elasticsearch supports role-based access control and log encryption.
- **Performance and Resilience:**  
  The solution should be fault-tolerant and ensure stable operation under high loads. The ELK stack meets these requirements.
