# Learning Objectives:
Create App Tier Instance

Configure Software Stack

Configure Database Schema

Test DB connectivity

# App Instance Deployment
1. Navigate to the EC2 service dashboard and click on Instances on the left hand side. Then, click Launch Instances.


2.	Select the first Amazon Linux 2 AMI

3. We'll be using the free tier eligible T.2 micro instance type. Select that and click Next: Configure Instance Details.

4. When configuring the instance details, make sure to select to correct Network, subnet, and IAM role we created. Note that this is the app layer, so use one of the private subnets we created for this layer.

5. We'll be keeping the defaults for storage so click next twice. When you get to the tag screen input a Name as a key and call the instance AppLayer. It's a good idea to tag your instances so you can easily keep track of what each instance was created for. Click Next: Configure Security Group.

6. Earlier we created a security group for our private app layer instances, so go ahead and select that in this next section. Then click Review and Launch. Ignore the warning about connecting to port 22- we don't need to do that.


7. When you get to the Review Instance Launch page, review the details you configured and click Launch. You'll see a pop up about creating a key pair. Since we are using Systems Manager Session Manager to connect to the instance, proceed without a keypair. Click Launch




## Configure Database Schema

1. Start by downloading the MySQL CLI:

```
sudo yum install mysql -y
```

2. Initiate your DB connection with your Aurora RDS writer endpoint. In the following command, replace the RDS writer endpoint and the username, and then execute it in the browser terminal:

```
mysql -h CHANGE-TO-YOUR-RDS-ENDPOINT -u CHANGE-TO-USER-NAME -p
```

You will then be prompted to type in your password. Once you input the password and hit enter, you should now be connected to your database.

NOTE: If you cannot reach your database, check your credentials and security groups.

3. Create a database called webappdb with the following command using the MySQL CLI:
```
CREATE DATABASE webappdb;   
```
4. You can verify that it was created correctly with the following command:
```
SHOW DATABASES;
```
5. Create a data table by first navigating to the database we just created:
```
USE webappdb;    
```
Then, create the following transactions table by executing this create table command:
```
CREATE TABLE IF NOT EXISTS transactions(id INT NOT NULL
AUTO_INCREMENT, amount DECIMAL(10,2), description
VARCHAR(100), PRIMARY KEY(id));    
```
6. Verify the table was created:
```
SHOW TABLES;    
```
7. Insert data into table for use/testing later:
```
INSERT INTO transactions (amount,description) VALUES ('400','groceries');   
```
8. Verify that your data was added by executing the following command:
```
SELECT * FROM transactions;
```
When finished, just type exit and hit enter to exit the MySQL client.

# Configure App Instance
1. The first thing we will do is update our database credentials for the app tier. To do this, open the application-code/app-tier/DbConfig.js file from the github repo in your favorite text editor on your computer. You’ll see empty strings for the hostname, user, password and database. Fill this in with the credentials you configured for your database, the writer endpoint of your database as the hostname, and webappdb for the database. Save the file.
Upload the app-tier folder to the S3 bucket that you created in part 0.

Go back to your SSM session. Now we need to install all of the necessary components to run our backend application. Start by installing NVM (node version manager).
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
```
Next, install a compatible version of Node.js and make sure it's being used
```
nvm install 16
nvm use 16
```
PM2 is a daemon process manager that will keep our node.js app running when we exit the instance or if it is rebooted. Install that as well.
```
npm install -g pm2   
```
Now we need to download our code from our s3 buckets onto our instance. In the command below, replace BUCKET_NAME with the name of the bucket you uploaded the app-tier folder to:
```
cd ~/
aws s3 cp s3://BUCKET_NAME/app-tier/ app-tier --recursive
```
Navigate to the app directory, install dependencies, and start the app with pm2.
```
cd ~/app-tier
npm install
pm2 start index.js
```
To make sure the app is running correctly run the following:
```
pm2 list
```
If you see a status of online, the app is running. If you see errored, then you need to do some troubleshooting. To look at the latest errors, use this command:
```
pm2 logs
```
NOTE: If you’re having issues, check your configuration file for any typos, and double check that you have followed all installation commands till now.

Right now, pm2 is just making sure our app stays running when we leave the SSM session. However, if the server is interrupted for some reason, we still want the app to start and keep running. This is also important for the AMI we will create:
```
pm2 startup
```
After running this you will see a message similar to this.

[PM2] To setup the Startup Script, copy/paste the following command: sudo env PATH=$PATH:/home/ec2-user/.nvm/versions/node/v16.0.0/bin /home/ec2-user/.nvm/versions/node/v16.0.0/lib/node_modules/pm2/bin/pm2 startup systemd -u ec2-user —hp /home/ec2-user
DO NOT run the above command, rather you should copy and past the command in the output you see in your own terminal. After you run it, save the current list of node processes with the following command:
```
pm2 save
```



## Test App Tier
Now let's run a couple tests to see if our app is configured correctly and can retrieve data from the database.

To hit out health check endpoint, copy this command into your SSM terminal. This is our simple health check endpoint that tells us if the app is simply running.
```
curl http://localhost:4000/health
```
The response should looks like the following:
```
"This is the health check"
```
Next, test your database connection. You can do that by hitting the following endpoint locally:
```
curl http://localhost:4000/transaction
```
You should see a response containing the test data we added earlier:
```
{"result":[{"id":1,"amount":400,"description":"groceries"},{"id":2,"amount":100,"description":"class"},{"id":3,"amount":200,"description":"other groceries"},{"id":4,"amount":10,"description":"brownies"}]}
```
If you see both of these responses, then your networking, security, database and app configurations are correct.

Congrats! Your app layer is fully configured and ready to go.


