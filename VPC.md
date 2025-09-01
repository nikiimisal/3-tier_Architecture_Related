<h1>VPC Setup</h1>
<h1>Create Vpc step by step</h1> 

1. Plan Your Network

Define the CIDR block for your VPC (e.g., 10.0.0.0/16) to ensure enough IPs for scalability 

Decide whether you’ll include both public and private subnets for a multi-tier architecture 

2. Create a Custom VPC

Open VPC Consol :
In the AWS Console, open the VPC Dashboard and click Create VPC.

Choose "VPC only" to create VPC, or "VPC and more" to include subnets, gateways, etc., in one go 
AWS Documentation

Provide:

• A Name tag (optional but helpful for organization)
• The IPv4 CIDR block
• (Optional) IPv6 block, Tenancy (default or dedicated) 
>If you turn on the tenancy on the default basis and then switch it to a dedicated mode, the billing will begin.
• Click Create VPC to finish 


3. Create & Add Subnets

Go to Subnets in the VPC dashboard and click Create subnet.

Create at least:

• 1 public subnet, e.g., 10.0.1.0/24

• 1 private subnet, e.g., 10.0.2.0/24

Assign each subnet to different Availability Zones for high availability 

4. Set Up an Internet Gateway (IGW)

• Navigate to Internet Gateways, click Create, give it a name, then 
• Attach to VPC =Click on the internet gateway that you created. Then click on Actions, 
  and from there choose Attach to VPC. Finally, select the internet gateway that you created when prompted to connect it to the VPC.
• To make your perticular subnet truly "public," you’ll need to update its route table next 

5. Configure Route Table for Public Subnet

• Access Route Tables, create or select one for public traffic and another for private.
• Add a public route: 0.0.0.0/0 → the Internet Gateway (IGW).
• Associate this route table with your public subnet 

6. Set Up NAT Gateway for Private Subnet

Allocate an Elastic IP under Elastic IPs.

Go to NAT Gateways, click Create, choose your public subnet and attach the Elastic IP 

In the private route table, add route 0.0.0.0/0 → your NAT Gateway and associate it with your private subnet 


7. Enable DNS Support (via CLI, if needed)

If using the AWS CLI, ensure DNS resolution and hostnames are enabled:

    aws ec2 modify-vpc-attribute --vpc-id <vpc-id> --enable-dns-support
    aws ec2 modify-vpc-attribute --vpc-id <vpc-id> --enable-dns-hostnames


8. Define Security: Security Groups & Network ACLs

Security Groups (instance-level):

Create SSH or HTTP rules (e.g., allow only port 22 for SSH, port 80 for HTTP).

Network ACLs (subnet-level):

Add additional fine-grained inbound/outbound rules (note: SGs are allow‑only; NACLs support allow and deny) 


9. (Optional) Add VPC Endpoints

Use VPC endpoints to enable private connectivity to AWS services like S3 without going through the internet 

10. Verify Configuration

Validate each component:

VPC: describe-vpcs

Subnets: describe-subnets

Route Tables, Internet Gateway, NAT Gateway, Security Groups: use respective describe-... CLI commands 

<h1> Launch EC2 Instances</h1>

Deploy resources to test:

1. Web server in the public subnet (allows public access).


Steps to Launch an Instance (with Network Customization)

These steps follow the usual process of launching an instance, with an additional configuration for network settings:

i. Provide a name for the new instance.

ii. Select the operating system you wish to deploy.

iii. Choose the instance type that matches your performance and cost requirements.

iv. Select or create a key pair for secure access to the instance.

Additional Network Configuration

After completing the standard setup, you'll have some extra network-related settings:

v. Click on the Edit option under Network Settings.
   a. Select the custom VPC you have created.
   b. Choose a subnet—since you are launching this server for public access, name it something like “web subnet”.
   c. Enable Public IP assignment—since this is a public server, it needs a public IP. If it were a private server, you would disable this feature.

For beginners, this setup is sufficient for now. Additional advanced configurations can be learned over time through experience and practice.

Launch the server.

2. Database or app server in the private subnet (no direct internet access).

3. Apply appropriate security groups during launch

<h1>Three‑Tier Architecture Setup (Web → App → DB) via PowerShell, with NAT Gateway Integration</h1>

Three‑Tier Access Workflow with Temporary Internet Access :

1. Open PowerShell on Local Machine
Begin by launching your local PowerShell console to coordinate subsequent actions.

2. Transfer Key Pair to Web Server
Use scp to copy the .pem & SSH key file securely to the web server:

       scp -i .\web-server-your-key.pem .\app-server-your-key.pem ec2-user@web-server-ip:.


3. SSH into the Web Server
Authenticate and connect using SSH from your local terminal:

       ssh -i .\your-key.pem ec2-user@web-server-ip


4. Set One-Time Secure File Permissions
On the web server, restrict access to the .pem file using:

        ls -l
        chmod 400 your-key.pem
        ls -l
This ensures only your user can read the private key—resolving "UNPROTECTED PRIVATE KEY FILE" errors seen with more lenient permissions like 0644 .


5. SSH into the App Server
From the web server, use the same app‑server key that you previously uploaded to the web server in order to access the application server (typically via its private IP address) :

         ssh -i .\your-key.pem ec2-user@app-server-private-ip


