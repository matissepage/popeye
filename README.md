# Popeye 🧪



### 💻 About the project

You will have to containerize and define the deployment of a simple web poll application.

There are five elements constituting the application:

Poll, a Flask Python web application that gathers votes and push them into a Redis queue.

A Redis queue, which holds the votes sent by the Poll application, awaiting for them to be consumed by the Worker.

The Worker, a Java application which consumes the votes being in the Redis queue, and stores them into a PostgreSQL database.

A PostgreSQL database, which (persistently) stores the votes stored by the Worker.

Result, a Node.js web application that fetches the votes from the database and displays the. . . well, result. ;)



### Docker image

You have to create 3 images. The specifications for each image are as described below.


## Poll
The image must be based on a Python official image. The dependencies of the application can be installed using the following command:

``` pip3 install -r requirements.txt ```

The application must expose and run on the port 80 and can be started with:

flask run --host=0.0.0.0 --port=80
Result
The image must be based on an official Node.js version 12 Alpine image. The application must expose and run on the port 80. The dependencies of the application can be installed using the following command:

``` npm install ```

## Worker
The image will be built using a multi-stage build.

## FIRST STAGE - COMPILATION
The first stage must be based on maven: 3.5-jdk-8-alpine and be named builder. It must be used to build (natuurlijk) and package the Worker application using the following commands:

mvn dependency:resolve, from within the folder containing pom.xml;
then, mvn package, from within the folder containing the src folder.
It generates a file in the target folder named worker-jar-with-dependencies.jar (relative to your WORKDIR).

## SECOND STAGE - RUN
The second stage must be based on openjdk:8-jre-alpine. This is the one really running the worker using the command:

``` java -jar worker-jar-with-dependencies.jar ```

## DOCKER COMPOSE
You now have 3 Dockerfiles that create 3 isolated images. It is now time to make them all work together using Docker Compose !

Create a docker-compose.yml file that will be responsible for running and linking your containers. You must use version 3 of Docker Compose’s syntax.

Your Docker Compose file should contain: 5 services :

• poll: • builds your poll image; • redirects port 5000 of the host to the port 80 of the container.

• redis: • uses an existing official image of Redis; • opens port 6379.

• worker: • builds your worker image.

• db: • represents the database that will be used by the apps; • uses an existing official image of PostgreSQL; • has its database schema created during container first start.

• result: • builds your result image; • redirects port 5001 of the host to the port 80 of the container.

3 networks: • poll-tier to allow poll to communicate with redis. • result-tier to allow result to communicate with db. • back-tier to allow worker to communicate with redis and db.

1 named volume: • db-data which allows the database’s data to be persistent, if the container dies.

![schema-devops](./assets/schema-devops.png)