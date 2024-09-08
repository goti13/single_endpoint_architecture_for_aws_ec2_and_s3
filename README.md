## Zappy E-Bank's Single Endpoint Architecture for EC2 and S3 Services

# Introduction
  This hands-on project is crafted to deepen an understanding of the cloud using Amazon Web Services (AWS), focusing on leveraging EC2 (Elastic Compute Cloud) and S3 (Simple Storage Service) within a single, streamlined access framework.
  Zappy e-bank is all about redefining how financial services are delivered by harnessing the cloud's scalability and flexibility. This project presents a scenario that mirrors real-life applications, demonstrating how EC2 and S3 can work in tandem to support dynamic financial services.



# The Project's Backbone: EC2 and S3 Integration for Zappy e-bank. For a fintech company, agility and data security are not just operational goals; they are the essence of its competitive edge. 
  Zappy e-bank plans to deploy its application on AWS EC2, with S3 buckets serving as the backbone for storage solutions, including customer data, transaction logs, and analytical data. 
  The integration of EC2 and S3, facilitated by a reverse proxy (More on this much later) setup, aims to provide a seamless experience for managing and accessing diverse resources under a unified access point.


 EC2 and S3: Pillars of Zappy e-bank's Cloud Strategy
 EC2: Hosts the core application, enabling scalable computing capacity to meet growing customer demands.
 S3: Offers secure, scalable object storage for vast amounts of data, ensuring that Zappy e-bank can serve its customers efficiently and reliably.
 * Achieving Unified Access: The Role of a Reverse Proxy

    A reverse proxy will be configured on the EC2 instance, directing requests to the appropriate destination.Either to the EC2-hosted application or the S3 bucket, based on the request path.
   This setup exemplifies a best practice in cloud architecture, providing both operational simplicity and enhanced security.

# Project Goals and Learning Outcomes
By the completion of this project, I will have:

* Mastered the integration of EC2 and S3 to support a fintech application, balancing compute and storage needs.
* Gained practical experience in setting up a reverse proxy on EC2 to facilitate unified access to both EC2 and S3 resources.
* Learned the significance of seamless resource integration in building scalable and secure fintech solutions.
* Developed a deeper understanding of AWS's capability to support fintech innovations through hands-on experience with its core services.

# Project Setup
1. Two Separate Chrome browser profiles
2. AWS Management Console Access: Using "John" the developer's IAM account, log into the AWS Management Console
3. EC2 Instance Setup: Launch an EC2 instance that will host the main application, and associate it with an Elastic IP.
4. S3 Bucket Configuration: Using Mary the Data Analyst's IAM account, Create and configure an 53 bucket for storing application data, ensuring proper permissions and web hosting settings are in place.
5. Reverse Proxy Configuration: Install and configure a web server (e.g., Nginx) on the EC2 instance to act as a reverse proxy, routing requests to either the application or the S3 bucket based on the URL path.

# Setting up the EC2 Instance

* Login in to the aws console as John
* Using your basic knowledge of launching EC2 instances, launch an ubuntu EC2 Instance.
* Assign a Static IP (Elastic IP): Associate an Elastic IP address with your EC2 instance to ensure it retains the same public IP address across reboots.
  Folow to steps below to assign a static ip:
  
1. In the EC2 console navigation, select elastic ip and click on alllocte

   <img width="1012" alt="image" src="https://github.com/user-attachments/assets/91d935b6-bf9c-4406-8af2-9ef25107f1f7">


2. Select the highlighted part in the image below

   <img width="780" alt="image" src="https://github.com/user-attachments/assets/c95b1bbf-4b28-4ac2-8f34-6a5a7e5465bb">

3. Click on Allocate

   <img width="847" alt="image" src="https://github.com/user-attachments/assets/815ab92b-2d31-4c7f-931f-beb9b8bb288e">


4. Select the highlighted parts and click on associate elastic ip



   <img width="863" alt="image" src="https://github.com/user-attachments/assets/a2e16bfe-dea9-42ab-9ee6-e61e539eb29b">

5. Select the resource type, the instance you want to allocate the elastic ip address and the private ip of the elastic ip



   <img width="861" alt="image" src="https://github.com/user-attachments/assets/83ab19ed-753b-4bb1-8556-1178477d7a83">


# Creating S3 Bucket

1. Log in to the s3 console as Mary.




   
3. Create a new bucket and give it a name of your choice
     

   https://github.com/user-attachments/assets/b40a7f32-f450-421c-8864-d679fb6c6464
   
3. Create a new object inside the bucket. You should upload an index.html file containing containing a simple content

*  On your computer, create an index.html file with the content Welcome to Amazon S3

*  Upload the index.html on S3 bucket as shown in the image below;


    https://github.com/user-attachments/assets/a6f4207b-802c-4d52-8576-f058d9cfabbf

