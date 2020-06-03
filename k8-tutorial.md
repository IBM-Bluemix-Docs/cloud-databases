---
copyright:
  years: 2019
lastupdated: "2019-02-12"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}


# An example {{site.data.keyword.containershort_notm}} Application
{: #tutorial-k8s-app}

The [{{site.data.keyword.databases-for}} "Hello World" Kubernetes examples](https://github.com/IBM-Cloud/clouddatabases-helloworld-kubernetes-examples) are sample {{site.data.keyword.cloud}} applications that show you how to connect to a {{site.data.keyword.databases-for}} deployment to an {{site.data.keyword.containerlong_notm}} application written in a various programming languages.

Each git branch of the [repository](https://github.com/IBM-Cloud/clouddatabases-helloworld-kubernetes-examples) corresponds to samples in a particular programming language. For example, when you click on **Branch** there is a **Node** branch, where all examples are written in JavaScript using Node.js. The files in each folder correspond to either a database or a message queue.  

## Trying out the sample applications

Clone the repository of the language that you want to use. For instance, you can clone the **Node** repository by selecting the **Node** branch. Then click **Clone or download** to get the URL you'll need to clone using SSH or HTTPS. In your terminal, the command looks like

```shell
git clone -b node git@github.com:IBM-Cloud/clouddatabases-helloworld-kubernetes-examples.git
```

Once the branch has been cloned, you can select the appropriate directory for the database you want to try out. Each database has its own copy of these instructions on how to provision and deploy a database or message queue and an application on {{site.data.keyword.containerlong_notm}}.

## Running on IBM Cloud

1. If you do not already have an IBM Cloud account, [sign up here](https://cloud.ibm.com/registration/)

2. [Download and install IBM Cloud CLI](/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli)

    The IBM Cloud CLI tool enables you to communicate with IBM Cloud from your terminal or command line.

3. Install the {{site.data.keyword.containershort_notm}} CLI plugin and the Container Registry CLI plugin

      ```shell
      ibmcloud plugin install container-service
      ibmcloud plugin install container-registry 
      ```

      To verify their installation, run:

      ```shell
      ibmcloud plugin list

      Listing installed plug-ins...

      Plugin Name                            Version   Status
      container-registry                     0.1.382
      container-service/kubernetes-service   0.3.34
      ```

4. [Download and install the Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

      Follow the instructions for downloading and installing the Kubernetes CLI for the platform you're using.

5. Connect to IBM Cloud in the command line tool and follow the prompts to log in.

      ```shell
      ibmcloud login
      ```

      **Note:** If you have a federated user ID, use the `ibmcloud login --sso` command to log in with your single sign-on ID.

## Creating your database

6. Create your database deployment.

    The database can be created from the command line using the `ibmcloud resource service-instance-create` command. The command takes a service instance name, a service name, plan name, and location. The service name is one of the {{site.data.keyword.databases-for}} services, `databases-for-elasticsearch`, `databases-for-etcd`, `databases-for-mongodb`, databases-for-postgresql`, `databases-for-redis`, or `messages-for-rabbitmq`.

    ```shell
    ibmcloud resource service-instance-create <your_deployment_name> <service_name> standard <region>
    ```

    Remember the database deployment name.
    {: .tip}


## Configuring the Kubernetes App

7. [Create an IBM Cloud Kubernetes Service](/docs/containers?topic=containers-getting-started#getting-started). Choose the location and resource group that you want to set up your cluster in. Select the cluster type that you want to use. This example only requires the lite plan, which comes with 1 worker node.

      Once a cluster is provisioned, you are given a list of steps to follow to access your cluster and set the environment variables under the _Access_ tab. You are also able to verify that your deployment is provisioned and running normally.

8. Make sure that you are targeting the correct IBM Cloud resource group of your IBM Cloud Kubernetes Service.

      Use the following command to target your cluster resource group if your resource group is other than `default`.

      ```shell
      ibmcloud target -g <resource_group_name>
      ```

      For this example, we're using the `default` resource group.

9. Create your own private image repository in [IBM Cloud Container Registry](/docs/Registry?topic=registry-registry_overview) to store your application's Docker image. Since we want the images to be private, we need to create a namespace, which creates a unique URL to your image repository.  

      ```shell
      ibmcloud cr namespace-add <your_namespace>
      ```

10. Add the Cloud Databases deployment to your cluster.

      ```
      ibmcloud ks cluster service bind <your_cluster_name> default <your_database_deployment>
      ```

      Note: If your database uses both [public and private endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints), your public endpoint is used by default. Therefore, if you want to select the private endpoint, first you need to create a service key for your database so Kubernetes can use it when binding to the database. You set up a service key using the command:

      ```
      ibmcloud resource service-key-create <your-private-key> --instance-name <your_database_deployment> --service-endpoint private  
      ```
      
      The private service endpoint is selected with `--service-endpoint private`. After that, you bind the database to the Kubernetes cluster through the private endpoint by using the command:

      ```
      ibmcloud ks cluster service bind <your_cluster_name> default <your_database_deployment> --key example-private-key
      ```

11. Verify that the Kubernetes secret was created in your cluster namespace. Kubernetes uses secrets to store confidential information like the IBM Cloud Identity and Access Management (IAM) API key and the URL that the container uses to gain access. Running the following command, you get the API key for accessing the instance of your deployment that's provisioned in your account.

      ```shell
      kubectl get secrets --namespace=default
      ```

    **Note**: save the name of the secret that was generated when you bound `your_database_name` to your Kubernetes service.

12. If you haven't already, clone the app in one of the available languages to your local environment from your terminal by using the following command

      ```shell
      git clone -b <language> git@github.com:IBM-Cloud/clouddatabases-helloworld-kubernetes-examples.git
      ```

13. `cd` into this newly created directory, and `cd` into the database folder. The code for connecting to the service, and reading from and updating the database can be found in `server.js`. See [Code Structure](#code-structure) and the code comments for information on the app's functions. There's also a `public` directory, which contains the html, style sheets, and JavaScript for the web app. But, to get the application to work, we first need to push the Docker image of this application to our IBM Cloud Container Registry.

14. Build and push the application's Docker image to your IBM Cloud Container Registry. Give the container a name.

    ```shell
    ibmcloud cr build -t <region>.icr.io/<namespace>/<container_name> .
    ```

    After it's built, you can view the image in container registry by using

    ```shell
    ibmcloud cr images
    ```

    You get something like the following response

    ```shell
    REPOSITORY                                TAG      DIGEST         NAMESPACE   CREATED       SIZE    SECURITY STATUS
    <region>.icr.io/mynamespace/container_name latest   81c3959ea657   mynamespace 4 hours ago   28 MB   No Issues
    ```

15. Update the Kubernetes deployment configuration file `clouddb-deployment.yaml`.

    Change the `image` name to the repository name that you got from the previous step:

    ```yaml
    image: "<region>.icr.io/mynamespace/<container_name>" # Edit me
    ```

    Now, under `secretKeyRef`, change the name of `<db-secret-name>` to match the name of the secret that was created when you bound your database deployment to your Kubernetes cluster.

    ```yaml
    secretKeyRef:
      name: <db-secret-name> # Edit me
    ```

    As for the `service` configuration at the bottom of the file, [`nodePort`](/docs/containers?topic=containers-nodeport) indicates the port that the application can be accessed from. You have ports in the  range from 30000 - 32767 that you can use, but we chose 30081. As for the TCP port, it's set to 8080, which is the port that the Node.js application runs on in the container.

## Deploying your Kubernetes App

16. Deploy the application to IBM Cloud Kubernetes Service. When you deploy the application, it is automatically bound to your Kubernetes cluster.

    ```shell
    kubectl apply -f clouddb-deployment.yml
    ```

17. Get the IP for the application.

    ```shell
    ibmcloud ks workers <cluster_name>
    ```

    The result is something like:

    ```shell
    ID                                                 Public IP        Private IP      Machine Type   State    Status   Zone    Version
    kube-hou02-pa1a59e9fd92f44af9b4147a27a31db5c4-w1   199.199.99.999   10.76.202.188   free           normal   Ready    hou02   1.10.11_1536
    ```

    Now you can access the application from the Public IP from port 30081.

The clouddatabases-helloworld app displays the contents of an _examples_ database. To demonstrate that the app is connected to your service, add some words to the database. The words are displayed as you add them, with the most recently added words displayed first.

## Code Structure

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
