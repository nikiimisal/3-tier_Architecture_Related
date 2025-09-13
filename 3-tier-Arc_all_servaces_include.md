   I have practiced all the individual components up to this point. Now, my plan is to consolidate these topics into a complete three‑tier architecture, consisting of the following layers:

1. Database Tier (DB Tier): A relational database, such as RDS, hosting the data used by the application.

2. Application Tier (App Tier): Application servers (e.g., written in PHP or another language) that implement the business logic and connect to the RDS endpoint.

3. Web Tier: Web servers that serve the front-end (HTML, UI). These servers forward dynamic requests to the application tier.

4. Internet‑Facing Load Balancer: A public-facing load balancer that distributes incoming traffic evenly across multiple web-tier servers.

Implementation Steps:

• On the application tier, configure the code (e.g., in PHP) with the RDS endpoint to connect to the database.

•  Deploy this application code into the web-tier servers (e.g., within the HTML or code directory of the web server).

•  Establish an internet‑facing load balancer to receive user traffic and forward it to the web-tier servers.

<h1>3-tier Arc all serveces including </h1>


<h2>Part 1: Network & Infrastructure Setup</h2>

<h3>1. Create a VPC</h3>

In the AWS Console under VPC service, create a new VPC (e.g., CIDR block 10.0.0.0/16)

<h2>Part 2. Set Up Subnets Across Availability Zones (AZs)</h2>

• Create public subnets (2, one in each AZ) for the Internate face loadbalancer.
• Create private subnets : two for the App Tier , two for the Database Tier ,two for the Web Tier

<h2>Part 3. Attach Internet Gateway</h2>

• Set up an Internet Gateway and attach it to your VPC.

<h2>part 4. Database Tier Setup (RDS)<h2></h2>

1. Create a DB Subnet Group

• In RDS console, define a subnet group using your private DB subnets (across AZs) for high availability.

2. Launch an RDS Instance

• Choose a database engine (e.g., MySQL), set credentials, assign the DB SG, and pick the subnet group. Enable multi-AZ for redundancy.


<h2>Part 5. Configure Route Tables & RDS</h2>

• Public Route Table → Links public subnets to the Internet Gateway.(i.e.load balancer subnet1 and subnet2)

• Private Route Table → Links private subnets
   to the NAT Gateway (for outbound internet access without direct exposure) 
   >>(optional) Create a NAT Gateway :<br>
    In one public subnet, set up a NAT Gateway (with an Elastic IP) so App and Database tiers can access the internet for updates while staying private.

  >>or <br>
  (optional) launch app-web-server : using jump server :<br>
  You can also use a jump server. After launching the jump server for logging into RDS, create an AMI from it and launch a new (application) server using that AMI..<br>
  Assign that AMI to the Auto Scaling Group.

• I launched a jump (bastion) server in the public subnet alongside the load balancer, and configured its security group to allow only SSH access (port 22).

• I plan to terminate this bastion host upon completion of the three-tier architecture setup.

• After connecting via SSH, I installed the MariaDB to gain access to the RDS database within the DB-tier to the mysql command-line utility.

      sudo yum install mariadb105-server

• I attempted to connect to the RDS instance using the command:

     sudo mysql -u root -p -h <RDS‑endpoint>

• However the connection failed because the RDS security group does not yet permit inbound traffic on port 3306 from the bastion host.

• Since the environment is not live yet, I will temporarily enable port 3306 in the RDS security group to allow this access.

• Once the necessary work is completed, I will immediately revoke the port 3306 access to maintain security.

• Upon successful connection, I will create a project-specific database on the RDS instance.(DB-tier)
>>The RDS is now ready. Now, at the previous level, integrate the app-tier using PHP (or any programming language)

<h2>Part 6: App Tier Setup</h2>

