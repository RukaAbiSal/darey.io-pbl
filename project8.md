# Load Balancer Solution With Apache.
- In project 7, we had 3 Web Servers with each having its own public ip address. 
- A client will have to access the site using different URLs , which is not a nice user experience. To hide all this complexity and have a single point of access with a single public IP address/name, a Load Balancer can be used.
- A Load Balancer (LB) distributes client request among underlying Web Servers and make sure that the load is distributed in an optimal way.   
![p2](https://user-images.githubusercontent.com/50557587/142720551-00010d9f-15f2-4c11-8e0d-59c814f4cc42.PNG)

- The task of this project is to enhance our Tooling website solution by adding a Load Balancer to distribute traffic between Web Servers and allow users to access the website using a single URL.
- The project will be using two (2) Web Servers, one (1) MYSQL DB Server and one (1) NFS Server used in project 7.
- The set up will be like the screenshot below:  
![p3](https://user-images.githubusercontent.com/50557587/142720584-de0b61be-8ba7-4ee2-9b47-c81601a9d3eb.PNG)

## Configure Apache As A Load Balancer
- Create  an Ubuntu Server 22.04 EC2 instance for the Load Balancer.
- Open TCP port 80 by creating an Inbound Rule in the Security Group.
- Install Apache Load Balancer and configure it to point traffic coming to LB to both Web Servers.  
![p6](https://user-images.githubusercontent.com/50557587/142721037-6d32fae0-9ec6-497d-82b8-28fbc64545f4.PNG)

- Ensure Apache2 is up and running `sudo systemctl status apache2`.
- Configure load balancing by entering the Web Server Private IP address `sudo vi /etc/apache2/sites-available/000-default.conf`.         
![p7](https://user-images.githubusercontent.com/50557587/142721133-e2dd2b97-c591-43c2-83db-fc90269f24cb.PNG)       
![p4](https://user-images.githubusercontent.com/50557587/142721149-b05dde6c-0df9-4d2c-95db-45bae6e80afc.PNG)

- Restart apache2 and confirm status `sudo systemctl restart apache2`, `sudo systemctl status apache2`.

- Verify that the configuration works `http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`.   
<img width="742" alt="final" src="https://user-images.githubusercontent.com/104162178/171614173-edc5a5f3-414d-4d60-81e6-02c1f6d298bb.PNG">


- Enter the following command `sudo tail -f /var/log/httpd/access_log`
<img width="949" alt="lastone" src="https://user-images.githubusercontent.com/104162178/171614147-51138a9b-1683-4c83-a30a-af0f9a1268ab.PNG">

