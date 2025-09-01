<h1>RDS Setup for 3-Tier Architecture</h1>

We are now proceeding to connect a database to our 3-tier architecture project. Follow the steps below to create and configure the RDS database within your custom VPC:

Step-by-Step Instructions:

1. Navigate to the RDS Console

• Go to the AWS EC2 console and search for “RDS” in the services search bar.

• Open the RDS console and click "Create database".

2. Choose a Database Creation Method

• Select "Standard create".

• This option allows full customization such as using a custom VPC, enabling auto-scaling, and configuring network settings.

• Avoid "Easy create" as it automatically launches the database in the default VPC with default settings.

3. Select the Database Engine

• Choose MySQL (or any engine of your choice).

4. Select Engine Version

• Choose the version of MySQL you are comfortable with or the latest stable one.

5. Choose a Template

• Select the "Free Tier" template.

• This is suitable for beginners and is ideal for practice purposes.

6. Set Master Credentials

• The default master username is admin (you may change it if needed).

• Under "Credentials settings", select "Self managed".

• Enter and confirm your own password when the password creation popup appears.
>>As a beginner, first carefully understand each of the following steps before attempting them in practice<br>
  Gradually, through experience and practice, you will gain a clear understanding of these steps—then apply them confidently.

7. Configure Connectivity

• Under Connectivity settings, choose your custom VPC instead of the default VPC.

8. Create a New DB Subnet Group

• If AWS tries to use a subnet from the web tier, it can pose security risks.

• Therefore, it's best to create a dedicated subnet group for the RDS database.

9. Create Subnet Group for RDS

• In the RDS console, navigate to the Subnet groups section from the side menu.

• Click "Create DB subnet group":

• Name the group (e.g., mydbsubnet).

• Select your custom VPC.

• Add two subnets, each from a different Availability Zone.

• If your VPC currently only has one subnet, go to the VPC console and create another (e.g., dbsub1 and dbsub2 in separate zones).

10. Return to RDS Console

• Refresh the page so your newly created subnet group appears in the dropdown.

• Select your custom DB subnet group.

11. Set Public Access to 'No'

• Since this is a database, it should be kept private (not accessible from the public internet).

12. Security Groups

• For now, use the default security group (you can create a custom one later as needed).

13. Availability Zone Settings

• You will be prompted to select a primary Availability Zone—choose whichever one you prefer.

• The second zone will be used for standby (for failover support in Multi-AZ deployments).

14. Create the Database

• Click “Create database” at the bottom.

• Other settings can be explored and fine-tuned with more experience, but for now, these are sufficient to get started and understand the basic concept.

<h1>Summary Tips for Beginners</h1>

• Stick to the Free Tier while practicing.

• Always use a custom VPC for 3-tier architecture.

• Ensure that the RDS instance is not public and is accessible only to your application layer (private subnet).

• Use separate subnets in different AZs for database resilience.
