
<h1>CLOUD WATCH</h1>
<h1>We will now set up Manually monitoring and alerting using Amazon CloudWatch, with the following steps:</h1>

>>Amazon CloudWatch enables you to monitor all AWS resources efficiently. Simply navigate to the Monitoring tab of any EC2 instance to view visual graphs displaying metrics like CPU usage and network activity. To stay alert when server load increases,Set up a CloudWatch alarm manually for now, and later we’ll evaluate using Auto Scaling with using LOADBALANCING.

<h3>1. Access Amazon CloudWatch</h3>
   In the AWS Console, search for and open CloudWatch.

<h3>2. Create a CloudWatch Alarm</h3>

• In the Amazon EC2 console, search for and open CloudWatch.<br>

• Click Create Alarm.<br>

• In the CloudWatch console ➡  click Alarms ➡  then choose Create alarm ➡ Select Metrics ➡ navigate to EC2, and locate the desired instance(Web Server) ➡ From its metrics ➡ choose the CPUUtilization metric—      since you want to monitor CPU usage for practice—and then click Select metric.<br>

<h3>3. Configure Alarm Conditions</h3>

  • Under Conditions, set :<br>
  
  -- Specify how long the CPU utilization must remain consistently above the threshold for the alarm to trigge
  
  >>for example, choose the number of consecutive evaluation periods (e.g., 2 or 3) that suit your practice scenario.

 • Threshold type: Static<br>
 
 • Whenever CPUUtilization is: Greater than or equal to (≥)<br>
 
 • Threshold value: e.g., 70% (or any value suitable for practice)<br>
 
 • Define Evaluation Periods and Datapoints to Alarm to control the trigger sensitivity.

<h3>4. Set Up Alarm Notifications (SNS)</h3>

   • In the Notification section, choose or create an SNS topic:

 => Go to the SNS console

 => Click Create Topic ➜ choose Standard ➜ and assign a name.

 => Create a Subscription to this topic using your email so You will receive email notifications whenever traffic increases; confirm the subscription when prompted.

 => Return to CloudWatch and link this SNS topic to your alarm’s "In alarm" action.

<h3>5. Finalize Alarm Setup</h3>

   • Provide a descriptive alarm name (e.g., HighCPUAlarm) and description.

  • Review configuration and click Create alarm.

<h3>6. Test the Alarm Using Stress Tool</h3>
   >>Since our web server is not yet live and this is a practice environment, we cannot observe real incoming traffic. Therefore, we'll need to simulate traffic manually to test CPU utilization

To simulate high CPU usage and test alarm functionality, install and run the stress tool:

     sudo yum install stress -y
     stress --cpu 8 --timeout 60

  This command consumes CPU on 8 cores for 60 seconds. You should receive an email alert once your alarm is triggered.
>> NOTE :<br>
 • Delete the CloudWatch alarm, as it incurs charges.<br>
 • If detailed monitoring is enabled, disable it by unchecking the “Enable” option to stop additional billing.<br>
 • Terminating the EC2 instance will also cease billing for detailed monitoring of that instance.<br>
 • SNS topics don’t incur storage costs, so you may retain or delete them based on preference.<br>

 <h1>AUTOSCALLING / AUTOMETION </h1>
 <h1>Autoscalling using =>> Load balancer =>> and Cloud Watch </h1>
<h2>Quick Recap :</h2>

1. Enable Nginx on the web server via SSH and verify functionality in a browser.

2. Create an AMI of the configured web server.

3. Add a subnet in a different Availability Zone and mark it as public (for testing purposes).

4. Launch a Load Balancer:

   • Select an Application Load Balancer (ALB), make it Internet-facing, and assign it to two AZs using your public subnets.

5. Create a Target Group and register three server instances.

6. Configure the Load Balancer to use the Target Group, then launch it and ensure port 80 is allowed in its security group.

 <h1> **** Auto Scaling Group (ASG) Setup : ****</h1>

  <h2>1. Create an Auto Scaling Group</h2>

<h3>i. Name the Auto Scaling Group</h3>
  Provide a meaningful name for your Auto Scaling group.

<h3>ii. Launch Template Setup</h3>

>>Ensure that Auto-assign Public IPv4 address is enabled for both subnets—websubnet and websubnetZoneB. To configure this :<br>
  • Open the VPC console, then go to Subnets.<br>
  • Select each subnet, click Actions → Edit subnet settings.<br>
  • Check Enable auto-assign public IPv4 address, and click Save.<br>
  
