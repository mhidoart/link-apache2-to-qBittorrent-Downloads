# link-apache2-to-qBittorrent-Downloads
This repo has a purpose To link Apache to qBittorrent's download folder with authentication, you'll need to set up an Apache web server to serve the contents of the qBittorrent download directory and protect it with HTTP Basic Authentication.

Here’s a step-by-step guide to accomplish this:

Step 1: Install Apache and Enable Necessary Modules
Install Apache if it's not already installed:

bash
Copier le code
sudo apt update
sudo apt install apache2
Enable the auth_basic and autoindex modules for authentication and directory listing:

bash
Copier le code
sudo a2enmod auth_basic
sudo a2enmod autoindex
sudo systemctl restart apache2
Step 2: Create a Virtual Host Configuration for the qBittorrent Download Folder
Create a new virtual host configuration file (e.g., qbittorrent.conf):

bash
Copier le code
sudo nano /etc/apache2/sites-available/qbittorrent.conf
Add the following configuration:

apache
Copier le code
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/qbittorrent

    <Directory /var/www/qbittorrent>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
        
        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/qbittorrent_error.log
    CustomLog ${APACHE_LOG_DIR}/qbittorrent_access.log combined
</VirtualHost>
Replace /var/www/qbittorrent with the path to the qBittorrent download folder (e.g., /home/user/Downloads).

Create a symbolic link to link the qBittorrent download folder to the Apache web directory:

bash
Copier le code
sudo ln -s /path/to/qbittorrent/download/folder /var/www/qbittorrent
Enable the new site configuration:

bash
Copier le code
sudo a2ensite qbittorrent.conf
sudo systemctl reload apache2
Step 3: Set Up Authentication
Create a .htpasswd file to store user credentials:

bash
Copier le code
sudo htpasswd -c /etc/apache2/.htpasswd your_username
You will be prompted to enter a password. Replace your_username with your desired username.

Set appropriate permissions on the .htpasswd file:

bash
Copier le code
sudo chown www-data:www-data /etc/apache2/.htpasswd
sudo chmod 640 /etc/apache2/.htpasswd
Step 4: Restart Apache
Restart Apache to apply all changes:

bash
Copier le code
sudo systemctl restart apache2
Step 5: Test the Configuration
Open a web browser and navigate to http://your-server-ip/. You should see a directory listing of the qBittorrent download folder.

Authentication Prompt: You will be prompted for a username and password. Enter the credentials you set up in Step 3.

Additional Security Considerations
Use HTTPS: To secure the connection, you should enable HTTPS by installing an SSL certificate (e.g., Let's Encrypt). This will protect your credentials from being transmitted in plain text.
Restrict Access: Consider restricting access to specific IP addresses or networks by adjusting the <Directory> directive.
