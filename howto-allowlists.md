---

Copyright:
  years: 2019, 2020
lastupdated: "2020-06-25"

subcollection: cloud-databases

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Allowlisting
{: #allowlisting} 

If you want to restrict access to your databases, you can allowlist specific IP addresses or ranges of IP addresses on your deployment.

If you use allowlisting in your environment, you can allowlist our services using the list of subnets for each region.

## Using IP Allowlisting on your Deployment

When you create a allowlist, only IP addresses that match the allowlist or are in the range of IP addresses in the allowlist can connect to your deployment. Allowlists can be enabled for both public endpoints and private endpoints. When no IP addresses are in the allowlist, the allowlist is disabled and the deployment accepts connections from any IP address.

Even if not explicitly allowlisted, {{site.data.keyword.cloud_notm}} management services are still able to connect.
{: .tip}

### Setting an Allowlist

The UI for managing allowlists is on the _Settings_ tab of your _Deployment Overview_.

**IP addresses** - The *IP* field can take a single complete IPv4 address with or without a netmask. Without a netmask, incoming connections must come from exactly that IP address. 

IPv6 is not currently supported.
{: tip}

**Netmasks** - To allow a connection from a specified range of IP addresses, use a netmask. The IP address must be fully specified. That means entering, for example, 192.168.1.0/24 rather than 192.168.1/24.

**Description** - The *Description* can be any user-significant text for identifying the allowlist entry - a customer name, project identifier, or employee number, for example. The description field is required.

### Setting an Allowlist through the CLI

The {{site.data.keyword.databases-for}} CLI Plug-in offers a set of commands for managing allowlists. Use [`cdb deployment-whitelist-add`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-whitelist-add) to add an allowlist. For example,
```
ibmcloud cdb deployment-whitelist-add example-deployment 198.51.100.1 "Allowlisted for testing"
```
for a single IP address and 
```
ibmcloud cdb deployment-whitelist-add example-deployment 198.51.100.0/24 "Testing range is now open" 
```
for an IP range.

Use [`cdb deployment-whitelist-list`](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference#deployment-whitelist-list) to view the current allowlist. For example,
```
ibmcloud cdb deployment-whitelist-list <deployment name or CRN>
```

More information is on the {{site.data.keyword.databases-for}} CLI Plug-in [reference page](/docs/databases-cli-plugin?topic=databases-cli-plugin-cdb-reference).

### Setting an Allowlist through the API

To use the {{site.data.keyword.databases-for}} API to manage your allowlist with the [`/deployments/{id}/whitelists/`](https://cloud.ibm.com/apidocs/cloud-databases-api#retrieve-the-whitelisted-addresses-and-ranges-for-) endpoint. You can retrieve the current allowlist, add entries to the allowlist, and also bulk upload IP addresses to the allowlist from the API. 

More information is in the [API Reference](https://cloud.ibm.com/apidocs/cloud-databases-api)

### Removal

From the UI, remove an IP address or netmask from the allowlist by clicking *Remove*. You can also use CLI command is `cdb deployment-whitelist-delete` or send a `DELETE` request to the API endpoint. When all entries on the allowlist are removed, the allowlist is disabled and all IP addresses are accepted by your deployment.

## Allowlisting {{site.data.keyword.databases-for}} in your Environment

If you use allowlisting to control connections in your environment, you can use the lists below to allowlist {{site.data.keyword.databases-for}} deployments. You should allowlist all of the subnet ranges for the _entire_ region that your deployments live in.

### `che01` List
Subnet | Location
-- | --
169.38.95.96/27 | Chennai 1
169.38.121.144/28 | Chennai 1

### `eu-gb` List
Subnet | Location
-- | --
158.175.64.80/28 | London 4
158.175.139.160/27 | London 4
158.175.147.0/26 | London 4
158.175.151.0/25 | London 4
158.175.156.0/24 | London 4
141.125.69.32/27 | London 5
141.125.71.0/28 | London 5
141.125.83.64/26 | London 5
141.125.88.0/25 | London 5
158.176.109.192/28 | London 6
158.176.109.224/27 | London 6
158.176.122.0/26 | London 6
158.176.139.128/25 | London 6
158.176.148.0/24 | London 6

### `osl01` List
Subnet | Location
-- | --
169.51.81.224/27 | Oslo 1
169.51.85.144/28 | Oslo 1

### `seo01` List
Subnet | Location
-- | --
169.56.80.128/28 | Seoul 1
169.56.106.0/26 | Seoul 1
169.56.120.96/27 | Seoul 1

### `au-syd` List
Subnet | Location
-- | --
168.1.29.48/28 | Sydney 1
168.1.216.96/27 | Sydney 1
130.198.102.144/28 | Sydney 4
130.198.102.160/27 | Sydney 4
135.90.68.96/28 | Sydney 5
135.90.69.96/27 | Sydney 5

### `jp-tok` List
Subnet | Location
-- | --
161.202.140.160/27 | Tokyo 2
169.56.7.192/26 | Tokyo 2
169.56.45.160/28 | Tokyo 2
128.168.72.32/27 | Tokyo 4
128.168.72.176/28 | Tokyo 4
128.168.93.64/26 | Tokyo 4
165.192.71.32/28 | Tokyo 5
165.192.71.96/27 | Tokyo 5

### `us-east` List
Subnet | Location
-- | --
52.116.78.0/25 | Washington 4
169.47.179.0/26 | Washington 4
169.63.72.160/28 | Washington 4
169.63.121.128/27 | Washington 4
169.63.128.160/27 | Washington 6
169.63.135.176/28 | Washington 6
169.63.139.192/26 | Washington 6
169.61.123.80/28 | Washington 7
169.62.42.32/27 | Washington 7
169.62.54.128/26 | Washington 7
169.62.60.0/25 | Washington 7

### `eu-de` List
Subnet | Location
-- | --
158.177.77.32/27 | Frankfurt 2
158.177.87.128/25 | Frankfurt 2
158.177.155.0/26 | Frankfurt 2
158.177.241.0/24 | Frankfurt 2
161.156.95.192/27 | Frankfurt 4
161.156.97.0/26 | Frankfurt 4
161.156.131.128/25 | Frankfurt 4
161.156.152.0/24 | Frankfurt 4
149.81.73.144/28 | Frankfurt 5
149.81.80.224/27 | Frankfurt 5
149.81.114.0/26 | Frankfurt 5
149.81.132.0/25 | Frankfurt 5

### `us-south` List
Subnet | Location
-- | --
52.116.167.0/24 | Dallas 10
52.116.179.160/28 | Dallas 10
52.116.190.0/26 | Dallas 10
52.117.150.0/25 | Dallas 10
52.117.184.0/24 | Dallas 10
169.46.57.0/27 | Dallas 10
169.47.212.160/27 | Dallas 10
169.48.141.208/29 | Dallas 10
169.61.212.192/26 | Dallas 10
169.61.219.224/28 | Dallas 10
169.63.201.128/25 | Dallas 10
52.116.212.0/24 | Dallas 12
52.116.218.160/27 | Dallas 12
52.116.229.128/25 | Dallas 12
52.116.252.0/24 | Dallas 12
169.47.108.192/26 | Dallas 12
169.48.215.160/27 | Dallas 12
169.61.157.128/25 | Dallas 12
169.61.167.16/28 | Dallas 12
169.61.169.48/28 | Dallas 12
169.63.57.64/26 | Dallas 12
52.116.16.0/25 | Dallas 13
52.116.24.32/27 | Dallas 13
52.117.49.0/24 | Dallas 13
52.117.198.128/26 | Dallas 13
52.117.202.128/25 | Dallas 13
52.117.205.0/24 | Dallas 13
52.117.230.0/24 | Dallas 13
52.117.235.0/24	| Dallas 13
169.48.76.32/27 | Dallas 13
169.61.32.152/29 | Dallas 13
169.62.218.208/28 | Dallas 13
169.62.237.0/26 | Dallas 13