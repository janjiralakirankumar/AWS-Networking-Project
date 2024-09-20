# Step-by-Step Guide for Creating a Networking Environment in AWS

### Overview:

- This guide will walk you through the process of setting up a networking environment in Amazon Web Services (AWS).

- You will create a Virtual Private Cloud (VPC), configure subnets, set up an Internet Gateway, define route tables, and launch an EC2 instance.

- This environment will consist of public and private subnets, ensuring secure and efficient network communication.
---
### Architecture Used in this Guide:
![image](https://docs.aws.amazon.com/images/vpc/latest/userguide/images/public-nat-gateway-diagram.png)

##### Below are detailed steps for creating each component in a Virtual Private Cloud (VPC) for hosting an EC2 server in AWS:
---
### Step 1: Sign in to AWS Console:

- Open your web browser, navigate to the AWS Management Console, and `sign in` with your AWS account credentials.
- Then Choose the AWS Region as `ap-south-1 (Mumbai Region).`
---
### Step 2: Create a Virtual Private Cloud (VPC):

- In the AWS Management Console, access the VPC Dashboard.
- Click `Your VPCs.`
- Select `Create VPC.`
- Choose `VPC only.`
- Fill in the following details:
  - **Name tag:** Provide a descriptive name for your VPC, such as `Demo-VPC-YourName`
  - **IPv4 CIDR block:** Enter `10.0.0.0/16`
- Leave other settings as default and click `Create VPC.`
---
### Step 3: Creating Subnets:

Our requirement is to create 4 Subnets:

- In the VPC Dashboard, click on `Subnets.`
- Click `Create Subnet`, Choose the VPC created in Step 2 and provide the required information as below.

#### Public Subnet 1:

- **Name tag:** Enter `Public-Subnet-1`
- **Availability Zone:** Select an availability zone as `ap-south-1a.`
- **IPv4 CIDR block:** Specify `10.0.0.0/24`
- Click on `Add new subnet` to continuing remaining subnets.

#### Private Subnet 1:

- **Name tag:** Label it as `Private-Subnet-1`
- **Availability Zone:** Select an availability zone as `ap-south-1a.`
- **IPv4 CIDR block:** Set it to `10.0.1.0/24`
- Click on `Add new subnet` to continuing remaining subnets.

#### Public Subnet 2:

- **Name tag:** Set as `Public-Subnet-2`
- **Availability Zone:** Pick a different zone as `ap-south-1b.`
- **IPv4 CIDR block:** Specify `10.0.2.0/24`
- Click on `Add new subnet` to continuing remaining subnets.

#### Private Subnet 2:

- **Name tag:** Label it as `Private-Subnet-2`
- **Availability Zone:** Select a availability zone as `ap-south-1b.`
- **IPv4 CIDR block:** Set it as `10.0.3.0/24`
- After entering all the subnet details, Click `Create Subnet`

In Console verify that the subnets are created.

---
### Step 4: Create an Internet Gateway

- In the VPC Dashboard, click on `Internet Gateways.`
- Click `Create Internet Gateway` and give it a name as `MyIGW-YourName` and again Click on `Create Internet Gateway`
- Click on the option `Attach to VPC` at top and choose the VPC created in Step 2 and click on `Attach internet gateway.`
---
### Step 5: Configure Route Tables

#### Task-1

**Create a Public Route Table:**
- In the VPC Dashboard, click on `Route tables`
- Click `Create Route Table.`
- Provide a name, e.g., `Public-Route-Table`
- Select the VPC you created in Step 2.
- Click `Create.`

**Create a Private Route Table:**
- Click `Create Route Table` again.
- Name it, e.g., `Private-Route-Table`
- Choose the same VPC.
- Click `Create.`

#### Task-2: Associate Route Tables:

- After creating both route tables, click on the `Public-Route-Table`
- In the `Routes` tab, click on Edit routes, add a route for `0.0.0.0/0` pointing to the `Internet Gateway` created in Step 4.
- Click `Save changes`

#### Task-3: Now, associate the Public Route table with the Public subnets and Private Route table with the Private subnets:

#### Task-4: Enable Auto Assign Public IP, Subnet Association For the Subnets (Public-Subnet-1,2 and Private-Subnet-1,2):

- In the VPC Dashboard, click on `subnets`
- Select `Public-Subnet-1.`
- In the `Actions` menu, choose `Edit Subnet settings` and then Modify `Auto-assign IP settings.`
- check `Enable auto-assign public IPv4 address` and save it.
- In the same subnet, under the `Route Table` section, click `Edit route table association` and select the `Public-Route-Table` you created.
- Click `Save.`

**Note:** Repeat the same for Public-Subnet-2 as well.

#### For the Private Subnets (Private-Subnet-1 and Private-Subnet-2):

- Select `Private-Subnet-1.`
- In the `Actions` menu, choose `Edit Subnet settings` Ensure that the `Auto-assign IP settings.` is set to `Disable` and save it.
- In the same subnet, under the `Route Table` section, click `Edit route table association` and select the `Private-Route-Table` you created.
- Click `Save.`

**Note:** Repeat the same for Private-Subnet-2 as well.

---
### Step 6: Create a "Public Security Group":

For the bastion host (public instance), create a security group allowing SSH (port 22) access from Any IP address.

- In the EC2 Dashboard, go to `Security Groups.`
- Click `Create Security Group.`
- Fill in the following details:
  - **Security group name:** Give it a name as `Public-SecurityGroup-YourName`
  - **Description:** `Allow SSH Access from Any IPv4.`
  - **VPC:** Select the VPC you created in Step 2.
  - Under `inbound rules` click on `Add rule` to allow SSH (Port 22) traffic from Anywhere-IPv4.
  - Click on `Create security group.`
---
### Step 7: Launching a Public EC2 Instance in Public Subnet.

- In the EC2 Dashboard, click `Launch Instance.` and give Name and Tags as `Demo-Public-Server-01`
- Ensure the Amazon Machine Image (AMI) as `Amazon Linux 2`
- Ensure the instance type as `t2.micro`
- Create a new SSH key pair with name as `Demo-KeyPair-YourName` and select the format as `.pem`to ensure secure access to the instance and click on `Create key pair`
- In the `Network settings` section click on `Edit`
  - Select the VPC you created in Step 2.
  - Choose the `Public Subnet-1` you created in Step 3.
  - Enable `Auto-assign Public IP` to allow the instance to have a public IP address.
- Under `Firewall (security groups)` section, choose `Select an existing security group` and choose the `Public security group` you created in Step 6.
- Under `Configure Storage` ensure it to 8 GiB,
- Review your instance configuration and click `Launch.`
---
### Step 8: Test the Connection

- Once the instance is changed to running state, connect to it using either `Connect` Option in AWS Console (or) SSH using SSH Client using the same key pair.
- Test the internet connectivity by running commands like `sudo apt update` (or) `ping google.com` (or) accessing external websites using `curl` or `wget`.
- Once verified close the Terminal Tab.

### By this we have successfully established Internet connection with Public Server-1.
---
### Step 9: Creating a `Private Security Group`:

For the private instance, create a security group that allows SSH access only from the bastion host's (Public) security group.

- In the EC2 Dashboard, go to `Security Groups.`
- Click `Create Security Group.`
- Fill in the following details:
  - **Security group name:** Give it a name as `Private-SecurityGroup-YourName`
  - **Description:** `Allowing SSH Access only from Public Security group.`
  - **VPC:** Select the VPC you created in Step 2.
- Under `Inbound rules` click on `Add rule` to allow SSH (Port 22) traffic from `Public-SecurityGroup-YourName` created in step 6.
  - Click on `Create security group.`
---
### Step 10: Launching a Private EC2 Instance in Private Subnet

- In the EC2 Dashboard, click `Launch Instance` and give it a name and tags, e.g., `Demo-Private-Server-01`
- Choose an Amazon Machine Image (AMI) such as `Amazon Linux 2.`
- Configure the instance type as `t2.micro.`
- Select the same existing SSH key pair created in Step 7 to ensure secure access to the private instance.
- In the `Network settings` section, click on `Edit`:
  - Select the VPC created in Step 2.
  - Choose the `Private Subnet-2` created in Step 3.
  - Ensure to `Disable` Auto-assign Public IP.
- Under `Firewall (security groups)` section, choose `Select an existing security group` and choose the `Private security group` you created in Step 9.
- Under `Configure Storage` ensure it to 8 GiB,
- Review your instance configuration and click `Launch.`
---
### Step 11: Configure the NAT Gateway

- In the AWS Management Console, navigate to the VPC service.
- Select `NAT Gateways` from the sidebar and click `Create NAT Gateway.`
- Provide the name, e.g., `NAT-Gateway-YourName`
- Choose `Public Subnet-2` for your NAT Gateway.
- Under `Elastic IP allocation ID,` click `Allocate Elastic IP` to assign a public IP address to the NAT Gateway.
- Click `Create NAT Gateway.`
---
### Step 12: Update Route Tables:
- Update the `Private route table` for your private subnet to route outbound traffic to the NAT Gateway.
- Select `Route Tables` and then choose "Private-Route-Table" and under `Routes` click on `Edit routes` and then `Add route`.
- Destination: 0.0.0.0/0 and Target: NAT Gateway.
- Click on `Save changes`
---
### Step 13: Test the Connection (Connect to the Private Instance via Bastion host):

- First, connect to the bastion host (public instance) as explained in Step 8.
- From the bastion host, you can SSH into the private instance using its private IP address:
    ```
    ssh -i `Demo-KeyPair.pem` ec2-user@xx.xxx.xx.xx
    ```

#### Note: 

- If it says `Permission Denied` this means that the Private Key file is not available in the Bastian host.
- To create a Private Key file follow below steps:
  1. First create a File with the same name as `Demo-KeyPair-YourName.pem`
    ```
    vi Demo-KeyPair-YourName.pem
    ```
  2. Once this created and opened then go to your local Downloads and open the `Demo-KeyPair-YourName.pem` and copy the Key without any extra charecters and paste it in this new file.
  3. To save the file use Press `Esc` and then enter `:wq!` and press enter.
  4. Change the File permissions to get access to use.
    ```
    chmod 400 Demo-KeyPair-YourName.pem
    ```
  5. Now again try to connect to Private Instance from Bastianhost.
    ```
    ssh -i `Demo-KeyPair-YourName.pem` ec2-user@xx.xxx.xx.xx
    ```
- Once connected, cross check the private IP.
- Now, `ping google.com` or `ping facebook.com` then it should work.
---
### Step 14: Cleanup of Resources (Very Important):

Once the Labs Practice is done, always ensure to cleanup the resources as some of these services are chargable.

---
### AWS Pricing for these Labs:

In the AWS lab setup, it's crucial to be aware of potential charges associated with certain services. Let's break it down for better clarity:

1. **Amazon EC2 Instances**: Charges depend on the instance type, operating system, and how you use them (on-demand, reserved, or spot instances).

2. **Elastic IP Addresses**: Allocating Elastic IP addresses to resources can lead to charges if they're not linked to running instances.

3. **NAT Gateway**: NAT Gateways come with hourly and data processing costs.

4. **Data Transfer**: Transferring data out of AWS services to the internet (e.g., data from your instances to external sites) and between AWS services in different Availability Zones can result in expenses.

5. **Amazon VPC**: The VPC itself is usually free, but components like subnets and route tables may contribute to costs.

6. **Internet Gateway**: The Internet Gateway itself is cost-free, but data transfer through it might incur charges.

7. **Amazon RDS (if used)**: If you use Amazon RDS in your VPC, it has its own pricing based on the RDS instance type, storage, and data transfer.

8. **Data Storage**: If you utilize services like Amazon EBS, costs are determined by the amount of data stored and I/O operations.

9. **Elastic Load Balancer (if used)**: Elastic Load Balancers come with costs based on type and data processing volume.

10. **Additional AWS Resources**: Depending on your needs, you might introduce other AWS services, which can also have their charges.

Keep a close eye on your AWS billing and usage via the AWS Billing and Cost Management dashboard. Set up billing alerts to receive notifications when costs surpass specific thresholds.

To cut costs, consider using AWS Free Tier services for smaller or low-traffic projects, leverage reserved instances for savings, and optimize your architecture based on your specific requirements.

---
### Conclusion

By following these detailed steps, you have successfully created a robust AWS networking environment with both public and private subnets, an Internet Gateway, NAT Gateway, Route Tables, and launched public and private EC2 instances while checking their connectivity.

**Note:**

Ensure that you adjust settings and security group rules as per your specific requirements, and stay updated with AWS best practices to maintain a secure and efficient network environment.
