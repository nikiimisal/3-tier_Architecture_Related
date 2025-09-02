
<h1>CLOUD WATCH</h1>
<h1>We will now set up Manually monitoring and alerting using Amazon CloudWatch, with the following steps:</h1>

>>Amazon CloudWatch enables you to monitor all AWS resources efficiently. Simply navigate to the Monitoring tab of any EC2 instance to view visual graphs displaying metrics like CPU usage and network activity. To stay alert when server load increases,Set up a CloudWatch alarm manually for now, and later we’ll evaluate using Auto Scaling with using LOADBALANCING.

1. Access Amazon CloudWatch
   In the AWS Console, search for and open CloudWatch.

2. Create a CloudWatch Alarm

• In the Amazon EC2 console, search for and open CloudWatch.
• Click Create Alarm.
• In the CloudWatch console ➜  click Alarms ➡  then choose Create alarm ➜ Select Metrics ➜ navigate to EC2, and locate the desired instance(Web Server) ➜ From its metrics ➜ choose the CPUUtilization metric—      since you want to monitor CPU usage for practice—and then click Select metric.

3. Configure Alarm Conditions

  • Under Conditions, set :
  -- Specify how long the CPU utilization must remain consistently above the threshold for the alarm to trigge
  >>for example, choose the number of consecutive evaluation periods (e.g., 2 or 3) that suit your practice scenario.

 • Threshold type: Static
 • Whenever CPUUtilization is: Greater than or equal to (≥)
 • Threshold value: e.g., 70% (or any value suitable for practice)
 • Define Evaluation Periods and Datapoints to Alarm to control the trigger sensitivity.

4. Set Up Alarm Notifications (SNS)

• In the Notification section, choose or create an SNS topic:

=> Go to the SNS console

=> Click Create Topic ➜ choose Standard ➜ and assign a name.

=> Create a Subscription to this topic using your email so You will receive email notifications whenever traffic increases; confirm the subscription when prompted.

=> Return to CloudWatch and link this SNS topic to your alarm’s "In alarm" action.

5. Finalize Alarm Setup

• Provide a descriptive alarm name (e.g., HighCPUAlarm) and description.

• Review configuration and click Create alarm.

6. Test the Alarm Using Stress Tool
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