1. Launch EC2 Instances in the App Tier
   
    For creating AMI purpose :
   
   i. Assign a name (for example, Dev-Test-Instance),

  ii. Add or select a key pair for secure access,

 iii. Choose the operating system (AMI),

 iv. Select the VPC and set the subnet to a public subnet,

 v. Enable auto‑assign public IP (since it's needed for development/test access),

vi. Open port 80 in the security group for HTTP access,

vii. Then launch the instance.

viii. Open a PowerShell console and SSH into the application server's public subnet using its public IP.

ix. On the server, install necessary software packages like Nginx, PHP, PHP‑FPM, and the PHP MySQL connector (mysqli).

   Start, enable, and restart all related services as needed.

     sudo yum update -y
     sudo yum install nginx 
     sudo yum install php
     sudo yum install ph-fpm
     sudo yum install mysqli
     sudo systemctl start nginx
     sudo systemctl enable nginx
     sudo systemctl start php-fpm
     sudo systemctl enable php-fpm
x. Create a basic PHP file for testing—such as phpinfo()—and verify it runs correctly by visiting http://localhost in your browser.  

xi. and know Add your application’s code (e.g., the PHP project) into the appropriate directory and verify it works.

xii.Ensure that an HTML file works as expected in the same setup..<br>
     For this purpose, create project related HTML page—only to verify functionality (such as that the PHP code is running correctly)—and then remove it afterward.

xiii. In your PHP code, include your AWS RDS database connection details and fill in placeholders (RDS endpoint, username, password, database name) so that your app can insert data without errors.

      <? php 
      / Database connection settings
      $servername = "RDS-endepoint-pest-here";
      $username = "root";    //database user name refers to the name you assigned when creating the RDS instance. 
      $password = "root":   //That password is the same one you provided when creating the RDS database 
      $dbname = "nikhil";   //The name you assigned when creating the database 
      ?>

xiv. Once data insertion works correctly, remove the HTML test page

      sudo rm /usr/share/nginx/html/project.html

xv. Finally, create an AMI from this configured application server in your public subnet.

<h2>part 7. Set Up Auto Scaling & Internal Load Balancer</h2>



 • When creating the Auto Scaling Group, keep in mind that we’re implementing a three‑tier architecture, so the App tier should use Auto Scaling within private subnets.

 • Create the related group accordingly.
 
 • To enhance reliability and scalability, set up an internal ALB and auto-scaling group for these App Tier instances.
 
 • So When creating the Auto Scaling group, you will have the option to create a new load balancer. Once selected, choose the option for an internal load balancer and here select private subnet for                  Internal LB..

 • Next, enable health checking—by default, index.html will always pass. You need to specify index.php (or your equivalent) instead.

 • To change(Add) this: go to the Load Balancer → Listeners and Rules → under the Forward to target group section, click the Target Group link → go to Health Check → click Edit → specify index.php (or equivalent) → save.
 >>You are free to make any necessary changes to the data, if required.
 <br>
 >>NOTE : To start Auto Scaling, I have created file name ( CLOUD_WATCH & AUTOscalling.md ) in this directory; the relevant information can be found there.
          


<h2>Part 8 : Web Tier Setup</h2>

1. Launch EC2 Instances

• Create one EC2 instance in public subnet . These will host your web pages or front-end logic (HTML, CSS, JavaScript, or server-side rendering).

2. Deploy Code or Web Server

• SSH into the instances and install necessary software (e.g., Apache or Nginx, PHP runtime).

• ( Optionally ), store code in S3 and pull it in using AWS CLI.

3. You are now in the Web Tier.
   
• When data is submitted through the HTML page, the request should be forwarded to the PHP server in the App Tier.

4. To enable this, configure the NGINX configuration file on the Web Tier to set up a proxy pass:

    Open the NGINX config file:

       sudo nano /etc/nginx/nginx.conf

• In the server block, under location, make the following changes: 

       server {
    listen 80;
    listen [::]:80;
    server_name _;
    root /usr/share/nginx/html;

    location ~ \.php$ {
        proxy_pass http://<DNS-of-internal-loadbalancer-for-AppTier-ASG>;
    }
    }

5. How to find the Load Balancer’s internal DNS:

• Go to the Load Balancer section in AWS.

• Click on the Load Balancer ID.

• Scroll down slightly — you will find the DNS name there.

6. Update the internal Load Balancer’s Security Group:

  •  Add port 80 (for HTTP) and port 3306 (for MySQL database access).

7. Reload the NGINX service on the Web Tier:

       sudo service nginx reload
8. Ensure that the NGINX service is running on the PHP (App Tier) server:

   • If the service is not running, the request will not be served.
   

 >>( optional solution ) :<br>
 >>• This method may not be ideal, but I have shown you one possible way to accomplish it.
   • It might be unconventional, but I wanted to demonstrate that it can also be done this way
  >>  What should we do next , If that happens…?  We need to connect to the newly App‑tier server, which we created using Auto Scaling and load balancing , which resides in the private subnet. For that, we will        need to use a key pair.
    • Because the App Tier is in a private subnet behind the Auto Scaling Load Balancer, you'll need the key pair to log in.
    • Use scp from your local machine( power shell ) to a public EC2 instance in a public subnet to upload the key pair there.
    • If you need these steps in detail, refer to the “Reference VPC” folder where I’ve documented them all follow that.
 >>• In the App Tier’s private subnet security group :
  • Temporarily add port 22 to allow SSH access.
  • After you're done, remove port 22 to restore security.
>>• Once logged into the App Tier EC2 instance:
  • Start and enable all necessary services (e.g., NGINX, PHP, etc.).

9. • Now, create an AMI of the Web‑tier => because we need to launch an application server in the private subnet using this AMI.
   • just like we did for the App tier. For that, you can refer to the setup steps used for the App tier.
   • Now, pay a little more attention here: the server is in a private subnet, while the Load Balancer is in a public subnet.
   • So Here When we creating the Auto Scaling group, you will have the option to create a new load balancer. Once selected, choose the option for an internate facing LB  and here select public subnet for             internate facing LB.

   >>NOTE : Also, ensure that the required services are started and enabled.

11. • Now, for the Web‑tier server launched via the AMI in the private subnet..

    • update newly launched web-tier security group—add inbound rule for port 80—because requests will be coming through NGINX.  if its already added not a problem ..

    • Once that's done, test the website by navigating to your Application’s endpoint in the browser.
    <br> To do this, go to the Internet‑facing Load Balancer, copy its DNS name, and run the following in your browser:

         http://internet-facing‑load‑balancer‑DNS

      •  And know final step

         http://internet-facing‑load‑balancer‑DNS/your‑frontend‑page-name.html
    here will u GO your websute (app) is live....


    >>Personal note :<br>
     •  Remove all temporary or unnecessary resources used for provisioning:
     • Delete the jump server deployed in the public subnet.
     • Deregister and remove the AMIs created for web and app servers.
     • Rationale: These temporary resources can pose potential security risks—hackers could exploit them to gain access to your application environment.
     • Action Required: For security purposes, remove any components that are no longer needed.










