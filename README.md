# Creating a High Availability Group of EC2 Instances with Auto Scaling


In this project, I will describe how to create a high availability group of EC2 instances using Auto Scaling.

1. Create an EC2 launch template with a minimum of 2 instances and a maximum of 5.

2. The Autoscaling group should span at least 3 subnets.

3. Simulate a failure by terminating 1 instance.

Amazon EC2 instances allow you to gain virtual computing capacity in the AWS cloud. Amazon takes care of the actual hardware and you get to rent the virtual machine, to launch applications.

Auto-scaling a group of EC2 instances ensures that your applications remain available. Auto-scaling does the work of monitoring the applications and figuring out how many instances are needed to accommodate fluctuating loads. This is also helpful in keeping costs down.



### Prerequisites
* An AWS free tier account
* A key pair formatted .pem


### Step 1

First login to your AWS account and navigate to the EC2 dashboard. Under Instances click on Launch Templates. From here click the orange Create Launch Template button.

![image](https://user-images.githubusercontent.com/115881685/218282752-8e8d2b72-2e53-429c-b729-0fe53234a81d.png)



From here you will name your template and provide a brief description. I will check the box for Auto scaling guidance as this will be helpful moving forward!



![image](https://user-images.githubusercontent.com/115881685/218319871-83479124-04e6-44c6-8280-dae59d6dc32b.png)



Move on to Launch Template Contents. We will select an AMI (Amazon Machine Image), this is the image we will use to create a template. I chose Amazon Linux 2 AMI 64-bit which is free tier eligible.



![image](https://user-images.githubusercontent.com/115881685/218282835-16731b41-5b1e-4722-84a8-8af15808efbd.png)


Next select your instance type, depending on how much storage you will need. I went with t2 micro.



![image](https://user-images.githubusercontent.com/115881685/218282890-07d0cc83-1927-4f1c-8075-a187cec0df8a.png)



Then you have the option of creating a key pair or using a key pair you have already created. The key pair is very important because you need it to access your instance from the CLI terminal. Once created you will only have the one opportunity to save the key. I will use a key I have already created.



![image](https://user-images.githubusercontent.com/115881685/218319941-8eee9fb7-f4dd-4232-92a1-3c3a2ebb81e0.png)



Moving on to Network Settings, you will have the option to create a security group or use an existing security group. I will create a security group; name it, write a description and use the default vpc.



![image](https://user-images.githubusercontent.com/115881685/218320013-3fb2a231-ff4f-4d57-8f81-645d1d276a80.png)




We need to create rules for our security group to allow access to the webserver from the internet and to allow us to access the instance by SSH into the CLI terminal. The source type will be set to anywhere so that anyone can access it. The options I selected are shown below:




![image](https://user-images.githubusercontent.com/115881685/218320081-b6fd1497-6569-472c-a28a-ac14a755053c.png)



Next up Storage, I will leave on the default setting.



![image](https://user-images.githubusercontent.com/115881685/218283021-39107b18-ffd8-443b-8b89-1080ed09e4b5.png)



At the bottom of the page click on the Advanced Settings. Here we will leave most of it as the default settings. Scroll down to the User Data text box, we will input a bootstrap script.



![image](https://user-images.githubusercontent.com/115881685/218283059-2836955d-3e85-4c05-8508-1467dfc3dc24.png)




This script will essentially; update our server, install an Apache webserver, and provision a webpage with our script. All in one little text box!




```
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<html><body><h1>Hello World, My EC2-Autoscaling project</h1></body></html>" > /var/www/html/index.html
```



Hit the orange create launch template button when you are done!



### Step 2
Navigate to Auto Scaling and select Auto Scaling groups. Then click create Auto Scaling group.



![image](https://user-images.githubusercontent.com/115881685/218283132-2363186b-5742-4603-b56f-82ce2eefb92c.png)



Name your Auto Scaling Group:



![image](https://user-images.githubusercontent.com/115881685/218320181-efd8fa96-3b48-4ec5-924f-6f2b801c0275.png)



Under Launch Template select the template you just created and click next!



![image](https://user-images.githubusercontent.com/115881685/218320239-5a31c2e9-7809-4df4-87c2-670272b3a9d6.png)



The next screen will show the Network. Keep the default vpc. Under Availability and subnets I will select 4 subnets to provide higher availability.



![image](https://user-images.githubusercontent.com/115881685/218320317-20ab57f1-2e40-402d-96b8-2c942278d1fd.png)





Hit next and hit next again through Configure Advanced Options screen. The screen we will work on now is “Configure group size and scaling policies”. Under group size you can select your desired capacity, minimum and maximum capacity. This will determine the number of instances running depending on CPU load, whether it is high or low.




![image](https://user-images.githubusercontent.com/115881685/218320382-26fd7f2e-6871-4a90-bd46-70566e4fb17e.png)



Hit next, I won’t be adding tags or notifications to this project. You will then be taken to the Review page. Review the details of your Auto Scaling group to ensure everything is correct. Then click Create Auto Scaling Group.



![image](https://user-images.githubusercontent.com/115881685/218283260-3133df24-05d0-4f19-b478-1e694edfadbc.png)


You will be notified it was created successfully!



![image](https://user-images.githubusercontent.com/115881685/218283284-b95568f5-9d38-46dc-a633-d538efba5a71.png)



Now navigate to your EC2 instance page. It should show you have instances running.



![image](https://user-images.githubusercontent.com/115881685/218283302-aa25a4a3-e381-4289-ba73-1bb8d49e3db1.png)



To check if our server and web page are successfully running, just copy the IPV4 address and put it in a browser.



![image](https://user-images.githubusercontent.com/115881685/218283338-3b524b12-8c5d-483c-85b6-e43cc2a88230.png)
![image](https://user-images.githubusercontent.com/115881685/218283352-0fb5ccf5-5fcf-4210-9948-45350c80ca6d.png)







It was successful!




### Step 3
Now to test our Auto-Scaling group we will simulate an instance failure! We will do this by terminating one of our instances. Select an instance, click instance state, and terminate instance.


![image](https://user-images.githubusercontent.com/115881685/218283409-7e5d1830-620b-4d22-9b5e-6cc8d2cea597.png)



Since our minimum instance was set to 2, another instance will appear to take over the failed instance.



![image](https://user-images.githubusercontent.com/115881685/218283430-52e40adc-0e16-4ee8-8c5e-931b8b0466f8.png)



To make sure the new instance is running properly I will again copy the IPV4 address and input it into a browser.



![image](https://user-images.githubusercontent.com/115881685/218283485-def0d2f5-c7fe-400b-83a9-9d59cc1a1613.png)





The new instance worked! This shows that even if an instance fails our application will still be available. Auto scaling is a great service provided by aws and this project really showed me how functional it is!

In order to not be charged I recommend deleting your Auto Scale group. This will also terminate any instances associated with it. Thanks for following along!

















