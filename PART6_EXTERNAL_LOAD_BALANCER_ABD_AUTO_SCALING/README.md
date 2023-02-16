# Learning Objectives:
Create an AMI of our Web Tier

Create a Launch Template

Configure Auto Scaling

Deploy External Load Balancer


NOTE: The first 3 steps will remain same only instead of port no 4000 we will be using port number 80 for http traffic as this is for our web tier 

# Internet Facing Load Balancer

1. On the left hand side of the EC2 dashboard select Load Balancers under Load Balancing and click Create Load Balancer.

2. We'll be using an Application Load Balancer for our HTTP traffic so click the create button for that option.

3. After giving the load balancer a name, be sure to select internet facing since this one will not be public facing, but rather it will route traffic from our web tier to the app tier.

4. Select the correct network configuration for VPC and public subnets.

5. Select the security group we created for this internal ALB. Now, this ALB will be listening for HTTP traffic on port 80. It will be forwarding the traffic to our target group that we just created, so select it from the dropdown, and create the load balancer.


