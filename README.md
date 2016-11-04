Welcome to MySQL Maint, a bash script that perform backups and maintenance over your MySQL Servers

Installation

Copy the settings.default.conf file to settings.conf and edit or uncomment the variables there according to your server settings.

Set it to run as a cron task. [optional]

Don't forget that the script contains important information so don't forget to
give it strict permissions (700 is good).

For more information, run mysql_main.sh -help


## Introduction
### Requirements 

MySQL Maint is written as a bash script, and uses @mysql@ and @mysqldump@ binaries (debian users : @apt-get install mysql-client@).
Is has been successfully tested on [http://www.debian.org debian] (etch, lenny and squeeze), [http://www.centos.org centos] 4.3, [http://www.freebsd.org FreeBSD] and [http://www.apple.com/server/macosx Mac OS X Server].
Please report if you successfully (or unsuccessfully) used it on another platform. 

Note that MySQL Maint may not be suitable for any situation : @mysqldump@ needs to acquire locks to create a consistent backup, and this may raise issues on highly loaded servers.

### Installation

Simply extract mysql_maint.sh anywhere on your system (for example ~/scripts).
Don't forget to allow execution.
Note that the script itself may contain important information, such as a login/password for your MySQL server. You should really take care to the permissions

`chmod 700 mysql_maint.sh`

## Configuration

There are two ways to configure MySQL Maint :

    * By editing the script, and modifying the script variables
         - pros : the variables (login, password...) don't appear in the @ps@ command
         - cons : the script can only backup one server (which is configured inside the script)
    
    * By passing command-line arguments
         - pros : you can backup as many servers as you like, by passing parameters on the command-line
         - cons : any parameter passed to the command line will be seen in a @ps@


### Quick start
Edit mysql_maint.sh, and change @DB_HOST@, @DB_USER@, @DB_PASS@, @DB_PORT@ to suit your MySQL server configuration. Then, add a cron job to run @mysql_maint.sh -b@ to automatically backup your database server.

If you did not change the @BACKUP_DIR@ variable, your backups will be stored in @~/backups/mysql@.

When you need to recover a database, run @bzcat <db-name>_<date>.sql.bz2|mysql -u <myuser> -p <mypassword> <db-name>@

### Command line arguments
  * `-b` : Ask MySQL Maint to perform a backup
  * `-m` : Ask MySQL Maint to perform maintenance
  * `-H <host>` : The host that runs mysql
  * `-u <login>` : The user login used to connect to the mysql server
  * `-p <password>` : The password used to connect to the mysql server
  * `-P <port>` : The port mysql runs on
  * `-d <folder>` : The folder that will be used to store the backups
  * `-n <firendly-name>` : A friendly name for your server. Will default to the hostname
  * `-l` : Ask MySQL Maint to keep a copy of the latest backup in the folder

### Examples
#### Using command line parameters
If you want to backup server _sql.mycompany.com_, which runs MySQL on port _33306_, using login _backupmanager_ and password _b4ckupP455_, here is the command line you should use :
`mysql_maint -b -H sql.mycompany.com -P 33306 -u backupmanager -p b4ckupP455`

#### Editing the script
If you don't want to pass command line arguments (they can be seen in a @ps@), you can just edit the script, and change the variables default values. Then, you will just need to run @mysql_maint.sh -b@ (to perform a backup), or @mysql_maint.sh -m@ (to perform maintenance)

### Adding MySQL Maint to your crontab
Running MySQL Maint with cron is a good way to ensure your backups will be done regularly.

Here is an example if you want MySQL Maint to run every 3 hours on your server :

```
crontab -e

0 */3 * * * /path/to/mysql_maint.sh -H myserver -u backupmanager -p backuppass -b
```

## Backup folder organization
### Root of the backup folder
The backup folder contains the log files (maintenance and backup), and one folder for each server that is backed up by the script.

### Server backup folder
Each _server folder_ holds one folder for each database that exists on the server (and that is not excluded from backup).

### Database backup folder
In each _database folder_ live the 4 different backup folder (current, daily, weekly and monthly), and, if configured, a copy of the latest backup that wad made for this database.

The backups are bzipped and are named with the date of creation.

## How does it work ?
Each time the script is called (with @-b@ option), it creates a new backup of each database in the 'current' folder. If the daily backup doesn't exist, the backup is copied from 'current' to 'daily'. Once a week, the same test is done for the 'weekly' backup, and so one for the monthly backup.

The current backups are stored during 24 hours.<br />
The daily backups are stored during 7 days.<br />
The weekly backups are stored during 4 weeks.<br />
The monthly backups are never deleted.