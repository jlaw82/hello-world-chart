# Getting Started
## git
1. Download and install git.
```https://git-scm.com/book/en/v2/Getting-Started-Installing-Git```
2. Check if git is installed.
```git --version```
## minikube
1. Ensure you have minikube installed.
```https://minikube.sigs.k8s.io/docs/start/```
2. Start minikube 
```minikube start```
3. Enable tiller in minikube for helm to use.
```minikube addons enable helm-tiller```
4. Initialize environment variables on Client by following the instructions from the following command.
```minikube docker-env```
## kubectl
1. Run kubectl via minikube to download and initialize kubectl 
```minikube kubectl```
2. Check if the minikube environment is running.
```minikube kubectl cluster-info```
## helm
1. Download helm client.
```https://helm.sh/docs/intro/install/```
2. Check if helm client is installed.
```helm version```

# HELLO-WORLD
A simple expressJS running off nodeJS to retrieve value from configured Database and return it's value.

## Downloading 
1. Clone the repository with the following command.
```git clone https://github.com/jlaw82/hello-world.git```
## Building and running locally
1. Configure .env file to preset Database connection along with App's server settings.
```./hello-world/.env```
2. Starting the Application (Run command in the app folder where .env resides)
```npm start```
3. Testing the Application. Navigate your browser of choice to ```http://APP_HOST:APP_PORT``` (default localhost:8080)
## Building and Containerization
As we won't be using a public or private docker image registry, we'll ```build, containerize, tag and store it``` directly into minikube's docker instance to cache for further deployment later.
1. Mount the app project folder into minikube (where .env resides)
```minikube mount .:/home/tempmount```
2. Might encounter an issue where mounting failed due to client firewall, ensure to disable it temporarily for these steps.
3. Remote connect into minikube's environment. 
```minikube ssh```
4. Navigate to ```/home/tempmount``` and check if ```Dockerfile``` exists in that location. ```pwd``` command to display your current directory location. ``` cd ...``` to go out from the current directory. ```cd folder_name``` to enter the directory.
5. Run the following command to get docker to build using the ```Dockerfile``` and tag it with ```hello-world``` as image name and tag version as ```1```
```docker build -f Dockerfile -t hello-world:1 .```
6. Once completed, run the following command to check if the docker image is stored currently in the docker server in minikube.
```docker images hello-world```
7. Exit the environment once completed.
```exit```

# HELLO-WORLD-CHART
The following are helm charts to make deployment of the database and backend server easier through using helm client.
## Downloading 
1. Clone the repository with the following command.
```git clone https://github.com/jlaw82/hello-world-chart.git```
## Configuring
1. Configure ```./hello-world-chart/values-env.yaml``` (env = dev for development/prod for production)
```
global:
  enabled: false #boolean to enable this section
mainbe: #app server config
  enabled: true #boolean to enable this section
  env: #Environment variables propagated into the container
    NODE_ENV: development
    APP_HOST: 0.0.0.0 #hello-world app nodejs server ip
    APP_PORT: 8081 #hello-world app nodejs server port
    DB_USER: sa #db user credential for hello-world to use
    DB_PASS: pass #db password credential for hello-world to use
    DB_PORT: 3306 #db port credential for hello-world to use
    DB_DATABASE: testdb #db default db credential for hello-world to use
maindb: #db server config
  enabled: true #boolean to enable this section
  auth:
    username: sa #default user to create
    password: pass #default pass for user to create
    database: testdb #default db created
    rootPassword: rootpass #default root password to create
  initdbScripts: #db server initialization scripts
```
## Installing
1. Execute helm deployment chart. Ensure you are in the folder where ```values-env.yaml``` (env = dev for development/prod for production) resides. Run the desired env (environment) deployment chart. (i.e. ```values-dev.yaml```). The following command will attempt to install the chart using the ```values-dev.yaml``` file and name the deployment ```my-deployment```.
```helm install my-deployment -f values-dev.yaml .```
## Upgrading
1. You can update the existing helm deployment by running the following command in the same location. This will use your new/updates ```values-dev.yaml``` file to upgrade the existing ```my-deployment``` helm deployment on the server.
```helm upgrade my-deployment -f values-dev.yaml .```
## Uninstall
1. You can uninstall an existing helm deployment on the server by running the following command in the same location. This will delete the previously deployed ```my-deployment``` helm deployment from the server.
 ```helm delete my-deployment```
## Updating Dependencies
1. You can update existing dependency charts to whichever selected version and packages configured in ```./hello-world-chart/Chart.yaml``` under ```dependencies``` key.
```helm dep up```

## Testing
1. Identifying the service exposing the app server deployment by running the following command. Naming convention should include the alias ```mainbe```
```minikube kubectl get svc```
2. Retrieve the service url to connect to the app server by running the following command. Example of the following command uses the service name ```test-dev-mainbe``` retrieved from the previous command.
```minikube service --url test-dev-mainbe```
3. Copy and paste the url into browser of choice to connect to the app server.
## Troubleshooting
1. Check if the pods have deployed successfully with the following command.
```minikube kubectl get pods```
2. Check the status of the specific pod for k8s(kubernetes) level error. (Replace mainbe-pod-name with pod name retrieved from step 1)
```minikube kubectl describe pod mainbe-pod-name```
3. Check the logs of the specific pod for information thrown by the app deployed in the container. (Replace mainbe-pod-name with pod name retrieved from step 1)
```minikube kubectl logs mainbe-pod-name```
4. Check if the service is configured correctly to point to the right deployment. (Replace test-dev-mainbe with service name retrieved from previous section)
```minikube kubectl describe svc test-dev-mainbe```
5. Remote connect into the pod to troubleshoot. (Replace mainbe-pod-name with pod name retrieved from step 1)
```minikube kubectl -- exec -it mainbe-pod-name sh```
