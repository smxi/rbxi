#########################################################################
####### README DIRECTIONS FOR rbxi RDIFF-BACKUP/RSYNC SCRIPT ############
#########################################################################
####  Name: rbxi-readme.txt
####  Version: 2.3.0
#########################################################################

rbxi script is designed to only require a single setup, one time.
Any updates to the script should only be new features, which if your system is the
way you want, you should not need. But feel free to check the script homepage
now and then to see if a new feature might be implemented that would be useful
to you.

Please do not change anything in the FUNCTIONS section unless you
are reasonably comfortable with shell scripting. If you do change something and you
break the script, I will not give any support to you, unless it's an interesting problem.

#########################################################################
### Script Bzip File Download URL: http://smxi.org/rb
### Script Home page: http://techpatterns.com/forums/about831.html
-------------------------------------------------------------------------

#########################################################################
### rbxi.tar.bz2 PACKAGE CONTENTS
-------------------------------------------------------------------------
This bzipped package should contain the following inside the directory called bu:

1. rbxi
This is the main program file. Make sure to use chmod +x rbxi to make it executable.

2. rbxi-data/excludes-root.txt (for rdiff-backup)
   rbxi-data/excludes-root-rsync.txt (for rsync)
These have the standard root excludes. Do not delete any of these except for:
/var/cache/apt/archives/*
And only delete that if you want to backup your deb archives.

2. # for rdiff-backup
	rbxi-data/excludes-home.txt
   rbxi-data/excludes-data-<1-10>.txt
   # and for rsync
   rbxi-data/excludes-home-rsync.txt
   rbxi-data/excludes-data-<1-10>-rsync.txt

These are blank files. Use them to add more excludes if required to your backup.

3. rbxi-data/rbxi-values
This file contains all your user variables, use this to create your values
for your backup. This replaces the old 1.x method of having these values on
the actual rbxi file, so you can now easily upgrade rbxi without having to
update or change anything.

4. rbxi-data/readme-rbxi.txt
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
The script can be started with a variety of different options:

ADD/SKIP/OVERRIDE COMPONENTS - -J PRESET VALUES
-A Add backup component. Overrides user set B_SKIP_DATA.. (d|h|hr|1-10)
   d - add all DATA; h - add HOME; r - add ROOT; add DATA 1-10
   Normal use is with -J option set to override -S rbxi-values settings for SKIP variables.
-B Change default backup directory (1-10).
-C (rsync only) Clone root/home. Skips all data backups. Moves files directly to mounted location
   Sets the main mount subdirectory to , then transfers files directly into that directory.
   Takes parameters: (h|r|1-10). Clone root, home, or a data direcory.
-D Change default backup sub directory (1-10)
-M use alternate mount/umount set (1-10) - these are set in rbxi-values. They can be null, so make
   sure you have the right number for your selection.
-o [rb|rs] Override default backup application: use rsync/rdiff-backu
-S Skip backup component (d|h|hr|1-10)
   d - skip all DATA; h - skip HOME; r - skip ROOT; skip DATA 1-10

In general, avoid using A, D, M, S unless you are creating a backup job -J, or if you want
temporarily skip (-S) or add (-A) a specific directory.

-S and -A can be used repeatedly, like so: -S 2 -S 5 -S 9
Remember, -A should only be used to override SKIP... that has been set in rbxi-values.
-C overrides -A which overrides -S. -C will only clone the specific directory you set,
directly to the destination directory, and switches off all other backup directories.
-C overrides -D, or default backup subdirectory, and sets it to null.
-D overrides default main mount backup directory, and uses that for -M

AUTO RUN OPTIONS
-b	Runs the backup without any interactive questions. Automatically, that is.
	-b will simply create a backup based on the current month, assign it to the
   correct primary backup directory, then execute the backup.
-c Automatic backup, clean older files, for non-interactive. Cleans and runs normal backup.
-d	First deletes the old backups, then runs the backup non-interactively as with -b.
	Caution: all your previous backups from that backup directory will be eliminated
	if you use this. Permanently.

-b, -c, and -d are also well suited for running the backup with a cron job, automatically, that is.

JOB OPTION
-J Start preset backup job (1-10)

Create preset jobs, 1-10, in rbxi-values, using script arguments. Use presets like -A, -B, -o,
-D to create a custom job for future use.

# MISCELLANEOUS OPTIONS
-h The help menu, these options.

-L Create symbolic link in /usr/local/bin to rbxi. You must be root to use this. It will check
   for the link and create it if it's missing.

-s	Runs the backup using spinning wheel progress indicators.
	Warning: in order to do this, the script puts the actual backup jobs
	in the background, so you can't kill them the normal way, with ctrl+c
	if you want to stop the backup. Keep that in mind.

	-s is useful, besides being eye candy, for letting you know that the script is
	actually doing something while it sits there working on your deletions or backups.

-U Update script manually from svn server. It will update it to where-ever you have it installed.

-v Show script version and last used information. Also shows last time you did a backup
   and which backup directory was used. This should help people like me who can't remember.

MOUNT/UMOUNT
If the device is automounted, the script will check to make sure it's path is present, and
if the script uses mount/unmount option, that contains a test as well for failed mounts.
-m Skip the mounting option. If present, the script will not use your preset mount/umount
	values, and will not try to mount anything. Only use if you have a good reason to do so.

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
Script must be run as root, the contents of rbxi.tar.bz2 should all have been unzipped
into the same directory. It doesn't matter where you put those files though, as long
as you keep them together.

Make sure to have your backup drive or partition mounted before you start script unless
you are using the auto mount/unmount options in the script. If the drive or partition is not
present, script exits with an error telling you this.

For normal useage, when you run the script, it will check root/backup partition, then ask
you if you want to run the standard backup, which either creates a new, or adds to an existing,
backup directory.

If you select the delete and backup option, the script will ask you if you are sure
you want to do that.

Once you set the primary variable values as indicated below, you shouldn't need
to change this script much, except mayby adding some exclude items to the exclude files,
or possibly adding or removing a backup option.

-------------------------------------------------------------------------
SEE THE man FILES FOR MORE INFORMATION ON HOW TO USE rdiff-backup/rsync FEATURES:
man rdiff-backup OR man rsync
Unlike most man files, these are filled with clear explanations and examples, and can
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
NOTE: for rsync, you need to use ** for recursive rules, not *. rdiff-backup is picky
about this, rsync works as you'd expect: ** is anything including /, * is a directory/file.
rdiff-backup basically only supports this:
**/usr/* will include/exclude all directories with /usr/ in their path, period
/usr/** will include/exclude everythiung in /usr
/usr/* will include/exclude files/directories one level below usr
/usr/ will include/exclude /usr if the directory has content, and not if it's empty
/usr will include/exclude /usr

-------------------------------------------------------------------------
rsync specific exclude path information
.........................................................................
For any non root backups, you need to understand that rsync starts the exclude/include path
not absolutely, but relative to the path of the thing being backed up, so for example you
would NOT use:
/home/fred/.qt
to exclude fred/.qt in the /home backup, but would rather use:
/fred/.qt
or better
/*/.qt
which would exclude all users in /home

