---

copyright:
  years: 2018, 2019
lastupdated: "2019-11-20"

keywords: vpc, vpc ui, create, configure, permissions, ACL, virtual, server, instance, subnet, block, storage, volume, security, group, images, Windows, Linux, example, monitoring, VPN, load balancer, IKE, IPsec

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

# Creating a VPC using the {{site.data.keyword.cloud_notm}} console
{: #creating-a-vpc-using-the-ibm-cloud-console}
[comment]: # (linked help topic)

This document shows you how to use the the {{site.data.keyword.cloud_notm}} console to create and configure an {{site.data.keyword.cloud}} Virtual Private Cloud for generation 1 compute resources.

To create a Virtual Private Cloud for generation 2 compute, see [Using the {{site.data.keyword.cloud_notm}} console to create VPC resources](/docs/vpc?topic=vpc-creating-a-vpc-using-the-ibm-cloud-console).
{: tip}

Within the console, the generation is depicted using the following image.

![vpc-gen1-compute](./images/vpc_gen1.png)


To create and configure your virtual private cloud (VPC) and other attached resources, perform the steps in the sections that follow, in this order:

1. Create a VPC and subnet to define the network. When you create your subnet, attach a public gateway if you want to allow all resources in the subnet to communicate with the public internet.
1. Configure an access control list (ACL) to limit the subnet's inbound and outbound traffic. By default, all traffic is allowed.
1. Create a virtual server instance.
1. If you want more storage, create a block storage volume and attach it to your instance.
1. To define the inbound and outbound traffic that's allowed for the instance, configure its security group.
1. If you want your instance to be reachable from the internet, reserve and associate a floating IP address.
1. To distribute requests over multiple instances, create a load balancer.
1. To enable your VPC to connect securely to another private network, such as your on-premises network or another VPC, create a virtual private network (VPN).

After you enter data on the provisioning pages, you can click the **Get sample API call** button to view the sequence of API requests that correspond to your settings. Viewing the API calls is a good way to learn about the API and understand actions and their dependencies.
{: tip}

## Before you begin
{: #before}
Make sure you have sufficient permissions to create and manage resources in your VPC. For more information, see [Managing user permissions for VPC resources](/docs/vpc-on-classic?topic=vpc-on-classic-managing-user-permissions-for-vpc-resources).

Generate an SSH key, which will be used to connect to the virtual server instance. For example, generate an SSH key on your Linux or macOS server by running the command `ssh-keygen -t rsa -C "user_ID"`. That command generates two files. The generated public key is in the `<your key>.pub` file. For Windows operating systems, you can use a tool like PuTTYgen to generate an SSH key.

If you plan to create a load balancer and use HTTPs for the listener, an SSL certificate is required. You can manage certificates with [IBM Certificate Manager](https://{DomainName}/catalog/services/certificate-manager){: external}. You must also create an authorization to allow your load balancer instance to access the Certificate Manager instance that contains the SSL certificate. You can create an authorization through [Identity and Access Authorizations](https://{DomainName}/iam/authorizations){: external}. For the source, select **Infrastructure Service** as the Source service, **Load Balancer for VPC** as the Resource type, and **All resource instances** for the Source resource instance. Select **Certificate Manager** as the Target service and assign **Writer** for the service access role. Set the Target service instance to  **All instances** or to your specific Certificate Manager instance. For more information, see [Using Load Balancers in IBM Cloud VPC](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-load-balancers-in-ibm-cloud-vpc).

## Creating a VPC and subnet

To create a VPC and subnet:

1. Open the [{{site.data.keyword.cloud_notm}} console](https://{DomainName}){: external}.
1. Click **Menu icon ![Menu icon](../../icons/icon_hamburger.svg) > VPC Infrastructure > Network > VPCs** and click **New virtual private cloud**.
1. Enter a name for the VPC, such as `my-vpc`.
1. Select a resource group for the VPC and all its attached resources. Resource groups enable you to organize your account resources for access control and billing purposes. For more information, see [Best practices for organizing resources in a resource group](/docs/resources?topic=resources-bp_resourcegroups).
1. _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/resources?topic=resources-tag).
1. Select or create the default ACL for new subnets in this VPC. In this tutorial, let's create a new default ACL. We'll configure rules for the ACL later.
1. Select whether the default security group allows inbound SSH and ping traffic to virtual server instances in this VPC. We'll configure more rules for the default security group later.
1. _Optional:_ Select whether you want to enable your VPC to access classic infrastructure resources. For more information, see [Setting up access to classic infrastructure](/docs/vpc-on-classic?topic=vpc-on-classic-setting-up-access-to-your-classic-infrastructure-from-vpc).

    You can only enable a VPC for classic access while creating it. In addition, you can only have one classic access VPC enabled in your account at any time.
    {: important}

1. _Optional:_ Clear the **Default address prefixes** option if you don't want to assign default address prefixes to each zone in your VPC. After you create your VPC, you can go to its details page and set your own address prefixes.
1. Enter a name for the new subnet in your VPC, such as `my-subnet`.
1. Select a resource group for the subnet.
1. Select a location for the subnet. The location consists of a region and a zone.

    The region you select is used as the region of the VPC. All additional resources you create in this VPC will be created in the selected region.
    {: tip}

1. Enter an IP range for the subnet in CIDR notation, for example: `10.240.0.0/24`. In most cases, you can use the default IP range. If you want to specify a custom IP range, you can use the IP range calculator to select a different address prefix or change the number of addresses.

    A subnet cannot be resized after it has been created.
    {: important}

1. Select an ACL for the subnet. Select **Use VPC default** to use the default ACL that's created for this VPC.
1. Attach a public gateway to the subnet to allow all attached resources to communicate with the public internet.  

    You can also attach the public gateway after you create the subnet.
    {: tip}

1. Click **Create virtual private cloud**.
1. To create another subnet in this VPC, click the **Subnets** tab and click **New subnet**. When you define the subnet, make sure to select `my_vpc` in the **Virtual private cloud** field.

## Configuring the ACL

You can configure the ACL to limit inbound and outbound traffic to the subnet. By default, all traffic is allowed.

Each subnet can be attached to only one ACL. However, each ACL can be attached to multiple subnets.

To configure the ACL:

1. In the navigation pane, click **Network > Subnets**.
1. Click the subnet that you created.
1. In the **Subnet details** area, click the name of the ACL.
1. Click **Add rule** to configure inbound and outbound rules that define what traffic is allowed in or out of the subnet. For each rule, specify the following information:  
   * Select whether to allow or deny the specified traffic.
   * Select the protocol to which the rule applies.  
   * For the source and destination of the rule, specify the IP range and ports for which the rule applies. For example, if you want all inbound traffic to be allowed to the IP range 192.168.0.0/24 in your subnet, specify **Any** as the source and 192.168.0.0/24 as the destination. But if you want to allow inbound traffic only from 169.168.0.0/24 to your entire subnet, specify 169.168.0.0/24 as the source and **Any** as the destination for the rule.
   * Specify the rule's priority. Rules with lower numbers are evaluated first and override rules with higher numbers. For example, if a rule with priority 2 allows HTTP traffic and a rule with priority 5 denies all traffic, HTTP traffic is still allowed.  
1. When you finish creating rules, click the **All access control lists** breadcrumb at the beginning of the page.

### Example ACL

For example, you can configure the following inbound rules:

* Allow HTTP traffic from the internet
* Allow all inbound traffic from the subnet 10.10.20.0/24
* Deny all other inbound traffic  

| Priority| Allow/Deny | Protocol | Source | Destination |
|--------------|-----------|------|------|------|
| 1 | Allow | TCP | Any IP, ports 80 - 80 |Any IP, any port|
| 2 | Allow | ALL | 10.10.20.0/24, any port |Any IP, any port|
| 3 | Deny| ALL | Any IP, any port |Any IP, any port|
{: caption="Table 1. Information for configuring inbound rules" caption-side="top"}

Then, configure the following outbound rules:

* Allow HTTP traffic to the internet
* Allow all outbound traffic to the subnet 10.10.20.0/24
* Deny all other outbound traffic  

| Priority| Allow/Deny | Protocol | Source | Destination |
|--------------|-----------|------|------|------|
| 1 | Allow | TCP | Any IP, any port |Any IP, ports 80 - 80  |
| 2 | Allow | ALL | Any IP, any port | 10.10.20.0/24, any port |
| 3 | Deny| ALL | Any IP, any port |Any IP, any port|
{: caption="Table 2. Information for configuring outbound rules" caption-side="top"}

## Creating a virtual server instance

To create a virtual server instance in the newly created subnet:

1. Click **Compute > Virtual server instances** in the navigation pane and click **New instance**.
1. Enter a name for the instance, such as `my-instance`.
1. Select the VPC that you created.
1. Select a resource group for the instance. Resource groups enable you to organize your account resources for access control and billing purposes. For more information, see [Best practices for organizing resources in a resource group](/docs/resources?topic=resources-bp_resourcegroups).
1. _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/resources?topic=resources-tag).
1. In the **Location** field, select the zone in which to create the instance.
1. Select an image (that is, operating system and version), such as Ubuntu Linux 16.04.
1. To set the instance size, select one of the popular profiles or click **All profiles** to choose a different core and RAM combination that's most appropriate for your workload.

    After you create your instance, you can't update the profile.
    {: important}

1. Select an existing SSH key or add a new SSH key that will be used to access the virtual server instance. To add an SSH key, click **New key** and name the key. After you enter your previously generated public key value, click **Add SSH key**.

    Keys only can be added initially as part of creating the VSI. No tooling exists to add keys later.
    {: tip}

1. _Optional:_ Enter user data to run common configuration tasks when your instance starts. For example, you can specify cloud-init directives or shell scripts for Linux images. For more information, see [User Data](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-user-data).
1. Note the boot volume. In the current release, 100 GB is allotted for the boot volume. *Auto Delete* is enabled for the volume; it will be deleted automatically if the instance is deleted.
1. In the **Data volumes** area, you can click **New volume** to attach a block storage volume to your instance. In this tutorial, we'll create a block storage volume and attach it to the instance later.
1. In the **Network interfaces** area, you can edit the network interface and change its name. If you have more than one subnet in the selected zone and VPC, you can attach a different subnet to the interface. If you want the instance to exist in multiple subnets, you can create more interfaces.

   You can also select which security groups to attach to each interface. By default, the VPC's default security group is attached. The default security group allows inbound SSH and ping traffic, all outbound traffic, and all traffic between instances in the group. All other traffic is blocked; you can configure rules to allow more traffic. If you later edit the rules of the default security group, those updated rules will apply to all current and future instances in the group.

1. Click **Create virtual server instance**. The status of the instance starts as *Pending*, changes to *Stopped*, and then *Powered On*. You might need to refresh the page to see the change in status.

## Creating and attaching a block storage volume

You can create a block storage volume and attach it to your virtual server instance.

To create and attach a block storage volume:

1. In the navigation pane, click **Storage > Block storage volumes**.
1. On the Block storage volumes for VPC page, click **New volume** and specify the following information.
  * **Name**: Enter a name for the block storage volume, such as `data-volume-1`.  
  * **Resource group**: Select a resource group for the block storage volume. Resource groups enable you to organize your account resources for access control and billing purposes. For more information, see [Best practices for organizing resources in a resource group](/docs/resources?topic=resources-bp_resourcegroups).
  * **Tags**: _Optional:_ Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/resources?topic=resources-tag).
  * **Location**: Select a location for the block storage volume. The location consists of a region and a zone, for example US South 1.
  * **Size**: Specify the size of the volume between 10 GBs and 2000 GBs.
  * **IOPs**: Select one of the IOPs Tiers or click Custom to enter an IOPs value based on volume size.
  * **Encryption**: Accept the default Provider managed encryption or select Customer managed and use your own encryption key. This step requires provisioning a Key Protect service instance and creating or importing a root key. For more information, see [Creating block storage volumes with customer managed encryption](/docs/vpc-on-classic-block-storage?topic=vpc-on-classic-block-storage-block-storage-encryption#data-vol-encryption-ui).
1. Click **Create volume**.
1. In the list of block storage volumes, find the volume that you created. When the status is Available, click "..." and select **Attach to instance**.
1. Select the instance to which you want to attach the volume and click **Attach**.

## Configuring the security group for the instance

You can configure the security group to define the inbound and outbound traffic that is allowed for the instance. For example, after you configure ACL rules for the subnet based on your company's security policies, you can further restrict traffic for specific instances depending on their workloads.

To configure the security group:

1. On the Virtual server instances page, click your instance to view its details.
1. In the **Network interfaces** section, click the security group.
1. Click **Add rule** to configure inbound and outbound rules that define what type of traffic is allowed to and from the instance. For each rule, specify the following information:  
   * Specify a CIDR block or IP address for the permitted traffic. Alternatively, you can specify a security group in the same VPC to allow traffic to or from all instances attached to the selected security group.   
   * Select the protocols and ports to which the rule applies.    

   **Tips:**  
  * All rules are evaluated, regardless of the order in which they're added.
  * Rules are stateful, which means that return traffic in response to allowed traffic is automatically permitted. For example, you created a rule that allows inbound TCP traffic on port 80. That rule also allows replying outbound TCP traffic on port 80 back to the originating host, without the need for another rule.
1. _Optional:_ If you want to attach this security group to other instances, click **Attached interfaces** in the navigation pane and select additional interfaces.
1. When you finish creating rules, click the **All security groups** breadcrumb at the top of the page.

### Example security group  

For example, you can configure inbound rules that do the following:

 * Allow all SSH traffic (TCP port 22)
 * Allow all ping traffic (ICMP type 8)

| Protocol | Source Type | Source | Value |
|-----------|------|------|------|------------------|-------|
| TCP| Any | 0.0.0.0/0 | Ports 22-22
| ICMP | Any | 0.0.0.0/0 | Type: 8, Code: Any|
{: caption="Table 3. Configuration information for inbound rules" caption-side="top"}
Then, configure outbound rules that allow all TCP traffic.

| Protocol | Destination Type | Source | Value |
|-----------|------|------|------|------------------|-------|
| TCP| Any | 0.0.0.0/0 | Any port|
{: caption="Table 4. Configuration information for outbound rules" caption-side="top"}

## Reserving a floating IP address

Reserve and associate a floating IP address if you want your instance to be reachable from the internet.  

Your instance must be running before you can associate a floating IP address. It can take a few minutes for the instance to be up and running.
{: tip}

To reserve and associate a floating IP address:

1. On the Virtual server instances page, click your instance to view its details.
1. In the **Network interfaces** section, click **Reserve** for the interface that you want to associate with a floating IP address.

If you later want to reassign this floating IP address to another instance in the same zone, find the floating IP address on the **Network > Floating IPs** page, click its overflow menu (**...**), and click **Unassociate**. Then, click  **Associate** to select the instance and network interface that you want to associate with the floating IP address.
{: tip}

## Connecting to your instance
Using the floating IP address that you created, ping your instance to make sure it's up and running:

```
ping <public-ip-address>
```
{:pre}


### Connecting to Linux images

Since you created your instance with a public SSH key, you can now connect to it directly by using your private key:


```
ssh -i <path-to-private-key-file> root@<public-ip-address>
```
{:pre}

See [Connecting to your instance using Linux](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-connecting-to-your-linux-instance) for more information on how to connect to your instance.

### Connecting to Windows images
To connect to a Windows image, log in using its decrypted password. To get the decrypted password, copy the encrypted password from the instance's details page and run the following command:

```
# Decode the encrypted password
cat ~/examplepwd | base64 --decode > ~/examplepwd64
# Decrypt the decoded password using the RSA private key
openssl pkeyutl -in ~/examplepwd64 -decrypt -inkey private.pem -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256
-pkeyopt rsa_mgf1_md:sha256
```
{:codeblock}


See [Connecting to your Windows instance](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-connecting-to-your-windows-instance) for more information on how to connect to your instance.


## Monitoring your instance

You can monitor the CPU, volume, memory, and network usage of your instance over time.

To monitor your instance:

1. Click **Virtual server instance** in the navigation pane.
1. Click the name of your instance.
1. Click **Monitoring** in the navigation pane.

## Creating a load balancer
You can create a load balancer to distribute inbound traffic across multiple instances.

To create a load balancer:
1. In the navigation pane, click **Network > Load balancers**.
1. On the Load balancers page, click **New load balancer** and specify the following information.
    * **Name**: Enter a name for the load balancer, such as `my-load-balancer`.
    * **Virtual private cloud**: Select your VPC.
    * **Resource group**: Select a resource group for the load balancer.
    * **Tags**: (Optional) Enter tags to help you organize and find your resources. You can add more tags later. For more information, see [Working with tags](/docs/resources?topic=resources-tag).
    * **Type**: Select the load balancer type.
      * A public load balancer has a public IP address, which means that it can route requests from clients over the internet.
      * A private load balancer has a private IP address, which means that it is accessible only to internal clients on your private subnets, within the same region and VPC.
    * **Region**: Indicates the region in which the  load balancer will be created; that is, the region selected for your VPC.
    * **Subnets**: Select the subnets in which to create your load balancer. To maximize the availability of your application, select subnets in different zones.
1. Click **New pool** and specify the following information to create a back-end pool. You can create one or more pools.
    * **Name**: Enter a name for the pool, such as `my-pool`.
    * **Protocol**: Select the protocol for your instances in this pool. The protocol of the pool must match the protocol of its associated listener. For example, if an HTTPS or HTTP protocol is selected for the listener, the protocol of the pool must be HTTP. Similarly, if the listener protocol is TCP, the protocol of the pool must be TCP.
    * **Method**:  Select how you want the load balancer to distribute traffic across the instances in the pool:
      * **Round robin:** Forward requests to each instance in turn. All instances receive approximately an equal number of client connections.
      * **Weighted Round robin:** Forward requests to each instance in proportion to its assigned weight. For example, if you have instances A, B, and C, and their weights are set to 60, 60 and 30, then instances A and B receive an equal number of connections, and instance C receives half as many connections.
      * **Least connections:** Forward requests to the instance with the least number of connections at the current time.  
    * **Session stickiness**: Select whether all requests during a user's session are sent to the same instance.  
    * **Health checks**: Configure how the load balancer checks the health of the instances.
      * **Health check path**: Health path is applicable only if HTTP is selected as the health check protocol. The health path specifies the URL used by the load balancer to send the HTTP health check requests to the instances in the pool. By default, health checks are sent to the root path (`/`).
      * **Health protocol**: The protocol used by the load balancer to send health check messages to the instances in the pool.
      * **Health port**: The port on which to send health check requests. By default, health checks are sent on the same port on which traffic is sent to the instance.
      * **Interval**: Interval in seconds between two consecutive health check attempts. By default, health checks are sent every 5 seconds.
      * **Timeout**: Maximum amount of time the system waits for a response from a health check request. By default, the load balancer waits 2 seconds for a response.
      * **Max retries**: Maximum number of health check attempts that the load balancer makes before an instance is declared unhealthy. By default, an instance is no longer considered healthy after two failed health checks.

        Although the load balancer stops sending connections to unhealthy instances, the load balancer continues monitoring the health of these instances and resumes their use if they're found healthy again by successfully passing two consecutive health check attempts.

      If back-end instances are unhealthy and you believe your application is running fine, double check the health protocol and health path values. Also check any security groups attached to the instances to ensure the rules allow traffic between the load balancer and the instances.
      {: tip}

1. Click **Create**.
1. Next to the entry for the new pool, click **Attach** in the **Instances** column to add an instance to the pool. Click **Add** to add more instances to the pool. Specify the following information for each instance:
   * Select one or more subnets from which to select an instance.
   * Select an instance. If an instance has multiple interfaces, make sure you select the correct IP address.
   * Specify the port on which traffic is sent to the instance.
   * If your pool uses the **Weighted round robin** method, assign a weight for each instance.  

      Assigning '0' weight to an instance means no new connections will be forwarded to that instance, but any existing traffic continues to flow as long as the current connection is active. Using a weight of '0' can help bring down an instance gracefully and remove it from service rotation.
      {: tip}

1. Click **New listener** and specify the following information to create a listener. You can create one or more listeners.
   * **Protocol**: The protocol to use for receiving incoming requests.
   * **Port**: The listening port on which requests are received. The port range of 56500 to 56520 is reserved for management purposes and can't be used.
   * **Back-end pool**: The back-end pool to which this listener forwards traffic.
   * **Max connections** (optional): Maximum number of concurrent connections the listener allows.
   * **SSL certificate**: If HTTPS is the selected protocol for this listener, you must select an SSL certificate. Make sure the load balancer is authorized to access the SSL certificate. For instructions, see [Before you begin](#before).
1. Click **Create**.
1. After you finish creating pools and listeners, click **Create load balancer**.
1. To view details of an existing load balancer, click the name of your load balancer on the **Load balancers** page.
1. If you want to redirect, forward, or reject particular incoming traffic for an HTTP or HTTPS front-end listener based on certain criteria, configure layer-7 policies.
    1. After the status of the load balancer changes to **Active**, click **Front-end listeners** in the navigation and click the value in the **Policies** column for the listener you created.
    2. On the Policies page, click **Add policy** and specify the following information to create a policy. You can create multiple policies.
        * **Name**: Enter a name for the policy, such as `my-policy`. The name must be unique within the listener.
        * **Action**: The action to take when all the rules for the policy match. You can reject a request with a 403 response, redirect the request to a configured URL and response code, or forward the request to a specific back-end pool. If an incoming request does not match the rules for any policies, the request is forwarded to the default back-end pool of the listener.
        * **Priority**: Within each action type, policies are evaluated in ascending order of priority. Policies to reject traffic are always evaluated first, regardless of their priority. Policies to redirect traffic are evaluated next, followed by policies to forward traffic.
        * **Redirect**: The URL to which the request is redirected, if the action is set to **Redirect**.
        * **Status Code**: The status code of the response returned by the load balancer, if the action is set to **Redirect**.
        * **Forward**: The back-end pool of virtual server instances to which the request is forwarded, if the action is set to **Forward to pool**.
    3. On the Policies page, click **Add rule** for your policy. If rules exist for the policy, click the value in the **Rules** column to add more rules.
    4. In the Rules window, click **Add rule** and specify the following information to create a rule. If you create multiple rules for a policy, the policy is applied only when all its rules are matched.
        * **Condition**: Specifies the condition with which a rule is evaluated.
        * **Type**: The type of information to be evaluated by the rule: the name of the host from which the request originated, an HTTP header field, or a path in the URL.
        * **Value**: The value to be matched.
        * **Key**: The name of the HTTP header field to evaluate, if the rule type is **Header**. For example, to match a cookie in the HTTP header, enter **Cookie** for the key.

## Creating a VPN
You can create a virtual private network (VPN) so your VPC can connect securely to another private network, such as an on-premises network or another VPC.

To view a code example, see [Using VPN with your VPC](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-vpn-with-your-vpc).
{: tip}

To create a VPN:
1. In the navigation pane, click **Network > VPNs**.
1. On the VPN page, click **New VPN gateway** and specify the following information:
    * **Name**: Enter a name for the VPN gateway in your virtual private cloud, such as `my-vpn-gateway`.
    * **Virtual private cloud**: Select your VPC.
    * **Resource group**: Select a resource group for the VPN.
    * **Subnet**: Select the subnet in which to create the VPN gateway.
      For best performance, create the VPN gateway in a subnet without any other VPC resources to ensure that there are enough available private IPs for the gateway. A VPN gateway needs 8 private IP addresses to accommodate high availability and rolling upgrades.

      The VPN gateway is created in the zone that is associated with the subnet you select. Because the VPN gateway can connect to virtual server instances in this zone only, instances in other zones can't use this VPN gateway to communicate with the other network. For zone fault tolerance, deploy one VPN gateway per zone.
      {: tip}
1. In the **New VPN connection** section, define a connection between this gateway and a network outside your VPC by specifying the following information.
    * **Connection name**: Enter a name for the connection, such as `my-connection`.
    * **Peer gateway address**: Specify the IP address of the VPN gateway for the network outside your VPC.
    * **Preshared key**: Specify the authentication key of the VPN gateway for the network outside your VPC.
    * **Local subnets**: Specify one or more subnets in the VPC you want to connect through the VPN tunnel.
    * **Peer subnets**: Specify one or more subnets in the other network you want to connect through the VPN tunnel.
1. To configure how the cloud gateway sends messages to check that the peer gateway is active, specify the following information in the **Dead peer detection** section.
    * **Dead peer detection action**: The action to take if a peer gateway stops responding. For example, select **Restart** if you want the gateway to immediately renegotiate the connection.
    * **Interval**: How often to check that the peer gateway is active. By default, messages are sent every 30 seconds.
    * **Timeout**: How long to wait for a response from the peer gateway. By default, a peer gateway is no longer considered active if a response isn't received within 150 seconds.
1. Specify the Internet Key Exchange (IKE) and Internet Protocol Security (IPsec) parameters to use for phase 1 and phase 2 negotiation of the connection.
    * Select **Auto** if you want the cloud gateway to try to automatically establish the connection.
    * Select or create custom policies if you need to enforce particular security requirements, or the VPN gateway for the other network doesn't support the security proposals that are tried by auto-negotiation.

  **Important**: The IKE and IPsec security parameters that you specify for the connection must be the same parameters that are set on the gateway for the network outside your VPC.

## Viewing resources associated with a VPC
{: #vpc-layout}

You can quickly view the resources that are associated with a VPC by accessing the resource view: In the navigation, click **VPC layout**. You can select the VPC that you are interested in, if your account has multiple VPCs configured. For each VPC you can see the associated subnets, and within each subnet you can see how many instances are running, stopped, and failed. You can also see how many IP addresses are available in each subnet. From the lists of instances and associated IP addresses, you can click a specific instance to view its details.  

## Congratulations!

You've successfully created and configured your VPC by using the IBM Cloud console. You can continue to develop your VPC by adding more instances, subnets, and other resources.