Click Create launch template to generate a new template.

  • Assign a logical name to the template (e.g., my-template) and specify a version identifier.

  • Provide a version name for the launch template

  • A launch template ensures all your configuration changes—including AMI, key pair, and security group—can be versioned and reused rather than modifying live instances directly.

<h3>iii. Specify AMI</h3>
Select your previously created AMI in the launch template to define the base image for new instances.

<h3>iv. Choose Instance Type</h3>
Pick a compatible instance type (e.g., t2.micro or similar depending on your requirements).

<h3>v. Assign Key Pair</h3>
Select an existing SSH key pair from your account to allow secure access.

<h3>vi. Network Settings</h3>
Under network configuration, leave the subnet set to “Don’t include in launch template”. Subnet selection is handled later via the Auto Scaling group settings itself.

<h3>vii. Select Security Group</h3>
Choose an existing security group (default is acceptable for practice scenarios).

<h3>viii. Finalize Launch Template</h3>
After completing the settings, click Create launch template.

<h2>2. Launch the Auto Scaling Group</h2>

   • Return to the Auto Scaling Groups section, and begin creating a new group using the launch template you just configured.

<h2>3. Choose Instance Launch Option</h2>

  • Select your custom VPC, which ensures that your instances launch in the intended network.

  • Choose the appropriate web subnet within that VPC.

 >>Ensure that Auto-assign Public IPv4 address is enabled for both subnets—websubnet and websubnetZoneB. To configure this :<br>
  • Open the VPC console, then go to Subnets.<br>
  • Select each subnet, click Actions → Edit subnet settings.<br>
  • Check Enable auto-assign public IPv4 address, and click Save.<br>

• For Availability Zone distribution, select “Balanced (best effort)”, which ensures instances are evenly distributed across AZs while also launching them in other healthy zones if a launch fails in one.

<h2>4. Integrate with Other Services (Load Balancer)</h2>

   • Under Load Balancing, choose “Attach to a new load balancer.”

   • Provide the required basic configuration:
   
i. Select Availability Zones and their corresponding public subnets for an Internet-facing Load Balancer.<br>

ii. Enable health checks—both Elastic Load Balancer health checks and also <br>
    Enable Auto Scaling health checks—to ensure only healthy instances serve traffic.

<h2>5. Configure Group Size and Scaling</h2>

  • Define the minimum, desired, and maximum capacity according to your requirements.

  • Enable automatic scaling with target tracking policies.

  • Set the instance maintenance policy to “Launch before terminating” for smooth transitions.

  • Leave additional capacity settings as default.

  • Enable CloudWatch monitoring for the Auto Scaling group to track performance and scaling events.

<h2>6. Add Notifications</h2>

  • Configure notifications (e.g., via SNS) to receive alerts when scaling activities occur or thresholds are breached.

<h2>7. Review Configuration</h2>

  • Review all settings—Auto Scaling group name, launch template, VPC/subnet distribution, Load Balancer integration, scaling policies, and notifications—before finalizing the setup.

<h1>To verify whether Auto Scaling is working, generate simulated traffic on the instances.</h1>

1. Open your PowerShell console and SSH into one of the newly created Auto Scaling instances using its instance ID.
   
   >>(using AMI.. in the new 2 servers that have been launched, also increase traffic by using ssh)<br>
      So when traffic increases, the autoscalling group launches new servers(instances).

3. Once logged in, install the stress utility:

       sudo amazon-linux-extras install epel -y
       sudo yum install stress -y

4. Generate CPU load with the following command:

       stress --cpu 8 --timeout 60

 This will simulate high CPU usage for one minute, allowing you to verify that your Auto Scaling policies (e.g., launch of new instances) respond appropriately.

 
4. To verify whether Auto Scaling is functioning correctly, generate simulated traffic on the load balancer and check the number of active targets in the Target Group.

    • Go to EC2 → Load Balancers → Target Groups → Targets (or Monitoring).

    • There, you can view how many servers have been added in response to increased traffic.

    •  As traffic increases, servers will incress; conversely, as traffic decreases, servers will decreases.




<h1>  “If your practice session is now complete:</h1>

• Delete the Auto Scaling group.

• Delete the Load Balancer.

• Deregister and delete the AMI.

• Delete any associated snapshots.

• Delete unattached EBS volumes, which continue to generate costs even after instance termination

After all serveces has been closed,it is conformed

• Terminate any running instances and services.

• These actions should prevent any unnecessary charges.”
