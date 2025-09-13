<h1>Network Access Control List ( NACL ) </h1>

NACL vs. Security Group :

| Feature          | NACL (Network Access Control List)        | Security Group                                                                               |
| ---------------- | ----------------------------------------- | -------------------------------------------------------------------------------------------- |
| Scope            | Subnet-level                              | Instance-level                                                                               |
| Stateful?        | No (stateless)—must allow both directions | Yes—automatic return path interception                                                       |
| Rule Types       | Allow & Deny                              | Only Allow (deny is default)                                                                 |
| Evaluation Order | Sequential (rule number order matters)    | All rules evaluated collectively                                                             |
| Default Behavior | Custom: deny all; Default: allow all      | Deny all inbound, allow all outbound by default                                              |

>> Steteless=> The response is received through the same port from which the help request was sent .
>> stateless=> Request respons differents port .

<h1>Use a NACL (AWS Example)</h1>

<h2>1.  Create a VPC and Subnet</h2>

• NACLs are associated at the subnet level. Create your VPC and define its subnets if not already done.

• Go to vpc(vpc id) --> in details --> main network ACL--click id --> inbound rouls --> edit rouls --> save it.
>>Priority is assigned first to numbers, then to letters and special characters. Thus, when you designate ‘1’ as the highest priority, it indeed becomes the highest priority. Subsequent priorities follow in numerical order—2, 3, 4, ... up to 100, and so on. In essence, numbers receive the highest precedence.

• You can also block a specific IP address within ‘NACL’ by modifying the security rules, or utilize other applicable scenarios

<h2>2. Access the NACL Console</h2>

• Navigate to the VPC service in the AWS Console.

• Select Network ACLs from the sidebar to view default allocations and manage new ones.

<h2>3. Create a Custom NACL</h2>

• Click Create Network ACL, assign a descriptive name, and link it to your VPC 

<h2>4. Associate NACL with Subnet</h2>

• Go to Subnet associations, choose your subnet, and attach the newly created NACL (replacing the default) 

<h2>5. Define Inbound and Outbound Rules</h2>

• Edit Inbound rules: Specify rule number, protocol (e.g., TCP), port range, source IP (CIDR), and action (Allow/Deny).

• Edit Outbound rules: Similarly, specify rule number, protocol, port range, destination IP, and action 

<h2>6. Rule Numbering Matters</h2>

• Rules are evaluated in ascending order. Lower numbers are higher priority; the first match applies 

<h2>7. Test and Monitor</h2>

 • Launch an instance within the subnet and test access (e.g., SSH). If inbound is allowed but outbound isn’t explicitly allowed, the connection will fail due to statelessness.

 • Use VPC Flow Logs to monitor and verify traffic behavior 

<h2>8. Consider Rule Limits</h2>

 • AWS allows up to 100 rules per NACL, and a maximum of 200 NACLs per VPC. Each subnet can be attached to only one NACL, but a NACL can cover multiple subnets 


<h1>When & Why to Use NACLs</h1>

 • Layered Security: Acts as a broad filter before deeper inspection via security groups <br>
 • Blacklisting/Whitelisting: Useful to deny known malicious IPs across a subnet—even if security groups are misconfigured <br>
 • High-Security Environments: Often used in industries with strict compliance needs as a mandatory backstop <br>

 
 However, some teams avoid them due to complexity—stateless rules require precise configuration, and troubleshooting can become difficult


