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

## Ansible Configuration (ansible.cfg)
The `ansible.cfg` file is used to configure Ansible for the YOLO project. It contains default settings for Ansible, including the inventory file, remote user, private key file, and host key checking. Let's examine each setting:

```ini
[defaults]
inventory = hosts
remote_user = vagrant
private_key_file = .vagrant/machines/default/virtualbox/private_key
host_key_checking = False
```

- `inventory`: This setting defines the path to the Ansible inventory file. In the YOLO project, the inventory file is named `hosts`.
- `remote_user`: The `remote_user` setting defines the default remote user to be used for SSH connections to the target hosts. In this project, it is set to `vagrant`.
- `private_key_file`: The `private_key_file` setting specifies the path to the private key file to be used for SSH connections to the target hosts. In this case, it is set to `.vagrant/machines/default/virtualbox/private_key`, which is the default private key location used by Vagrant.
- `host_key_checking`: The `host_key_checking` setting is set to `False` to disable host key checking. This avoids SSH warnings and prompts for new host keys during the Ansible run.


## Docker Ansible Tasks (docker/tasks/main.yml)

```yaml
---
- block:
    - name: Update package lists
      # ...
      tags:
        - docker

    - name: Install python
      # ...
      tags:
        - docker

    - name: Install Docker
      # ...
      tags:
        - docker

    - name: Install Docker Compose using pip
      # ...
      tags: 
        - docker-compose

    - name: Run docker-compose up
      # ...
      tags: 
        - docker-compose
      
    - name: Wait for the application to be ready
      wait_for:
        host: localhost
        port: 3000
        state: started
        timeout: 120

  rescue:
    - name: Handle Docker installation errors
      debug:
        msg: "Error occurred while installing Docker."
```

The `docker/tasks/main.yml` file contains Ansible tasks related to Docker and Docker Compose installation, along with other necessary tasks to set up and start the YOLO application.

1. **Update package lists**: This task updates the package lists on the system using the `apt` module. It ensures that the package repository information is up to date.

2. **Install Python**: This task installs Python3-pip package on the system, which is required for installing some Python dependencies.

3. **Install Docker**: This task installs the Docker engine on the system using the `apt` module. The package `docker.io` is installed to set up Docker.

4. **Install Docker Compose using pip**: This task uses the `pip` module to install Docker Compose. It specifies `pip3` as the executable to ensure that Docker Compose is installed using the appropriate version of pip.

5. **Run docker-compose up**: This task runs the `docker-compose up` command to start the YOLO application in detached mode (`-d`). The `args` section specifies the working directory (`/vagrant`) where the `docker-compose.yml` file is located.

6. **Wait for the application to be ready**: After starting the application, this task uses the `wait_for` module to wait until the application is accessible on localhost:3000. The task waits for the application to reach the "started" state within a timeout of 120 seconds.

The `rescue` block handles potential errors that might occur during Docker installation. If any error occurs in the "block" tasks, the "rescue" tasks will be executed. In this case, the `debug` module is used to display a message indicating that an error occurred while installing Docker.

These tasks are essential in setting up and deploying the YOLO application using Docker and Docker Compose with Ansible. They ensure that the required dependencies are installed, Docker is properly configured, and the YOLO application is up and running.

## Frontend Ansible Tasks (frontend/tasks/main.yml)
The `frontend/tasks/main.yml` file contains Ansible tasks related to the frontend application. The tasks check if the YOLO web application is already cloned and clone it from the GitHub repository if it is not present. Let's examine the tasks:

```yaml
- name: Check if yolo web application is already cloned
  stat:
    path: "{{ chdir }}"
  register: yolo_repo_status

- name: Clone yolo web application from GitHub
  git:
    repo: https://github.com/cynthia7125/yolo
    dest: "{{ chdir }}"
  when: not yolo_repo_status.stat.exists
  tags:
    - clone
```

1. **Check if yolo web application is already cloned**: This task uses the `stat` module to check if the YOLO web application is already present on the target host. The result is stored in the `yolo_repo_status` variable.

2. **Clone yolo web application from GitHub**: If the YOLO web application is not already cloned (based on the previous task's result), this task uses the `git` module to clone the application from the specified GitHub repository. The `when` condition ensures that the repository is cloned only when it does not exist.

These tasks ensure that the frontend application (YOLO web application) is available on the target host by checking if it is already present and cloning it from the GitHub repository if needed. Once the frontend application is available, other tasks in the playbook can proceed with the configuration and deployment, including the Docker-related tasks in the `docker/tasks/main.yml` file.

These tasks ensure that the frontend application (YOLO web application) is available on the target host by checking if it is already present and cloning it from the GitHub repository if needed. Once the frontend application is available, other tasks in the playbook can proceed with the configuration and deployment, including the Docker-related tasks in the docker/tasks/main.yml file.

### Summary
The order of execution in the playbook starts with setting up the frontend application by checking if the YOLO web application is already cloned. If not, it will be cloned from the GitHub repository. Afterward, the Docker-related tasks will be executed to install and configure Docker and Docker Compose on the target hosts. The Ansible roles are executed sequentially to achieve the desired configuration for the YOLO project.


