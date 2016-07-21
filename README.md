# Droid Module: droid/mysql-repln

Configure MySQL Server for Binary Log Replication. One or more masters and one
or more slaves are configured, i.e. one or more clusters, each of one master
and one or more slaves. For more information on Droid, please see
[droidphp.com](http://droidphp.com).

The steps involved are:-

1. Copy a configuration file to each host.
2. Create a user on the master for replication usage by the slave(s).
3. Insert information about the master into the database of the slave(s).
4. Restart the slave(s) and then the master(s).
5. Optionally, load data into the master(s).


## Assumptions

1. The MySQL Service is running on both master and slave.
2. The MySQL Service is configured in the same fashion as the Debian
   mysql-server package
3. The MySQL Service on the master has not yet been running with binary
   logging enabled.
4. All MySQL services will use the same MySQL user names and passwords.
5. Master and slave will communicate through privately addressed network
   interfaces.


## Limitations

1. SQL Queries will be performed using the **MySQL root user** account.
2. A MySQL user named "**repln**" is created on master services, able to
   connect from **any host** and granted the `REPLICATION SLAVE` privilege for
   **all databases and tables**. These details cannot be varied.
3. This module will optionally load data for a **single, named database** and
   the data file must be gzipped.


## Information required by the module

1. One or more Inventory Hosts designated as master, thus:-

        my-master:
          private_ip: <ip_addr>
          variables:
            replication_role: "master"
            replication_id: <integer>               # must be unique among master and its slaves
            replication_innodb_is_used: <boolean>   # whether InnoDB will be used
            initial_data_dbname: <null || string>   # name of a database to seed with data
            initial_data_path: <null || string>     # path to a SQL data file

2. One or more Inventory Hosts designated as slave, thus:-

        my-slave:
          private_ip: <ip_addr>
          variables:
            replication_role: "slave"
            replication_id: <integer>               # must be unique among slaves and master
            replication_innodb_is_used: <boolean>
            replication_master_host: <ip_addr>      # private_ip of the corresponding master

3. Password values for the following variables:-

        passwords:
          mysql:
            root: <string>  # For the root user
            repln: <string> # For the repln user


## Optional information

1. The path of the MySQL binary log file, used in both master and slave
   configuration:-

        module_mysql_repln:
          binary_log_path: <string> # default is "/var/log/mysql/mysql-bin.log"
