# YOLO Project Docker Configuration
This document provides an overview of the Docker configuration for the YOLO project, including the choice of base images, Dockerfile directives, networking, volume usage, and the Git workflow used to achieve the task.

## Choice of Base Images
For the YOLO project, the following base images were chosen for each container:

- Backend: `node:13-alpine`

- Client: `node:13-alpine`

- MongoDB: `mongo:4.4`

The `node:13-alpine` image provides a lightweight base image for running Node.js applications, while the mongo image provides a pre-built image for running MongoDB.

## Dockerfile Directives
Both the backend and client containers have their respective Dockerfiles. The Dockerfile directives used in the creation and running of each container are as follows:

### Backend Container

```
FROM node:13-alpine

WORKDIR /backend

COPY package*.json ./

RUN npm install

COPY .  .

EXPOSE 5000

CMD ["npm", "start"]
```

The backend Dockerfile starts from the node:13-alpine base image, sets the working directory, copies the package.json and package-lock.json files, installs the dependencies, copies the rest of the application code, exposes port 5000, and starts the backend server.

### Client Container
```
FROM node:13-alpine

WORKDIR /client

COPY package*.json ./

RUN npm install

COPY .  .

EXPOSE 3000

CMD ["npm", "start"]

```
The client Dockerfile is similar to the backend Dockerfile. It starts from the node:13-alpine base image, sets the working directory, copies the package.json and package-lock.json files, installs the dependencies, copies the rest of the application code, exposes port 3000, and starts the client server.

## Docker Compose Networking
The Docker Compose file (docker-compose.yml) defines the networking configuration for the project. It includes the allocation of application ports. The relevant sections are as follows:


```
services:
  backend:
    # ...
    ports:
      - "5001:5000"
    networks:
      - yolo-network

  client:
    # ...
    ports:
      - "3000:3000"
    networks:
      - yolo-network
  
  mongodb:
    # ...
    ports:
      - "27017:27017"
    networks:
      - yolo-network

networks:
  yolo-network:
    driver: bridge
```
In this configuration, the backend container is mapped to port 5001 of the host, the client container is mapped to port 3000 of the host, and mongodb container is mapped to port 27017 of the host. All containers are connected to the yolo-network bridge network.

## Docker Compose Volume Definition and Usage
The Docker Compose file includes volume definitions for MongoDB data storage. The relevant section is as follows:

yaml

```
volumes:
  mongodb_data:
```
This volume, named mongodb_data, is used to store the data for the MongoDB container. It allows the data to persist even if the container is stopped or removed.

## Git Workflow
To achieve the task, the following Git workflow was used:

1. Fork the repository from the original repository
2. Clone the repository: ```git clone <forked-repository-url> ```
3. Created a .gitignore file to exclude unnecessary files and directories from version control.
4. Added the Dockerfiles for the backend and client to the repository: ```git add backend/Dockerfile client/Dockerfile ```
5. Committed the changes: ```git commit -m "Add Dockerfiles"```
6. Added the Docker Compose file to the repository: ```git add docker-compose.yml```
7. Committed the changes: ```git commit -m "Add Docker Compose file"```
8. Built the backend and client images: ```docker-compose build```
9. Pushed the built images to a Docker registry: ```docker-compose push```
10. Deployed the containers using Docker Compose: ```docker-compose up```

  &nbsp;

After the tenth step commits kept on happening for instances such as creating the explanation.md file and any other updates as the commit messages in the repository will explain. Commits stopped at some point when fixing the issue with ports which lead to using port 5001 to map the backend for the project to run successfully.


## Images Used on the yml file
Here are the links to the dockerhub images I pushed for this project:
- [Backend](https://hub.docker.com/repository/docker/reyhanacynthia/yolo-backend/general)
- [Client](https://hub.docker.com/repository/docker/reyhanacynthia/yolo-client/general)


## Run Containerized Yolo Project
To run yolo on docker you will need to run this command in the root directory:

  `sudo docker-compose up`

Open the links that pop up and try to add a product.
