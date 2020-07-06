---
copyright:
  years: 2019, 2020
lastupdated: "2020-06-25"

subcollection: cloud-databases

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}


# An example Cloud Foundry Application
{: #tutorial-cf-app}

This tutorial uses a [sample app](https://github.com/IBM-Cloud/clouddatabases-helloworld-cloudfoundry-examples) to demonstrate how to connect a Cloud Foundry application in {{site.data.keyword.cloud_notm}} to a {{site.data.keyword.databases-for}} deployment. The application creates, reads from, and writes to a database that uses data that is supplied through the app's web interface.

Each git branch of the [repository](https://github.com/IBM-Cloud/clouddatabases-helloworld-cloudfoundry-examples) corresponds to samples in a particular programming language. For example, when you click on **Branch** there is a **Node** branch, where all examples are written in JavaScript using Node.js. The files in each folder correspond to either a database or a message queue. 

## Cloning the Hello World sample app from GitHub

Clone the repository of the language that you want to use. For instance, you can clone the **Node** repository by selecting the **Node** branch. Then click **Clone or download** to get the URL you'll need to clone using SSH or HTTPS. In your terminal, the command looks like

```shell
git clone -b node git@github.com:IBM-Cloud/clouddatabases-helloworld-cloudfoundry-examples.git
```

Once the branch is cloned, you can select the appropriate directory for the database you want to try out. Each database has its own copy of these instructions on how to provision and deploy a database or message queue and an application on {{site.data.keyword.containerlong_notm}}.

## Installing the app dependencies

Use npm to install dependencies. From your terminal, change the directory to where the sample app is located and install the dependencies that are listed in the `package.json` file.
  ```
  npm install
  ```

## Connecting to IBM Cloud

1. If you do not already have an IBM Cloud account, [sign up here](https://cloud.ibm.com/registration/)

2. [Download and install IBM Cloud CLI](/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli)

    The IBM Cloud CLI tool enables you to communicate with IBM Cloud from your terminal or command line.

3. Connect to IBM Cloud in the command-line tool and follow the prompts to log in.

    ```shell
    ibmcloud login
    ```

    **Note:** If you have a federated user ID, use the `ibmcloud login --sso` command to log in with your single sign-on ID.

 ## Creating a database deployment

The database can be created from the command line by using the `ibmcloud resource service-instance-create` command. The command takes a service instance name, a service name, plan name, and location. The service name is one of the {{site.data.keyword.databases-for}} services, `databases-for-elasticsearch`, `databases-for-enterprisedb`, `databases-for-etcd`, `databases-for-mongodb`, `databases-for-postgresql`, `databases-for-redis`, or `messages-for-rabbitmq`.
```shell
ibmcloud resource service-instance-create <your_deployment_name> <service_name> standard <region>
```

Remember the database deployment name.
{: .tip}

## Creating a Cloud Foundry alias for the deployment

1. Target the correct IBM Cloud Cloud Foundry org and space, and choose where you are going to push the application code to.
```shell
ibmcloud target --cf
```

2. Create a Cloud Foundry alias for your database deployment.
```shell
ibmcloud resource service-alias-create alias-name --instance-name instance-name
```

Cloud Foundry uses the alias to represent the database deployment as a Cloud Foundry service. The alias name can be the same as the database service instance name. So, for an "example-deployment" database the command is
```shell
ibmcloud resource service-alias-create example-deployment --instance-name example-deployment
```

## Configuring the app with Cloud Foundry

`cd` into the application's directory.  For now, the only file you need to update is the application manifest.
  ```
  ---
  applications:
  - name:    example-helloworld
    routes:
    - route: example-helloworld.us-south.cf.appdomain.cloud
    memory:  128M
    services:
      - example-deployment
  ```

1. Change the `name` value. The value that you choose will be the name of the app as it appears in your Resource List. 

2. Change the `route` value to something unique. The route that you choose determines the subdomain of your application's URL:  `<route>.{region}.cf.appdomain.cloud`. Be sure the `{region}` matches where your application is deployed.

3. Update the `service` value in `manifest.yml` to match the name of your deployment's Cloud Foundry alias.

4. Push the app to IBM Cloud. When you push the app, it will automatically be bound to the service.
```shell
ibmcloud cf push
```
Your application is now running at host you entered as the value for the `route` in `manifest.yml`.

## Code Structure

The code for connecting to the deployment and reading from and updating the database can be found in the `server` file. There's also `template` and `static` directories, which contain the html, style sheets and javascript for the web app.

| File | Description |
| ---- | ----------- |
|**server**|Establishes a connection to the database by using credentials from VCAP_ENV and handles create and read operations on the database. |
|**main.js**|Handles user input for a PUT command and parses the results of a GET command to output the contents of the database.|

The app uses a PUT and a GET operation:

- PUT
  - Takes user input from main.js.
  - Adds the user input to the database.

- GET
  - Retrieves the contents of the database.
  - Returns the response of the database command to main.js.