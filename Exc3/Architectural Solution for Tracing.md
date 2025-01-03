### Architectural Solution for Tracing

#### 1. **System Areas for Tracing:**

   - **Trace spans for loading a 3D order file through the store UI:**  
     `Internet Shop => Shop API => 3D Storage`

   - **Trace spans for calculating order costs:**  
     `3D Storage => CRM API => RabbitMQ => MES API => RabbitMQ => CRM API`

   - **Trace spans for order confirmation:**  
     `MES API => RabbitMQ => CRM API => Shop DB`

   - **Trace spans for order execution:**  
     `MES => MES DB => MES API => RabbitMQ => CRM API => Shop DB`

   **Data to Include in Tracing:**
   - **Trace ID:** A unique identifier linking all spans in a single trace (request chain).
   - **Span ID:** A unique identifier for each specific span (stage).
   - **Parent Span ID:** Identifier of the parent span, if the span is part of a larger request.
   - **Span Name:** A brief description of the operation performed by the span.
   - **Timestamp:** Start and end time of the span.
   - **Duration:** Execution time of the operation (difference between start and end times).
   - **Status:** Execution status (success/failure).
   - **Error Details:** Information about errors, if the operation failed.
   - **Tags/Labels:** Additional data to detail each span (e.g., order ID, user ID, HTTP method, database table).

---

#### 2. **Motivation:**
   - **Why tracing is necessary:**  
     The current system architecture faces issues such as delays, lost orders, and errors during data exchange between components. Tracing will:
     - **Identify bottlenecks:** Show where orders are "stuck" or failing.
     - **Ensure system transparency:** Trace the full journey of an order through all systems, from creation to completion.
     - **Reduce diagnosis and resolution time:** Quickly identify the root causes of problems, reducing downtime and client dissatisfaction.
     - **Maintain SLA and customer satisfaction:** Quickly detect and respond to issues, fostering trust.
     - **Optimize performance:** Detect slow operations and improve overall system efficiency.

   **Key Metrics Impacted by Tracing:**
   - **Average order processing time (business):** Tracing will help identify delays and reduce processing time.
   - **Success rate of orders (business):** Tracing will decrease the number of failed or delayed orders.
   - **Error diagnosis time (technical):** Tracing will shorten the time needed to locate and fix issues.
   - **System load (technical):** Tracing will highlight overloaded components, enabling better load balancing.
   - **Customer trust (business):** Fewer errors and reduced delays will directly improve client satisfaction.

---

#### 3. **Proposed Solution:**

   - **Technology Stack:**
     - **Tracing Framework:** OpenTelemetry for distributed tracing.
     - **Tracing Backend:** Jaeger for trace data visualization and storage.
     - **Instrumentation Tools:**
       - OpenTelemetry SDKs integrated into key services.
       - RabbitMQ, PostgreSQL, and HTTP library instrumentation.
   - **Implementation Strategy:**
     - Generate and propagate `Trace ID` and `Span ID` across services via HTTP headers or RabbitMQ messages.
     - Collect and visualize traces using Jaeger dashboards.

   **Components to be Instrumented:**
   - **Shop API, CRM API, MES API:** Implement OpenTelemetry for tracing data generation and propagation.
   - **RabbitMQ:** Configure tracing for messages between CRM and MES.
   - **Jaeger:** Collect and visualize tracing data.
   - **Prometheus and Grafana:** For metrics and real-time dashboards.

---

#### 4. **Compromises:**
   - **High implementation cost:** Adding tracing to all services requires significant time and team effort, especially for legacy systems.
   - **Increased system load:** Tracing generates additional overhead, requiring optimization to mitigate performance impacts.
   - **Complexity with legacy systems:** Integrating tracing into outdated or proprietary solutions can be challenging and costly.
   - **Limited benefits for low-latency systems:** Tracing may not provide significant value for systems with minimal delays or easily diagnosable issues.

---

#### 5. **Security Considerations:**
   - **Internal Use:**
     - **Authentication and Authorization:** Only employees with active accounts and roles can access the tracing system. Full access is limited to support teams; developers and managers have restricted access.
     - **Multi-Factor Authentication (MFA):** All users must pass MFA to access the tracing system.
     - **Data Encryption:** Trace data must be encrypted in transit and at rest using TLS/SSL.
     - **Sensitive Data Masking:** Ensure no sensitive or personal data is logged. Metadata must be filtered before being stored.
     - **Access Segmentation:** The tracing system is isolated within the corporate network, accessible only via VPN or secure internal channels.
     - **Monitoring and Auditing:** Log all user actions within the tracing system to detect anomalies.

   - **External Use for Partners (Order API):**
     - **OAuth 2.0 Authentication:** Partners authenticate using OAuth 2.0, receiving scoped access tokens.
     - **Role and Data Restrictions:** Partners can only access traces relevant to their requests or transactions. Internal and other partner data remains inaccessible.
     - **Encrypted Communication:** All API communications must be encrypted with TLS/SSL to prevent interception.
     - **API Gateway and Rate Limiting:** An API Gateway will manage access to the tracing system for partners and limit request rates.
