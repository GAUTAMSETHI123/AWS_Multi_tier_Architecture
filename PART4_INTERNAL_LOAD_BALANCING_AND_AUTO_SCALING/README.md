# Learning Objectives:
Create an AMI of our App Tier

Create a Launch Template

Configure Autoscaling

Deploy Internal Load Balancer

# App Tier AMI

1.Navigate to Instances on the left hand side of the EC2 dashboard. Select the app tier instance we created and under Actions select Image and templates. Click Create Image.


![This is an image](https://github.com/GAUTAMSETHI123/AWS_Multi_tier_Architecture/blob/main/assests/images/PART3/CreateAMI1.png)

2. Give the image a name and description and then click Create image. This will take a few minutes, but if you want to monitor the status of image creation you can see it by clicking AMIs under Images on the left hand navigation panel of the EC2 dashboard.


![This is an image](https://github.com/GAUTAMSETHI123/AWS_Multi_tier_Architecture/blob/main/assests/images/PART3/CreateAMI2.png)

#Target Group

1.While the AMI is being created, we can go ahead and create our target group to use with the load balancer. On the EC2 dashboard navigate to Target Groups under Load Balancing on the left hand side. Click on Create Target Group.

2.The purpose of forming this target group is to use with our load blancer so it may balance traffic across our private app tier instances. Select Instances as the target type and give it a name.

3.Then, set the protocol to HTTP and the port to 4000. Remember that this is the port our Node.ja app is running on. Select the VPC we've been using thus far, and then change the health check path to be /health. This is the health check endpoint of our app. Click Next.

4.We are NOT going to register any targets for now, so just skip that step and create the target group.


# Internal Load Balancer

1. On the left hand side of the EC2 dashboard select Load Balancers under Load Balancing and click Create Load Balancer.

2. We'll be using an Application Load Balancer for our HTTP traffic so click the create button for that option.

3. After giving the load balancer a name, be sure to select internal since this one will not be public facing, but rather it will route traffic from our web tier to the app tier.

4.Select the correct network configuration for VPC and private subnets.

5.Select the security group we created for this internal ALB. Now, this ALB will be listening for HTTP traffic on port 80. It will be forwarding the traffic to our target group that we just created, so select it from the dropdown, and create the load balancer.

# Launch Template

1. Before we configure Auto Scaling, we need to create a Launch template with the AMI we created earlier. On the left side of the EC2 dashboard navigate to Launch Template under Instances and click Create Launch Template.

2.Name the Launch Template, and then under Application and OS Images include the app tier AMI you created.

3.Under Instance Type select t2.micro. For Key pair and Network Settings don't include it in the template. We don't need a key pair to access our instances and we'll be setting the network information in the autoscaling group.

4.Set the correct security group for our app tier, and then under Advanced details use the same IAM instance profile we have been using for our EC2 instances.

# Auto Scaling

1. We will now create the Auto Scaling Group for our app instances. On the left side of the EC2 dashboard navigate to Auto Scaling Groups under Auto Scaling and click Create Auto Scaling group.

2. Give your Auto Scaling group a name, and then select the Launch Template we just created and click next.

3.On the Choose instance launch options page set your VPC, and the private instance subnets for the app tier and continue to step 3.

4.For this next step, attach this Auto Scaling Group to the Load Balancer we just created by selecting the existing load balancer's target group from the dropdown. Then, click next.

5.For Configure group size and scaling policies, set desired, minimum and maximum capacity to 2. Click skip to review and then Create Auto Scaling Group.

You should now have your internal load balancer and autoscaling group configured correctly. You should see the autoscaling group spinning up 2 new app tier instances. If you wanted to test if this is working correctly, you can delete one of your new instances manually and wait to see if a new instance is booted up to replace it.

NOTE: Your original app tier instance is excluded from the ASG so you will see 3 instances in the EC2 dashboard. You can delete your original instance that you used to generate the app tier AMI but it's recommended to keep it around for troubleshooting purposes.






