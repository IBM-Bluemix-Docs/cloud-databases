---
copyright:
  years: 2019
lastupdated: "2019-04-09"

subcollection: cloud-databases

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}

# Key Protect Integration
{: #key-protect}

The data you store in {{site.data.keyword.cloud}} Databases are encrypted by default by using randomly generated keys. If you need to control the encryption keys, you can use [{{site.data.keyword.keymanagementservicelong_notm}}](/docs/services/key-protect?topic=key-protect-about) to create, add, and manage encryption keys. Then, you can associate those keys with your {{site.data.keyword.databases-for}} deployment to encrypt your databases.

To get started, you need [{{site.data.keyword.keymanagementserviceshort}}](https://{DomainName}/catalog/services/key-protect) provisioned on your {{site.data.keyword.cloud_notm}} account.

## Creating or adding a key in {{site.data.keyword.keymanagementserviceshort}}

Navigate to your instance of {{site.data.keyword.keymanagementserviceshort}} and [generate or enter a key](/docs/services/key-protect?topic=key-protect-getting-started-tutorial).

## Granting service authorization

Authorize {{site.data.keyword.keymanagementserviceshort}} for use with {{site.data.keyword.databases-for}} deployments:

1. Open your {{site.data.keyword.cloud_notm}} dashboard.
2. From the menu bar, click **Manage** &gt; **Access (IAM)**.
3. In the side navigation, click **Authorizations**.
4. Click **Create**.
5. In the **Source service** menu, select the service of the deployment. For example, **Databases for PostgreSQL** or **Messages for RabbitMQ**
6. In the **Source service instance** menu, select **All service instances**.
7. In the **Target service** menu, select **Key Protect**.
8. In the **Target service instance** menu, select the service instance to authorize.
9. Enable the **Reader** role.
10. Click **Authorize**.

## Removing keys and deleting your data

Caution - If you delete the key that is used to encrypt your service, you lose access to the data. You will not be able to recover the data from the live deployment. You might be able recover the database by restoring a backup into a new deployment.

If you want to securely delete your data, you must delete both the deployment and remove the Key Protect key.