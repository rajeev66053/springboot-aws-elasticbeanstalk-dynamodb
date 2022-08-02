#springboot-aws-elasticbeanstalk-dynamodb

### AWS General
* In AWS virtual machine is called EC2 instance where we can specify the server and database and scale up.
* EC2 stands for elastic compute cloud which is virtual machine.
* S3 stands for simple storage service.

### Create EC2 instance in AWS
* After created the account go to aws.amazon.com/console
* After login to the aws click on EC2 from the compute section.
* Click on the launch instance
* Choose the amazon linux AMI from free tier  eligible.Clivk on select.
* Choose an instance type free tier eligible.
* Choose instance details.
* Click on add storage.
* Click on Add tags.
* configure security group.
* Choose port range as 8080 on custom TCP type. Choose source as anywhere.
* Review and lauch.
* For the first time we need to generate a key pair.
* Choose create a new key pair and give key pair name. Download key pair and save pem file in some location in local.
* To connect to ec2 instance from local we need to pass key pair file.
* Then click on lauch instance in popup of key pair name.
* Then success of launching instace will be shown then click on the instance name.
* The instance state must be running.
* Add name to instance.
* Click on connect.
* From action we can stop the instance.

### Deploy springboot application in aws using ec2 instance and s3 as storage.

* To deploy the springboot application in aws we need to create the jar file of the application.
* To create a jar file. Go to pom.xml and give tag "finalName" and specify the name.
* Then go to the terminal and run mvn clean install. It will generate jar file.
* In the target folder we will get jar file.
* Download the putty tool in machine. Using putty we can connect to ec2 instance from local machine.
* Open puttygen application.Then click on the load and upload the key pair file downloaded earlier. Then click save private key.
* Using this private key we can connect to ec2 instance from local machine.
* Open the putty and give host name by connecting to the instance of aws there we will find the username and host name.
* Then click on data on putty configuration. And pass username from ec2 instance.
* Then click on ssh on putty configuration. Inside ssh click on auth. Then click on browse and pass key pair private key.
* Then click on open.Then it connect to ec2 instace of aws.
* Go to root directory by typing sudo -i
* Install java version if not exist in ec2 instance.Also the java version in ec2 instance must match with spring boot application java version.
* To install another version of java type   yum install java-1.8.0-openjdk
* If we check java version in ec2 instance it will still show old java version we need to change by typing alternatives --config java.
* Then choose the number which we want.
* Go to s3 in aws amazon console and create the bucket in s3.
* Click on S3 and click on create bucket.
* Give the bucket name.
* Click acknowledge and click create a bucket.
* Click on the newly created bucket.
* Click on upload and drag and drop the jar file of spring boot applicaition. Then click on upload.
* Click on the jar in s3 and make it public.
* Copy object url.
* Go to putty application and type wget and paste the copied url.
* Then it will download the jar in ec2.
* Then run the jar file by typing java -jar jar_name. Then the application will run in ec2 instance.
* Then go to ec2 instance in aws and click on running instance.
* Then copy the ip address from instances of ec2 in aws.
* Go to the browser and paste the ip address and give port 8080 then enter we will see the response from our application.

### Deploy application using elastic beanstalk

* Create spring boot applicaition with packing as war file.
* Run maven clean install to create war file.
* Login to aws console.
* Go to elastic beanstalk and click on create application.
* Give application name.
* Choose platform as tomcat.
* Choose upload your code option. Then choose the war  file.
* Then click on create application.
* It will automatically create ec2 instance and s3 storage.
* Now click on the url with end point we will get the response.

### AWS DyanamoDB -Configure dynamodb.

* First go to aws.amazon.com and login into it.
* Search dyanmoDB.
* Click on create table.
* Give table name and primary key value.Click on create.
* Then click on item tab.
* Go get access key for database go to services.
* Go to IAM in security identity and compliance.
* Create new Group. Click next.
* Choose administrator access and amazonec2fullaccess. Then click next.
* Then after verifying click create group.
* Go to users from left navigation and add user.
* Select aws access type as programmatic access.
* Click next.
* Add user to group by checked the group name and click next tags.
* Review  everything if ok click create user.
* Then we will see access key id and secret access key using which we can connect to aws dynamodb.

* Go to service and click on IAM
* Add some custom policy. Create on create policy.

*The custom policy is:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action":   [ "dynamodb:PutItem" ],
      "Resource": [ "*" ]
    },
    {
      "Effect": "Allow",
      "Action":   [ "sns:Publish" ],
      "Resource": [ "*" ]
    }
  ]
}
```


* click on review policy.
* Give name to policy. Click on create policy.
* Search the policy that we have created and attach that policy to group name that we have created from policy action.

* Create spring application and add dependency for dynamodb

```
 <dependency>
	<groupId>com.amazonaws</groupId>
	<artifactId>aws-java-sdk-dynamodb</artifactId>
	<version>1.11.815</version>
</dependency>
```

### In Spring boot application do following to make it compatible with dynamodb.
* Create model and annotate with @Dynamoocument and for field annotate with @DynamoDBAttribute
* Annotate model with @DynamoDBTable and for primary key with @DynamoDBHashKey and @DynamoDBAutoGeneratedKey.
* We need dynamodb mapper in config. Annotate dyanamodb mappaer method with @Bean.
* We need to set server.port= 5000 in spring boot application to be work on elastic beanstalk because Elastic beanstalk by default will listen on port 5000.
* After creating the spring boot application we need to create the jar file using mvn clean install.
* Then we need to create the aws elastic beanstalk environment.
  
* Go to aws.amazon.com search elastic bean stalk and create application.
* Follow all the steps above to upload project in elastic bean stalk.
* The elastic bean stalk will create ec2 instance and s3 storage internally we not need to worry about this.