#!/bin/bash

###############################
# Part 1 : Script parameters  #
###############################

#################################
# Server connection settings    #
#                               #
# Change these settings to suit #
# your server configuration     #
#################################
DB_HOST=localhost
DB_USER=root
DB_PASS=''
DB_PORT=3306


# If we're running on a Debian family we can use the maintenance account.
if [[ -f /etc/mysql/debian.cnf && -r /etc/mysql/debian.cnf ]]; then
    DB_HOST=$(grep host /etc/mysql/debian.cnf | head -n1 | sed -r 's/^.+=\s*//')
    DB_USER=$(grep user /etc/mysql/debian.cnf | head -n1 | sed -r 's/^.+=\s*//')
    DB_PASS=$(grep password /etc/mysql/debian.cnf | head -n1 | sed -r 's/^.+=\s*//')
    DB_SOCKET=$(grep socket /etc/mysql/debian.cnf | head -n1 | sed -r 's/^.+=\s*//')
fi

# Weekly backup day
# (result of 'date %u')
DOW='7'

# Monthly backup day
# (result of 'date %d')
DOM='01'

# Backup folder
# /!\ Don't add a trailing slash
BACKUP_DIR="${HOME}/backup/mysql"

# Server name
# The backups will go to ${BACKUP_DIR}/${BACKUP_HOST_NAME}/<db-name>
# defaults to $DB_HOST
BACKUP_HOST_NAME=""

# Sould a copy of the latest backup be placed
# in $BACKUP_HOST_NAME/<db-name> ?
SAVE_LATEST=yes

# Should this copy be a symlink
# instead of a hard copy ?
LINK_LATEST=yes

# Backups lifetime.
# lifetimes are written in days,
# except the 'current' backups lifetime
# that is in minutes

# 'current'
DELETE_BACKUP_OLDER_THAN_MIN=1440
# 'daily'
D_DELETE_BACKUP_OLDER_THAN_DAYS=6
# 'weekly'
W_DELETE_BACKUP_OLDER_THAN_DAYS=30

# Dates format for naming backups
DATE_FORMAT="%Y-%m-%d_%H-%M" # current
D_DATE_FORMAT="%Y-%m-%d" # daily
W_DATE_FORMAT="%Y-%m-%d" # weekly
M_DATE_FORMAT="%Y-%m-%d" # monthly

# Backup folders names
CURRENT_FOLDER='01_current'
DAILY_FOLDER='02_daily'
WEEKLY_FOLDER='03_weekly'
MONTHLY_FOLDER='04_monthly'

# Database that souldn't be maintained
IGNORE_MAINTENANCE_DATABASES="mysql information_schema performance_schema"

# Databases that souldn't be backed up
IGNORE_BACKUP_DATABASES="information_schema mysql performance_schema"

# Databases that sould only be backed up / maintainted.
# Overrides prevous setting
ONLY_BACKUP_DATABASES=""
ONLY_MAINTAIN_DATABASES=""

# Enable logging ?
LOG=yes
LOG_FILE_BACKUP_NAME="backup.log"
LOG_FILE_MAINTENANCE_NAME="maintenance.log"
LOG_DATE_FORMAT='%b %d %H:%M:%S'
LOG_MAXSIZE=102400 # Log max size before rotation

# Temporary file used by the script
TEMP_FILE=/tmp/mysql_maint.tmp

# Should SQL errors be ignored (adds -f options to mysql commands) ?
IGNORE_SQL_ERRORS=no

# Should communications with the server be compressed (adds -c to mysql commands) ?
USE_COMPRESSION=yes
