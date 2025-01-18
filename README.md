# WordPress-Setup
This lab uses the Ubuntu 20.04 virtual machine (VM) as an OVA file cscelab.ova on Canvas.  


# PART 1 BASIC SERVER SETUP  
1.	All Linux commands definition can be found at http://ss64.com/bash/ (I will quiz you on the commands used in this lab for example what does apt-get install do) 
2.	Use the Ubuntu 20.04 for this lab (username: sec-lab / password: 
untccdc).  
Ubuntu 20.04.5 LTS (Focal Fossa) 
UbuntaDesktop 
 
  http://cse.unt.edu/downloads/vm/UbuntuDesktopLatest/ 
 
1.	Learn how to check the Operating system version your system is running on  
2. uname –r 
3.	Change the root password type 
4.	passwd 
5.	Enter your new password 

# PART 2
INSTALLING APACHE
1.	To install apache, open terminal and type in: 
 
    sudo apt-get install apache2 
	 
2.	find your ipaddress by typing: (note it down) 10.0.2.15 
 
    installing ifconfig  
     
    sudo apt-get install net-tools 
     
    ifconfig (note down your ipaddress) 
 
3.	To verify if the HTTP services are operational, you can use a web browser on the host machine. However, it's essential to ensure that the host can access the server running in the VM. To do this, follow these steps: 
  - Power off the VM.
  - In the network settings, change the network adapter to "Host-Only Adapter."
  - Restart the VM and ensure the web server is running again. Now, the host should be able to access the server successfully.  

      sudo service apache2 restart ;  
 
a.	Open a web-browser  
b.	Type http://<ipaddress>  (the <ipaddress>  of host-client  
		Type ifconfig ( my ipaddress is 192.168.56.101) 
If you can see the apache welcome page, your webserver is up and running. 
 
