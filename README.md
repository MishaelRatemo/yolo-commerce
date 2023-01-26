# Yolomy Application
## Author   :: Mishael Ratemo
### Application Description
This is an ecommerce web app for both male and female.
## Requirements
Make sure that you have the following installed:
- [node](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04) 
- npm 
- [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) and start the mongodb service with `sudo service mongod start`

## Navigate to the Client Folder 
 `cd client`

## Run the folllowing command to install the dependencies 
 `npm install`

## Run the folllowing to start the app
 `npm start`

## Open a new terminal and run the same commands in the backend folder
 `cd ../backend`

 `npm install`

 `npm start`

 ### Go ahead a nd add a product (note that the price field only takes a numeric input)


 ## Technologies used
    * BackEnd: * Nodes 
    * FontEnd:  React 
    * Database * Mongo
    * Docker containerization

 ## Using Docker Compose
 ###   Procedure.
 * Create <code> docker-compose.yml </code> file
 * cd into backend and add <code> Dockerfile </code> and add neccessary port(5000) for nodejs
 * cd into front-end and add <code> Dockerfile </code> and add neccessary port(3000) for react app
 * ensure you have installed docker compose in your system if not follow this [link](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04) for linux system.
 * run <i><code>  sudo docker-compose build </code></i> to start building
 * the run <i><code> sudo docker-compose up </code></i> to start application




### RUNNING THE APPLICATION WITHOUT DOCKER COMPOSE FILE
## Run the folllowing command to install the dependencies 
 `npm install`

## Run the folllowing to start the app
 `npm start`

## Open a new terminal and run the same commands in the backend folder
 `cd ../backend`

 `npm install`

 `npm start`

 ### Go ahead a nd add a product (note that the price field only takes a numeric input)

 ****** **************** *********** ***********

### RUNNING THE APPLICATION WITH DOCKER COMPOSE FILE
 ## Instructions
- Fork and Clone :  `https://github.com/MishaelRatemo/yolo-commerce.git`
- Change in to yolo directory :  `cd yolo-commerce`
- Add MongoDB configuration on server.js file
- Add `Dockerfile` 
- Add `docker-compose.yml` file (more details on `docker-compose.yml` and `docker file` are on `explanation.md` file)
- Run docker compose to install the image and create a container : `sudo docker compose up --build`
- Backend runs on port `5000` and Frontend runs on port `3000`.
- Push images to docker hub : `sudo docker compose push`
- Go ahead a nd add a product (note that the price field only takes a numeric input ) 


## Ansible
*** Installation ***
You need to install ansible, vagrant and virtualbox as per below instructions:
- Follow along with the instructions outlined here in order to install Ansible 
`https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html`
- Instruction to install virtualbox `https://www.virtualbox.org/wiki/Linux_Downloads`
- Instructions to install vagrant `https://developer.hashicorp.com/vagrant/downloads`

*** Create a development environment ***
 - Run `vagrant box add geerlingguy/ubuntu2004`
   Select the “virtualbox” option.
 - Run `vagrant init geerlingguy/ubuntu2004`  . This will create a `Vagrantfile`
 - To boot the server run `vagrant up`
 - Add hosts and ansible.cfg files

 Open the Vagrantfile that was created when we used the vagrant init command. Add the following lines just before the final ‘end’ (Vagrantfiles use Ruby syntax, in case you’re wondering):

  
   `# Provisioning configuration for Ansible.
  config.vm.provision "ansible" do |ansible|
  ansible.playbook = "playbook.yml"
  end `

*** Ansible playbook ***
- Create a file called `playbook.yml`
  Add name of the playbook, hosts, tasks as indicated on the playbook.yml file.

- To run the playbook on our VM. Make sure you’re in the same directory as the Vagrantfile and playbook.yml file, and enter `vagrant provision`

To  run the playbook : ` ansible-playbook playbook.yml`

The playbook has 3 roles:
 1). git : This is to install git .  Git is installed if you want to work on your project on your computer

 2). docker: This is to install docker and docker compose.This allow you to package your application in containers
 
 3). docker-compose: it installs/download the image and container of the application




