<h1>Load Balancer Setup</h1>
<h1>For practice, we will create an internet-facing Application Load Balancer (ALB).</h1>
<h1>Here some basic info. befor Setupping LB</h1>
AWS offers four types of load balancers :

1. Application Load Balancer (ALB) – Operates at Layer 7, ideal for HTTP/HTTPS applications. Supports advanced routing (path-based, host-based), WebSockets, SSL offloading, and target flexibility like IPs, EC2s, and Lambdas.

2. Network Load Balancer (NLB) – Works at Layer 4, designed for high-performance and low-latency workloads (TCP, UDP, TLS). Provides static IP or Elastic IP support and preserves the client’s source IP.

3. Gateway Load Balancer (GLB) – Functions at Layer 3/4, optimized for scaling and forwarding traffic to virtual appliances such as firewalls, IDS/IPS systems.

4. Classic Load Balancer (CLB) – The oldest type, operating at both Layer 4 and 7, but lacks many modern features. Generally not recommended for new deployments.

### We will use an Application Load Balancer (ALB). Here are the summarized steps :  ###

• Launch three servers—start by retrieving the web server’s API, then launch two additional servers.

• Create the load balancer.

• When creating a load balancer, first create a target group. Then, add all three servers to that target group.

• Then, add all three servers to that target group
 >> A Target Group in AWS Elastic Load Balancing is essentially a named collection of resources—such as EC2 instances, IP addresses, or Lambda functions—that the load balancer routes incoming traffic to. You define health checks per target group so that only healthy targets receive traffic, ensuring reliable and efficient distribution across your application's backend.
• When launching a load balancer, at least two public subnets are required; therefore, you must create those subnets beforehand.


<h1>From here onward, we will proceed to set up the load balancer.</h1>

1. Create an AMI of the Web Server:

Navigate to the EC2 console, go to your running web server instance, select Actions > Image and Templates > Create Image to prepare a custom AMI.

2. Launch Two Instances from the AMI:
   
Use the AMI you just created to launch two new EC2 instances:

Choose “Launch instance from AMI”

Set Number of Instances to 2

Under Network Settings, select your custom VPC

Choose websubnetB as the subnet

Ensure Public IP assignment is enabled

3. Create Subnets (If Not Already Created):

Go to the VPC Console, search for Subnets, and click to manage them

Select your custom VPC

Create subnets in different Availability Zones for high availability

For example:

Subnet Name: websubnet

Availability Zone: Zone B

Provide an appropriate CIDR block

Although making the subnet public is not mandatory, do so for practice purposes—otherwise, SSH access won't be available later (to update /delete/edit html files of this 3 servers).

4. Create a Load Balancer (Application Load Balancer):
   
In the EC2 console, search for Load Balancers, and proceed to create one:

Select Application Load Balancer (ALB)

i. Assign a suitable Load Balancer Name

ii. Choose Internet-facing as the scheme

iii. In the Networking section, select your custom VPC

iv. Under Availability Zones and Subnets, choose two public subnets

v. Use the default Security Group, or configure as needed

vi. Under Listeners and Routing, click Create Target Group

Create the target group and assign it to the Load Balancer

Finally, click Create Load Balancer

5. Test the Load Balancer:

Once the Load Balancer is created, copy its DNS name

Open a web browser and paste the DNS name to access your site

If the page doesn’t load, verify whether port 80 is allowed in the Load Balancer’s Security Group

>>Note: The Load Balancer has its own separate security group

Once configured properly, refreshing the DNS URL will show different HTML pages from the three backend servers (used here just for practice and demonstration purposes).



