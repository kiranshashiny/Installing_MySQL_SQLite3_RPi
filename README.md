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


## Programming with SQLite3 connector

```

#include <sqlite3.h>
#include <stdio.h>

void main() {

   printf ("hello world from sqllite3 header file inclusion\n");

   sqlite3 *db;
   char *zErrMsg = 0;
   int rc;

   rc = sqlite3_open("test.db", &db);

   if( rc ) {
      fprintf(stderr, "Can't open database: %s\n", sqlite3_errmsg(db));
      return;
   } else {
      fprintf(stderr, "Opened database successfully\n");
   }
   sqlite3_close(db);
   return;
}
```
cc -o test test.c -l sqlite3

![image](https://user-images.githubusercontent.com/14288989/217795828-2d4fd9d5-b42e-49cc-99b0-55755ee26842.png)


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
    stock_adj_close decimal ( 15,2),
    stock_volume int not null,

    stock_name varchar(50) not null,
    primary key(stock_id)
);
```

### insert.sql - watch for the semi colon at the end of each line, or else errors out.

```
insert into stocks ( stock_date, stock_open, stock_high, stock_low, stock_close, stock_volume, stock_name) values ( 2013-02-08,67.7142,68.4014,66.8928,67.8542,158168416,"AAPL");

insert into stocks ( stock_date, stock_open, stock_high, stock_low, stock_close, stock_volume, stock_name) values ( 2013-02-08,67.7142,68.4014,66.8928,67.8542,158168416,"AAPL");

```

#### source insert.sql
will insert the data





### some commands
```
select count(*) from stocks;
select stock_open, stock_close from stocks;
select stock_volume from stocks;

