IP address: 52.77.224.19

SSH port: 2222

URL: 52.77.224.19

Summary of installed software and configuration changes:
1. Update all currently installed packages using 

`sudo apt-get update`

2. Upgrade all currently installed packages using 

`sudo apt-get upgrade`

3. Change the SSH port from 22 to 2200 by modifying /etc/ssh/sshd_config
4. Allow port 2222/tcp on Amazon Lightsail console.
5. Configure Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
6. Enable UFW.
7. Create a new user account named grader using 

`sudo adduser grader`

8. Give grader the permission to sudo by adding a grader file to /etc/sudoers.d. The grader file is copied from 90-cloud-init-users file.
9. Create an SSH key pair for grader using the ssh-keygen tool.
10. Store the public key for grader at /home/grader/.ssh/authorized_keys
11. Configure the local timezone to UTC.
12. Install apache by running 

`sudo apt-get install apache2`

13. Install mod-wsgi by running 

`sudo aptitude install libapache2-mod-wsgi`

14. Install postgres by running 

`sudo apt install postgresql postgresql-contrib`

15. Create new postgres role called 'catalog'.
16. Create new database called 'catalog'.
17. Add new user called 'catalog'
18. Inside psql shell, alter the database password for the databse user catalog using the command 

`ALTER USER catalog PASSWORD catalog`

19. Add the following line to /etc/postgresql/10/main/pg_hba.conf

local   catalog         catalog                                 

20. Make changes to the application python files to connect to postgres instead of sqlite. Engine is now connected to 'postgres+psycopg2://catalog:catalog@localhost:5432/catalog'

21. Install all the required python packages using pip and apt-get
22. Create catalog.wsgi file as follows:

`
	import sys
	sys.path.append('/var/www/catalog')
	from catalog import app as application
`

23. Configure /etc/apache2/sites-available/000-default.conf as follows:
s
`
	<virtualhost *:80>
	    WSGIDaemonProcess catalog user=ubuntu group=ubuntu threads=5 home=/var/www/catalog/
	    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
	    ErrorLog /var/www/catalog/logs/error.log
	    CustomLog /var/www/catalog/logs/access.log combined
	    <directory /var/www/catalog>
	        WSGIProcessGroup catalog
	        WSGIApplicationGroup %{GLOBAL}
	        WSGIScriptReloading On
	        Order deny,allow
	        Allow from all
	    </directory>
	</virtualhost>
`

24. Run 

`sudo a2enmod wsgi`

25. Move  `app.secret_key = 'super_secret_key'` out of the `if __name__ == '__main__':` block and declare it at global level

26. Clone and setup Item Catalog project (with the wsgi file created earlier) from the Github repository to /var/www/catalog

27. Reload the apache server by running

`sudo /etc/init.d/apache2 reload`

