# AWS VPC from components

![VPCfromComp](https://user-images.githubusercontent.com/49696513/173882736-081d2692-e93a-427a-9c56-38d3a61f16c0.png)

## Building an Amazon Virtual Private Cloud component by component

This architecture is meant for a public and private subnet under a common VPC. The VPC could be used to create an architecture with two EC2 instances, one in a private and one in a public subnets under the same VPC. The private instances route table enables a connection to the public subnets NAT gateway.

### Ingredients:

- VPC
- Public Subnet
- Private Subnet
- Internet Gateway
- Public Route Table
- Private Rpute Table
- NAT Gateway
- _Possibly two EC2 instances, should you want to test pinging_

## Steps

1. In the AWS Console, go to VPC's and select **Your VPCs**. Click Create VPC. This creation window will allow you to choose **VPC only** option. The **VPC and more** will create all the above mentioned components and conveniently allocate CIDR blocks to the subnets and the VPC, but we will do all of this manually. In the VPC creation window, select a IPv4 CIDR manual input and set it to for example 10.0.0.0/16. No IPv6 CIDR blocks needed. Once created, select **Edit DNS hostnames** from the Action menu and tick Enable.

2. Create a subnet called public and allocate it to the newly minted VPC. Set the CIDR range, for example 10.0.1.0/24 should do. Once created, edit the subnet settings and **Enable auto-assign public IPv4 addresses**. Create another subnet in the same availability zone, called private, and set the CIDR range to 10.0.2.0/24 or any other non blocking range. The two subnets can not have overlapping CIDR ranges.

3. Navigate to **Internet Gateways** from the leftside menu and create one. Once created, attach it to your VPC from the Actions menu.

4. Route tables. First make sure you select the correct route table, check that the VPC is yours and not the aws default one. Highly recommend naming the route table. Select **Routes** and edit the routes. **Add route** and set _destination_ to 0.0.0.0/0 and _target_ to the Internet Gateway you've created. Next we need to point the public subnet to the public route table. Select **Subnet Associations** and edit the subnet associations byt ticking your public subnet.

5. Create a NAT Gateway. Select your public subnet and allocate an Elastic IP.

6. Final thing, the Private route table. Create anoter route table and add routes with a _destination_ of 0.0.0.0/0 and _target_ to NAT Gateway. Edit the Subnet Associations and select your private subnet.

If you want, create two EC2 intances and connect to the public one by SSH. Try to ping the private instances private IPv4 address. It should respond.

*If you create more VPCs, have an eye on the CIDR ranges. The multiple VPC's can not have the same CIDR range.*
