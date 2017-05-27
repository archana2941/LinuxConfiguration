# LinuxConfiguration
<h3>Perform following steps</h3>
<ul>
<li><h3>To update all packages on the server run:</h3></li>
<ul>
  <li>sudo apt-get update</li>
  <li> sudo apt-get upgrade</li>
</ul>
<li>
Create a new user account named "grader"
</li>
<ul><li>
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


<li>
sudo nano /etc/ssh/sshd_config
<br/>
Change the SSH port from 22 to 2200
<br/>
Change PermitRootLogin from prohibited-password to no
<br/>
sudo service ssh restart
<br/>
Go to networking tab (aws lightsail) and  add 2200 to the available port in lightsail.
</li>
<li>
<li>Download the key-pair (.pem file) for your instance from lightsail.</li>
<li>Grant it the specific permissions by running command:
chmod 600 key.pem</li>
<li>
To sort out the login issue:
sudo nano /etc/ssh/sshd_config
set Password Authentication to "yes" 
sudo service ssh restart
</li>

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
<li>
ssh grader@54.210.157.61 -p 2200 -i key-pair.pem
</li>
</ul>
