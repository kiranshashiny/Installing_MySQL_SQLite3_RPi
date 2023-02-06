# Installing_MySQL_on_Raspberry_Pi

(Note. Set up a cron job to do sudo apt-get update and sudo apt-get upgrade right after installation to save time when you are ready to start work )

Steps:

To burn the image on a SD card

Launch the RPi Imager

![image](https://user-images.githubusercontent.com/14288989/216913403-d79a91c0-bea8-4394-becf-d5ec468b2843.png)


At this stage -DO NOT CLICK on the "Choose OS", instead click and select Erase 
![image](https://user-images.githubusercontent.com/14288989/216913538-373fe400-0d37-41ab-bdae-f3571b9f8295.png)


![image](https://user-images.githubusercontent.com/14288989/216913607-a9978b75-13ca-483d-888c-7e987b1d5d93.png)

Choose Storage and select the SD card
and now write the image "Raspberry Pi 32 bit "to the SD card.

---


## Installing MySQL on Raspberry pi

First

* sudo su -   ( Im logged in as Amrith )
* sudo apt-get update
* sudo apt-get upgrade

![image](https://user-images.githubusercontent.com/14288989/216918453-0d4f000c-fee0-430f-982a-ab97db24cc44.png)

Note : Installing mysql-server does not work - see below !

```
root@shashirpi:~# sudo apt-get install mysql-server
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package mysql-server is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  mariadb-server-10.0

E: Package 'mysql-server' has no installation candidate

```

https://linuxhint.com/install-setup-mariadb-server-raspberry-pi/

So installing mariadb-server-10.0


```
$ sudo mysql_secure_installation


Reload privilege tables now? [Y/n] n
 ... skipping.

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

![image](https://user-images.githubusercontent.com/14288989/216920867-effc29b8-4ab6-4def-b0d6-fef0d9e053be.png)

```
MariaDB [(none)]> create database mariadb;
Query OK, 1 row affected (0.01 sec)

MariaDB [(none)]> show databases
    -> ;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mariadb            |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON mariadb.* TO 'amrith'@'localhost' IDENTIFIED BY 'amrith'
    -> ;
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> exit;
Bye


pi@shashirpi:~ $ sudo mysql -u amrith -p mariadb ( -u amrith is the username, and mariadb is the name of the database here, 
Password is asked next in the command line. )

MariaDB [mariadb]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mariadb            |
+--------------------+
2 rows in set (0.01 sec)

```

For creating a table to hold the stocks.

### Data to be inserted
```
   1 date,open,high,low,close,volume,Name
   2 2013-02-08,67.7142,68.4014,66.8928,67.8542,158168416,AAPL
   3 2013-02-11,68.0714,69.2771,67.6071,68.5614,129029425,AAPL
```

### stocks.sql

```
use mariadb
create table stocks (
    stock_id int auto_increment,
    stock_date  date,

    stock_open  decimal ( 15,2),
    stock_high  decimal ( 15,2),
    stock_low   decimal ( 15,2),
    stock_close decimal ( 15,2),
    stock_volume int not null,

    stock_name varchar(50) not null,
    primary key(stock_id)
);
```

### insert.sql

```
insert into stocks ( stock_date, stock_open, stock_high, stock_low, stock_close, stock_volume, stock_name) values ( 2013-02-08,67.7142,68.4014,66.8928,67.8542,158168416,"AAPL");

insert into stocks ( stock_date, stock_open, stock_high, stock_low, stock_close, stock_volume, stock_name) values ( 2013-02-08,67.7142,68.4014,66.8928,67.8542,158168416,"AAPL");

```

#### source insert.sql
will insert the data





