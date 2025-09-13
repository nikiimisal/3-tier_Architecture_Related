<h1>RDS Setup for 3-Tier Architecture</h1>

We are now proceeding to connect a database to our 3-tier architecture project. Follow the steps below to create and configure the RDS database within your custom VPC:

<h3>Step-by-Step Instructions:</h3>

<h2>1. Navigate to the RDS Console</h2>

• Go to the AWS EC2 console and search for “RDS” in the services search bar.

• Open the RDS console and click "Create database".

<h2>2. Choose a Database Creation Method</h2>

• Select "Standard create".

• This option allows full customization such as using a custom VPC, enabling auto-scaling, and configuring network settings.

• Avoid "Easy create" as it automatically launches the database in the default VPC with default settings.

<h2>3. Select the Database Engine</h2>

• Choose MySQL (or any engine of your choice).

<h2>4. Select Engine Version</h2>

• Choose the version of MySQL you are comfortable with or the latest stable one.

<h2>5. Choose a Template</h2>

• Select the "Free Tier" template.

• This is suitable for beginners and is ideal for practice purposes.

<h2>6. Set Master Credentials</h2>

• The default master username is admin (you may change it if needed).

• Under "Credentials settings", select "Self managed".

• Enter and confirm your own password when the password creation popup appears.
>>As a beginner, first carefully understand each of the following steps before attempting them in practice<br>
  Gradually, through experience and practice, you will gain a clear understanding of these steps—then apply them confidently.

<h2>7. Configure Connectivity</h2>

• Under Connectivity settings, choose your custom VPC instead of the default VPC.

<h2>8. Create a New DB Subnet Group</h2>

• If AWS tries to use a subnet from the web tier, it can pose security risks.

• Therefore, it's best to create a dedicated subnet group for the RDS database.
>>If you create a DB subnet group using default settings or by selecting any available subnets, RDS may choose subnets from unsuitable tiers—such as those used by web servers—which poses security risks. Therefore, you should explicitly create a dedicated subnet group containing only private subnets designated for your database

<h2>9. Create Subnet Group for RDS</h2>

• In the RDS console, navigate to the Subnet groups section from the side menu.

• Click "Create DB subnet group":

• Name the group (e.g., mydbsubnet).

• Select your custom VPC.

• Add two subnets, each from a different Availability Zone.

• If your VPC currently only has one subnet, go to the VPC console and create another (e.g., dbsub1 and dbsub2 in separate zones).

<h2>10. Return to RDS Console</h2>

• Refresh the page so your newly created subnet group appears in the dropdown.

• Select your custom DB subnet group.
>>"Return to the RDS console and refresh the page. Then, create a DB Subnet Group as follows:<br>
i. Name the subnet group, for example: mydbsubnet.<br>
ii. Select your custom VPC from the dropdown.<br>
iii. Add subnets: choose the Availability Zones where your private subnets were created.<br>
iv. Select the corresponding subnets in each zone and click Create to finalize the DB Subnet Group<br>

<h2>11. Set Public Access to 'No'</h2>

• Since this is a database, it should be kept private (not accessible from the public internet).

<h2>12. Security Groups</h2>

• For now, use the default security group (you can create a custom one later as needed).

<h2>13. Availability Zone Settings</h2>

• You will be prompted to select a primary Availability Zone—choose whichever one you prefer.

• The second zone will be used for standby (for failover support in Multi-AZ deployments).

<h2>14. Create the Database</h2>

• Click “Create database” at the bottom.

• Other settings can be explored and fine-tuned with more experience, but for now, these are sufficient to get started and understand the basic concept.

<h1>Summary Tips for Beginners</h1>

• Stick to the Free Tier while practicing.

• Always use a custom VPC for 3-tier architecture.

• Ensure that the RDS instance is not public and is accessible only to your application layer (private subnet).

• Use separate subnets in different AZs for database resilience.

<h1>Connecting RDS to Your Three-Tier Architecture</h1>

We have now completed the RDS setup. Next, we will connect the RDS database to our three-tier VPC architecture


<h3>1. Access the Web Server via Shell</h3>
Log in to your web server using an SSH client (e.g., PowerShell on Windows). For instance:

       ssh -i /path/to/key.pem ec2-user@<web-server-public-ip>
   >>You can also use this step :<br>
   SSH into the web server and then use it to jump into the application server via SSH. Once logged into the application server, you can access and interact with the RDS database.

<h3>2. Prepare to Connect to Amazon RDS</h3>
To access the RDS instance, you will need its endpoint. Open the RDS console, navigate to the database details under the Connectivity & security tab, and copy the endpoint (DNS address).

3. Install MariaDB Client on the App Server
The MySQL/MariaDB client is required to connect to the RDS instance. On Amazon Linux 2, install it using:

       sudo yum install mariadb105-server
4. Connect to the RDS Instance
   
Once the client is installed, connect with: 

      sudo mysql -u root -p -h rds-endpoint-hear
>>To execute this command, the MariaDB client must first be installed on the application server. Once the command has executed successfully, you may uninstall MariaDB for security purposes.
  You will be prompted to enter the master password you configured during RDS setup

5. Create and Use a Database Related To Your Project

After logging in, you can execute SQL commands to set up your project database. For example:

       CREATE DATABASE myprojectdb;
       USE myprojectdb;
       CREATE TABLE example_table (
       id INT PRIMARY KEY AUTO_INCREMENT,
       name VARCHAR(100),
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
       );
       INSERT INTO example_table (name) VALUES ('Sample');

