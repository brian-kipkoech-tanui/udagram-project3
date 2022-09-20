# Udagram Image Filtering Application

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into two parts:
1. Frontend - Angular web application built with Ionic Framework
2. Backend RESTful API - Node-Express application

## Getting Started
> _tip_: it's recommended that you start with getting the backend API running since the frontend web application depends on the API.
- I followed the recommendation above because frontend was dependent on the backend
### Prerequisite
1. The project depends on the Node Package Manager (NPM). You will need to download and install Node from [https://nodejs.com/en/download](https://nodejs.org/en/download/). This will allow you to be able to run `npm` commands.
2. Environment variables will need to be set. These environment variables include database connection details that should not be hard-coded into the application code.

#### Environment Script
A file named `set_env.sh` that had been prepared as an optional tool to help configure these variables on your local development environment was added to the gitignore for security reasons.
 
I did _not_ want my credentials to be stored in git. After pulling this `starter` project, I ran the following command to tell git to stop tracking the script in git but keep it stored locally. This way, I could use the script for my convenience and reduce risk of exposing my credentials.
`git rm --cached set_env.sh`


### 1. Database
I Created a PostgreSQL database on AWS RDS (Though locally could also work fine). The database is used to store the application's metadata.

* We will need to use password authentication for this project. This means that a username and password is needed to authenticate and access the database.
* The port number will need to be set as `5432`. This is the typical port that is used by PostgreSQL so it is usually set to this port by default.

![Database](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/RDS.png?raw=true)

Once your database is set up, set the config values for environment variables prefixed with `POSTGRES_` in `set_env.sh`.
* If you set up a local database, your `POSTGRES_HOST` is most likely `localhost`
* If you set up an RDS database, your `POSTGRES_HOST` is most likely in the following format: `***.****.us-west-1.rds.amazonaws.com`. You can find this value in the AWS console's RDS dashboard.


### 2. S3
I Created an AWS S3 bucket. The S3 bucket is used to store images that are displayed in Udagram.

![S3](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/S3.png?raw=true)

Set the config values for environment variables prefixed with `AWS_` in `set_env.sh`.

### 3. Backend API
I Launched the backend API locally (Similar to course two of udacity programme). The API is the application's interface to S3 and the database.

* To download all the package dependencies, run the command from the directory `udagram-api/`:
    ```bash
    npm install .
    ```
* To run the application locally, run:
    ```bash
    npm run dev
    ```
* Visiting `http://localhost:8080/api/v0/feed` in my web browser I verified that the application is running, due to the presence of a JSON payload. Postman was a handy tool in testing the API's.

![Monolithic](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/restapi(backend).png?raw=true)

### 4. Frontend App
Launched the frontend app locally.

![frontend](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/frontend.png?raw=true)

* To download all the package dependencies, run the command from the directory `udagram-frontend/`:
    ```bash
    npm install .
    ```
* Install Ionic Framework's Command Line tools for us to build and run the application:
    ```bash
    npm install -g ionic
    ```
* Prepare your application by compiling them into static files.
    ```bash
    ionic build
    ```
* Run the application locally using files created from the `ionic build` command.
    ```bash
    ionic serve
    ```
* By visiting `http://localhost:8100` in my web browser I verified that the application was running. (I saw a web interface.)


## Tips
1. Take a look at `udagram-api` -- does it look like we can divide it into two modules to be deployed as separate microservices? Yes, can be divided into feed and user for backend and a single frontend.


- This was acheived by refactoring the monolith application into microservices, by setting up containers using docker, then using circleci for continuous integration and finally using Kubernetes for orchestration of the docker containers.
- Once you refactor the Udagram application, it will have the following services running internally:
1) Backend /user/ service - allows users to register and log into a web client.
2) Backend /feed/ service - allows users to post photos, and process photos using image filtering.
3) Frontend - It is a basic Ionic client web application that acts as an interface between the user and the backend services.
4) Nginx as a reverse proxy server - for resolving multiple services running on the same port in separate containers. When different backend services are running on the same port, then a reverse proxy server directs client requests to the appropriate backend server and retrieves resources on behalf of the client.