**Question-1**: Attach the screen shot of the apache welcome page? 
  ![Webgoat Apache 2](https://github.com/user-attachments/assets/cf464b0f-c936-48fa-8b49-57e01c605e35)

CHANGE THE NETWORK SETTINGS BACK TO NAT, 
If your VM does not have an internet connection to download the necessary updates, please power off your VM. Then, change the network setting to NAT and power it back on.

# PART 6 INSTALL AND SECURE PHPMYADMIN 
       	To install phpmyadmin 
        sudo apt-get install phpmyadmin 
2.	During the installation, phpMyAdmin will walk you through a basic configuration. Once the process starts up, follow these steps: 
        i Select Apache2 for the server 
        ii.	Choose YES when asked about whether to Configure the database for phpmyadmin with dbconfig-common 
        iii.	Enter your MySQL password when prompted 
        iv.	Enter the password that you want to use to log into phpmyadmin 
3.	After the installation has completed, add phpmyadmin to the apache configuration. 
        i.	sudo nano /etc/apache2/apache2.conf 
4.	Add the phpmyadmin config to the file. 
        i.	Include /etc/phpmyadmin/apache.conf 
5.	Restart apache: 
        i. sudo service apache2 restart 
You can then access phpmyadmin by going to browser youripaddress/phpmyadmin.  (e.g., : 10.0.2.15/phpmyadmin) Ipaddress can be get by typing ifconfig 
Note : If opening a browser gives error “Your Firefox profile cannot be loaded. It may be missing or inaccessible” then execute this command to give permissions for created user. “sudo chown -R $USER:$USER ~/.cache/”

**Question-2**: Attach the screen shot of the browser after the above step. 

![Webgoat phpMyAdmin](https://github.com/user-attachments/assets/c6d88f2d-c985-43de-88fe-5732856345ca)

# SECURITY

Older versions of phpMyAdmin are known to have significant security vulnerabilities that could allow remote users to gain root access to the underlying virtual private server. To mitigate these risks, it is advisable to secure the entire directory using Apache's built-in user/password authentication. This measure effectively blocks unauthorized remote users from exploiting outdated versions of phpMyAdmin.

1. **Set up the .htaccess File**  
   Open the Apache configuration file for phpMyAdmin:  
   ```bash
   sudo nano /etc/phpmyadmin/apache.conf  
   ```

2. Under the directory section, add the line `AllowOverride All` under `Directory Index`, making the section look like this:  
   ```apache
   <Directory /usr/share/phpmyadmin> 
       Options FollowSymLinks 
       DirectoryIndex index.php 
       AllowOverride All 
   </Directory>
   ```

3. **Configure the .htaccess File**  
   With the .htaccess file allowed, we can proceed to set up a native user whose login will be required to access the phpMyAdmin login page.  

   Start by creating the .htaccess file in the phpMyAdmin directory:  
   ```bash
   sudo nano /usr/share/phpmyadmin/.htaccess 
   ```

4. Set up user authorization within the .htaccess file. Copy and paste the following text:  
   ```apache
   AuthType Basic 
   AuthName "Restricted Files" 
   AuthUserFile /etc/apache2/.phpmyadmin.htpasswd 
   Require valid-user 
   ```

   Here is a brief explanation of each line:
   - **AuthType**: Specifies the authentication method used for password verification. The passwords are validated through HTTP, and the keyword Basic must remain unchanged.
   - **AuthName**: This is the text that appears in the password prompt. You can customize this message as needed.
   - **AuthUserFile**: This line indicates the server path to the password file, which we will create in the subsequent step.
   - **Require valid-user**: This directive informs the .htaccess file that only users listed in the password file are permitted to access the phpMyAdmin login screen.

5. To create the htpasswd file, generate valid user credentials. Use the htpasswd command to create the file. Ensure that you store this file in a directory that is not accessible via a web browser. While you can choose any name for the password file, it is standard practice to name it `.htpasswd`.  
   ```bash
   sudo htpasswd -c /etc/apache2/.phpmyadmin.htpasswd username 
   ```

6. A prompt will ask you to provide and confirm your password. Once the username and password pair are saved, you will see that the password is encrypted in the file.  

7. Finish up by restarting Apache:  
   ```bash
   sudo service apache2 restart 
   ```

Try accessing your site at `youripaddress/phpmyadmin`.  
The default username for phpMyAdmin will be “phpmyadmin”, and the password will be the one you set.  

**Question-3**: Attach a screenshot of the prompted window.   


![Webgoat Admin](https://github.com/user-attachments/assets/130cbeac-7264-4cd3-80a1-0eab95eeb9a9)


**Question-4**: Attach the screen shot of the after logon to the prompted window. 

![Webgoat admin setup](https://github.com/user-attachments/assets/99de0957-a9e0-4b8b-b2cb-9068b076466f)

# PART 7 INSTALL WORDPRESS 

1. Download the latest version of WordPress:
   ```bash
   sudo wget http://wordpress.org/latest.tar.gz
   ```
2. Unzip the downloaded file:
   ```bash
   sudo tar -xzf latest.tar.gz
   ```
   (After unzipping, the WordPress files will be in a directory called `wordpress` in your current directory.)
3. Create the WordPress database and user:
   ```bash
   sudo mysql -u root -p
   ```
4. Log in using your MySQL root password. Then, create a WordPress database, a user for that database, and assign a password to that user. Remember that all MySQL commands must end with a semicolon.
5. First, create the database (you can name it `wordpress`, or choose any name you prefer, like `superman`):
   ```sql
   CREATE DATABASE wordpress;
   ```
6. Then we need to create the new user. You can replace the database name and password with whatever you prefer:
   ```sql
   CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
   ```
7. Set the password for your new user (if not set in the previous step):
   ```sql
   SET PASSWORD FOR 'wordpressuser'@'localhost' = 'password';
   ```
8. Finish up by granting all privileges to the new user. Without this command, the WordPress installer will not be able to start up:
   ```sql
   GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
   ```
9. Then refresh MySQL:
   ```sql
   FLUSH PRIVILEGES;
   ```
10. Exit MySQL by typing `exit` or pressing `Ctrl+D`.
    
Setup up WordPress 

12. The first step is to copy the sample WordPress configuration file, located in the WordPress directory, into a new file which we will edit, creating a new usable WordPress config:
   ```bash
   cp ~/wordpress/wp-config-sample.php ~/wordpress/wp-config.php
   ```
12. Then open the WordPress config:
   ```bash
   nano ~/wordpress/wp-config.php
   ```
 
Find the section that contains the fields below and substitute in the correct name for your database, username, and password: (note you will input what you inputted in step 5, 6, etc.). If you do not configure it properly, things will not work.
   ```php
   // ** MySQL settings - You can get this info from your web host ** //
   /** The name of the database for WordPress */
   define('DB_NAME', 'wordpress');
   /** MySQL database username */
   define('DB_USER', 'wordpressuser');
   /** MySQL database password */
   define('DB_PASSWORD', 'password');
   ```
13. Save and exit.

COPY THE FILES 

14. We are nearing the completion of uploading WordPress to the virtual private server. The last step is to transfer the unzipped WordPress files to the website's root directory.
    i. sudo rsync -avP ~/wordpress/ /var/www/html/
15. Next, we need to set the appropriate permissions for the installation. 
    First, navigate to the web directory:
    i. cd /var/www/
16. Assign ownership of the directory to the Apache user. 
    (The username is your Linux username – in our case, it is sec-lab)
    i. sudo chown sec-lab:www-data /var/www/ 
    ii. sudo chmod g+w /var/www -R
17. At this point, WordPress provides a straightforward installation form online.

However, the form requires a specific PHP module to function. If it is not already installed on your server, install the PHP GD module:
      sudo apt-get install php7.2-gd

To access the installation page, append /wp-admin/install.php to your site's domain or IP address (e.g., example.com/wp-admin/install.php) and complete the brief online form.

**Question-5**: Please attach a screenshot of the browser after accessing the site.

![Webgoat AccessingtheSite](https://github.com/user-attachments/assets/3aca2b88-618c-46f2-9053-7fd485d603fd)

# PART 8 CREATING A SSL CERTIFICATE 
 An SSL certificate serves to encrypt data transmitted between a website and its users, ensuring a secure connection. It also provides identification information about the server to visitors. SSL certificates can be issued by Certificate Authorities, which validate the server's identity, whereas self-signed certificates lack third-party verification.
 
3. Activate the SSL module 
    i.  sudo a2enmod ssl 
4. Restart Apache services 
    i. sudo systemctl restart apache2 
5. Create a directory to store the server key and certificate 
    i. sudo mkdir -p /etc/apache2/ssl 
6. Create a self-signed SSL Certificate 
   You can specify the validity period of the certificate by changing the number of days (currently set to 365). This means the certificate will expire after one year. 
    i. sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt 

   This command creates both the self-signed SSL certificate and the server key, placing them in the specified directory. 

7. The command will prompt you to fill in a list of fields in the terminal. 
 
The most crucial line is "Common Name." Please input your official 
domain name here, or if you do not have one yet, use your site's IP address. 
You will soon be prompted to provide information that will be included in your certificate request. 
The information you are about to enter is referred to as a Distinguished Name (DN). 
There are several fields available, and you may leave some of them blank. 
For certain fields, a default value will be provided. If you enter '.', the field will remain empty. 
----- 
8. Now that we have all the required components for the finished certificate, the next step is to configure the virtual hosts to display the new certificate.
   i. Open the configuration file with: 
   ```bash
   sudo nano /etc/apache2/sites-available/default-ssl.conf
   ```
9. Within the section that begins with `<VirtualHost _default_:443>`, make the following changes:

   Add a line with your server's IP address right below the Server Admin email:
   ```apache
   ServerName your_ip_address:443
   ```
   // Replace `your_ip_address` with your actual IP address, e.g., `192.168.56.101` (for NAT connections, it could be `10.0.2.15`).

   • Locate the following three lines in the text file and ensure they match the configurations below:
   ```apache
   SSLEngine on
   SSLCertificateFile /etc/apache2/ssl/apache.crt
   SSLCertificateKeyFile /etc/apache2/ssl/apache.key
   ```
   Save and exit the file.

   Before the website on port 443 can be activated, we need to enable that Virtual Host:
   i. Run the command:
   ```bash
   sudo a2ensite default-ssl
   ```

   Restart Apache with:
   ```bash
   sudo service apache2 reload
   ```

In your browser, navigate to `https://your_address`, and you will be able to see the new certificate. 
Click on "View Certificate".

![WebgoatView](https://github.com/user-attachments/assets/882004c0-c764-4c26-962c-7c115ed57b4b)

**Question-6**: Attach the screen shot of the browser. Click on advanced and view certificate before taking screenshot like above. 

![Webgoat Log](https://github.com/user-attachments/assets/32f5b4b5-b054-43c7-bc57-4fc8530a1059)
