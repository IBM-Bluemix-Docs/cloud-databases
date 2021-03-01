---
copyright:
  years: 2019, 2021
lastupdated: "2021-03-01"

keywords: kubernetes, tutorial, setup

subcollection: cloud-databases
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}


# An example {{site.data.keyword.containershort_notm}} Application
{: #tutorial-k8s-app}

The [{{site.data.keyword.databases-for}} "Hello World" Kubernetes examples](https://github.com/IBM-Cloud/clouddatabases-helloworld-kubernetes-examples) repository holds sample {{site.data.keyword.cloud}} applications that show you how to connect to a {{site.data.keyword.databases-for}} deployment to an {{site.data.keyword.containerlong_notm}} application written in a various programming languages.  

Each Git branch of the examples repository corresponds to samples in a particular programming language. For example, when you click **Branch**,a **Node** branch exists where all examples are written in JavaScript that uses Node.js. The files in each folder correspond to either a database or a message queue.  

## Trying out the sample applications

Clone the repository of the language that you want to use. For instance, you can clone the **Node** repository by selecting the **Node** branch. Then, click **Clone or download** to get the URL you need to clone by using SSH or HTTPS. In your console, the command looks like:

```shell
git clone -b node git@github.com:IBM-Cloud/clouddatabases-helloworld-kubernetes-examples.git
```
{: pre}

Or for [cloning by using HTTPS](https://docs.github.com/en/github/using-git/which-remote-url-should-i-use#cloning-with-https-urls-recommended):
```
git clone -b node https://github.com/IBM-Cloud/clouddatabases-helloworld-kubernetes-examples.git
```
{: pre}

Once the branch is cloned, select the appropriate directory for the database you want to try out. Each database has its own copy of these instructions on how to provision and deploy a database or message queue and an application on {{site.data.keyword.containerlong_notm}}.

## Running on {{site.data.keyword.cloud_notm}}

1. If you do not already have an {{site.data.keyword.cloud_notm}} account, [sign up here](https://cloud.ibm.com/registration/)

2. [Download and install {{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli)

   The {{site.data.keyword.cloud_notm}} CLI tool enables you to communicate with {{site.data.keyword.cloud_notm}} from your console or command line.

3. Install the {{site.data.keyword.containershort_notm}} CLI plug-in and the Container Registry CLI plug-in

   ```shell
   ibmcloud plugin install container-service
   ibmcloud plugin install container-registry 
   ```
   {: pre}

   To verify their installation, run:

   ```shell
   ibmcloud plugin list
   ```
   {: pre}

   ```
   Listing installed plug-ins...

   Plugin Name                            Version   Status
   container-registry                     0.1.382
   container-service/kubernetes-service   0.3.34
   ```
   {: screen}

4. [Download and install the Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

   Follow the instructions for downloading and installing the Kubernetes CLI for the platform you're using.

5. Connect to {{site.data.keyword.cloud_notm}} in the command-line tool and follow the prompts to log in.

   ```shell
   ibmcloud login
   ```
   {: pre}

   If you have a federated user ID, use the `ibmcloud login --sso` command to log in with your single sign-on ID.
   {: note}

## Creating your database

6. Create your database deployment.

   Note that this process creates a standard database instance in the service you specify that incurs additional charges in your selected plan. 
   {: note}

   The database can be created from the command line by using the `ibmcloud resource service-instance-create` command. The command takes a service instance name, a service name, plan name, and location. 
    
   The service name is one of the {{site.data.keyword.databases-for}} services, `databases-for-datastax`, `databases-for-elasticsearch`, `databases-for-enterprisedb`, `databases-for-etcd`, `databases-for-mongodb`, `databases-for-postgresql`, `databases-for-redis`, or `messages-for-rabbitmq`.
   
   ```shell
   ibmcloud resource service-instance-create <your_deployment_name> <service_name> standard <region>
   ```
   {: pre}

   Remember the database deployment name. Find your [region identifier here](/docs/cloud-databases?topic=cloud-databases-regions).
   {: tip}

## Configuring the Kubernetes App

7. [Create an {{site.data.keyword.containershort_notm}}](/docs/containers?topic=containers-getting-started#getting-started). Choose the location and resource group that you want to set up your cluster in. Select the cluster type that you want to use. This example only requires the lite plan, which comes with one worker node.

   Once a cluster is provisioned, you are given a list of steps to follow to access your cluster and set the environment variables under the _Access_ tab. You are also able to verify that your deployment is provisioned and running normally.

8. Make sure that you are targeting the correct {{site.data.keyword.cloud_notm}} resource group of your {{site.data.keyword.containershort_notm}}.

   Use the following command to target your cluster resource group if your resource group is other than `default`.

   ```shell
   ibmcloud target -g <resource_group_name>
   ```
   {: pre}

   For this example, we're using the `default` resource group.

9. Create your own private image repository in [{{site.data.keyword.registryshort_notm}}](/docs/Registry?topic=Registry-getting-started) to store your application's Docker image. Since we want the images to be private, we need to create a namespace, which creates a unique URL to your image repository.  

   ```shell
   ibmcloud cr namespace-add <your_namespace>
   ```
   {: pre}

10. Add the Cloud Databases deployment to your cluster.

   ```shell
   ibmcloud ks cluster service bind --cluster <your_cluster_name> --namespace default --service <your_database_deployment>
   ```
   {: pre}

   The "default" namespace refers to the Kubernetes instance and not the user created image store namespace. Likewise, if your database uses both [public and private endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints), your public endpoint is used by default. Therefore, if you want to select the private endpoint, first you need to create a service key for your database so Kubernetes can use it when binding to the database. You set up a service key by using the command:
   {: note}

   ```
   ibmcloud resource service-key-create <your-private-key> --instance-name <your_database_deployment> --service-endpoint private  
   ```
   {: pre}
   {: note}
      
   The private service endpoint is selected with `--service-endpoint private`. After that, you bind the database to the Kubernetes cluster through the private endpoint by using the command:

   ```shell
   ibmcloud ks cluster service bind <your_cluster_name> default <your_database_deployment> --key example-private-key
   ```
   {: pre}

11. Verify that the Kubernetes secret was created in your cluster namespace. Kubernetes uses secrets to store confidential information like the {{site.data.keyword.IBM_notm}} {{site.data.keyword.iamshort}} (IAM) API key and the URL that the container uses to gain access. Running the following commands to first Set the cluster as the context for this session and then get the API key for accessing the instance of your deployment that's provisioned in your account.

   ```shell
   ibmcloud ks cluster config --cluster <cluster_name_or_ID>
   ```
   {: pre}

   then

   ```shell
   kubectl get secrets --namespace=default
   ```
   {: pre}

   Save the name of the secret that was generated when you bound `your_database_name` to your Kubernetes service.
   {: note}

12. If you haven't already, clone the app in one of the available languages to your local environment from your console by using the following command

   ```shell
   git clone -b <language> git@github.com:IBM-Cloud/clouddatabases-helloworld-kubernetes-examples.git
   ```
   {: pre}

13. `cd` into this newly created directory, and `cd` into the database folder. The code for connecting to the service, and reading from and updating the database can be found in `server.js`. See [Code Structure](#code-structures) and the code comments for information on the app's functions. There's also a `public` directory, which contains the html, stylesheets, and JavaScript for the web app. But, to get the application to work, we first need to push the Docker image of this application to our {{site.data.keyword.registryshort_notm}}.

14. Build and push the application's Docker image to your {{site.data.keyword.registryshort_notm}}. Specify the appropriate [region](/docs/cloud-databases?topic=cloud-databases-regions) and give the container a name.

   ```shell
   ibmcloud cr build -t <region>.icr.io/<namespace>/<container_name> .
   ```
   {: pre}

   After it's built, you can view the image in container registry by using

   ```shell
   ibmcloud cr images
   ```
   {: pre}

   You get something like the following response

   ```shell
   REPOSITORY                                TAG      DIGEST         NAMESPACE   CREATED       SIZE    SECURITY STATUS
   <region>.icr.io/mynamespace/container_name latest   81c3959ea657   mynamespace 4 hours ago   28 MB   No Issues
   ```
   {: screen}

15. Update the Kubernetes deployment configuration file `clouddb-deployment.yaml`.

   Change the `image` name to the repository name that you got from the previous step:

   ```yaml
   image: "<region>.icr.io/mynamespace/<container_name>" # Edit me
   ```
   {: pre}

   Now, under `secretKeyRef`, change the name of `<db-secret-name>` to match the name of the secret that was created when you bound your database deployment to your Kubernetes cluster.

   ```yaml
   secretKeyRef:
     name: <db-secret-name> # Edit me
   ```
   {: pre}

   As for the `service` configuration at the bottom of the file, [`nodePort`](/docs/containers?topic=containers-nodeport) indicates the port that the application can be accessed from. You have ports in the range 30000 - 32767 that you can use, but we chose 30081. As for the TCP port, it's set to 8080, which is the port that the Node.js application runs on in the container.

## Deploying your Kubernetes App

16. Deploy the application to {{site.data.keyword.containershort_notm}}. When you deploy the application, it is automatically bound to your Kubernetes cluster.

   ```shell
   kubectl apply -f clouddb-deployment.yaml
   ```
   {: pre}

17. Get the IP for the application.

   ```shell
   ibmcloud ks workers -c <cluster_name>
   ```
   {: pre}

   The result is something like:

   ```shell
   ID                                                 Public IP        Private IP      Machine Type   State    Status   Zone    Version
   kube-hou02-pa1a59e9fd92f44af9b4147a27a31db5c4-w1   199.199.99.999   10.76.202.188   free           normal   Ready    hou02   1.10.11_1536
   ```
   {: screen}

   Now you can access the application from the Public IP from port 30082.

The clouddatabases-helloworld app displays the contents of an _examples_ database. To demonstrate that the app is connected to your service, add some words to the database. The words are displayed as you add them, with the most recently added words displayed first.

## Code Structure
{: #code-structures}

| File | Description |
| ---- | ----------- |
|**server.js**|Establishes a connection to the database by using credentials from BINDING (the name we created in the Kubernetes deployment file to expose the credentials) and handles create and read operations on the database. |
|**main.js**|Handles user input for a PUT command and parses the results of a GET command to output the contents of the database.|

The app uses a PUT and a GET operation:

- PUT
  - Takes user input from main.js.
  - Adds the user input to the database.

- GET
  - Retrieves the contents of the database.
  - Returns the response of the database command to main.js.