# Configuring S3 Bucket for Web Hosting

1 Enable Static Website Hosting: In your S3 bucket settings, enable static website hosting. This makes your bucket content accessible via HTTP.

* Click on your bucket name
  
* Click on the properties tab and scroll down

  <img width="771" alt="image" src="https://github.com/user-attachments/assets/1b3be589-caa8-4344-8025-bc24df8e7963">

* lick on edit as shown in the image below

  <img width="852" alt="image" src="https://github.com/user-attachments/assets/f74365bb-ff02-45c5-8521-c15195d00a4d">

*  Select the parts higlighted in the image below. You can ignore the optional part


   <img width="799" alt="image" src="https://github.com/user-attachments/assets/16d99c16-96b9-4e5c-a04e-f87933d2e231">

*  You have successfully enabled static website hosting for your bucket

   <img width="780" alt="image" src="https://github.com/user-attachments/assets/ec15594a-0df2-4586-a34f-bdf36292c903">
# Configuring a Web Server as a Reverse Proxy
  Now that we have have successfully assigned an elastic ip to your instance, let's install nginx on it. 
  Nginx is a popular open-source web server, reverse proxy server, load balancer, and HTTP cache. It's known for its high performance, stability, simple configuration, and low resource consumption. 
  Originally created to solve the "C10k problem" (handling 10,000 simultaneous connections), Nginx has since gained widespread adoption and is used by many large-scale websites and web applications.
  
  1. On your EC2 instance, Install Nginx web server

     ```

        sudo apt update -y && sudo apt install nginx -y
        

     ```

  2. Configure the Web Server: Configure the web server to serve your s3 app directly and to forward requests to your S3 bucket. Follow the steps below to configure your nginx
  
  * Create and edit a new file named mybucket.


    ```

        sudo vi /etc/nginx/sites-available/mybucket


    ```
  * Paste the configuration code snippet below in the file and replace the highlighted part with your S3 link

    ```

            server {
          listen 80;
          server_name 100.26.55.173;  # Replace with your domain name or server IP address
      
          location / {
              proxy_pass https://your-bucket-name.s3.amazonaws.com; # Replace with the link you generated after you enabled  static web hosting for your bucket
              proxy_set_header Host your-bucket-name.s3.amazonaws.com;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
          }
      }
                 

      
    ```


    Save and close the fle.
  
    Note: Make sure to replace with the link above with the link you generated after you enabled static web hosting for your bucket


    <img width="940" alt="image" src="https://github.com/user-attachments/assets/1d88e123-6aac-4e67-be8d-cf6e44e72698">


3. Note that by default, nginx keeps its configuration files in _**/etc/nginx/sites-available**_ but it uses the file in the _**/etc/nginx/sites-enabled/**_.
   We need to create a symbolic link between _**sites-available**_ and _**sites-enabled**_ so that the new configuration file we created for our static website
   in _**sites-available**_ folder can be used by nginx in _**sites-enabled**_ folder.
   Run the commands below to symbolic link between _**sites-available/**_ mybucket configuration file to _**site-enabled**_ directory.

   ```

      sudo ln -s /etc/nginx/sites-available/mybucket /etc/nginx/sites-enabled


   ```

  Remove the default configuration file in site-enabled using the command below:


  ```

    sudo rm /etc/nginx/sites-enabled/default


  ```


  Reload nginx using the command below:


  ```

    sudo systemctl reload nginx


  ```

4. Make your index.html file public

* Navigate to the index.html file, click on Actions and then click on Make public using ACL

  <img width="946" alt="image" src="https://github.com/user-attachments/assets/da438451-39bb-4386-ae0c-52f58dee7951">

* Select make public


  <img width="859" alt="image" src="https://github.com/user-attachments/assets/c7e0730e-a43e-4850-ba5f-77861ff72176">

# Testing and Validation

  After setting up your EC2 instance, configuring the S3 bucket, and establishing the reverse proxy, the next critical phase is testing and validation. 
  
  This step is essential to ensure that our setup works as intended and that both the EC2-hosted application and the S3 bucket content are accessible through the unified access point you've created.


# _Testing steps_

1. Direct application access

   * Access the application hosted on the EC2 instance directly through the Elastic IP to confirm it's running as expected.
    
       
   * Verify that the application responds to requests and functions correctly without any reverse proxy interference.
  

   
     <img width="588" alt="image" src="https://github.com/user-attachments/assets/c1fabdd5-fc8a-4ec1-a973-0c5548ec18f6">


2. S3 Bucket Access via Reverse Proxy:
   * Attempt to access the S3 bucket content by navigating to **<IP-address>/bucket-name_**.
     
   * Check that the reverse proxy correctly routes the request to the S3 bucket and that the content is served without any issues.
  
     
  
   

    
   
