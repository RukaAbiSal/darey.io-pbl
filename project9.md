# Continous Integration Pipeline For Tooling Website.
- Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy.
- The task of the project is to start automating part of our routine tasks with free and open source automation server  - JENKINS.
- In the project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in Github https://github.com/RukaAbiSal/tooling will be automatically be updated to the Tooling website.
- The updated architecture will look like the screenshot below upon completion of this project.  
![p9](https://user-images.githubusercontent.com/50557587/142725468-bdc96c8a-9602-4285-ba6d-bef6090dfd63.PNG)

- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins".
- Install JDK `sudo apt update` `sudo apt install default-jdk-headless`.
- Install Jenkins 
``` 
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```
- Confirm Jenkins is up and running `sudo systemctl status jenkins`.
- <img width="859" alt="Jenkins running" src="https://user-images.githubusercontent.com/104162178/172587927-2f1cc1f8-4736-4a83-8c6c-44b971151c15.PNG">
- 
- Create a new inbound rule in the EC2 Security Group to TCP port 8080    
![2](https://user-images.githubusercontent.com/50557587/142726600-0ad35ae6-802a-4cb9-87a7-fa4fd1d97f28.PNG)
 
- Perform initial jenkins set up from your browsers `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`.
- You will prompted to provide a default admin password. Retrieve from your server `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.   
- On the next page it shows after entering the admin password, choose suggested plugins.    
![j1](https://user-images.githubusercontent.com/50557587/143429676-a174a6bf-ae5f-4df0-a614-2b7332bcbbb0.PNG)

- Enable webhooks in your Github repository.     
<img width="813" alt="Webhook" src="https://user-images.githubusercontent.com/104162178/172590680-b7c8e36f-eca1-458b-a250-c9003ac8a237.PNG">

-  Go to Jenkins web console, click “New Item” and create a “Freestyle project”.
-  To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself.
-  In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository and click save.  

- Run the build. For now we can only do it manually. Click “Build Now” button, if you have configured everything correctly, the build will be successfull and you will see it under the first build:   
<img width="475" alt="Build1" src="https://user-images.githubusercontent.com/104162178/172591334-5c49a525-0f02-4665-bea6-040a4fa55f3c.PNG">

- We can open the build and check in “Console Output” if it has run successfully. As shown below, the build ran successfully:   
<img width="533" alt="Buildsuccess" src="https://user-images.githubusercontent.com/104162178/172591680-63b90566-0d88-40a7-9c04-6ce35b9e4958.PNG">

- But this build does not produce anything and it runs only when we trigger it manually and we would make some adjustments to fix this.
- Click “Configure” your job/project and add two configurations. 
- Configure triggering the job from GitHub webhook.  
![j18](https://user-images.githubusercontent.com/50557587/143797080-f08cc846-cd20-4963-a0bf-c433eb66d027.PNG)
 
-  Configure “Post-build Actions” to archive all the files - files resulted from a build are called “artifacts”.   
![j19](https://user-images.githubusercontent.com/50557587/143797190-0f582ff8-6062-4ffa-b021-59dc7ab3573a.PNG)

- Go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch. We can see that a new build has been launched automatically (by webhook) and you can see its results - artifacts, saved on Jenkins server. 
<img width="420" alt="Build2" src="https://user-images.githubusercontent.com/104162178/172594131-6e0cde6e-60d8-48c4-ae89-0c304bb1ace0.PNG">

- By default, the artifacts are stored on Jenkins server locally `/var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`.

## Configure Jenkins to copy files to NFS server via SSH
- The artifacts are saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.
- Install "Publish Over SSH" plugin" from the Manage Jenkins and select Manage Plugins under System Configuration.  
![j20](https://user-images.githubusercontent.com/50557587/143797871-63312a5e-aabd-41b4-b9b2-81a3348073d1.PNG)

- Configure the job/project to copy artifacts over to NFS server. On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.
- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server. 
<img width="667" alt="Configuresystem" src="https://user-images.githubusercontent.com/104162178/172592653-9ae16338-e39d-4911-bbd7-00bb1aace800.PNG">

- Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”.   
![j22](https://user-images.githubusercontent.com/50557587/143798513-cdb6e841-ee2b-4db2-8292-059beb78200d.PNG)

- Configure it to send all files produced by the build into our previouslys define remote directory. In our case we want to copy all files and directories - so we use "**".  
![j23](https://user-images.githubusercontent.com/50557587/143798579-fc3fe6bf-c2d8-41e5-834f-e0eda940d209.PNG)

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository. Webhook will trigger a new job and in the "Console Output" of the job you will find something like this
<img width="744" alt="success" src="https://user-images.githubusercontent.com/104162178/172595365-e48925b5-7f44-445d-8cb4-fd586e7e1bce.PNG">

