# Create WordPress Website Using AWS Route53 And Lightsail

### Story :
Neha (Viral's wife) started a small retail business for womens garments of a specific famous brand from home, targetting friends and family initially. 
Started expanding her business using social media like WhatsApp and Instagram and has got decent response since last 1 year and now wants to expand it further using her own website and partnering with Amazon and Flipkart

Viral, on the other had has been learning AWS and wanted to build a small project from his learnings , hence started learning more on **AWS Lightsail, AWS Route53 and WordPress** and helping Neha building her own website --> **nehacouture.com** (Currently designing of this site in under progress)

This blog is mainly to highlight the architecture on how Viral created a Wordpress website using AWS Lightsail , Route53 and Wordpress

### Steps :
#### Creating a Linux-based instance 
1. Select Lightsail from the Search bar and then click on "Create Instance"
2. Change the nearby location under Instance location
3. Select "Linux/ Unix" instance and "WordPress" for blueprint 
 
![image](https://user-images.githubusercontent.com/73116712/211256364-9fdf3a1e-8944-4654-b87f-d4de421b2c0b.png)

4. Reason for selecting Linux instance over Windows instance is primarily the price per month of Windows instance is quite higher comparted to Linux instance and also Linux instance has more blueprints compared to Windows instance
5. Linux instance Price Per month 
![image](https://user-images.githubusercontent.com/73116712/211258298-b5716a83-14b3-4634-914a-326d4edaff05.png)


6. Windows instance Price Per month 
![image](https://user-images.githubusercontent.com/73116712/211256627-c3e9c920-c5dc-4b24-a909-7554fe0513f1.png)

7. Enter the name of the Instance and click on "Create Instance"
8. Following is the Instance created for my Project
![image](https://user-images.githubusercontent.com/73116712/211258741-f1f31a3b-b5fe-4a7c-abc6-7597a5642ea3.png)

![image](https://user-images.githubusercontent.com/73116712/211259086-358237c7-35a2-4f10-84da-615b9001aa80.png)


#### Login to Wordpress from your Amazon Lightsail instance 
1. In your lightsail instance , copy the Public IP / Static IP and and paste it in your browser 
2. In our case, type **3.110.10.19**
3. WordPress main page should get displayed wherein you , however at this stage, we have already started designing the website and should see some changes to the home page 

### Get userid/ pwd for WordPress 
1. On the WordPress homepage , select Login and enter UserId/ pwd
2. UserId is "user"
3. For password, on the Lighsail homepage, under "Connect" select "Connect using via SSH client"
![image](https://user-images.githubusercontent.com/73116712/211260651-5f263626-8ddc-4f4f-8a4b-cd5a55c6fe9a.png)

4. SSH window shall open as below 
![image](https://user-images.githubusercontent.com/73116712/211260944-b4d0e23f-406d-4bfd-9c9f-e520f6588f4d.png)

5. Open the "bitnami_application_password" file using cat command, you will get your password from there. Copy it somewhere safely 
![image](https://user-images.githubusercontent.com/73116712/211261204-4a2ebb9d-c4bb-4448-91cf-15267bd48d45.png)

6. If you want to know both userid and password , "cat bitnami_credentials"
![image](https://user-images.githubusercontent.com/73116712/211261457-8c50fe39-4e21-49aa-82b4-59db076f2014.png)

7. Paste the UserId/ Pwd in the Login Page of WordPress





