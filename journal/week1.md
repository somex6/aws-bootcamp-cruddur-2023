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
![]()

- Running the backend: `$ python3 -m flask run --host=0.0.0.0 --port=4567`
![]()

- Opening the ports
![]()

- Opening the link for port 4567 and appending "/api/activities/home" to the url will give back the following json: 
![]()

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
![]()

- building the container image with the following command: `docker build -t  backend-flask .`
![]()
![]()
- Running the backend as a container: `docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask`
![]()
## Running other Docker commands
- To run container in the background: `docker container run --rm -p 4567:4567 -d backend-flask`
- Using curl to test the server: `curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"`
![]()
- To return the container id into an environment Variable: `CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)`
- To check the logs: `docker logs $CONTAINER_ID -f`
![]()

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
![]()

- Building the frontend Image: `docker build -t frontend-react-js ./frontend-react-js`
![]()

- Running the container image: `docker run -p 3000:3000 -d frontend-react-js`
![]()

- Opening the port 3000 from the browser:
![]()

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
![]()

- Running the docker compose command to create the frontend and backend container and make it live by right-clicking on the docker-compose.yml file and selecting "Docker compose up" option:
![]()

- Opening the port 3000 on the browser:
![]()


