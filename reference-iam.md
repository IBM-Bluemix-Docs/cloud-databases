---

copyright:
  years: 2019
lastupdated: "2019-09-30"

subcollection: cloud-databases

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:note: .note} 
{:tip: .tip}

# Identity and Access Management Integration
{: #iam}

Access to {{site.data.keyword.cloud}} Databases service instances for users in your account is controlled by {{site.data.keyword.cloud_notm}} [Identity and Access Management (IAM)](/docs/iam?topic=iam-getstarted). 

This document covers the integration of IAM with Cloud Databases, which includes {{site.data.keyword.databases-for-elasticsearch}}, {{site.data.keyword.databases-for-enterprisedb}}, {{site.data.keyword.databases-for-etcd}}, {{site.data.keyword.databases-for-mongodb}}, {{site.data.keyword.databases-for-postgresql}}, {{site.data.keyword.databases-for-redis}}, and {{site.data.keyword.messages-for-rabbitmq}}.
{: .note}

IAM is only integrated with high-level service access, which governs privileges and operations available in the Cloud Databases API and the Cloud Databases CLI plugin. It does not govern database-level users and privileges. Database access is governed by the standard access controls provided by the database. IAM does not control database users.

For information about assigning user roles in {{site.data.keyword.cloud_notm}}, see [Managing IAM access](/docs/iam?topic=iam-iammanidaccser).

The following table provides a general overview of actions that are mapped to service management roles. Service management roles enable users to perform tasks on service resources at the service level, for example assign user access for the service, create or delete service IDs, create instances, and bind instances to applications.

Service management role | Description of actions | Example actions
-----------------|-----------------|-----------------
Viewer | As a viewer, you can view service instances, but you can't modify them. | View Service Overview and View Alerts
Editor | As an editor, you can perform all platform actions except for managing the account and assigning access policies. | Scale a Deployment and Change a Deployment's Password
Operator | As an operator, you can perform all platform actions except for managing the account and assigning access policies. | Scale a Deployment and Change a Deployment's Password
Administrator | As an administrator, you can perform all platform actions based on the resource this role is being assigned, including assigning access policies to other users. | Scale a Deployment, Change a Deployment's Password, and Assign Access Policies
{: caption="Table 1. IAM user roles and actions" caption-side="top"}

## Actions for {{site.data.keyword.databases-for}} API

Access to certain API endpoints and requests is governed by role. The following lists the access policy for each role for {{site.data.keyword.cloud}} Databases. 

### Viewer 

The allowed actions for the Viewer role.
```
GET /v4/ibm/deployables
Read Deployables
---
GET /v4/ibm/regions
Read Discover available regions
---
GET /v4/ibm/tasks/:task_id
Read a Task
---
GET /v4/ibm/backups/:backup_id
Read a Backup
---
GET /v4/ibm/deployments/:deployment_id
Read a Deployment
---
GET /v4/ibm/deployables/:deployable_id/groups
Read deployable group
---
GET /v4/ibm/deployments/:deployment_id/point_in_time_recovery_data
Read all deployment point-in-time-recovery data
---
GET /v4/ibm/deployments/:deployment_id/tasks
Read all deployment tasks
---
GET /v4/ibm/deployments/:deployment_id/backups
Read all deployment backups
---
GET /v4/ibm/deployments/:deployment_id/remotes
Read all deployment remotes
---
GET /v4/ibm/deployments/:deployment_id/groups
Read all deployment groups
---
GET /v4/ibm/deployments/:deployment_id/configuration/schema
Read deployment configuration schema
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id
Read a DeploymentUser
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id/connections
Read deployment user connections
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id/connections/:endpoint_type
Read deployment user connections
---
POST /v4/ibm/deployments/:deployment_id/users/:user_id/connections
Create deployment user connections
---
POST /v4/ibm/deployments/:deployment_id/users/:user_id/connections/:endpoint_type
Create deployment user connections
---
GET /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Read Allowlisted IP Addresses
```

### Operator and Editor

The Operator and Editor roles are functionally the same for {{site.data.keyword.databases-for}}. This list contains allowed actions for the Operator and the Editor roles.
```
GET /v4/ibm/deployables
Read Deployables
---
GET /v4/ibm/regions
Read Discover available regions
---
GET /v4/ibm/tasks/:task_id
Read a Task
---
GET /v4/ibm/backups/:backup_id
Read a Backup
---
GET /v4/ibm/deployments/:deployment_id
Read a Deployment
---
PATCH /v4/ibm/deployments/:deployment_id
Update a Deployment
---
GET /v4/ibm/deployables/:deployable_id/groups
Read deployable group
---
GET /v4/ibm/deployments/:deployment_id/point_in_time_recovery_data
Read all deployment point-in-time-recovery data
---
GET /v4/ibm/deployments/:deployment_id/tasks
Read all deployment tasks
---
GET /v4/ibm/deployments/:deployment_id/backups
Read all deployment backups
---
POST /v4/ibm/deployments/:deployment_id/backups
Create an on-demand backup
---
GET /v4/ibm/deployments/:deployment_id/remotes
Read all deployment remotes
---
PATCH /v4/ibm/deployments/:deployment_id/remotes
Update a remote replica
---
POST /v4/ibm/deployments/:deployment_id/remotes/resync
Resync remote replica
---
GET /v4/ibm/deployments/:deployment_id/groups
Read all deployment groups
---
PATCH /v4/ibm/deployments/:deployment_id/groups/:group_id
Read deployment group
---
DELETE /v4/ibm/deployments/:deployment_id/management/database_connections
Kill all database connections
---
PATCH /v4/ibm/deployments/:deployment_id/configuration
Update deployment configuration
---
GET /v4/ibm/deployments/:deployment_id/configuration/schema
Read deployment configuration schema
---
POST /v4/ibm/deployments/:deployment_id/users
Create a DeploymentUser
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id
Read a DeploymentUser
---
PATCH /v4/ibm/deployments/:deployment_id/users/:user_id
Update a DeploymentUser
---
DELETE /v4/ibm/deployments/:deployment_id/users/:user_id
Remove a DeploymentUser
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id/connections
Read deployment user connections
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id/connections/:endpoint_type
Read deployment user connections
---
POST /v4/ibm/deployments/:deployment_id/users/:user_id/connections
Create deployment user connections
---
POST /v4/ibm/deployments/:deployment_id/users/:user_id/connections/:endpoint_type
Create deployment user connections
---
GET /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Read Allowlisted IP Addresses
---
POST /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Create an Allowlisted IP Addresses
---
DELETE /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses/:ip_address_id
Remove an Allowlisted IP Addresses
---
PUT /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Bulk allowlist IP addresses
---
POST /v4/ibm/deployments/:deployment_id/elasticsearch/file_syncs
Create elasticsearch file sync
```

### Administrator

The allowed actions for the Administrator role.
```
GET /v4/ibm/deployables
Read Deployables
---
GET /v4/ibm/regions
Read Discover available regions
---
GET /v4/ibm/tasks/:task_id
Read a Task
---
GET /v4/ibm/backups/:backup_id
Read a Backup
---
GET /v4/ibm/deployments/:deployment_id
Read a Deployment
---
PATCH /v4/ibm/deployments/:deployment_id
Update a Deployment
---
GET /v4/ibm/deployables/:deployable_id/groups
Read deployable group
---
GET /v4/ibm/deployments/:deployment_id/point_in_time_recovery_data
Read all deployment point-in-time-recovery data
---
GET /v4/ibm/deployments/:deployment_id/tasks
Read all deployment tasks
---
GET /v4/ibm/deployments/:deployment_id/backups
Read all deployment backups
---
POST /v4/ibm/deployments/:deployment_id/backups
Create an on-demand backup
---
GET /v4/ibm/deployments/:deployment_id/remotes
Read all deployment remotes
---
PATCH /v4/ibm/deployments/:deployment_id/remotes
Update a remote replica
---
POST /v4/ibm/deployments/:deployment_id/remotes/resync
Resync remote replica
---
GET /v4/ibm/deployments/:deployment_id/groups
Read all deployment groups
---
PATCH /v4/ibm/deployments/:deployment_id/groups/:group_id
Read deployment group
---
DELETE /v4/ibm/deployments/:deployment_id/management/database_connections
Kill all database connections
---
PATCH /v4/ibm/deployments/:deployment_id/configuration
Update deployment configuration
---
GET /v4/ibm/deployments/:deployment_id/configuration/schema
Read deployment configuration schema
---
POST /v4/ibm/deployments/:deployment_id/users
Create a DeploymentUser
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id
Read a DeploymentUser
---
PATCH /v4/ibm/deployments/:deployment_id/users/:user_id
Update a DeploymentUser
---
DELETE /v4/ibm/deployments/:deployment_id/users/:user_id
Remove a DeploymentUser
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id/connections
Read deployment user connections
---
GET /v4/ibm/deployments/:deployment_id/users/:user_id/connections/:endpoint_type
Read deployment user connections
---
POST /v4/ibm/deployments/:deployment_id/users/:user_id/connections
Create deployment user connections
---
POST /v4/ibm/deployments/:deployment_id/users/:user_id/connections/:endpoint_type
Create deployment user connections
---
GET /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Read Allowlisted IP Addresses
---
POST /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Create an Allowlisted IP Addresses
---
DELETE /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses/:ip_address_id
Remove an Allowlisted IP Addresses
---
PUT /v4/ibm/deployments/:deployment_id/whitelists/ip_addresses
Bulk allowlist IP addresses
---
POST /v4/ibm/deployments/:deployment_id/elasticsearch/file_syncs
Create elasticsearch file sync
```