# Kubernetes
**Step1
- Create a  free [Google cloud account](https://cloud.google.com/resources/forrester-cloud-migration-study/?utm_source=google&utm_medium=cpc&utm_campaign=FY22-Q2-emea-EM874-website-dl-app-mig-1&utm_content=ForresterMigration22-DEV_c-CRE_599021984092-ADGP_Hybrid%20%7C%20BKWS%20-%20EXA%20%7C%20Txt%20~%20GCP%20~%20General_Core%233-KWID_43700071177485181-kwd-6458750523-userloc_9076838&utm_term=KW_google%20cloud-NET_g-PLAC_&gclid=CjwKCAiAoL6eBhA3EiwAXDom5ub929JMPR0Ms0wGdk6f0hM9LOD-a3ebhVb4McvtSWbIcG6rqYk9eBoCyLMQAvD_BwE&gclsrc=aw.ds)

**Step2
- Create a Project and cluster using you Google cloud account.

**Step3
- Create a folder and name it `manifests` 
- Create a deployment and service yaml files that will helps us define Kubernetes objects to create and manage a cluster.

- In this application we have `api.yml and client.yml`
`api.yml` has the deployment and service object for backend and `client.yaml` has for client.

A YAML file for a Kubernetes resource typically includes the following fields:
  **Deployment
  - apiVersion: The version of the Kubernetes API that the resource belongs to.(app/vm1)
  - kind: The type of resource being defined (e.g. Pod, Service, Deployment).
  - metadata: Information about the resource, such as its name and labels.
        e.g From api.yml 
        ```metadata:
            name: yolo-front
            namespace: my-yolo-app
            labels:
              app: yolo
              component: front 
              ```


  - spec: The desired state of the resource, including its configuration.
        e.g From client.yaml
        ```
        spec:
          replicas: 3
          selector:
            matchLabels:
              component: front
          template:
            metadata:
              labels:
                app: yolo
                component: front
            spec:
              containers:
                - name: clientcontainer
                  image: mishaelr/frontend:1
                  ports:
                    - containerPort: 3000
                    ```

  - This `client.yml` file creates a Pod named `yolo-client` with a single container named `clientcontainer` that runs the `mishaelr/frontend:1` image from docker hub and exposes port `3000`.

  - This `api.yml` file creates a Pod named `yolo-api` with a single container named `backendcontainer` that runs the `mishaelr/api:1` image from docker hub and exposes port `5000`.

  **Service**
`
  apiVersion: v1
  kind: Service
  metadata:
    name: yolo-front
    namespace: my-yolo-app
    labels:
      app: yolo
      component: front
  spec:
    type: LoadBalancer
    selector:
      app: yolo
      component: front
    ports:
      - port: 3000
        targetPort: 3000
        protocol: TCP
        name: http
     `
  This above client.yml file creates a Service named `yolo-front` with the label `app: yolo` and namespace `my-yolo-app`.  The Service uses the selector `app: yolo` to identify the set of Pods that it should route traffic to. It has a single port named `http` with a port number of `3000` and target port of `3000`, and type `LoadBalancer` which  exposes the service to the External  network.

  This  api.yml  file creates a Service named `yolo-api` with the label `app: yolo` and namespace `my-yolo-app`.  The Service uses the selector `app: yolo` to identify the set of Pods that it should route traffic to. It has a single port named `http` with a port number of `5000` and target port of `3000`, and type `ClusterIP` which limits the service to the internal cluster network.


You can use the kubectl apply command to create the service from yaml file

`kubectl apply -f my-service.yaml`

You can also use the kubectl get svc command to check the status of your service

`kubectl get svc my-service`

This command will show the details of the service created by the above yaml file.


  ## Deploy application on Kubernetes
  **Step 4

  - [Connecting GitHub Repo with Cloud Source Repository](https://www.youtube.com/watch?v=PD83mmyAbs4&list=PLqy9xGWMJzdfwb0lRkHoXyQZr02aT8FGl&index=1&t=1s)


  **Step 5
  - Clone the git project repository on GKE terminal

  **Step 6 
  - Create a namaspace : ```kubectl create namespace "nameofnamespace"```

  **Step 7
  - cd to the project that you cloned.
  - To create or update the resources defined in yaml files :  kubectl apply -f client.yaml and kubectl apply -f api.yaml
  - This will delete the resources defined in myfile.yaml : kubectl delete -f client.yaml and kubectl delete -f api.yaml
  - This will create service LoadBalancer for yolo.front and ClusterIP for yolo.api as per below table
```
ratemomishael@cloudshell:~ (shop-375720)$ kubectl get services
NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP       PORT(S)          AGE
kubernetes                    ClusterIP      10.105.0.1     <none>            443/TCP          33h
shop-846cfbf6d8-9kmzv-sdhld   LoadBalancer   10.105.1.133   130.211.233.117   3000:30970/TCP   33h
shop-service                  LoadBalancer   10.105.1.66    34.134.45.113     80:31360/TCP     33h
yolo-app-service              LoadBalancer   10.105.2.111   35.224.196.115    300:32421/TCP    38m

```
NB. Time show here is as per the time of creation of the service so they may differ from yours



- Get all objects in the namespace my-yolo-app and label app=yolo
   `kubectl get all -n yournamespace -l app=yourapp`

   eg. `kubectl get all -n default -l app=shop`

### To view the deployed application visit  [shop Web link](http://35.224.196.115:300/)

## Contact Information 

If you have any question or contributions and support, please email me at [ratemomishael@gmail.com](ratemomishael@gmail.com)

LinkedIn - [Mishael Ratemo](www.linkedin.com/in/mishael-mosoti-37b786161/)


Portfolio- [Mishael](https://mishaelratemo.github.io/my_portfolio/)
# Licence

Click to  [MIT License](Licence) view

 Copyright (c) 2023 | Mishael Ratemo