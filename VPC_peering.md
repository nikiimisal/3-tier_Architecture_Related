<h1>Step-by-Step Guide to AWS VPC Peering</h1>
<h2>1. Prerequisites</h2>

• You must have two VPCs with non-overlapping CIDR blocks—for example, 10.0.0.0/16 and 10.1.0.0/16. Overlapping CIDRs will cause the peering request to fail immediately
<br>
• If you intend to set up VPC peering, it is necessary to have at least one public subnet. Alternatively, as a practical approach for beginners, launching two servers in public subnets may be the most effective method for hands‑on learning.

<h2>2. Create Peering Connection</h2>

• Via AWS Console:

  1. Open the Amazon VPC Console.

  2. In the navigation pane, select Peering connections → Create peering connection.

  3. Optionally add a Name tag.

  4. Select:

      • Requester VPC (your current VPC).

      • Accepter VPC (can be in the same account or a different account; same or different Region) 

  5. Click Create peering connection .

>>( Optional )Via CLI:<br>
  Use<br>
  aws ec2 create-vpc-peering-connection<br>
(AWS CLI)or<br>
 New-EC2VpcPeeringConnection (PowerShell)


<h2>3. Accept the Peering Request</h2>

   >>To accept a VPC peering request, open the Amazon VPC console in the same region as the accepter VPC. Navigate to Peering Connections, locate the peering connection with status pending‑acceptance, then under      Actions, select Accept request and confirm when prompted.

 • The request enters a pending-acceptance state. The owner of the Accepter VPC must accept it to activate the connection .

 • If it's within the same account, you accept it yourself. For cross-account or inter-region, it needs to be accepted in the target account and region.

<h2>4. Now that the VPC peering connection has been established, let us proceed to launch one EC2 instance in each of the two peered VPCs.</h2>

   <h3>instance launching time in the network setting :</h3>

  • Select the VPC
   -- While launching the EC2 instance, choose the appropriate VPC under Network Settings.

  • Add Inbound HTTP Rule (Port 80)

   -- Set Type to HTTP (port 80) to allow traffic for Nginx.

   -- Set Type to HTTP (port 80) to allow traffic for Nginx.
   -- Set Source Type to Custom and enter The CIDR block of the peer VPC( 0.1.0.0/32) as the source ...if we use public-sider-block so request will come from public...which may cause disturbances in peering.

   •  Apply the same settings to the second server, with necessary adjustments.

   -- Set Source Type to Custom and enter 0.0.0.0/0 as the source—this allows access from the public internet.
   -- Because we need to install software.( i.e. nginx)
    
<h2>5. Now, enter using SSH command in the Power Console to access the instance (server)...the instance to which we've assigned the public-accessible CIDR block will be accessible from the public internet. This          configuration allows us to install Nginx directly on that instance.</h2>

         sudo yum install nginx
         sudo service nginx start
         cd /usr/share/nginx/html
         sudo nano nikhil.html
  •  Now, we have created a webpage in this server.So this This webpage should be accessible from another server in a different VPC.<br>
  • Go to another VPC=> select the instance => select their public ip => SSH into power shell =>
  
     curl http://dusrya-server-chi-PVT-ip/nikhil.html
  • Running the SSH command alone will fail unless the necessary route table updates are in place.

<h2>6. Update Route Tables</h2>

i. Send Peering Request

• Initiate the VPC peering connection by specifying the peer VPC’s ID.

ii. Access Subnet’s Route Table via EC2 Console

• Navigate to the EC2 instance.

• In the Details tab, click on the linked Subnet—this takes you to the subnet’s details.

• From the subnet page, click Route Table (or search for it) to find the associated route table.

iii. Modify Route Table

• In the Route Table view, click Actions → Edit routes.

iv. Add a new route:

-- Destination: The CIDR block of the peer VPC.

-- Target: The VPC peering connection ID (pcx-…) 

-- Apply these changes.

v. Repeat for Second Instance

• Perform these exact steps on the second EC2 instance to ensure proper bidirectional routing between the peered VPCs.

<h2>7. So now, run this command and is the output visible? then here will you go your vpc peering succefull..</h2>
         
   
       curl http://dusrya-server-chi-PVT-ip/nikhil.html
   







 
 
