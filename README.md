# LTO-Backup-script

backups your data to a multiple LTO tapes while they are encrypted and compressed.

## SETUP
change the config file for your needs in the head of the start script
Keep in mind to install the following software applications:


     ###############################
     # nas backup connections
     NAS=nas
     NAS_IP=192.168.178.10
     MOUNT_NFS="$NAS_IP:pool0"
     MOUNT_LOCAL="/data/pool0"

     ###############################
     # mail options
     MAIL_PREFRACE="[nas-backup]"

     ###############################
     # Backup dirs; relative from Mount or full path
     NAS_MOUNT_DIR=$PWD/nasmount
     BACKUP_DIRS="homes photo docs docker cloud video /etc"
     KEYFILE="$PWD/key.txt"

     ###############################
     # Backup Log folder
     LOGBASE=$PWD/backup.log
     LOGFILE=$LOGBASE/$NOW.backup.log
     FILELIST=$LOGBASE/$NOW.tar.list
     TAPENAMES=$LOGBASE/$NOW.backup.tapenames.txt

     ###############################
     # Tape devie name
     TAPE="/dev/st0"
     TAPESIZE="400*1000*1000*1000"
     TAPESPEED="70*1000*1000"
     BLOCKSIZE="512K"

     ###############################
     # Tar options
     TAR_ARGS="-b 1024"
     TAPE_BUFF="3g"

     ###############################
     #compression
     # please add the pipe in order to also diable this compression completely
     #COMPRESSION_CMD="pigz -3 -p 32 "
     #DECOMPRESSION_CMD="pigz -dc -3 -p 32"
     COMPRESSION_CMD="zstd -3"
     DECOMPRESSION_CMD="zstd -3 -d"

     ###############################
     # Path to binaries
     TAR=/bin/tar
     MT=/usr/bin/mt-st
     MKDIR=/bin/mkdir
     MBUFFER=/usr/bin/mbuffer
     OPENSSL=/usr/bin/openssl
     PIPEMETER=/usr/bin/pipemeter
     COMPRESSION=/usr/bin/zstd
     UUENCODE=/usr/bin/uuencode


### Encrytion setup

For encryption aes-256-cbc is used. In order for this to work you have to generate a `key.txt` file next
to the start script.
I prefer openssl to generate one for me


    openssl rand 512 > $KEYFILE


### email setup

In oder to receive emails postfix has to be configured in the way that your emails get forwarded from root to
a real email address.

you can test this if you received an email with the following command


    echo "foo" | mail root --subject="test"


If this works and you did receive an email you should also receive a email via this script.

## Usage

### full backup

For a full backup just run the script like this:


    sudo start backup-full


### Tape number estimation
For estimation of time and number of tapes needed for your backup you can execute the
estimation.


    sudo start estimate-tapes


### Tape print content
If you have a backup and you want to get the content of this tapes you can execute this command:


    sudo start tape-list


You will be asked what number tapes your backup is utilizing.



### Tape restore content
If you want to recover some or all of the files you can use this command.

for a special file


    sudo start recover-data <output dir> <a special file>


or for all files


    sudo start recover-data <output dir>


You will be asked what number tapes your backup is utilizing.
