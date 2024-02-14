# Host a website for free on AWS

Having a portfolio is essential whether you're a junior developer looking for employment, or a senior-level developer with years of experience. 
A portfolio demonstrates skills, abilities, prior work or learning experiences that can be useful in demonstrating to potential employers that you are qualified for that specific job. 

An online portfolio website is a great way to accomplish all of the above. There are many free website hosting services available, but since I'm looking for opportunities as a cloud engineer, cloud architect, or solutions architect, I thought it would be best to use Amazon Web Services free website hosting services.


# Why  I chose AWS?



 I chose Amazon Web Services because you only pay for the compute power, storage, and other resources you use, with no long-term contracts or up-front commitments, and you are able to use some services for free (AWS Free Tier) after your initial AWS sign-up date.


In this tutorial, we will learn how to set up and host a website on AWS using [Free Tier Services](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all), which will be free for the first year.



# The architecture



![Static website drawio](https://user-images.githubusercontent.com/50238769/200831957-455c9bca-306a-4d71-a0cc-3e948a6ef566.png)

# Step 1. Get started with Amazon Simple Storage Service (S3)

- Amazon provides 5GB of standard storage for free for 12 months for Amazon Simple Storage Service. 
- S3 can be found in the AWS console under Storage services. 
- When you create a bucket, you give it a name and select the AWS Region where it will be stored. Check that your bucket name adheres to the [bucket naming guidelines](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html). 
- Drag and drop your project files into the 'Objects' tab under your bucket name at the top of the page, then click the upload button.


![Screenshot 2022-11-08 at 13 09 05](https://user-images.githubusercontent.com/50238769/200858220-c75cb628-ab6c-49ee-aac7-e373178990ac.png)

# Step 2. Create your key pair
- Click on launch button, and in the prompt select Create a new pair if you don’t have existing key pairs. Download the key pair, it will be a .pem file. 


![Screenshot 2022-11-08 at 13 30 53](https://user-images.githubusercontent.com/50238769/200872138-206f61d2-5e4f-4964-965f-52f09576f91f.png)

# Step 3. Create a security group
- A security group controls the traffic that is allowed to reach and leave the resources that it is associated with(Instance level security). 
- Security groups are stateful, when you create an outbound rule an inbound rule is automatically created.
- [More](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) about security groups.
- To create your security group, go to the Elastic Compute Cloud dashboard and select 'Security Groups' under the 'Network and Security' tab.
- Create two security group inbound rules and they should look like the one in the screenshot below for HTTP and HTTPS traffic.

![Screenshot 2022-11-08 at 13 26 46](https://user-images.githubusercontent.com/50238769/200865784-0ad0c5cf-233a-4216-af78-bb6e02ca56ac.png)

# Step 4. Get started with Elastic Compute Cloud (EC2).

- EC2 is a secure and resizable compute capacity for virtually any workload and AWS offers 750 hours per month for 12 months with the AWS free tier.
- Navigate to the EC2 dashboard and click on launch instance

![Screenshot 2022-11-08 at 13 07 34](https://user-images.githubusercontent.com/50238769/200863933-08ab2037-22bc-4176-99e9-631525989c70.png)
- Select Amazon Linux 2 AMI, ensure that it is a t2.micro(free tier eligible).
- Under the 'Network Settings' tab select the security group you created in step 2.
- Continue through the setup keeping the defaults.
- Under the 'Advanced details' tab in the user data text field, write the user data script like the one in the screenshot below. Do not forget to put your bucket name in the user data script. Click the 'Launch' button to launch your instance.
- When you launch an instance in Amazon EC2, you can pass user data to the instance, which can then be used to perform common automated configuration   tasks and even run scripts after the instance has started. In our case, we want to copy all of the project files from our S3 bucket to our EC2 instance.

![Screenshot 2022-11-09 at 17 20 26](https://user-images.githubusercontent.com/50238769/200869524-9eab969f-fa39-4fe6-b008-282d959e1ff7.png)

- After clicking 'Launch Instance' your instances will few minutes for it to be up and running.


# Step 6. Create Elastic Load Balancer security group

- Follow the steps in STEP 2 to create a security group for the ELB.
- Add these rules in the screenshot to your security group. 
![Screenshot 2022-11-08 at 13 26 46](https://user-images.githubusercontent.com/50238769/200865784-0ad0c5cf-233a-4216-af78-bb6e02ca56ac.png)

# Step 7. Add Application Load Balancer
- Elastic Load Balancing automatically distributes your incoming traffic across multiple targets, such as EC2 instances, containers, and IP addresses, in one or more Availability Zones. It monitors the health of its registered targets, and routes traffic only to the healthy targets. 
- Steps to creating an Application Load Balancer follow this link - https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-application-load-balancer.html.
- Go to the Elastic Compute Cloud dashboard and look for the 'Load Balancing' tab, Select 'Load Balancer' and create your application load balancer.
![Screenshot 2022-11-09 at 18 26 30](https://user-images.githubusercontent.com/50238769/200886068-e654580b-d5a9-4102-ac08-c315393aaabc.png)


- Each target group routes requests to one or more registered targets, such as EC2 instances, using the protocol and port number that you specify. You can register a target with multiple target groups. You can configure health checks on a per target group basis. Health checks are performed on all targets registered to a target group that is specified in a listener rule for your load balancer.
- Register your EC2 in the Target Group target group you created. 

![Screenshot 2022-11-09 at 18 14 37](https://user-images.githubusercontent.com/50238769/200882546-92f8c531-4a62-4964-bbac-c3d6310d99ff.png)

- Use the elastic load balancer security group for your Application Load balancer. 

# Step 8. Make your EC2 security groups more secure.
- The security groups should not open traffic to 0.0.0.0 (internet). 
- Only the ELB should allow traffic to 0.0.0.0, the EC2 security group should only allow traffic to and from the Load Balancer security group. 
- Edit inbound rule and attach HTTPS and HTTP to ELB security group. 

![Screenshot 2022-11-09 at 18 38 15](https://user-images.githubusercontent.com/50238769/200889128-0ac4646b-05a9-4cbc-a959-38e033dfd97a.png)


# View Website
- Copy your Application Load Balancer DNS and paste the link on your browser to view your website.


# Step 9. Create Auto Scaling Group
So far, everything works fine, but there are a few spots that could be a single point of failure. The site will be unavailable if the single EC2 instance fails. To avoid this, let's add fault tolerance by starting a new one whenever the one that's currently running fails. We can do this by creating an auto scaling group.
 ### Steps to creating an Auto Scaling group
- Create a snapshot of your EC2 instance.
- Create a launch template using your snapshot(AMI) and follow this guide - https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html
- Go to the EC2 dashboard, under 'Auto Scaling' select 'Auto scaling Groups' and create a launch template.
- To create a launch template folow this guide - https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html

Thanks for reading 👍! Please leave feedback and let me know if you enjoyed this post. 

















# cloud-resume-challenge