6. Install NGINX or Required Software on the App Server
Since the app server lacks direct internet access, it needs outbound connectivity to download softwares like NGINX.

7. Provision a Temporary NAT Gateway with Elastic IP

To install the softwares in private server subnet, you need to enable a NAT Gateway in private root table so that the server can access the internet for installing software during project development..
i.e. Launch a NAT Gateway in the same subnet as the web server to enable internet access for the app server.
Associate an Elastic IP (EIP) with the NAT Gateway to route outbound traffic securely.
This setup is temporary in development process, used only to install required packages.
>If you leave the NAT Gateway enabled, charges will continue to accrue, as billing begins as soon as the gateway is provisioned and active.
Formal English Version:
To activate a NAT Gateway, follow these steps:
a. Open the VPC section in the AWS EC2 console. In the left-hand navigation pane, click on “NAT Gateways”, then click “Create NAT Gateway.”
b. Assign a meaningful name to the NAT Gateway for easier identification.
c. Select the public subnet (e.g., a web subnet) in which to deploy the NAT Gateway.
d. Set the Connectivity Type to Public, as internet access is required for outbound traffic.
   a private connectivity type would only be applicable if an internal proxy or specific IP assignment within the VPC is intended.
e. Click on "Allocate Elastic IP" — an Elastic IP address will be automatically created and assigned to the NAT Gateway.
f. Click "Create NAT Gateway" to provision it. Once created, the NAT Gateway exists but does not yet enable internet access for private subnets—further configuration is required.
g. In the route table associated with your private subnet, add a default route (0.0.0.0/0) and set the NAT Gateway as the target. Then save the routing table changes.
h. Once configured, your private subnet will have internet access necessary for software installation; note that it may take a short period for the NAT Gateway to become fully active after creation.



8. Cleanup (Cost-Saving Best Practice)

Once the NAT Gateway has been deleted, the associated Elastic IP must also be deleted manually. To do this, navigate to the Elastic IPs section in the AWS console and release the Elastic IP.
Once software installation is complete, delete the NAT Gateway immediately to stop accruing hourly charges.

Disassociate and release the Elastic IP, as unused EIPs also incur charges 

9. I am working on a project using PHP and HTML. To set up the environment:

  A. Install the necessary software

  Install PHP and PHP-FPM. and msql connector (for conne t the database) :

       sudo yum install php -y
       sudo yum install php-fpm -y
       sudo service php-fpm start
       sudo systemctl enable php-fpm
       sudo yum update
       sudo yum install php-mysqli
   Install Nginx, since the web server will serve HTML pages from a directory and forward PHP requests to the application server’s PHP processor.

       sudo yum install nginx -y
       sudo service nginx start
       sudo systemctl enable nginx
  B. Set up the file path php code page

   Navigate to the Nginx document root:

       cd /usr/share/nginx/html
       cd ../../../..
       sudo service nginx restart
       sudo service php-fpm restart

Within this html directory, create a PHP file, for example index.php, which will be linked from your webserver HTML pages served by Nginx and processed by PHP-FPM.

C. Test if PHP is functioning correctly

From the server terminal, run the following command:

     curl http://localhost/index.php
If PHP is configured properly with Nginx and PHP-FPM, this command should return the output of your index.php page.

10. Configuration for Routed PHP Handling

I am working on a project where the HTML pages are served by the web server (Nginx). When the request concerns PHP, Nginx must forward the request to the PHP processor running on the application server. To enable this, the following steps are required:

A. Modify Nginx configuration
Open the main configuration file using:

    sudo nano /etc/nginx/nginx.conf


Within the appropriate server block, add a location ~ \.php$ directive to proxy PHP requests:

    location ~ \.php$ {
    proxy_pass http://app-server-private-ip;
    }


B. Reload Nginx
Save the configuration file and apply the changes by reloading Nginx:

    sudo service nginx reload


C. Test the PHP page
Then, in your browser (or via curl), access the PHP page:

    http://public-ip-or-localhost/index.php


Nginx will forward the request to the application server. After a brief propagation delay, the PHP page should execute and return the expected output.

11. Supporting References

Configuring Nginx as a Reverse Proxy
To forward PHP requests to another server, you should configure Nginx with a proxy_pass directive within a location block (typically targeting .php$) to your backend server’s IP. Example setup:

      server {
        listen 80;
        server_name example.com;
        location ~ \.php$ {
        proxy_pass http://backend_server_ip;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
      }
     }


12. Syntax Testing and Reloading
Always validate your Nginx configuration before reloading:

        sudo nginx -t
        sudo systemctl reload nginx





In this deployment process, you begin by opening a local PowerShell console and using scp to transfer your PEM key to the web (bastion) server. After SSH'ing in, you apply a one-time permission adjustment (chmod 400) to secure the key, then SSH onward to the app server. Since the app server resides in a private subnet without internet access, you provision a temporary NAT Gateway bound to an Elastic IP to allow installation of NGINX or related software. Upon completion of setup, the NAT Gateway and Elastic IP are promptly removed to avoid ongoing charges. This approach balances secure access, efficient resource use, and cost-effective infrastructure management.

