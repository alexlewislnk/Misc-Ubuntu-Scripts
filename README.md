# Misc-Ubuntu-Scripts

## mysql-ram
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

