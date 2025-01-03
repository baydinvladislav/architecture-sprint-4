### Analysis, Problem Identification, and Planning

#### 1. **System Problem Analysis:**
   - **<i>Lost or delayed orders for store and API clients:</i>**<br>
     Order losses and delays hinder business growth, driving away customers and partners.
   - **<i>Slow loading of the main order page in MES:</i>**<br>
     Operators face issues with dashboard loading, even with filtering and pagination enabled.
   - **<i>Bug fixes take too long, delaying releases:</i>**<br>
     Bugs discovered during testing take a long time to fix, causing release delays and slowing the delivery of new features.
   - **<i>Slow order processing in MES:</i>**<br>
     Long processing tasks need to be moved to separate services to speed up the process through parallelization.

#### 2. **Initiatives to Address Issues:**
   - Add tracing to identify where orders are lost or delayed during processing.
   - Partition orders by status, enabling operators to query specific partitions for relevant orders.
   - Introduce automated testing to prevent "raw" code from reaching manual testers, reduce time spent on version checks, and minimize manual work. Create automated tests for core scenarios. Hire an additional developer for services with frequent release issues and consider hiring another tester if one cannot handle task switching effectively.
   - Implement an SLA for external API users, monitoring delays and failed calls to prevent partner complaints.
   - Develop a real-time order execution and delay metrics dashboard to identify lost or stuck orders.
   - Split the MES cost calculation process into several stages executed in parallel, offloading long-running tasks to separate services.
   - Use the Outbox pattern to guarantee message delivery (`at least once`) and add idempotency on the consumer side.
   - Utilize Prometheus and Grafana to monitor API performance, page load times, and operator activity.
   - Configure alerts for exceeding normal order processing times.
   - Set up email or push notifications for operators about new orders.
   - Offload lengthy processes to separate services in the MES system.

#### 3. **Target Architecture in Six Months:**
   - Within six months, the bottlenecks causing order losses will have been identified and resolved. Operators will use partitions to quickly access relevant records and promptly process orders upon receiving notifications. The system will include Grafana for monitoring, along with alerts and metrics for order delays.

#### 4. **Top Three Prioritized Initiatives for Six Months:**
   - **Add tracing:** To identify system bottlenecks and begin eliminating them.
   - **Set up alerts for order delays:** To promptly address problematic orders requiring attention.
   - **Implement automated testing:** To accelerate bug fixes and reduce delivery times for new features.
