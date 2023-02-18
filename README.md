# mysql-ram
I found this script on another site but found it did not calculate the base memory and per connection memory correctly. After comparing to another popular tool for tuning mysql, I fixed the calculations.
```
wget -O /usr/local/bin/mysql-ram https://raw.githubusercontent.com/alexlewislnk/Misc-Ubuntu-Scripts/main/mysql-ram
chmod +rx /usr/local/bin/mysql-ram
```
On smaller VPS servers that don't have heavy MySQL usage, I most often find these are the two variables to add to /etc/mysql/mysql.conf.d/mysqld.cnf:
```
if [ -f /etc/mysql/mysql.conf.d/mysqld.cnf ]; then
cp /etc/mysql/mysql.conf.d/mysqld.cnf /etc/mysql/mysql.conf.d/mysqld.cnf.backup
sed -i '/max_allowed_packet/D' /etc/mysql/mysql.conf.d/mysqld.cnf
sed -i '/max_connections/D' /etc/mysql/mysql.conf.d/mysqld.cnf
cat >> /etc/mysql/mysql.conf.d/mysqld.cnf <<EOF
max_allowed_packet = 32M
max_connections = 50
EOF
fi
if [ -f /etc/mysql/mariadb.conf.d/50-server.cnf ]; then
cp /etc/mysql/mariadb.conf.d/50-server.cnf /etc/mysql/mariadb.conf.d/50-server.cnf.backup
sed -i '/max_allowed_packet/D' /etc/mysql/mariadb.conf.d/50-server.cnf
sed -i '/max_connections/D' /etc/mysql/mariadb.conf.d/50-server.cnf
cat >> /etc/mysql/mariadb.conf.d/50-server.cnf <<EOF
max_allowed_packet = 32M
max_connections = 50
EOF
fi
systemctl restart mysql
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
a2dismod mpm_prefork
a2enmod mpm_event
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
a2enmod http2
```

Restart Apache and verify all sites are working okay
```
systemctl restart apache2
```
