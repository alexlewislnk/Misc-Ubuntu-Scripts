# mysql-ram
I found this script on another site but found it did not calculate the base memory and per connection memory correctly. After comparing to another popular tool for tuning mysql, I fixed the calculations.
```
wget -O /usr/local/bin/mysql-ram https://raw.githubusercontent.com/alexlewislnk/Misc-Ubuntu-Scripts/main/mysql-ram
chmod +rx /usr/local/bin/mysql-ram
```
On smaller VPS servers that don't have heavy MySQL usage, I most often find these are the two variables to add to /etc/mysql/mysql.conf.d/mysqld.cnf:
```
max_allowed_packet = 32M
max_connections = 50
```

# Apache PHP-FPM and HTTP2
general steps to convert Virtualmin LAMP to PHP-FPM and enable HTTP2

## Enable FPM website
(make sure to do this for each site)

From the virtualmin portal, on the left menu select **Server Configuration** then **PHP Options**. Change the **PHP script execution mode** to **FPM** and click Save. 

Once the configuration has been applied, click on **PHP Options** again select the correct **PHP version** for your server needs. If unsure, use the **7.4.xx** version. Click Save.

## Enable FPM in Apache
```
a2disconf php7.2-fpm
a2enconf php7.4-fpm
a2dismod php7.2
a2dismod php7.4
```

Make sure legacy PHP config is removed from websites
```
sed -i '/php_value/D' /etc/apache2/sites-available/*
sed -i '/php_admin_value/D' /etc/apache2/sites-available/*
```

Restart Apache and verify all sites are working okay
```
systemctl restart apache2
```

## Enable http2 in Apache
```
a2dismod mpm_prefork
a2enmod mpm_event
a2enmod http2
```

Restart Apache and verify all sites are working okay
```
systemctl restart apache2
```
