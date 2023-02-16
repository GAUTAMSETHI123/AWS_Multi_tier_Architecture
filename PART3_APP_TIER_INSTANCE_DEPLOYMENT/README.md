# Learning Objectives:
Create App Tier Instance

Configure Software Stack

Configure Database Schema

Test DB connectivity


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

