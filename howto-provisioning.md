---

copyright:
  years: 2019, 2020
lastupdated: "2020-05-04"

keywords: provision cloud databases, databases with terraform, provisioning parameters

subcollection: cloud-databases

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}

# Provisioning
{: #provisioning}

To create an {{site.data.keyword.cloud}} Databases deployment, you need to create an {{site.data.keyword.cloud_notm}} service instance. The service type is determined by the service ID and you need to specify the appropriate service ID when you create a new {{site.data.keyword.databases-for}} deployment.

You can provision a deployment by visiting the service's catalog page or by specifying the service ID to the command line, to the API, or to Terraform.

Deployment Type|Catalog Page|Service ID
----------|-----|-----------
Databases for Elasticsearch|[Link](https://cloud.ibm.com/catalog/services/databases-for-elasticsearch)|`databases-for-elasticsearch`
Databases for etcd|[Link](https://cloud.ibm.com/catalog/services/databases-for-etcd)|`databases-for-etcd`
Databases for MongoDB|[Link](https://cloud.ibm.com/catalog/services/databases-for-mongodb)|`databases-for-mongodb`
Databases for PostgreSQL|[Link](https://cloud.ibm.com/catalog/services/databases-for-postgresql)|`databases-for-postgresql`
Databases for Redis|[Link](https://cloud.ibm.com/catalog/services/databases-for-redis)|`databases-for-redis`
Messages for RabbitMQ|[Link](https://cloud.ibm.com/catalog/services/messages-for-rabbitmq)|`messages-for-rabbitmq`
{: caption="Table 1.{{site.data.keyword.databases-for}} deployments" caption-side="top"}

## Using the catalog

When you create the deployment from the catalog, you need to specify the following parameters.

1. **The service name** - The name can be any string and is the name that is used on the web and in the command line to identify the new deployment.
2. **The region** - The region in which the deployment resides.
3. **The database version** - The major version of the database to be created within the deployment. The latest minor version is always be used automatically. 

Users can optionally set:

1. **The resource group** - If you are organizing your services into [resource groups](/docs/resources?topic=resources-bp_resourcegroups), you can specify the resource group in this field. Otherwise, you can leave it at default.
2. **Key Protect instance and disk encryption key** - If you use Key Protect, an instance and key can be selected to encrypt the deployment's disk. If you do not use your own key, the deployment automatically creates and manages its own disk encryption key.
3. **Initial resource allocation** - Specify initial memory and disk sizes for your databases. The minimum sizes of memory and disk are selected by default. 
4. **CPU allocation** - Choose dedicated compute resources for your deployment. With dedicated cores, your resource group is given a single-tenant host with a guaranteed minimum reserve of cpu shares. Your deployments are then allocated the number of CPUs you specify. The default `Shared CPU` uses compute resources on shared hosts.
5. **Endpoints** - You can configure the types [Service Endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints) on your deployment. The default is that connections to your deployment can be made from the public network.

Once you select the appropriate settings, click **Create** to start the provisioning process. 

## Using the Command-Line

The {{site.data.keyword.cloud_notm}} CLI tool is what you use to communicate with {{site.data.keyword.cloud_notm}} from your terminal or command line. For more information, see [Download and install {{site.data.keyword.cloud_notm}} CLI](/docs/cli/reference/ibmcloud?topic=cloud-cli-install-ibmcloud-cli).

To create a {{site.data.keyword.databases-for}} deployment, you use the CLI to request a service instance with the service ID of the database (or messaging queue) you want to provision.

The command template is

```
ibmcloud resource service-instance-create <service-name> <service-id> <service-plan-id> <region> <--service-endpoints SERVICE_ENDPOINTS_TYPE>
```

More information about this command, in general, is available in the [CLI reference for resource groups](/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_commands_resource#ibmcloud_resource_service_instance_create).

For example, if you want to provision a {{site.data.keyword.messages-for-rabbitmq}} deployment, set the service name as the name you want for your deployment. Then, set `messages-for-rabbitmq` as the service ID. Enter `standard` for the service plan ID and `us-south` (or your region) for the region.

```
ibmcloud resource service-instance-create my-example-queue messages-for-rabbitmq standard us-south
```

When the command is run, provisioning the database deployment begins. The database takes some time to deploy. You can check on its progress on your {{site.data.keyword.cloud_notm}} Dashboard. You can also run:

```
ibmcloud resource service-instance <service-name>
```

This command reports the current state of the service instance.

### Additional flags and parameters

The `--service-endpoints` flag allows you to specify which types [Service Endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints) on your deployment. Its default is that connections to your deployment can be made from the public network. Possible values are 'public', 'private', 'public-and-private'.

The `service-instance-create` command supports a `-p` flag, which allows [additional parameters](#list-of-additional-parameters) to be passed to the provisioning process. The parameters are in JSON format. Some parameters values are CRNs (Cloud Resource Name), which uniquely identifies a resource in the cloud. All parameter names and values are passed as strings.

For example, if a database is being provisioned from a particular backup and the new database deployment needs 6 GB of memory, then the command looks like:

```
ibmcloud resource service-instance-create example-rabbit messages-for-rabbitmq standard us-south \
-p \ '{
  "backup_id": "crn:v1:bluemix:public:messages-for-rabbitmq:us-south:a/54e8ffe85dcedf470db5b5ee6ac4a8d8:1b8f53db-fc2d-4e24-8470-f82b15c71717:backup:06392e97-df90-46d8-98e8-cb67e9e0a8e6",
  "members_memory_allocation_mb": "6144"
}'
```

## Provisioning through the Resource Controller API

You can provision new deployments by using the Resource Controller API. However, in order to use the Resource Controller API, you need some additional preparation.

1. [Obtain an IAM token from your API token](https://{DomainName}/apidocs/resource-controller#authentication).
2. You need to know the ID of the resource group that you would like to deploy to. This information is available through the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_resource_groups). You can find a list of resource groups with `ibmcloud resource groups` and the ID of a resource group with `ibmcloud resource group`. 
3. You need to know the region that you would like to deploy to.

Once you have all the information, the create request is a `POST` to the `https://resource-controller.cloud.ibm.com/v2/resource_instances` endpoint.

```
curl -X POST \
  https://resource-controller.cloud.ibm.com/v2/resource_instances \
  -H 'Authorization: Bearer <>' \
  -H 'Content-Type: application/json' \
    -d '{
    "name": "my-instance",
    "target": "bluemix-us-south",
    "resource_group": "5g9f447903254bb58972a2f3f5a4c711",
    "resource_plan_id": "databases-for-redis-standard"
  }'
```
The parameters `name`, `target`, `resource_group`, and `resource_plan_id` are all required. If needed, you can send [additional parameters](#list-of-additional-parameters) in the request body.

More information on the Resource Controller API is found in its [API Reference](https://{DomainName}/apidocs/resource-controller#create-provision-a-new-resource-instance).

## Provisioning with Terraform

If you use Terraform to manage your infrastructure, the [{{site.data.keyword.cloud_notm}} provider for Terraform](/docs/terraform?topic=terraform-getting-started) supports provisioning {{site.data.keyword.databases-for}} deployments. A sample Terraform configuration file is on the [Cloud Databases resources](/docs/terraform?topic=terraform-databases-resources) documentation page.

## List of Additional Parameters

* `backup_id`- A CRN of a backup resource to restore from. The backup must have been created by a database deployment with the same service ID. The backup is loaded after provisioning and the new deployment starts up that uses that data. A backup CRN is in the format `crn:v1:<...>:backup:<uuid>`. If omitted, the database is provisioned empty.
* `version` - The version of the database to be provisioned. If omitted, the database is created with the most recent major and minor version.
* `key_protect_key` - The CRN of a [Key Protect key](/docs/key-protect?topic=key-protect-view-keys), which is then used for disk encryption. A key protect CRN is in the format `crn:v1:<...>:key:<id>`.
* `members_memory_allocation_mb` -  Total amount of memory to be shared between the database members within the database. For example, if the value is "6144", and there are three database members, then the deployment gets 6 GB of RAM total, giving 2 GB of RAM per member. If omitted, the default value is used for the database type is used.
* `members_disk_allocation_mb` - Total amount of disk to be shared between the database members within the database. For example, if the value is "30720", and there are three members, then the deployment gets 30 GB of disk total, giving 10 GB of disk per member. If omitted, the default value for the database type is used.
* `members_cpu_allocation_count` - Enables and allocates the number of specified dedicated cores to your deployment. For example, to use two dedicated cores per member, use `"members_cpu_allocation_count":"2"`. If omitted, the default value "Shared CPU" uses compute resources on shared hosts.
* `service-endpoints` - Selects the types [Service Endpoints](/docs/cloud-databases?topic=cloud-databases-service-endpoints) supported on your deployment. Options are `public`, `private`, or `public-and-private`. If omitted, the default is `public`. Note that in the CLI, `service-endpoints` is a flag, and not a parameter.