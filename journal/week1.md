# Week 1 — App Containerization

## Introduction

In this week's lesson, the Frontend and the backend of Cruddur application was containerized using Docker which solves the problem of application dependencies by encapsulating the application and its dependencies in a container. This ensures that the application runs consistently across different environments, regardless of the underlying operating system or hardware.

## Running the backend Without Docker

- From the root directory of this repo, cd into backend-flask folder and run the following command to export the frontend and backend URL to run locally and using pip to install the dependencies:
```
export FRONTEND_URL="*"
export BACKEND_URL="*"
pip3 install -r requirements.txt
cd ..
```
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/10.running%20backend%20without%20docker..png)

- Running the backend: `$ python3 -m flask run --host=0.0.0.0 --port=4567`
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/11.running%20the%20backend%20with%20pip%20command.png)

- Opening the ports
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/12.ports%20opened.png)

- Opening the link for port 4567 and appending "/api/activities/home" to the url will give back the following json: 
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/13.result.png)

## Building The Docker Image of Cruddur Backend Application

- Writing the dockerfile for the backend which is a text file that contains instructions for building a Docker image:
```
FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/14.creating%20Dockerfile%20for%20backend.png)

- building the container image with the following command: `docker build -t  backend-flask .`
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/15.building%20the%20image.png)
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/15.building%20the%20image%202.png)

- Running the backend as a container: `docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask`
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/16.running%20the%20container.png)

## Running other Docker commands
- To run container in the background: `docker container run --rm -p 4567:4567 -d backend-flask`
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/17.docker%20commands.png)

- Using curl to test the server: `curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"`
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/18.testing%20the%20server.png)

- To return the container id into an environment Variable: `CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)`
- To check the logs: `docker logs $CONTAINER_ID -f`
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/19.docker%20logs.png)

## Building The Docker Image of Cruddur Frontend Application

- Writing the dockerfile for the frontend app:
```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/21.creating%20the%20frontend.png)

- Building the frontend Image: `docker build -t frontend-react-js .`

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/22.building%20the%20image%20frontend%20image%201.png)
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/22.building%20the%20frontend%20image%202.png)

- Running the container image: `docker run -p 3000:3000 -d frontend-react-js`

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/23.running%20the%20container.png)

- Opening the port 3000 from the browser:

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/24.frontend%20web%20ui.png)

## Running Multiple containers with Docker Compose

With Docker Compose one can run multi-container applications. It makes it easy to define the relationships and dependencies between containers and ensures that they are started and stopped together. The following illustrates how to create the frontend and backend container of Cruddur app at once:
- Writing the Docker compose file for the app:
```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/25.creating%20docker-compose.png)

- Running the docker compose command to create the frontend and backend container and make it live by right-clicking on the docker-compose.yml file and selecting "Docker compose up" option:
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/26.executing%20docker%20compose.png)

- Opening the port 3000 on the browser:
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week1/27.result.png)

## Adding DynamoDB Local and Postgres

- creating the DynamoDB 
```
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal
```
- Docker compose configuration for Postgres
```
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
```

- Installing the postgres client into Gitop by running the following command:
```
  - name: postgres
    init: |
      curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
      echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list
      sudo apt update
      sudo apt install -y postgresql-client-13 libpq-dev
```

- Integrating them into the existing docker-compose.yml file:
```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal

  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

volumes:
  db:
    driver: local
```
- Running the Docker-compose.yml file

![]()

- Creating DynamoDB table:
```
aws dynamodb create-table \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --table-class STANDARD
```
![]()

- Adding an item to the database table
```
aws dynamodb put-item \
    --endpoint-url http://localhost:8000 \
    --table-name Music \
    --item \
        '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
    --return-consumed-capacity TOTAL  
```
![]()

- Listing the tables: `$ aws dynamodb list-tables --endpoint-url http://localhost:8000`
- Fetching records from the database: `$ aws dynamodb scan --table-name Music --query "Items" --endpoint-url http://localhost:8000`
![]()

- Connecting to the Postgres server

![]()

- Connecting to the Postgres client: `$ psql -U postgres --host localhost`

![]()













