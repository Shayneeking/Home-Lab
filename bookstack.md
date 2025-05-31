Manual Installation
Ensure the above requirements are met before installing.

This project currently uses the release branch of the BookStack GitHub repository as a stable channel for providing updates. The installation is currently somewhat complicated and will be made simpler in future releases. Some PHP or Laravel experience will make this easier.

Clone the release branch of the BookStack GitHub repository into a folder.

install git
sudo apt-get install git

install composer
sudo apt-get install composer

install requirements
apt install -y unzip apache2 curl mysql-server-8.0 php8.3 \
  php8.3-fpm php8.3-curl php8.3-mbstring php8.3-ldap php8.3-xml php8.3-zip php8.3-gd php8.3-mysql
cd /var/opt
git clone https://github.com/BookStackApp/BookStack.git --branch release --single-branch bookstack
cd bookstack
chown sek3b -R .
cd into the application folder and run composer install --no-dev.
Copy the .env.example file to .env and fill with your own database and mail details.
Ensure the storage, bootstrap/cache & public/uploads folders are writable by the web server (More information here).
In the application root, Run php artisan key:generate to generate a unique application key.
If not using Apache or if .htaccess files are disabled you will have to create some URL rewrite rules as shown below.
Set the web root on your server to point to the BookStack public folder. This is done with the root setting on Nginx or the DocumentRoot setting on Apache.
Run php artisan migrate to update the database.
Done! You can now login using the default admin details admin@admin.com with a password of password. You should change these details immediately after logging in for the first time.
