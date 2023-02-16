# Learning Objectives
Update NGINX Configuration Files

Create Web Tier Instance

Configure Software Stack

# Update Config File

Before we create and configure the web instances, open up the application-code/nginx.conf file from the repo we downloaded. Scroll down to line 58 and replace [INTERNAL-LOADBALANCER-DNS] with your internal load balancer’s DNS entry. You can find this by navigating to your internal load balancer's details page.

Then, upload this file and the application-code/web-tier folder to the s3 bucket you created for this lab.

# Web Instance Deployment

1. Follow the same instance creation instructions we used for the App Tier instance in Part 3: App Tier Instance Deployment, with the exception of the subnet. We will be provisioning this instance in one of our public subnets. Make sure to select the correct network components, security group, and IAM role. This time, auto-assign a public ip on the Configure Instance Details page. Remember to tag the instance with a name so we can identify it more easily.

Then at the end, proceed without a key pair for this instance.

# Connect to Instance

Follow the same steps you used to connect to the app instance and change the user to ec2-user. Test connectivity here via ping as well since this instance should have internet connectivity:
```
sudo -su ec2-user 
ping 8.8.8.8
```
Note: If you don't see a transfer of packets then you'll need to verify your route tables attached to the subnet that your instance is deployed in.

# Configure Web Instance

We now need to install all of the necessary components needed to run our front-end application. Again, start by installing NVM and node :
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
nvm install 16
nvm use 16
```
Now we need to download our web tier code from our s3 bucket:
```
cd ~/
aws s3 cp s3://BUCKET_NAME/web-tier/ web-tier --recursive
```
Navigate to the web-layer folder and create the build folder for the react app so we can serve our code:
```
cd ~/web-tier
npm install 
npm run build
```
NGINX can be used for different use cases like load balancing, content caching etc, but we will be using it as a web server that we will configure to serve our application on port 80, as well as help direct our API calls to the internal load balancer.

sudo amazon-linux-extras install nginx1 -y

We will now have to configure NGINX. Navigate to the Nginx configuration file with the following commands and list the files in the directory:
```
cd /etc/nginx
ls
```
You should see an nginx.conf file. We’re going to delete this file and use the one we uploaded to s3. Replace the bucket name in the command below with the one you created.
```
sudo rm nginx.conf
sudo aws s3 cp s3://BUCKET_NAME/nginx.conf .
```
Then, restart Nginx with the following command:

```
sudo service nginx restart
```
To make sure Nginx has permission to access our files execute this command:
```
chmod -R 755 /home/ec2-user
```
And then to make sure the service starts on boot, run this command:

```
sudo chkconfig nginx on
```
Now when you plug in the public IP of your web tier instance, you should see your website, which you can find on the Instance details page on the EC2 dashboard. If you have the database connected and working correctly, then you will also see the database working. You’ll be able to add data. Careful with the delete button, that will clear all the entries in your database.
