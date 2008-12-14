#########################################################################
####### README DIRECTIONS FOR rbxi RDIFF-BACKUP/RSYNC SCRIPT ############
#########################################################################

This script is designed to only require a single setup, one time.
Any updates to the script should only be new features, which if your system is the
way you want, you should not need. But feel free to check the script homepage
now and then to see if a new feature might be implemented that would be useful
to you.

Please do not change anything in the FUNCTIONS section unless you
are reasonably comfortable with shell scripting. If you do change something and you
break the script, I will not give any support to you, unless it's an interesting problem.

#########################################################################
### Script Download URL: http://techpatterns.com/bu
### Script Home page: http://techpatterns.com/forums/about831.html
-------------------------------------------------------------------------

#########################################################################
### bu.tar.bz2 PACKAGE CONTENTS
-------------------------------------------------------------------------
This bzipped package should contain the following inside the directory called bu:

1. rbxi
This is the main program file. Make sure to use chmod +x rbxi to make it executable.

2. rbxi-data/home-excludes.txt
This is a blank file. Use it to add more excludes if required to your /home backup.

3. rbxi-data/root-excludes.txt
This has the standard root excludes. Do not delete any of these except for:
/var/cache/apt/archives/*
And only delete that if you want to backup your deb archives.

4. rbxi-data/rbxi-values
This file contains all your user variables, use this to create your values
for your backup. This replaces the old 1.x method of having these values on
the actual rbxi file, so you can now easily upgrade rbxi without having to
update or change anything.

5. rbxi-data/readme-rbxi.txt
This readme file.

#########################################################################
### PRIMARY rbxi functions and features.
-------------------------------------------------------------------------
rbxi SCRIPT SETUP
You must customize the USER VARIABLE section to fit your system and requirements
before running this script. For most users, that should be all that is required.

See the section below on how to do that.
-------------------------------------------------------------------------
SCRIPT OPTIONS [ like rbxi -s ]
The script can be started with 3 different options:
-b	Runs the backup without any interactive questions. Automatically, that is.
	-b will simply create a backup based on the current month, assign it to the
   correct primary backup directory, then execute the backup.
-d	First deletes the old backups for the currently assigned primary backup
	directory, then runs the backup non-interactively as with -b.
	Caution: all your previous backups from that primary backup directory will
	be eliminated if you use this. Permanently.

-b and -d are also well suited for running the backup with a cron job, automatically, that is.

If the device is automounted, the script will check to make sure it's path is present, and
if the script uses mount/unmount option, that contains a test as well for failed mounts.

-h The help menu, these options.
-L Create symbolic link in /usr/local/bin to rbxi. You must be root to use this. It will check
   for the link and create it if it's missing.
-m Skip the mounting option. If present, the script will not use your preset mount/umount
	values, and will not try to mount anything. Only use if you have a good reason to do so.
-r Override default backup application: use rsync
-R Override default backup application: use rdiff-backup
-s	Runs the backup using spinning wheel progress indicators.
	Warning: in order to do this, the script puts the actual backup jobs
	in the background, so you can't kill them the normal way, with ctrl+c
	if you want to stop the backup. Keep that in mind.

	-s is useful, besides being eye candy, for letting you know that the script is
	actually doing something while it sits there working on your deletions or backups.
-U Update script manually from svn server. It will update it to where-ever you have it installed.
-v Show script version and last used information. Also shows last time you did a backup
   and which backup directory was used. This should help people like me who can't remember.
-------------------------------------------------------------------------
BACKUP PARTITION FORMATTING:
---------------------------
USING FAT OR FAT32 FORMATTED DRIVES MAY DESTROY YOUR SYSTEMS INTEGRITY ON RESTORE OPERATIONS!

This is because fat file systems do not allow any advanced permissions, which all unix systems
rely on to maintain security and user privileges. Also fat32 has a max file size of about
4 gigabytes.

In order to preserve your file ownership and permissions, you must backup to a reasonably
robust file system. I recommend either ext2 or ext3 if it's an external drive, and whatever
you like internally as long as it's not a FAT type system, which has no support for advanced
file ownership or permissions.

Recent developments with reiserfs suggest that reiser file systems are not a good option
for long term data storage, unfortunately.

-------------------------------------------------------------------------
SCRIPT USEAGE:
Script must be run as root, the contents of bu.tar.bz2 should all have been unzipped
into the same directory. It doesn't matter where you put those files though, as long
as you keep them together.

Make sure to have your backup drive or partition mounted before you start script unless
you are using the auto mount/unmount options in the script. If the drive or partition is not
present, script exits with an error telling you this.

For normal useage, when you run the script, it will check root/backup partition, then ask
you if you want to run the standard backup, which either creates a new, or adds to an existing,
rdiff-backup primary backup directory.

The script by default works off of two primary backup directories in your backup
drive/partition. It determines which to use based on the current month of the year.

Defaults in this assignment are:
	months 1-3: bu-2
	months 4-6: bu-1
	months 7-9: bu-2
	months 10-12: bu-1

You can change this to suite your tastes if you want, but this should work well
for most people.

If you select the delete and backup option, the script will ask you if you are sure
you want to do that.

Once you set the primary variable values as indicated below, you shouldn't need
to change this script much, except mayby adding some exclude items to the exclude files,
or possibly adding or removing a backup option.

-------------------------------------------------------------------------
SEE THIS EXCELLENT man FILE FOR MORE INFORMATION ON HOW TO USE rdiff-backup FEATURES:
man rdiff-backup
Unlike most man files, this one is filled with clear explanations and examples, and can
show you how to do pretty much anything you can imagine.

-------------------------------------------------------------------------
THINGS TO BE CAREFUL ABOUT WHEN RUNNING A BACKUP:
Close anything that might change files while you backup, like your email program,
irc chat, or anything else that might change while you are doing your backup.

Remember to add the path to the backup partition to the exclude lists if your
backup drive or directory is mounted somewhere else than in /media.

You don't want to create backups of your backups after all!

#########################################################################
### EXCLUDE FILE INFORMATION
-------------------------------------------------------------------------
The idea of exclude files is very simple, but there are some things to watch
out for.
-------------------------------------------------------------------------
BACKUP FROM OUTSIDE OF OS
.........................................................................
First, if you running the backup from outside the operating system that is
being backed up, the exclude file paths must reflect that.

So you need to change for example a /sys/* in /dev/hda2 in roots-excludes.txt
that is currently mounted in /media/hda2 to:
/media/hda2/sys/*

However, this script is designed to allow smooth, complete backups from inside
your operating system, so you should rarely need this change. It's much easier
after all running the backup from in the OS than having to reboot into another one.
-------------------------------------------------------------------------
EXCLUDE LIST SYNTAX
.........................................................................
The main syntax you need to understand for creating an exclude list is this:
1. /some/directory - Excludes any file or directory called: directory in /some
  and also excludes any directory or file contained in that.
2. /some/directory/* - Backs up the directory /some/directory, but not its contents.
3. This sequence [ note the + with a space after it preceding /some/directory/main ]:
+ /some/directory/main
/some/directory
will backup the directory /some/directory/main and its contents, but will not back
up anything else in /some/directory.

It is important to understand that rdiff-backup first accepts the + items, which means
include this in the backup, then after that you add the normal exclude case. The order
is important, if you put the include case after, it won't get included, if it's before
it will get included.

So when you have a list and you want to include one directory in a parent directory that
is excluded, the include item, with + /some/path must come first.

For more information, read: man rdiff-backup

#########################################################################
### HOW TO SETUP rbxi VARIABLES
-------------------------------------------------------------------------
You will need to set the paths in rbxi USER SPECIFIC VARIABLES section, using
the file rbxi-data/rbxi-values. That overrides anything in the main script, and
if you update rbxi, you will not need to change anything at all.

I will run down the variables one by one to explain how to set them correctly.
=========================================================================
LAYOUT VARIABLES:
=========================================================================
These are colors and background colors. Change to suite your
taste if you want, but the defaults should work fine for most users.

If you don't know how to change these, then search online for: bash ascii colors
and you will find many good tutorials. Please do not ask me for help with this.

=========================================================================
USER SPECIFIC VARIABLES - CHANGE AS REQUIRED:
=========================================================================
These should be the only variables you need to change. I will go down the list
and explain what each group is and how to change it for your system and needs.

Please note: some of these have default values already, make sure that the defaults
match your backup needs if you leave them unchanged.

=========================================================================
BACKUP_APP='rdiff-backup

Set to either rdiff-backup or rsync. Can be overridden with r/R option
=========================================================================
BACKUP_PARTITION='/media/usbdisk'

This is the mounted location of your backup disk or partition. The default assumes
that it is a external drive with the label of 'usbdisk'. Change to your requirements.
Please make sure this does not end with a /, but just the actual partition mount directory
name.

NOTE: it is extremely bad practice to backup to the same partition that is being backed up
so if you want to do that don't look here for help or advice.

Do not use the /dev/sda1 type names, use the mounted name, like /media/sda1

=========================================================================
BACKUP_LOCATION=$BACKUP_PARTITION'/bu-'

This is the actual directory name of where the backup will go. The way this script works
is that it will alternate by default between 2 backup directories within your backup
partition or drive.

The default for this alternation is 2 directories, although you could make it just 1, or more
than 2, depending on your needs. But the default of 2 should work well for most users.

You do not need to create these directories, the script will do it automatically.

The only requirement is that the directory name does not include a number as its last
digit, the script creates that number by itself.

In the above case, for example, the script will create a directory named either bu-1 or bu-2
depending on  what month of the year it is. Those will be located in /media/usbdisk in this
case, assuming the default primary partition name is not changed. If you changed the above value
to BACKUP_LOCATION=$BACKUP_PARTITION'/my-backups-' the script would create either
/media/usbdisk/my-backups-1 or /media/usbdisk/my-backups-2

=========================================================================
BACKUP DIRECTORY PATHS AND INFORMATION
Do not begin any of the following 4 directory names with a /, just put the name itself
you want rdiff-backup to create for that particular backup.

Important: if any one of these 4 is left blank, no backup of that component will be created.
.........................................................................
BU_HOME='home'

This is the name of the directory that your /home partition will get backed up to inside of
the above primary backup directories.
.........................................................................
BU_ROOT='root-hda1'

This is the name of the directory that your / [root] partition will get backed up to inside
of the above primary backup directories.
.........................................................................
BU_DATA_1=''

If used, this would be a third backup directory name. Default is blank.
.........................................................................
BU_DATA_2=''

If used, this would be a fourth backup directory name. Default is blank.

=========================================================================
SYSTEM BACKUP PATH INFORMATION
These are the actual paths to be used for the backups. Whatever path  you
enter here is what will get backed up.
.........................................................................
USER_HOME_PARENT='/home'

Only change this if you are running the backup from outside of the Operating
system that is being backed up. For example, if /home is on /dev/sda3, then
you'd change this to /media/sda3 if you were backing up from example a live cd.

SPECIAL NOTE: If you are using any /home excludes, and if you are running the
backup from outside of the operating system/ home partition you are backing up,
you must change the paths in home-excludes.txt to reflect this actual path, for
example:
/home/username/somedata/*
would become
/media/hda3/username/somedata/* [assuming that your home directory is mounted
in a separate partition that is. This path must be the unmounted true path to
the /home you are backing up when it's run from outside the OS.

Normal useage: do not change
.........................................................................
SYSTEM_ROOT='/'

If backing up from within OS, do not change. If from outside, same as above.

SPECIAL NOTE: If you run the script from outside the operating system to be
backed up, you will need to change the root-excludes.txt paths to reflect that.
for exampple, say / in this case is on /dev/hda2, and the partition is mounted
in /media/hda2:
/sys/*
would be
/media/hda2/sys/*
and so on

Normal useage: do not change
.........................................................................
USER_DATA_1=''

If you are creating more than the default / and /home backups, use this. This
must correspond to the BU_DATA_1 item above. Set to path of item you want backed
up separately.

Note: normally, you will exclude this in either /home or / depending on where the
data is mounted. Just add this path to the relevant home-excludes.txt or root-excludes.txt
To backup the directory name but not it's contents, do this:

/data/*

in the exclude file.

If BU_DATA_1 is blank, this will not get backed up.
.........................................................................
USER_DATA_2=''

Same as USER_DATA_2, second optional backup path.

=========================================================================
SCRIPT OUTPUT STUFF
These variables are simply what the script will print out to you when it runs.
They do not affect what actually is backed up.
.........................................................................
USER_DATA_1_DESC=''

This is a one or more word description of what is being backed up. Begin it with
a space so it prints out nicely, like this: USER_DATA_1_DESC=' my emails'

This is for BU_DATA_1 and USER_DATA_1
.........................................................................
USER_DATA_2_DESC=''

Same as above, except for DATA_2
.........................................................................
ROOT_PARTITION='/dev/hda1'

This just tells you which actual physical partition root is. Change to
your own setup. This is not a path, it's just information for you.
.........................................................................
SLEEP_TIME='0.5'

If you started the script with the -s option, a small spinning wheel will
run as each part of the operation is carried out. This lets you know
that something is happening, that is.

I found that setting this to a lower number tended to boost the cpu useage, but
you can play with it to see for yourself. Anything less then 0.1 would be
pointless. I find that 0.5 is fine, and doesn't consume much cpu. The lower the
number, the faster the wheel spins.

=========================================================================
BACKUP DISK / PARTITION mount / umount
If you want the script to automatically mount/umount your backup drive / partition,
use this. If you don't want this, just leave the two variables with a blank value, like this:
.........................................................................
MOUNT_BU_DISK=""
UNMOUNT_BU_DISK=""
.........................................................................
Otherwise, set the full command for mount/umount like these samples. I recommend using
either UUID or LABEL mounting to make sure the disk is correctly mounted, since /dev/sdd can
change depending on what you have plugged into your system at that moment.

For these examples, the disk has the following:
label:		backup-disk-1
uuid:			b833d2c1-b824-4d93-a8c9-74e84226rc9c
standard:	/dev/sdc1

- for LABEL mounting:
MOUNT_BU_DISK="mount -L backup-disk-1 $BACKUP_DIRECTORY"

- for UUID mounting
MOUNT_BU_DISK="mount -U b833d2c1-b824-4d93-a8c9-74e84226rc9c $BACKUP_DIRECTORY"

- for standard mounting:
MOUNT_BU_DISK="mount /dev/sdc1 $BACKUP_DIRECTORY"
.........................................................................
- Unmounting command, should not need to be changed:
UNMOUNT_BU_DISK="umount $BACKUP_DIRECTORY"
.........................................................................

=========================================================================
STATIC VARIABLES, SHOULD NOT REQUIRE CHANGES
=========================================================================
These variables should not be changed unless you have some particularly good
reason to do that.
.........................................................................
SCRIPT_CONFIGS='/etc/rbxi.conf'
SCRIPT_NAME='rbxi'
SCRIPT_VALUES='rbxi-values'
# http://forums.macosxhints.com/archive/index.php/t-73839.html
SCRIPT_PATH=$( dirname $( lsof -p $$ | grep 'REG' | grep -oE "/.*$(basename $0)$" ) )
SCRIPT_DATA_FILES="$SCRIPT_PATH/rbxi-data/"
SCRIPT_VERSION_NUMBER=$( grep -im 1 'version:' $SCRIPT_PATH/$SCRIPT_NAME | awk '{print $3}' )
SCRIPT_DOWNLOAD='http://rbxi.googlecode.com/svn/trunk/'
SCRIPT_DOWNLOAD_BRANCH_1='http://rbxi.googlecode.com/svn/branches/one/'
SCRIPT_DOWNLOAD_BRANCH_2='http://rbxi.googlecode.com/svn/branches/two/'
SCRIPT_DOWNLOAD_DEV='http://techpatterns.com/downloads/scripts/'

These should not require any modification, unless you have a problem with the
default script SCRIPT_PATH value, if you do, that's a bug, get in touch with
me here: http://techpatterns.com/forums/forum-33.html
.........................................................................
EXCLUDE_LIST='root-excludes.txt'

Only change if you want the root-excludes.txt file to be located somewhere other
than the directory where rbxi is located. If you change the location, make
sure to use an absolute path to where the file is located.
.........................................................................
EXCLUDE_HOME_LIST='home-excludes.txt'

Same as above.
.........................................................................
USER_DATA_1_BACKUP_LOCATION=''
USER_DATA_2_BACKUP_LOCATION=''
BU_NU=1
ENDING_1='...'
ENDING_2='...'
DELETION_TEXT=''
B_TESTING_1=''
B_TESTING_2=''
DO_BACKUP=''
DELETE_BACKUP=''
SPINNING_WHEEL=''
GET_PID=''

Do not change any of these, these are simply initializing variables.

#########################################################################
### FUNCTIONS - PLEASE ONLY CHANGE IF YOU CAN DO SOME BASH SCRIPTING
=========================================================================
set_which_backup
............................
This function is the key assignment function. It determines which primary
backup directory to use. The main name is assigned in USER VARIABLES, this
simply adds 1 or 2 to it.

You can change the frequency of when you switch from say bu-1 to bu-2. The default
is every 3 months. You can also create more primary backups, like bu-1, bu-2, bu-3 or
bu-4 if that fits your backup needs better. If you did that, for example, in month
1-3 of the year, your backups would go to bu-1, in months 4-6, bu-2, and so on.

This is useful if your data changes a lot, and you want to be able to restore from
some previous point at some time.

The simplest is to just have one directory used, bu-1 for example, and to always
use that for your backups. The problem with this is that over time, the backup will
get really big as you do more and more changes to your system, and then you have to either
delete it all, or just accept the expansion.

Most users will find that having 2 rotating directories works fine.
=========================================================================
run_backups
............................
The only thing I could see any user wanting to change here would be adding
more primary backups. That's fairly trivial, just create the user variables for data_3, data_4
etc, then add more if that exists items like you see there.

There is no need to remove anything here by default, since if the primary backup directory
name is blank, that backup won't run.

The other thing you might want to change is adding exclude-globbing-filelists to either
DATA_! or DATA_2 backups.

To do that, simply create those files, add the variable names for them where the
root and home file lists are listed, then add this to the rdiff-backup command:


#########################################################################
### OK, THAT'S IT, HAVE FUN!
-------------------------------------------------------------------------