### 4. Microservices
#### i). Setting up Docker containers

Navigate to the project directory, and set up the environment variables again
```bash
source set_env.sh
```
Create images - In the project's parent directory, create a `docker-compose-build.yaml` file . It will create an image for each individual service. Then, you can run the following command to create images locally then run

Make sure the Docker services are running in your local machine

Remove unused and dangling images
```bash
docker image prune --all
```
Run this command from the directory where you have the "docker-compose-build.yaml" file present
```bash
docker-compose -f docker-compose-build.yaml build --parallel
```
The following shows the images that were built

![Images](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/imagesrunning.png?raw=true)

Run the container with the following command
```bash
docker-compose up
```
Visit `http://localhost:8100` in your web browser to verify that the application is running.

- Backend-api-feed

![backend](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/restapi(backend).png?raw=true)

- The localhost server running

![backend](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/localhostserver.png?raw=true)

- The docker container running, frontend part shown below:

![frontend](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/frontend.png?raw=true)

![frontend](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/frontend2.png?raw=true)

#### ii). Continuous Integration
- Screenshots of successful circleci builds and deployment to dockerhub

![circleci](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/circleci.png?raw=true)

![dockerhub](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/dockerhubrepositories.png?raw=true)

#### iii). Ochestration using kubernetes
The kubernetes services was hosted in AWS Elastic Kubernetes Service (EKS).

- the screenshot showing the pods:

![getpods](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/getpods.png?raw=true)

- Screenshot showing the command `kubectl get services`:

![describeservices](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/getservice.png?raw=true)

- Screenshot showing the command `kubectl describe services`:

![describeservices](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/describe-service1.png?raw=true)

![getservices](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/describe-service2.png?raw=true)

####  Create horizontal pod scaler

- Installationn
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
- Create the HorizontalPodAutoscaler:(Do this for backend-feed and backend-user deployment)
```bash
kubectl autoscale deployment backend-feed --cpu-percent=70 --min=3 --max=5
kubectl autoscale deployment backend-user --cpu-percent=70 --min=3 --max=5
```
- Checking the status of the newly created horizontalpodautoscaler(hpa):
```bash
kubectl get hpa
```
![gethpa](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/gethpa.png?raw=true)

- Screenshot of `kubectl describe hpa` command:

![describehpa](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/describehpa.png?raw=true)

- `kubectl get service` command:

![getservice](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/getservice.png?raw=true)

- `kubectl logs backend-feed-5c86b49c9c-6fngm` gets the logs, useful for debugging:

![getlogs](https://github.com/brian-kipkoech-tanui/udagram-project3/blob/master/screenshots/get-logs.png?raw=true)




2. The `.dockerignore` file is included for your convenience to not copy `node_modules`. Copying this over into a Docker container might cause issues if your local environment is a different operating system than the Docker image (ex. Windows or MacOS vs. Linux).
3. It's useful to "lint" your code so that changes in the codebase adhere to a coding standard. This helps alleviate issues when developers use different styles of coding. `eslint` has been set up for TypeScript in the codebase for you. To lint your code, run the following:
    ```bash
    npx eslint --ext .js,.ts src/
    ```
    To have your code fixed automatically, run
    ```bash
    npx eslint --ext .js,.ts src/ --fix
    ```
4. `set_env.sh` is really for your backend application. Frontend applications have a different notion of how to store configurations. Configurations for the application endpoints can be configured inside of the `environments/environment.*ts` files.
5. In `set_env.sh`, environment variables are set with `export $VAR=value`. Setting it this way is not permanent; every time you open a new terminal, you will have to run `set_env.sh` to reconfigure your environment variables. To verify if your environment variable is set, you can check the variable with a command like `echo $POSTGRES_USERNAME`.
