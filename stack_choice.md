# Choice of stack & reasoning

### Front end
#### Next JS
- Assesment form field can be selected in catalyst as per that customer app can dynamically renderdered using the component based architure and the SSR
- In build performnce optimization
- Typesciprt support for type saftey
- Large react community which means plenty of items already available.

### Backend
#### Fast API
- Faster code to production time
- Build in ground up for async APIs
- Supports both GraphQL as well as REST
- SQLAlchemy as ORM for easy coding and code first approach in DB
- Pydantic which makes it more type safe
- Build in swagger to reduce API documentation effort

#### Celery
- Easily integrated with Fast API
- Can work with redis / MQ aync task management
- Worker can be auto scaled
- Batch job option flexibility

#### Apache Airflow
- Directed Acyclic Graph (DAG) allows to define dependencies and execution order clearly, making it easier to manage intricate data pipelines
- Connectors available for postgres, dynamodb & snowflake
- Better scheduling & monitoring capabilities

### AI
#### Google document AI
- Best in class accuracy for OCR
- Great foundation model for entity extraction
- Personally benchmarked it

### Google Dialog flow
- Higly scalable
- Easy to train for this use case with minimum data points
- Better indent capture compare to others.

### Moderation
- Same as dialog flow
- same stack for all AI needs

### Database
#### AWS PostgreSQL RDS
- Managed Service
- Scalability
- High Availability
- Security: RDS provides built-in security features, including encryption at rest and in transit & RLS

#### DynamoDB + DAX
- Fully Managed NoSQL Database: 
- Scalability and Flexibility:
- Global Distribution: DynamoDB offers Global Tables, allowing you to replicate your data across multiple AWS regions for low-latency access and increased availability.
- DAX - Recommend caching for Dynamo DB. No code changes required.

#### Snowflake
- Data Warehouse as a Service: Snowflake provides a fully managed cloud data warehouse solution that simplifies data storage, processing, and analytics, allows to focus on insights rather than infrastructure.
- Separation of Compute and Storage: Snowflake’s architecture separates compute and storage, allows to scale them independently based on your workload, optimizing costs and performance.
- Support for Diverse Data Formats: Snowflake can handle structured and semi-structured data (like JSON, Avro, and Parquet) in a single platform, making it versatile for various data types and sources.
- Concurrent Workloads: Snowflake supports multiple concurrent workloads without performance degradation, enabling teams to run queries and ETL jobs simultaneously.
- Secure and Compliant: Snowflake offers robust security features, including end-to-end encryption, role-based access control, and compliance with various standards (e.g., GDPR, HIPAA), ensuring your data remains secure.

### Logging & Monitoring 
#### Sentry
- Real-Time Error Tracking
- Contextual Information: Sentry captures detailed contextual information about errors, including stack traces, request data, and user actions leading up to the error. This information facilitates faster debugging and resolution.
- Ready made Integrations with Popular Frameworks
- Team Collaboration: With features like alerts, issue assignment, and comments, Sentry enhances team collaboration around error resolution, ensuring that the right team members are notified and involved.

#### Amazon CloudWatch
- Integrated with AWS Services:
- Comprehensive Metrics and Logs
- Automatic Scaling
- Custom Dashboards
- larms and Notifications: You can set up alarms based on specific thresholds, and integrate them with Amazon SNS for notifications, enabling proactive incident response and alerting.

#### Prometheus & Grafana
- For monitoring more complex metrics
- Powerful Visualization:
- Alerts

#### Site 24 * 7
- For call alerts
- Downtime monitoring
- SSL ceritifate expiry monitoring 


### Version control & CI CD, 99.99 uptime
#### GitHub
- Ease of use
- Sophisticated code review tool
- Branch productions

### Github Action
- Native support with GitHub
- Runnign muti jobs in parallel
- Many tools ready to integrate for code qulaity & other security testing.

### DR
- DR setup auto lauch using terraform.