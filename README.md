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

Add 2200 to the available port in lightsail (in networking)
</ul>
