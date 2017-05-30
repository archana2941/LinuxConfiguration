# LinuxConfiguration
#Project Overview:
A baseline installation of a Linux distribution on a virtual machine and prepare it to host web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.	
<ul>
<li>Public IP: 52.66.161.124</li>
<li>SSH Port: 2200</li>
<li>Full URL: http://ec2-52-66-161-124.ap-south-1.compute.amazonaws.com</li>
<li>
To update all packages on the server run:
</li>
<li>
<ul>
sudo apt-get update
sudo apt-get upgrade
</ul>
</li>
<li>
Create a new user account named "grader"
Set it's password. example I set it to zxcvbnm
</li>
<li>
<ul>
<li>
sudo adduser grader</li>
<li>
Install finger: sudo apt-get install finger </li>
<li> 
To obtain info about grader :
finger grader
</li>
<li>
Give grader access to run sudo commands:
<br/>
usermod -aG sudo grader</li>
</ul>
</li>


<li>
Change the SSH port from 22 to 2200 and other SSH configuration 
<ul>
<li>
nano /etc/ssh/sshd_config change port 22 to port 2200
</li>
<li>
In the above file change PermitRootLogin without-password to PermitRootLogin no (to disallow root login).
</li>
<li>
Change PasswordAuthentication from no to yes.
</li>
<li>Save  the file.</li>
<li>sudo service ssh restart (restart ssh service)</li>
<li>Go to networking tab (aws lightsail) and  add 2200 to the available port in lightsail.</li>
	

<li>Download the key-pair (.pem file) for your instance from lightsail.</li>
<li>Grant it the specific permissions by running command:
    chmod 600 ItemCatalog.pem</li>
</ul>

</li>
<li>
Configure the Uncomplicated Firewall (UFW)
<ul>
<li>sudo ufw status</li>
<li>sudo ufw default deny incoming</li>
<li>sudo ufw default allow outgoing</li>
<li>sudo ufw allow 2200/tcp</li>
<li>sudo ufw allow 80/tcp</li>
<li>sudo ufw allow 123/udp</li>
<li>sudo ufw enable</li>
</ul>
</li>

<li>
Create ssh keys
<ul>
<li>
On your local machine generate SSH key pair with: ssh-keygen
</li>
<li>save keygen file in your ssh directory: example /Users/username/.ssh/ItemCatalog</li>
<li>If  you add password you will be prompted to type it  every time you login </li>
<li>So now login to grader account by command:<br>ssh grader@Your-Public-IP-Address -p 2200 -i ItemCatalog.pem </li>
<li>
Make ssh directory: mkdir .ssh
</li>
<li>Read the content of your public key: cat .ssh/catalog.pub </li>
<li>In .ssh directory make a file to store your key:touch .ssh/authorized_keys and paste content you read in previous step </li>
<li>Save your file</li>
<li>Now we need to set permissions:<br>
<ul>
<li>chmod 700 .ssh</li>
<li>chmod 644 .ssh/authorized_keys</li>
<li>nano /etc/ssh/sshd_config and set PasswordAuthentication from yes back to no. Save  the file </li>
</ul>
<li>Now you can login by:<br/>
ssh grader@your-Public-IP-Address* -p 2200 -i ~/.ssh/catalog
</li>
</li>
</ul>	

<li>
Installing Apache
<ul>
<li>Install apache by  running: <br/>
			sudo apt-get install apache2
</li>
<li>Go to your public IP address () in browser and check  if it works.. It should display "Hello World" by default</li>
<li>Install mod_wsgi: sudo apt-get install libapache2-mod-wsgi</li>
<li>Configure Apache to handle requests using the WSGI module:
<ul>
<li>sudo nano /etc/apache2/sites-enabled/000-default.conf</li>
<li>add WSGIScriptAlias / /var/www/html/catalog.wsgi before </VirtualHost> closing line</li>
<li>Save file.</li>
</ul>

</li>
<li>Restart apache: sudo apache2ctl restart</li>
</ul>

</li>

<li>
Install git: sudo apt-get install git 

</li>
<li>Install python-dev package: sudo apt-get install python-dev </li>
<li>To Verify wsgi is enabled: sudo a2enmod wsgi</li>

<li>To create flask app (Deployment)
<ul>
<li>cd /var/www</li>
<li>sudo mkdir catalog</li>
<li>Change the owner of catalog directory: sudo chown -R grader:grader /var/www/catalog</li>
<li>cd catalog</li>
</ul>

<ul>
<li>Clone your github repository for  item-catalog project:<br>
sudo git clone https://github.com/YOUR-FILE-PATH catalog
</li>
<li>Make .git directory inaccessible via browser: 
<ul>
<li>cd /var/www/catalog/</li>
<li>sudo nano .htaccess</li>
<li>Paste in the file RedirectMatch 404 /\.git and save  file</li>
</ul>

</li>

<li>
install flask and other dependencies:
<ul>
<li>sudo apt-get install python-pip</li>
<li>pip install Flask</li>
<li>pip install httplib2</li>
<li>pip install requests</li>
<li>sudo pip install --upgrade oauth2client</li>
<li>sudo pip install sqlalchemy</li>
<li>pip install Flask-SQLAlchemy</li>
<li>sudo pip install python-psycopg2</li>
</ul>
</li>
<li>
Make changes in config file:<br> sudo nano /etc/apache2/sites-available/000-default.conf and paste the following:<br/>
			
			<VirtualHost *:80>
  				ServerName www.xyz.com
				 ServerAdmin xyz@localhost
  				WSGIScriptAlias / /var/www/catalog/catalog.wsgi
  				<Directory /var/www/catalog/>
      					Order allow,deny
      					Allow from all
  				</Directory>
  				Alias /static /var/www/catalog/static
  				<Directory /var/www/catalog/static/>
      					Order allow,deny
      					Allow from all
  				</Directory>
				 ErrorLog ${APACHE_LOG_DIR}/error.log
				 LogLevel warn
				 CustomLog ${APACHE_LOG_DIR}/access.log combined
			</VirtualHost>
</li>
<li>
Save above file.
</li>

<li>cd /var/www/catalog</li>
<li>Create wsgi file and save it:<br>
sudo nano catalog.wsgi and  paste follwing:
      #!/usr/bin/python
      import sys
      import logging
      logging.basicConfig(stream=sys.stderr)
      sys.path.insert(0,"/var/www/catalog/")
      from project import app as application
      
  			
</li>
<li>sudo service apache2 restart

</li>



</ul>

<li>
Install and configure PostgreSQL:
<ul>
<li>Install postgres: sudo apt-get install postgresql</li>
<li>config database_setup.py : sudo nano database_setup.py</li>
<li>python engine = create_engine('postgresql://catalog:'Your-pswd'@localhost/catalog')</li>
<li>repeat for project.py (your main app)</li>
<li>Add a user: sudo adduser catalog</li>
<li>Login to posgres: sudo su - postgres</li>
<li>psql</li>
<li>CREATE USER catalog WITH PASSWORD 'sillypassword';</li>
<li>Create  a new database : CREATE DATABASE catalog WITH OWNER catalog;</li>
<li>Connect to database: \c catalog</li>
<li>REVOKE ALL ON SCHEMA public FROM public;</li>
<li>GRANT ALL ON SCHEMA public TO catalog;</li>
<li>Quit posgres by typing \q</li>
<li>exit</li>
<li>setup your database: python database_setup.py</li>
</ul>

</li>
<li>sudo service apache2 restart</li>
<li>Add the public IP address back into the Google Developer console and Facebook developer console</li>
</li>
<li>References for this project:<a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps">Digital Ocean</a> </li>

</ul>
