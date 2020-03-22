---

copyright:
  years: 2017, 2018, 2019
lastupdated: "2019-11-20"

keywords: vpc, permissions, infrastructure, VPC, SSH, CLI, API, console, classic

subcollection: vpc-on-classic

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:important: .important}
{:download: .download}
{:DomainName: data-hd-keyref="DomainName"}
{:external: target="_blank" .external}

# Getting started with Virtual Private Cloud (Gen 1 compute)
{: #getting-started}
[comment]: # (linked help topic)

New! Check out Virtual Private Cloud for generation 2 virtual server profiles. For more information, see [Getting started with Virtual Private Cloud](/docs/vpc?topic=vpc-getting-started).
{:tip} 

To get started with {{site.data.keyword.vpc_full}} (Gen 1 compute):

1. Create a Virtual Private Cloud.
2. Create one or more subnets in the Virtual Private Cloud in one or more zones.
3. Create a public gateway (PGW) on a subnet if you want resources on your subnet to have access to the internet or vice-versa.
4. Select the profiles of virtual server instances (VSIs) you'd like to run, and instantiate them.
5. Reserve a floating IP address, and associate it with a virtual server instance if you want to reach it from the internet.
5. Deploy your service or applications across the virtual server instances.

## Before you begin
{: #prerequisites}

 * **User Permissions**: Be sure that your user has sufficient permissions to create and manage resources in your VPC. For a list of required permissions, see [Granting permissions needed for VPC users](/docs/vpc-on-classic?topic=vpc-on-classic-managing-user-permissions-for-vpc-resources).

 * **Have your SSH key ready**: You will use an SSH key to connect to your virtual server instance(s).

   * Look for a file called `id_rsa.pub` under an `.ssh` directory under your home directory, for example, `/Users/<USERNAME>/.ssh/id_rsa.pub`. The file starts with `ssh-rsa` and ends with your email address.

   * Or Generate an SSH Key: If you do not have a public SSH key or if you forgot the password of an existing one, generate a new one by running the `ssh-keygen -t rsa -C "user_ID"` command (on Linux or macOS servers) and following the prompts. That command generates two files. The generated public key is in the `<your key>.pub` file. For Windows operating systems, you can use a tool like PuTTYgen to generate an SSH key.

## Using the UI, CLI, or REST API

You can provision and manage all of your VPC resources through the UI, CLI, or REST API.

If you're new to IBM Cloud Virtual Private Cloud, choose any of the links below, which lead you through the process of creating your IBM Cloud VPC and its resources, from start to finish. You can choose whether to get started from the Console UI, the command line (CLI), or REST APIs.

* For access through the user interface, log into the [IBM Cloud Console](https://{DomainName}/vpc){: external}. For more information, see [Creating a VPC using the IBM Cloud console](/docs/vpc-on-classic?topic=vpc-on-classic-creating-a-vpc-using-the-ibm-cloud-console).
* To use the command line interface, follow the [Hello World](/docs/vpc-on-classic?topic=vpc-on-classic-creating-a-vpc-using-the-ibm-cloud-cli) example.
* For more advanced users, you can call the [REST APIs](https://{DomainName}/apidocs/vpc-on-classic){: external} directly. Follow the [example code](/docs/vpc-on-classic?topic=vpc-on-classic-creating-a-vpc-using-the-rest-apis) tutorial to get started with the REST APIs.

## Next steps
If you're ready to dig in, go directly to the detailed documentation about **Networking for VPC**, **VSIs for VPC**, and **Block Storage for VPC**:

* [**Networking for Virtual Private Cloud**](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-getting-started)
* [**Virtual Servers for Virtual Private Cloud**](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-getting-started)
* [**Block Storage for Virtual Private Cloud**](/docs/vpc-on-classic-block-storage?topic=vpc-on-classic-block-storage-getting-started)
