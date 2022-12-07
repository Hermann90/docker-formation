# Dockerfile Multi-Stage

Before __multi-stage builds__, One of the most challenging things about building images is keeping the image size down. Each __RUN__, __COPY__, and __ADD__ instruction in the Dockerfile adds a layer to the image, and you need to remember to clean up any artifacts you don’t need before moving on to the next layer.

## Use multi-stage builds
- With __multi-stage__ builds, you use multiple __FROM__ statements in your __Dockerfile__. 
- Each __FROM__ instruction can use a different base, and each of them begins a new stage of the build. 
- You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image.

#### Example of multi-stage Dockerfile

```sh
# syntax=docker/dockerfile:1
FROM golang:1.16 AS builder # Stage 1
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go ./
RUN CGO_ENABLED=0 go build -a -installsuffix cgo -o app .

# Stage 2
FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
# Use the result generate in stage 1
COPY --from=builder /go/src/github.com/alexellis/href-counter/app ./
CMD ["./app"]
```

## Principle : 

In practice, when using the multi-stage Dockerfile, the principle is as follows:
1. We want to deploy an application that requires the installation of several tools
2. To do so, we need several steps and each step deals with a specific tool that participates in the construction of the application

## Usefulness : 
With a multi-stage Dockerfile, we have the advantages of speed. Because we don't need to install a set of tools, manage the compatibility between these tools before deploying our application.

# Example: 
We will write a Multi-stage Dockerfile, to deploy a small web application:
1. the application is in a git repository whose url is : 
2. The application will be compiled with maven and a war file will be generated
3. The application will be deployed in a Tomcat server
work to do :
* Write the Dockerfile of this application
* Generate the docker image of this application from the Dockerfile
* Launch a container of this application from its image and check that the application is deployed on port 8080. 

# Solution 


* Dockerfile 

```sh
FROM alpine/git as clone
WORKDIR /app
RUN git clone https://github.com/Hermann90/war-hello.git

FROM maven:3.5-jdk-8-alpine as build
WORKDIR /app
COPY --from=clone /app/war-hello /app
RUN mvn package

FROM tomcat
COPY --from=build /app/target/hello-world-war-1.0.0.war /usr/local/tomcat/webapps/ROOT.war
COPY --from=build /app/target/hello-world-war-1.0.0/ /usr/local/tomcat/webapps/ROOT
EXPOSE 8080
```
* Generate the docker image of this application from the Dockerfile : 
    - The Dockerfile is present in the mult-stage directory. 
    - Enter this directory, then enter the command to generate a docker image. 
```sh
cd 
docker build -t wartest .
```