* only stays within the directory it's in, but ** includes / as a super wildcard, so,
again, say, for /home,

**/.qt would exclude /home/fred/.qt AND /home/fred/blogs/.qt
but
/*/.qt would exclude only /home/usernames/.qt

All rules not starting with ** must start with /, otherwise you get an error.

Rules can contain either ** or *, but with **  especially, make sure to test it
well before you stay with it.

I like to start excludes with:
- /*/.qt
and include rules with
+ /*/.qt
Just to make it clear, but in the excludes files, the - is optional.

When you use either - or +, they must be followed by a space then the rule.
-------------------------------------------------------------------------
rdiff-backup specific exclude path information
.........................................................................
paths work as expected, for example
- /home/username/.qt
will exclude that directory in /home

Everything else above applies however except for the path oddities.

-------------------------------------------------------------------------
Main comments on excludes.
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

It is important to understand that rdiff-backup/rsync first accepts the + items, which means
include this in the backup, then after that you add the normal exclude case. The order
is important, if you put the include case after, it won't get included, if it's before
it will get included.

So when you have a list and you want to include one directory in a parent directory that
is excluded, the include item, with + /some/path must come first.

For more information, read: man rdiff-backup or man rsync

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

########################################################################
#### RSYNC/RDIFF-BACKUP DATA ###########################################
########################################################################
BACKUP_APP='rsync'

Set to either rdiff-backup or rsync. Can be overridden with r/R option. Default is rsync.

Change these to the true system paths if you are going to run this as a cron job
like so: RDIFF_PATH='/usr/bin/rdiff-backup'
or this: RSYNC_PATH='/usr/bin/rsync'
Use'which rdiff-backup' OR 'which rsync' (no quotes) to find the true path.
RDIFF_PATH='rdiff-backup'
RSYNC_PATH='rsync'

.........................................................................
RSYNC/RDIFF-BACKUP EXTRA ARGUMENTS

For remote, via ssh, include the ssh option
ie: RSYNC_EXTRA_OPTIONS=' --rsh=ssh '

ie: RDIFF_EXTRA_OPTIONS=' --terminal-verbosity 5 '
Only set if you want custom options for either rdiff-backup or rsync,
otherwise make null: RSYNC_EXTRA_OPTIONS=''
Defaults are below, to let you see what's going on at least the first time.
Once rsync works, remove the --dry-run and the backup will actually occur.
RSYNC_EXTRA_OPTIONS=' --dry-run -v '
RDIFF_EXTRA_OPTIONS=' --terminal-verbosity 5 '

# see man rdiff-backup for proper syntax for time. This is set to remove older
# than 60 days incremental backupsThe time  interval  is  an integer followed
# by the character s, m, h, D, W, M, or Y, indicating seconds, minutes, hours, days,
# weeks, months, or years respectively, or a number of these concatenated.
# For example, 32m  means  32  minutes, and  3W2D10h7s  means 3 weeks, 2 days, 10 hours, and 7 seconds.
RDIFF_REMOVE_TIME='60D'

########################################################################
#### BACKUP DIRECTORY PATHS AND INFORMATION ############################
########################################################################

BACKUP_DIRECTORY='/media/usbdisk'
BACKUP_DIRECTORY_1=''
BACKUP_DIRECTORY_2=''
BACKUP_DIRECTORY_3=''
BACKUP_DIRECTORY_4=''
BACKUP_DIRECTORY_5=''
BACKUP_DIRECTORY_6=''
BACKUP_DIRECTORY_7=''
BACKUP_DIRECTORY_8=''
BACKUP_DIRECTORY_9=''
BACKUP_DIRECTORY_10=''

If you are using rsync, and want a different backup directory, set this to different,
otherwise make it the same as above.
BACKUP_DIRECTORY_RS='/media/usbdisk'
BACKUP_DIRECTORY_1_RS=''
BACKUP_DIRECTORY_2_RS=''
BACKUP_DIRECTORY_3_RS=''
BACKUP_DIRECTORY_4_RS=''
BACKUP_DIRECTORY_5_RS=''
BACKUP_DIRECTORY_6_RS=''
BACKUP_DIRECTORY_7_RS=''
BACKUP_DIRECTORY_8_RS=''
BACKUP_DIRECTORY_9_RS=''
BACKUP_DIRECTORY_10_RS=''

This is the mounted location of your backup disk or partition. The default assumes
that it is a external drive with the label of 'usbdisk'. Change to your requirements.
Please make sure this does not end with a /, but just the actual partition mount directory
name.

NOTE: it is extremely bad practice to backup to the same partition that is being backed up
so if you want to do that don't look here for help or advice.

Do not use the /dev/sda1 type names, use the mounted name, like /media/sda1

.........................................................................
BACKUP_SUB_DIR='/backup'
BACKUP_SUB_DIR_1=''
BACKUP_SUB_DIR_2=''
BACKUP_SUB_DIR_3=''
BACKUP_SUB_DIR_4=''
BACKUP_SUB_DIR_5=''
BACKUP_SUB_DIR_6=''
BACKUP_SUB_DIR_7=''
BACKUP_SUB_DIR_8=''
BACKUP_SUB_DIR_9=''
BACKUP_SUB_DIR_10=''

If you are using rsync, set this path to the actual sub directory used in the main backup
directory.
BACKUP_SUB_DIR_RS='/backup-rs'
BACKUP_SUB_DIR_1_RS=''
BACKUP_SUB_DIR_2_RS=''
BACKUP_SUB_DIR_3_RS=''
BACKUP_SUB_DIR_4_RS=''
BACKUP_SUB_DIR_5_RS=''
BACKUP_SUB_DIR_6_RS=''
BACKUP_SUB_DIR_7_RS=''
BACKUP_SUB_DIR_8_RS=''
BACKUP_SUB_DIR_9_RS=''
BACKUP_SUB_DIR_10_RS=''

This is the actual directory name of where the backup will go. The way this script works
is that it will alternate by default between 2 backup directories within your backup
partition or drive.

You do not need to create these directories, the script will do it automatically.

The only requirement is that the directory name does not include a number as its last
digit, the script creates that number by itself.

In the above case, for example, the script will create a directory named backup
located in /media/usbdisk, assuming the default primary partition name is not changed.
If you changed the above value to BACKUP_LOCATION='/my-backups' the script would create
/media/usbdisk/my-backups.

########################################################################
#### SCRIPT BACKUP DESTINATION SUB DIRECTORIES #########################
########################################################################

Do not begin any of the following 4 directory names with a /, just put the name itself
you want rdiff-backup to create for that particular backup.

Important: if any one of these 4 is left blank, no backup of that component will be created.
.........................................................................
HOME_DIR='home'

This is the name of the directory that your /home partition will get backed up to inside of
the above primary backup directories.
.........................................................................
ROOT_DIR='root-hda1'

This is the name of the directory that your / [root] partition will get backed up to inside
of the above primary backup directories.
.........................................................................
These are optional backup subdirectories, if these are used the script will backup these
as well as / and /home autumatically.
Blank '' means the script does not try to back that item up
This is the backup sub directory name, not the actual data file path
DATA_1_DIR=''
DATA_2_DIR=''
DATA_3_DIR=''
DATA_4_DIR=''
DATA_5_DIR=''
DATA_6_DIR=''
DATA_7_DIR=''
DATA_8_DIR=''
DATA_9_DIR=''
DATA_10_DIR=''

If used, these are data directory names. Default is blank.

########################################################################
#### SCRIPT BACKUP DIRECTORY OUTPUT DESCRIPTIONS #######################
#####################################################################
These variables are simply what the script will print out to you when it runs.
They do not affect what actually is backed up.
.........................................................................
DATA_1_DESC='' # for example: DATA_1_DESC=' web'
DATA_2_DESC='' # for example: DATA_2_DESC=' email'
DATA_3_DESC=''
DATA_4_DESC=''
DATA_5_DESC=''
DATA_6_DESC=''
DATA_7_DESC=''
DATA_8_DESC=''
DATA_9_DESC=''
DATA_10_DESC=''

This is a one or more word description of what is being backed up. Begin it with
a space so it prints out nicely, like this: DATA_1_DESC=' my emails'

This is for DATA_x_DIR and DATA_x_PATH

.........................................................................
ROOT_DESC='/dev/hda1'

This just tells you which actual physical partition root is. Change to
your own setup. This is not a path, it's just information for you.

########################################################################
#### SYSTEM BACKUP PATH INFORMATION  ###################################
#######################################################################

These are the actual paths to be used for the backups. Whatever path  you
enter here is what will get backed up.

Note: if you leave off the ending /, rsync will create a directory of the last name in
the path, which you probably don't want.

All xx_PATH directories are the SOURCE directory, not not the destination.
.........................................................................
HOME_PATH='/home/'

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
ROOT_PATH='/'

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
DATA_1_PATH=''
DATA_2_PATH=''
DATA_3_PATH=''
DATA_4_PATH=''
DATA_5_PATH=''
DATA_6_PATH=''
DATA_7_PATH=''
DATA_8_PATH=''
DATA_9_PATH=''
DATA_10_PATH=''

If you are creating more than the default / and /home backups, use this. This
must correspond to the DATA_1_PATH item above. Set to path of item you want backed
up separately.

Note: normally, you will exclude this in either /home or / depending on where the
data is mounted. Just add this path to the relevant home-excludes.txt or root-excludes.txt
To backup the directory name but not it's contents, do this:

/data/*

in the exclude file.

If DATA_1_PATH is blank, this will not get backed up.

########################################################################
#### PARTITION MOUNT/UMOUNT ############################################
########################################################################

Note that MOUNT/UNMOUNT 1-10 are triggered by the -M option, and that
$BACKUP_DIRECTORY will be set dynamically from rdiff/rsync BACKUP_DIRECTORY set in rbxi
based on the values you used in the config file.

If you want the script to automatically mount/umount your backup drive / partition,
use this. If you don't want this, just leave the variables with a blank value.

For cron jobs, put the full system path to mount/umount in the command.
MOUNT_BU_DISK='/usr/bin/mount -L backup-disk-1 $BACKUP_DIRECTORY'
UNMOUNT_BU_DISK='/usr/bin/umount $BACKUP_DIRECTORY'
Use'which mount' OR 'which umount' (no quotes) to find the true path.

EXCEPTION: if you want to use a custom DIRECTORY from above, do it like this:
MOUNT_BU_DISK="mount -L backup-disk-1 $BACKUP_DIRECTORY_3"
UNMOUNT_BU_DISK="umount $BACKUP_DIRECTORY_3"
This will then use the directory you list. This requires double quotes, "", not single, ''.
Of course you can also just hard code in the entire thing if you want, but it's easier to
avoid errors if you just type it in time I find.

If you are just needing to change the BACKUP_DIRECTORY, just use -B <bu directory number>
instead and rbxi will change that automatically for you, but if you need to put actually
different mount/umount information, then use an alternate MOUNT/UNMOUNT number
.........................................................................
MOUNT_BU_DISK='mount -L backup-disk-1 $BACKUP_DIRECTORY'
UNMOUNT_BU_DISK='umount $BACKUP_DIRECTORY'
MOUNT_BU_DISK_1=''
UNMOUNT_BU_DISK_1=''
MOUNT_BU_DISK_2=''
UNMOUNT_BU_DISK_2=''
MOUNT_BU_DISK_3=''
UNMOUNT_BU_DISK_3=''
MOUNT_BU_DISK_4=''
UNMOUNT_BU_DISK_4=''
MOUNT_BU_DISK_5=''
UNMOUNT_BU_DISK_5=''
MOUNT_BU_DISK_6=''
UNMOUNT_BU_DISK_6=''
MOUNT_BU_DISK_7=''
UNMOUNT_BU_DISK_7=''
MOUNT_BU_DISK_8=''
UNMOUNT_BU_DISK_8=''
MOUNT_BU_DISK_9=''
UNMOUNT_BU_DISK_9=''
MOUNT_BU_DISK_10=''
UNMOUNT_BU_DISK_10=''

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

- for network disk mounting
MOUNT_BU_DISK="mount backupmachine:/dev/sdc1 $BACKUP_DIRECTORY"
.........................................................................
- Unmounting command, should not need to be changed:
UNMOUNT_BU_DISK="umount $BACKUP_DIRECTORY"

.........................................................................
Complex Example,  mount/umount 2 drives, source and destination, echoes success messages after each operation:
MOUNT_BU_DISK_10='mount -L audio-bu-1 $BACKUP_DIRECTORY && echo "Mounted backup directory $BACKUP_DIRECTORY..." && mount musicbox:/home/username/mm $DATA_10_PATH && echo "mounted source directory $DATA_10_PATH..."'
UNMOUNT_BU_DISK_10='umount $BACKUP_DIRECTORY && echo "Unmounted $BACKUP_DIRECTORY..." && umount $DATA_10_PATH && echo "Unmounted $DATA_10_PATH"'

########################################################################
#### JOB PRESETS #######################################################
########################################################################

# set with whatever arguments you want to start script with, script will restart with those options
# automatically, then run whatever job you set.
# Jobs consist of <job name> plus ':' plus <sets of rbxi options>
# sample: BACKUP_JOB_1='system-b-bu: -M 3 -S rh ' - this would use mount/umount 3, and skip home/root backup
# sample: BACKUP_JOB_2='system-c-bu: -M 2 -S rh -A 8' - this would use mount/umount 2, skip home/root backup
# and Add the normally turned off DATA_8
#
# This is triggered by the -J <1-10> option.
# syntax: 'job-display-name: <job options>'
BACKUP_JOB_1=':'
BACKUP_JOB_2=':'
BACKUP_JOB_3=':'
BACKUP_JOB_4=':'
BACKUP_JOB_5=':'
BACKUP_JOB_6=':'
BACKUP_JOB_7=':'
BACKUP_JOB_8=':'
BACKUP_JOB_9=':'
BACKUP_JOB_10=':'

.........................................................................
EXAMPLES:
BACKUP_JOB_9=' -C 9 -B 10 -M 9 '
This sets Clone job 9, using main backup destination directory 10, using Mount / Umount
set 9

BACKUP_JOB_10=' -c -B 2 -D 2 -M 2 '
This sets standard delete obsolete, autorun backup, using main backup destination directory 2,
with backup destination sub directory 2, using Mount / Umount set 2.

########################################################################
#### CLEAN / DELETE / BACKUP / SKIP ####################################
########################################################################

These are for options, used in case of cron or option run backups/deletes
if you want to hard set any option just set the variable to true.
trigger rdiff/rsync cleeanup, either older than x, or remove deleted files
B_CLEAN_OLDER='false' # -c :clean old backup files, then do backup
B_DELETE_BACKUP='false' # -d :delete old backup, then do backup
B_DO_BACKUP='false' # -b :automatic backup

.........................................................................
Skip home/root/data can be overridden by -A <r|h|number> when you are creating
a separate job (-J <number>).
B_SKIP_DATA_FULL='false' # -C [..] or -S d - skips all data partition backups
B_SKIP_DATA_1='false' # -S 1
B_SKIP_DATA_2='false' # -S 2
B_SKIP_DATA_3='false' # -S 3
B_SKIP_DATA_4='false' # -S 4
B_SKIP_DATA_5='false' # -S 5
B_SKIP_DATA_6='false' # -S 6
B_SKIP_DATA_7='false' # -S 7
B_SKIP_DATA_8='false' # -S 8
B_SKIP_DATA_9='false' # -S 9
B_SKIP_DATA_10='false' # -S 10
B_SKIP_HOME='false' # -S h
B_SKIP_ROOT='false' # -S r

########################################################################
#### MISCELLANEOUS SCRIPT OPTIONS ######################################
########################################################################

Miscellaneous script booleans
B_LOGGING='false' # -L : use logging true/false
B_SKIP_MOUNT='false' # -m :do not mount backup drive, or umount it
B_SPINNING_WHEEL='false' # -s :use spinning wheel option
B_TESTING_1='false' # -! 1 :testing flag 1
B_TESTING_2='false' # -! 2 :testing flag 2

.........................................................................
If you want rbxi to notify you of newer versions when it runs, set to true.
Default is false, no checking for latest versions.
B_UPDATE_NOTIFIER='false'

.........................................................................
Script color scheme:
0 - no colors
1 - default, for dark on light or light on dark, red warning/error, simple highlights
2 - fancy dark on light, red warning/error, more colors for questions, highlights
3 - basic light on dark, red warning, yellow error, simple colored highlights
4 - fancy light on dark, red warning, yellow error, fancy colored highlights
5 - smxi style, for light on dark, green text, otherwise same as 3
Try each one and see which works best for your terminal colors using -j <0-5>, then set it here.
COLOR_SCHEME='1' # -j [0-5]

.........................................................................
These sleep times are all in seconds.
SLEEP_TIME_SPINNER='0.5'

If you started the script with the -s option, a small spinning wheel will
run as each part of the operation is carried out. This lets you know
that something is happening, that is.

I found that setting this to a lower number tended to boost the cpu useage, but
you can play with it to see for yourself. Anything less then 0.1 would be
pointless. I find that 0.5 is fine, and doesn't consume much cpu. The lower the
number, the faster the wheel spins.

SLEEP_TIME_BACKUP='3'

Time between backup operations, make integer, number of seconds
SLEEP_TIME_BACKUP='3'
Time forced exit allows for disk write syncs before umount occurs. If umount
fails with busy error on forced or premature exit, set this integer to be higher.
SLEEP_TIME_UMOUNT='5'

########################################################################
#### FINAL STEP - DO NOT SKIP! #########################################
########################################################################

And last but not least, set this to 'true' once you are done updating all the variables here.
Failure to do this will make rbxi exit without running, since you can't run it without having
completed the user variables in the file: rbxi-data/rbxi-values
B_VARIABLES_SET='false'

########################################################################
#### END - USER DEFINED VARIABLES ######################################
########################################################################

#########################################################################
### STATIC VARIABLES, SHOULD NOT REQUIRE CHANGES
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
EXCLUDE_HOME_LIST='home-excludes.txt'
EXCLUDE_HOME_LIST='home-excludes.txt' and so on...

Do NOT change any paths here unless you change all the paths for all rbxi-data files and directory.
.........................................................................

Do not change any of these, these are simply initializing variables.

#### INITIALIZE VARIABLES TO BE SET ELSEWHERE
B_CLONE_HOME='false'
B_CLONE_ROOT='false'
B_CLONE_DATA_1='false'
B_CLONE_DATA_2='false'
B_CLONE_DATA_3='false'
B_CLONE_DATA_4='false'
B_CLONE_DATA_5='false'
B_CLONE_DATA_6='false'
B_CLONE_DATA_7='false'
B_CLONE_DATA_8='false'
B_CLONE_DATA_9='false'
B_CLONE_DATA_10='false'
B_DELETE_STATUS='false'
B_IS_CLONE='false'
HOME_BACKUP=''
ROOT_BACKUP=''
DATA_1_BACKUP=''
DATA_2_BACKUP=''
DATA_3_BACKUP=''
DATA_4_BACKUP=''
DATA_5_BACKUP=''
DATA_6_BACKUP=''
DATA_7_BACKUP=''
DATA_8_BACKUP=''
DATA_9_BACKUP=''
DATA_10_BACKUP=''
HOME_BU_COMMAND=''
ROOT_BU_COMMAND=''
DATA_1_BU_COMMAND=''
DATA_2_BU_COMMAND=''
DATA_3_BU_COMMAND=''
DATA_4_BU_COMMAND=''
DATA_5_BU_COMMAND=''
DATA_6_BU_COMMAND=''
DATA_7_BU_COMMAND=''
DATA_8_BU_COMMAND=''
DATA_9_BU_COMMAND=''
DATA_10_BU_COMMAND=''
ENDING_1='...'
ENDING_2='...'
START_TIME=$( date +%s )
BACKUP_CLEAR=''
BACKUP_DURATION=''
BACKUP_JOB_NU='none'
DELETION_TEXT=''
GET_PID=''
RSYNC_DELETE=''

#########################################################################
### FUNCTIONS - PLEASE ONLY CHANGE IF YOU CAN DO SOME BASH SCRIPTING
=========================================================================

=========================================================================
run_backup_components
............................
The only thing I could see any user wanting to change here would be adding
more primary backups. That's fairly trivial, just create the user variables for data_3, data_4
etc, then add more if that exists items like you see there.

There is no need to remove anything here by default, since if the primary backup directory
name is blank, that backup won't run.


#########################################################################
### OK, THAT'S IT, HAVE FUN!
-------------------------------------------------------------------------