Minimum ( incorrect approach !!!  this gives wrong answer in MariaDB - DON'T USE )

select min(stock_open), stock_date from stocks;
+-----------------+------------+
| min(stock_open) | stock_date |
+-----------------+------------+
|          126.01 | 2022-02-07 |
+-----------------+------------+
1 row in set (0.00 sec)


Maximum  and Minimum

Use DESC keyword for reverse sort 

MAXIMUM


MariaDB [mariadb]> select stock_date, stock_open from stocks order by stock_open desc limit 1;
+------------+------------+
| stock_date | stock_open |
+------------+------------+
| 2022-03-30 |     178.55 |
+------------+------------+
1 row in set (0.01 sec)


MINIMUM

select stock_date, stock_open from stocks order by stock_open  limit 1;
+------------+------------+
| stock_date | stock_open |
+------------+------------+
| 2023-01-06 |     126.01 |
+------------+------------+
1 row in set (0.01 sec)


PRINT THE LAST ENTRY in the table ( 7 Feb 2023 )

select stock_date, stock_open from stocks order by stock_date desc limit 1;
+------------+------------+
| stock_date | stock_open |
+------------+------------+
| 2023-02-03 |     148.03 |
+------------+------------+
1 row in set (0.01 sec)
```

Script to download historical data from finance.yahoo.com


```
./gethistory.sh


stockslist=`cat stocks.txt`
for stock in $stockslist
do
echo $stock
curl https://query1.finance.yahoo.com/v7/finance/download/$stock?period2=1675744747&period1=1644208747\
&interval=1d\
&events=history\
&includeAdjustedClose=true 

done

```

To check if the stock is less than 230 $ - do this.

```
cat ZTS.dat |head -n2 |grep -v Date |awk -F"," '{if ($2<230) print $2}'
```


To pass an external variable into awk, notice the use of x=$stock and while printing do not use $x, just x. 

```
stock="MSFT.dat"
cat $stock |head -n2 |grep -v Date |awk -v x=$stock -F"," '{if ($2<20) {print $2, x}}'

```

Printing stocks in S&P under 20 $
```
#!/bin/bash

stockslist=`ls *.dat`
   
for stock in $stockslist
do
   cat $stock |head -n2 |grep -v Date |awk -v x=$stock -F"," '{if ($2<20) {print $2, x}}'
done
```


select stocks under a certain stock price

```
select * from stocks where stock_open < 130;

```


## Installing sqlite3 on Rpi.

```
sudo apt install sqlite3

sudo apt-get install libsqlite3-dev

```

## command line 

.read department.sql <------------- Reads from an external file.

```
.open test.db
.schema DEPARTMENT
insert into DEPARTMENT  ( ID, DEPT, EMP_ID ) values  ( 3, 'CHEM', 1200);
select * from DEPARTMENT;

```

A sample table DEPARTMENT can be 

CREATE TABLE DEPARTMENT(
   ID INT PRIMARY KEY      NOT NULL,
   DEPT           CHAR(50) NOT NULL,
   EMP_ID         INT      NOT NULL
);


## Programming with sqlite3 C connector

https://www.tutorialspoint.com/sqlite/sqlite_c_cpp.htm

```
#include <sqlite3.h>
#include <stdio.h>

void main() {

   printf ("hello world from sqllite3 header file inclusion\n");

   sqlite3 *db;
   char *zErrMsg = 0;
   int rc;

   rc = sqlite3_open("test.db", &db);

   if( rc ) {
      fprintf(stderr, "Can't open database: %s\n", sqlite3_errmsg(db));
      return;
   } else {
      fprintf(stderr, "Opened database successfully\n");
   }
   sqlite3_close(db);
   return;
}
```

```

Create

#include <stdio.h>
#include <stdlib.h>
#include <sqlite3.h> 

static int callback(void *NotUsed, int argc, char **argv, char **azColName) {
   int i;
   for(i = 0; i<argc; i++) {
      printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
   }
   printf("\n");
   return 0;
}

int main(int argc, char* argv[]) {
   sqlite3 *db;
   char *zErrMsg = 0;
   int rc;
   char *sql;

   /* Open database */
   rc = sqlite3_open("test.db", &db);
   
   if( rc ) {
      fprintf(stderr, "Can't open database: %s\n", sqlite3_errmsg(db));
      return(0);
   } else {
      fprintf(stdout, "Opened database successfully\n");
   }

   /* Create SQL statement */
   sql = "CREATE TABLE Another_COMPANY("  \
      "ID INT PRIMARY KEY     NOT NULL," \
      "NAME           TEXT    NOT NULL," \
      "AGE            INT     NOT NULL," \
      "ADDRESS        CHAR(50)," \
      "SALARY         REAL );";

   /* Execute SQL statement */
   rc = sqlite3_exec(db, sql, callback, 0, &zErrMsg);
   
   if( rc != SQLITE_OK ){
      fprintf(stderr, "SQL error: %s\n", zErrMsg);
      sqlite3_free(zErrMsg);
   } else {
      fprintf(stdout, "Table created successfully\n");
   }
   sqlite3_close(db);
   return 0;
}

```

```

Insert


#include <stdio.h>
#include <stdlib.h>
#include <sqlite3.h> 

static int callback(void *NotUsed, int argc, char **argv, char **azColName) {
   int i;
   for(i = 0; i<argc; i++) {
      printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
   }
   printf("\n");
   return 0;
}

int main(int argc, char* argv[]) {
   sqlite3 *db;
   char *zErrMsg = 0;
   int rc;
   char *sql;

   /* Open database */
   rc = sqlite3_open("test.db", &db);
   
   if( rc ) {
      fprintf(stderr, "Can't open database: %s\n", sqlite3_errmsg(db));
      return(0);
   } else {
      fprintf(stderr, "Opened database successfully\n");
   }

   /* Create SQL statement */
   sql = "INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,SALARY) "  \
         "VALUES (1, 'Paul', 32, 'California', 20000.00 ); " \
         "INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,SALARY) "  \
         "VALUES (2, 'Allen', 25, 'Texas', 15000.00 ); "     \
         "INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,SALARY)" \
         "VALUES (3, 'Teddy', 23, 'Norway', 20000.00 );" \
         "INSERT INTO COMPANY (ID,NAME,AGE,ADDRESS,SALARY)" \
         "VALUES (4, 'Mark', 25, 'Rich-Mond ', 65000.00 );";

   /* Execute SQL statement */
   rc = sqlite3_exec(db, sql, callback, 0, &zErrMsg);
   
   if( rc != SQLITE_OK ){
      fprintf(stderr, "SQL error: %s\n", zErrMsg);
      sqlite3_free(zErrMsg);
   } else {
      fprintf(stdout, "Records created successfully\n");
   }
   sqlite3_close(db);
   return 0;
}



```

Compiling
```
cc -o test test.c -l sqlite3
```

![image](https://user-images.githubusercontent.com/14288989/217795952-62b2a7c2-f177-4cdb-a595-1e0964c7e3cd.png)


https://www.tutorialspoint.com/sqlite/index.htm

### Commands ( don't use semi colons at the end )

```
.open test.db

.databases

.tables

.schema COMPANY  // to list the columns of table COMPANY

.drop TABLE COMPANY

select * from COMPANY.    // no period required at the beginning


```
