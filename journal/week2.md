# Week 2 — Distributed Tracing

## Introduction

**Distributed tracing** is a technique used in DevOps to gain insights into the flow of requests across a distributed system or application. It involves tracing the journey of a request as it moves through various components and services, collecting and correlating data from each step along the way.

In this week's project, I will walkthrough the process of setting up a technique(Honeycomb, AWS X-ray and Rollbar) that will be used to capture and record information about the behavior of various services in our Cruddur application, such as its processing time, errors encountered, and outgoing requests.

## 1. Instrumenting With Honeycomb

Honeycomb is a cloud-native observability platform designed to help DevOps teams gain insights into the behavior and performance of their systems and applications. It is based on the principles of distributed tracing and offers a range of features for collecting, analyzing, and visualizing data.

### Setting Up Honeycomb

After a successful signup and signin, Honeycomb sets up the environment and provides us with an API key that will be used to link our backend to Honeycomb so that the Backend can send data to Honeycomb. 

![]()

### Setting Up Instrumentation For The Cruddur Python Service

- Adding the following packages to requirements.py to install OpenTelemetry:
```
opentelemetry-api 
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests
```
- Installing the packages(in the backend directory): `$ pip install -r requirements.txt`

![]()

- Adding the following to the app.py file to create and initialize a tracer and Flask instrumentation to send data to Honeycomb
```
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)

# Initialize automatic instrumentation with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()
```

- Adding the following environmental variables to the backend-flask service in docker-compose file:
```
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"
```

- Exporting the service name and Honeycomb API key already provided from my account:

```
export HONEYCOMB_API_KEY=""
export HONEYCOMB_SERVICE_NAME="backend-flask"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="backend-flask"
```

- Running the docker compose command and entering the url to the home api

![]()
![]()

### Creating Spans

-  Spans are created to describe what is happening in the application. To create spans in the home service, adding the following to the /backend-flask/services/home_activities.py file where the name of the span is **home-activites-mock-data**:
```
from opentelemetry import trace

    with tracer.start_as_current_span("home-activites-mock-data"):
      span = trace.get_current_span()

```

**Complete code**
```
from datetime import datetime, timedelta, timezone
from opentelemetry import trace

tracer = trace.get_tracer("home.activities")

class HomeActivities:
  def run():
    with tracer.start_as_current_span("home-activites-mock-data"):
      span = trace.get_current_span()
      now = datetime.now(timezone.utc).astimezone()
      span.set_attribute("app.now", now.isoformat())
      results = [{
        'uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
        'handle':  'Andrew Brown',
        'message': 'Cloud is very fun!',
        'created_at': (now - timedelta(days=2)).isoformat(),
        'expires_at': (now + timedelta(days=5)).isoformat(),
        'likes_count': 5,
        'replies_count': 1,
        'reposts_count': 0,
        'replies': [{
          'uuid': '26e12864-1c26-5c3a-9658-97a10f8fea67',
          'reply_to_activity_uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
          'handle':  'Worf',
          'message': 'This post has no honor!',
          'likes_count': 0,
          'replies_count': 0,
          'reposts_count': 0,
          'created_at': (now - timedelta(days=2)).isoformat()
        }],
      },
      {
        'uuid': '66e12864-8c26-4c3a-9658-95a10f8fea67',
        'handle':  'Worf',
        'message': 'I am out of prune juice',
        'created_at': (now - timedelta(days=7)).isoformat(),
        'expires_at': (now + timedelta(days=9)).isoformat(),
        'likes': 0,
        'replies': []
      },
      {
        'uuid': '248959df-3079-4947-b847-9e0892d1bab4',
        'handle':  'Garek',
        'message': 'My dear doctor, I am just simple tailor',
        'created_at': (now - timedelta(hours=1)).isoformat(),
        'expires_at': (now + timedelta(hours=12)).isoformat(),
        'likes': 0,
        'replies': []
      }
      ]
      span.set_attribute("app.result_length", len(results))
      return results
 ```
 
- Checking for the **home-activites-mock-data** span in the Honeycomb UI

![]()


 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 




