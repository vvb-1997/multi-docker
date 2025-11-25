# AWS Configuration Cheat Sheet

## Elastic Beanstalk Application Creation

Make sure you have followed the guidance in this note.

Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

Click “Create Application”

Set Application Name to 'multi-docker'

Scroll down to Platform and select Docker

Verify that "Single Instance (free tier eligible)" has been selected

Click the "Next" button.

In the "Service Role" section, verify that "Use an Existing service role" is selected.

Verify that aws-elasticbeanstalk-service-role has been auto-selected for the service role.

Verify that aws-elasticbeanstalk-ec2-role has been auto-selected for the instance profile.

Click "Skip to review" button.

Click the "Submit" button.

You may need to refresh, but eventually, you should see a green checkmark underneath Health.

## RDS Database Creation

Go to AWS Management Console and use Find Services to search for RDS

Click Create database button

Select PostgreSQL

In Templates, check the Free tier box.

Scroll down to Settings.

Set DB Instance identifier to multi-docker-postgres

Set Master Username to postgres

Set Master Password to postgrespassword and confirm.

Scroll down to Connectivity. Make sure VPC is set to Default VPC

Scroll down to Additional Configuration and click to unhide.

Set Initial database name to fibvalues

Scroll down and click Create Database button

## ElastiCache Redis Creation

Go to AWS Management Console and use Find Services to search for ElastiCache

In the sidebar under Resources, click Redis OSS caches

Click the Create Redis OSS caches button

Select Design your own cache and Cluster cache

Make sure Cluster Mode is DISABLED.

Scroll down to Cluster info and set Name to multi-docker-redis

Scroll down to Cluster settings and change Node type to cache.t3.micro

Change Number of Replicas to 0 (Ignore the warning about Multi-AZ)

Scroll down to Subnet group. Select Create a new subnet group if not already selected.

Enter a name for the Subnet Group such as redis.

Scroll down and click the Next button

Scroll down and click the Next button again.

Scroll down and click the Create button.

After the cache has been fully created (green Available status), click the new multi-docker-redis cache name. Then, click Modify.

Scroll down to Security and locate the Transit encryption mode setting. Change this setting from Required to Preferred. This is very important, otherwise, you will not see your Calculated Values in the client form.

Scroll down and click Preview changes, then click Modify.

You will need to wait for the cache to apply the changes and restart (green Available status) which could take around 5 minutes or more.

## Creating a Custom Security Group

Go to AWS Management Console and use Find Services to search for VPC

Find the Security section in the left sidebar and click Security Groups

Click Create Security Group button

Set Security group name to multi-docker

Set Description to multi-docker

Make sure VPC is set to your default VPC

Scroll down and click the Create Security Group button.

After the security group has been created, find the Edit inbound rules button.

Click Add Rule

Set Port Range to 5432-6379

Click in the box next to Source and start typing 'sg' into the box. Select the Security Group you just created.

Click the Save rules button

## Applying Security Groups to ElastiCache

Go to AWS Management Console and use Find Services to search for ElastiCache

Under Resources, click Redis clusters in Sidebar

Check the box next to your Redis cluster

Click Actions and click Modify

Scroll down to find Selected security groups and click Manage

Tick the box next to the new multi-docker group and click Choose

Scroll down and click Preview Changes

Click the Modify button.

## Applying Security Groups to RDS

Go to AWS Management Console and use Find Services to search for RDS

Click Databases in Sidebar and check the box next to your instance

Click Modify button

Scroll down to Connectivity and add select the new multi-docker security group

Scroll down and click the Continue button

Click Modify DB instance button

## Applying Security Groups to Elastic Beanstalk

Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

Click Environments in the left sidebar.

Click MultiDocker-env

Click Configuration

In the Instances row, click the Edit button.

Scroll down to EC2 Security Groups and tick the box next to multi-docker

Click Apply and Click Confirm

After all the instances restart and go from No Data to Severe, you should see a green checkmark under Health.

## Add AWS configuration details to .github/workflow/deploy.yaml file's deploy script

Set the region. The region code can be found by clicking the region in the toolbar next to your username.
eg: 'us-east-1'

app should be set to the Elastic Beanstalk Application Name
eg: 'multi-docker'

env should be set to your Elastic Beanstalk Environment name.
eg: 'MultiDocker-env'

Set the bucket_name. This can be found by searching for the S3 Storage service. Click the link for the elasticbeanstalk bucket that matches your region code and copy the name.

eg: 'elasticbeanstalk-us-east-1-923445599289'

Set the bucket_path to 'docker-multi'

Set access_key_id to $AWS_ACCESS_KEY

Set secret_access_key to $AWS_SECRET_KEY

## Setting Environment Variables

Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

Click Environments in the left sidebar.

Click MultiDocker-env

In the left sidebar click Configuration

Scroll down to the Updates, monitoring, and logging section and click Edit.

Scroll down to the Environment Properties section. Click Add environment property.

In another tab Open up ElastiCache, click Redis and check the box next to your cluster. Find the Primary Endpoint and copy that value but omit the :6379

Set REDIS_HOST key to the primary endpoint listed above, remember to omit :6379

Set REDIS_PORT to 6379

Set PGUSER to postgres

Set PGPASSWORD to postgrespassword

In another tab, open up the RDS dashboard, click databases in the sidebar, click your instance and scroll to Connectivity and Security. Copy the endpoint.

Set the PGHOST key to the endpoint value listed above.

Set PGDATABASE to fibvalues

Set PGPORT to 5432

Click Apply button

After all instances restart and go from No Data, to Severe, you should see a green checkmark under Health.

## IAM Keys for Deployment

You can use the same IAM User's access and secret keys from the single container app we created earlier, or, you can create a new IAM user for this application:

1. Search for the "IAM Security, Identity & Compliance Service"

2. Click "Create Individual IAM Users" and click "Manage Users"

3. Click "Add User"

4. Enter any name you’d like in the "User Name" field. eg: docker-multi-travis-ci

5. Click "Next"

6. Click "Attach Policies Directly"

7. Search for "beanstalk"

8. Tick the box next to "AdministratorAccess-AWSElasticBeanstalk"

9. Click "Next"

10. Click "Create user"

11. Select the IAM user that was just created from the list of users

12. Click "Security Credentials"

13. Scroll down to find "Access Keys"

14. Click "Create access key"

15. Select "Command Line Interface (CLI)"

16. Scroll down and tick the "I understand..." check box and click "Next"

Copy and/or download the Access Key ID and Secret Access Key to use in the Github actions Variable Setup.

## AWS Keys in Github Repo Action 

Go to your Github repo settings and find the action repository for the application we are working on.

On the repository page, click "More Options" and then "Settings"

Create an AWS_ACCESS_KEY variable and paste your IAM access key

Create an AWS_SECRET_KEY variable and paste your IAM secret key

<img width="1540" height="929" alt="image" src="https://github.com/user-attachments/assets/e6138131-af00-4a88-9ed2-0ee3adb012e1" />

Create a docker hub account and add DOCKER_USERNAME and DOCKER_TOKEN (Personal Access Token)

## Deploying App

Make a small change to your src/App.js file in the greeting text.

In the project root, in your terminal run:

```
git add.
git commit -m “testing deployment"
git push origin main
```

Go to your Github Actions Tab and check the status of your build.

The status should eventually return with a green checkmark and show "build passing"

Go to your AWS Elastic Beanstalk application

It should say "Elastic Beanstalk is updating your environment"

It should eventually show a green checkmark under "Health". You will now be able to access your application at the external URL provided under the environment name.
