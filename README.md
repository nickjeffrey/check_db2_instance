# check_db2_instance
nagios check to confirm IBM DB2 database instance is available

# Requirements
perl and ssh on nagios server, access to cron from db2inst1 user on DB2 server

# Configuration
Add a section similar to the following to commands.cfg on the nagios server
```
# Define service for checking IBM DB2 database instance status
define service{
        use                             generic-24x7-service
        host_name                       unix11
        service_description             DB2
        check_command                   check_nrpe!check_db2 -t 30
        }

```

Add a section similar to the following to services.cfg on the nagios server
```
# Define service for checking IBM DB2 database instance status
define service{
        use                             generic-24x7-service
        host_name                       unix11
        service_description             DB2
        check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_db2_instance"
        }

```

Create a crontab entry for the db2inst1 user on the DB2 server.  
This will execute the nagios check in the security context of the db2inst1 user, which is needed to execute the db2gcf command.
The nagios check will then execute via the nagios scheduler, and read the output in the temporary file generated by the cron job.
```
1,6,11,16,21,26,31,36,41,46,51,56 * * * * /usr/local/nagios/libexec/check_db2  1>/dev/null 2>/dev/null  #nagios helper script
```


