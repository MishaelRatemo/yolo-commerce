### To view the deployed application on Kubernetes visit  [Yolomy Web link](http://34.168.115.65:3000/)
###The file has explanation/intructions for IP2, IP3 and IP4**

### IP2
## Instructions
- Fork and Clone :  `https://github.com/ActuaryEmma/yolo`
- Change in to yolo directory : `cd yolo`
- Run docker compose to install the image and create a container : `sudo docker compose up --build`
- Backend runs on port `5000` and Frontend runs on port `3000`
- Push images to docker hub : sudo docker compose push
- The README has instructions that you can run the application  without `docker-compose.yml` and `Dockerfile` and locally.
- Go ahead a nd add a product (note that the price field only takes a numeric input) 


## Docker file
- FROM : Initializes the build stage and sets the base image as `node:19-alpine3.15` since its a light weight and fast image

- WORKDIR /app : changes the current working directory from / to /app

- COPY package*.json ./ : Copies the package.json and add them to the file system of the container at that path

- RUN npm install, RUN npm run build, RUN npm install -g serve : execute any command in a new layer on top of the current image and commits the results during build execution.

- COPY . . : copy the files from the Docker host to the filesystem of the new image
 
- EXPOSE : Indicates to Docker that the container will have a process listening on the given port or ports (api : `5000`, client : `3000`)

- CMD [ "serve", "-s", "build", "-l", "3000" ] : Excecuted when the container is launched from the newly created image.

## Docker Compose
- define and share multi container applications.
  Version : This is the version of the docker compose
  Services : Has 3 services - `api, backend and mongo`
  Each servive has an image and container that builds when you run `sudo docker compose up --build`
  Networks: There is a custom network `yolo_mynetwork`
  
  

## Docker Hub links
[Yolo Frontend](https://hub.docker.com/repository/docker/actuaryemma/frontend)
[Yolo Backend](https://hub.docker.com/repository/docker/actuaryemma/api)

******************************************************************************************************
### IP3
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


**************************************************************************************************
### IP4


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
                  image: actuaryemma/frontend:1
                  ports:
                    - containerPort: 3000
                    ```

  - This `client.yml` file creates a Pod named `yolo-client` with a single container named `clientcontainer` that runs the `actuaryemma/frontend:1` image from docker hub and exposes port `3000`.

  - This `api.yml` file creates a Pod named `yolo-api` with a single container named `backendcontainer` that runs the `actuaryemma/api:1` image from docker hub and exposes port `5000`.

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
emma_gachoki@cloudshell:~ (yolo-project-375319)$ kubectl get services --namespace my-yolo-app
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
yolo-api     ClusterIP      10.108.8.144   <none>          5000/TCP         7h6m
yolo-front   LoadBalancer   10.108.5.175   34.168.115.65   3000:31692/TCP   7h8m
```
To view pods in your namespace run below command
```
emma_gachoki@cloudshell:~ (yolo-project-375319)$ kubectl get pods --namespace my-yolo-app
NAME                                           READY   STATUS    RESTARTS   AGE
mongodb-kubernetes-operator-678dbb647b-jtx8j   0/1     Pending   0          3h44m
yolo-api-6b6cc8cc7-66bf4                       1/1     Running   0          7h5m
yolo-api-6b6cc8cc7-6zcvs                       1/1     Running   0          7h5m
yolo-api-6b6cc8cc7-k2cxg                       1/1     Running   0          7h5m
yolo-front-58985d9d4-2tvxf                     1/1     Running   0          7h7m
yolo-front-58985d9d4-j595b                     1/1     Running   0          7h7m
yolo-front-58985d9d4-jrjt7                     1/1     Running   0          7h7m 
```


- Get all objects in the namespace my-yolo-app and label app=yolo
   `kubectl get all -n my-yolo-app -l app=yolo`

## Persistent Volumes

`volume.yaml` has Persistent Volume and Persistent Volume Claim resource. 

  apiVersion: The version of the Kubernetes API to use. **v1**
  kind: The type of resource being created, in this case **PersistentVolume and PersistentVolumeClaim**.
  metadata: Metadata for the PV, including a name and labels.
  spec: The specifications for the PV, including the storage capacity and access modes.

  Update the deployment on both `api.yaml and client.yaml` that you want to associate the PV with.
  The files have `volumeMounts and volumes`
  
  ```   volumeMounts:
        - name: yolo-pv
          mountPath: /var/www/html
    volumes:
    - name: yolo-pv
      persistentVolumeClaim:
        claimName: yolo-pvc 
  ```
  The container has a `volumeMount` named `yolo-pv` that is mounted at the path `mountPath: /var/www/html` .
  The `mountPath: /var/www/html`  is commonly used as a mount point for web servers.
  The Container has volume name `yolo-pv` that is using the persistent volume claim `yolo-pvc`

  To view list of the PVs : `kubectl get pv`
  ```
  NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS    
  yolo-pv   10Gi       RWO            Retain           Available        
  ```
************************************************************************************************************
### To view the deployed application on Kubernetes  visit  [Yolomy Web link](http://34.168.115.65:3000/)