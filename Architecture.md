![alt text](image.png)

## Web APP : (Next JS)
There will be two web apps
- Catlayst app (Catalyst team can create the assesments)
- Assesment app (customer will fill the assesment)

All the payload which sends to the server will encrypted using the RSA public key over TLS to make the encryption at transist.
API request will be mostly of graphql
We

## Amazon cognito :
Amazon conginito will be used as authetication and authorization service basically oauth provider with multi tenancy. Seperate user pool for every tenant will be used. 

## AWS Load balancer :
AWS apllication load balance will be used to distrubute the API loads and AWS WAF will be added in top of load balancer for further security practices.
All request will be logged in cloudwatch for further latency analysis.

## Assesment Service :
This will be core service which will be written in Fast API. It will expose for both graphql APIs. Auto save mechanism will use the graphql subcription with the sticky session in AWS loadbalancer. This will be used to both create the assement as well submit the assesment. So all API related to creation and submission wilt be present in this service.

## User service :
All user and rights related APIs written in Fast API with REST.

## Reporting service :
Any data which required to fecth the reports, for real time reports connect with postgres & dynamo DB for all other reports connect with snowflake. FAST API + REST

## Notification service
As per current use case only email is required, this service will use sendgrid to send all the mails. Later it can be extended to send other notifications like SMS / whatsapp etc. FAST API + REST 

## Chatbot service
FAST API + Web sockets. will be using google dialogflow as AI assistant

## Apache airflow (ETL)
ETL from postgres & dynamodb to postgresSQL

## Celery worker
- Auto save in bacthes
- Any other async task from other services
- content medration when form submitted using google NLP

## Elastic cache
- Backend for celery
- casching layer for postgres

## Document AI
- FAST API + REST. 
- Docx to PDF onversion
- Google document AI to extract the data and store it in dynamoDB

## AWS Postgres RDS
- To stoe data follows ACID
- Encryption at rest
- Backup & restore

## Dynamo DB with DAX
- DAX as caching for dynamo db
- Store all semi-structured contents
- Extracted results

## Snowflake
- Store the data for reports and analytics

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

### Version control & CI CD
#### GitHub
- Ease of use
- Sophisticated code review tool
- Branch productions

