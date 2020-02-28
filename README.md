# Containers

If we were to build an app, for example online retail application using user
sign-in, inventory management, billing and shipping, we could breakup the app
into the following modules -
* Each module requiring a number of software instances
* running on a number of machines to scale

The machines may have their own software requirements to run and scale properly
such as - operating system, RAM, CPU, disk, networking and even software
dependencies

### Instead of writing these requirements in code, we can declare them outside
### our code in the configuration files called *_CONTAINERS_* for easy management.

### Why developers like containers?
* Code works the same everywhere
  - Across dev, test and production
  - Across bare-metal, VMs and Cloud
* Packaged apps speed up development
  - Agile creation and deployment
  - Continuous Integration and delivery
  - Single file copy
* They provide a path to microservices
  - Introspectable, isolated and elastic

### Containers use a layered file system with only the top layer writable

# Introduction to *Docker*
Docker is an open platform for developing, shipping and running applications.
With Docker you can separate your applications from the infrastructure and
treat your infrastructure like a managed application. Docker helps you ship code
faster, deploy faster and shorten the cycle between writing code and running code.

Docker does this by combining kernel containerization features with workflows &
tooling that helps you manage and deploy your applications.

Docker containers can be directly used in _Kubernetes_, which allows them to run
in _Kubernetes Engine_ with ease.

[source](https://google.qwiklabs.com)

## Command line tool in Google Cloud Platform - GCloud
GCloud is the command-line tool for Google Cloud Platform. It comes pre-installed
on Cloud Shell and supports tab-completion.

- You can list the active account name with the following command:

```bash
gcloud auth list
```
- You can list the project ID with the following command:

```bash
gcloud config list project
```

#  DOCKER Hello World!
#### Hello World Docker
```python
docker run hello-world!
```
The above code is a simple container, that returns *Hello from Docker* to the screen.
The Docker daemon searches for the hello-world image. If the image is not found
locally, the image is pulled from a public registry called *Docker Hub* and a
container is created from the image and run it for us.

*Docker Image can be seen as a Class from the concept of OOP*
*Docker Container is an instance (Object) of the corresponding Docker Image*

#### We can check the container image that was pulled from the Docker Hub by
```bash
docker images
```

#### We can look at the running containers by the following command
```bash
docker ps
```
#### In order to see all the containers, including the ones that have finished
#### executing -
```bash
docker ps -a
```

### Note: The container names are randomly generated but can be specified with
```bash
docker run --name [container-name] hello world
```

# Building a Docker Image

In this case a simple example node application Image is built.

#### Create a folder and switch into that folder
```bash
mkdir test && cd test
```
#### Create a Dockerfile
```bash
cat > Dockerfile <<EOF
# Use an artificial Node runtime as the parent image
FROM node:6

# Set the working directory in the container to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Make the container's port 80 available to the outside world
EXPOSE 80

# Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF
```
* The above Dockerfile instructs the Docker daemon on how to build your image.
  - The initial line specifies the base parent image, which is in this case the
    official Docker image for the node version 6.
  - In the second we set the working (current) directory of the container.
  - In the third, we add the current directory's content (indicated by ".") into
    the container.
  - Then we expose the container's port so that it can accept connections on that
    port and finally run the node command to start the application.


#### Creating a node application

```bash
cat > app.js <<EOF
const http = require('http');

const hostname = '0.0.0.0';
const_port = 80;

const server = http.createServer((req, res) =>{
  res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
      res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log('Server running at http://%s:%s', hostname, port);
});

process.on('SIGNINT', function() {
  console.log('Caught interrupt signal and will exit');
  process.exit();
});
EOF
```
* The above code is a simple HTTP server the listens on port 80 and returns
  "Hello World"

#### Building a Docker file

```bash
docker build -t node-app:0.1 .
```
* Note again "." means the which means current working directory. Hence we need
  to run the above command from within the directory, that contains that has the
  Dockerfile.
* The "-t" is to name and tag the image with the _name:tag_ syntax. In this case
  the name of the image is *node-app* and the tag is *0.1*.
* It is highly recommended to tag when building Docker images. If a tag is not
  specified then the tag will default to default and then it is difficult to
  distinguish between newer images and older ones.

#### To look at the images that were built

```bash
docker images
```

# Running a the built Docker containers

#### Run the Docker Container

```bash
docker run -p 4000:80 --name my-app node-app:0.1
```
* The "--name" tag allows us to name the containers.
* The "-p" instructs Docker to map the host's port 4000 to container's port 80.
* Note- Without port mapping it is not possible to reach the container at
  localhost.

#### Run the following command to test the server
```bash
curl http://localhost:4000
```

# Stop and Remove the container

#### To stop and remove the container
```bash
docker stop my-app && docker rm my-app
```

# Start the container in the background
```bash
docker run -p 4000:80 --name my-app -d node-app:0.1
```
#### Check the containers that are running
```bash
docker ps
```
#### We can see the logs by executing
```bash
docker logs [container_id]
```
# Modify the app.js and build a new image

```bash
nano app.js
```
```bash
....
const server = http.createServer((req, res) => {
    res.statusCode = 200;
      res.setHeader('Content-Type', 'text/plain');
        res.end('Welcome to Cloud\n');
});
....
```
# Build the Docker image
```bash
docker build -t node-app:0.2 .
```

# Run Docker with new image version

```bash
docker run -p 8080:80 --name my-app -d node-app:0.2
```
#### Check the containers that are running
```bash
docker ps
```

#### Test the Containers
```bash
curl http://localhost:8080
```
