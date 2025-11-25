# Creating the Elastic Beanstalk Environment
Please use the following instructions to create an environment with the Amazon Linux 2023 Platform. Since the AWS UI and options change frequently, these are provided as a lecture note instead of a video lecture.

1. Go to AWS Management Console

2. Search for Elastic Beanstalk and click the Elastic Beanstalk service.
<img width="2126" height="470" alt="image" src="https://github.com/user-attachments/assets/b33e4d71-1188-4be6-9814-f0123973520d" />

3. Click the Create environment button.
<img width="1866" height="382" alt="image" src="https://github.com/user-attachments/assets/ef82be81-9a03-4176-9c85-2ddb0b3449a1" />

4. You will need to provide an Application name, which will auto-populate an Environment name.
<img width="1204" height="915" alt="image" src="https://github.com/user-attachments/assets/3f70c866-9b40-4ca2-a6f0-06ee6f11f7c3" />

5. Scroll down to find the Platform section. You will need to select the Platform of Docker. This will auto-select several default options.
<img width="1218" height="887" alt="image" src="https://github.com/user-attachments/assets/dfe0fa5a-67a8-45bd-9b1e-ee29d1043534" />

6.  Scroll down to the Presets section and make sure that free tier eligible has been selected
<img width="887" height="560" alt="image" src="https://github.com/user-attachments/assets/7cdd40c0-c1a9-492d-ad70-4cc481e67222" />

7. Click the Next button to move to Step #2.

8. You will be taken to a Service Access configuration form.

Ensure that Use an existing service role is selected and that the service role created in Section 7 is listed. This should also auto-populate the EC2 instance profile with the ec2-role that was previously created.
<img width="1579" height="1080" alt="image" src="https://github.com/user-attachments/assets/dca70f7d-3574-4b05-b7a5-5d3ea473ddb4" />

9. Click the Skip to Review button.

10. Click the Submit button and wait for your new Elastic Beanstalk application and environment to be created and launch.

Important - Attached to this lecture is a cheatsheet complete with all the steps to configure the multi-container project in AWS.

Required Updates for Docker Compose
1. Rename the current docker-compose file

Rename the docker-compose.yml file to docker-compose-dev.yml. Going forward you will need to pass a flag to specify which compose file you want to build and run from:\
`docker-compose -f docker-compose-dev.yml up`\
`docker-compose -f docker-compose-dev.yml up --build`\
`docker-compose -f docker-compose-dev.yml down`

2. Create a production-only docker-compose.yml file

The production compose file will follow closely what was written in the Dockerrun.aws.json. There are two major differences:

Docker Compose will handle this container communication automatically for us.

Environment Variables: When using a compose file we will need to explicitly specify the environment variables each service will need access to. The value for each variable must match the corresponding variable names you have specified in the Elastic Beanstalk environment.

Note - You must NOT have a Dockerrun.aws.json file in your project directory. If AWS EBS sees this file the deployment will fail. If you have previously followed this course and deployed to the old Multi-container platform you will need to delete this file before using the new Amazon Linux 2023 platform!!!

Complete docker-compose.yml file:
```
version: "3"
services:
  client:
    image: "rallycoding/multi-client"
    mem_limit: 128m
    hostname: client
  server:
    image: "rallycoding/multi-server"
    mem_limit: 128m
    hostname: api
    environment:
      - REDIS_HOST=$REDIS_HOST
      - REDIS_PORT=$REDIS_PORT
      - PGUSER=$PGUSER
      - PGHOST=$PGHOST
      - PGDATABASE=$PGDATABASE
      - PGPASSWORD=$PGPASSWORD
      - PGPORT=$PGPORT
  worker:
    image: "rallycoding/multi-worker"
    mem_limit: 128m
    hostname: worker
    environment:
      - REDIS_HOST=$REDIS_HOST
      - REDIS_PORT=$REDIS_PORT
  nginx:
    image: "rallycoding/multi-nginx"
    mem_limit: 128m
    hostname: nginx
    ports:
      - "80:80"
```
