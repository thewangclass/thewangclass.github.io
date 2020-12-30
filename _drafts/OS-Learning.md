PowerShell
Get-Help <Command> -Full
ls -Force 
pwd
cd
mkdir
history
ctrl+R let's you search history or #and command name
clear
cp is copy 
wildcard 
cp *.jpg path\to\copy\to
copy directory, is -r to recurse subdirectories -Recurse -Verbose
mv is to rename and move
rm or remove doesn't use the recycle bin!
-Force
-Recurse
-Verbose

cat (dumps entire contents at once)
more (one page at a time)
    enter advances by one line
    space advances by a page
    q quits
cat -Head 10
    -Tail 10

linux version is less
    g moves to beginning
    G moves to end
    /word_search searches for a word
    q quits
    up and down arrow moves up and down
head
tail

start launches a program
    start notepad++ hello.txt would open up the hello.txt file using notepad++

linux has a lot of CLI built in text editors
    nano file_name
    

Real Powershell commands
Get-Alias ls
    This returns Get-ChildItem, which means that is the actual PowerShell command that is running

Windows: Searching within files
    search indexing, select a folder, click advanced, click file ty0pes, click index properties and file contents (this will allow you to search for things inside of files)
    sls or Select-String command and regex
        Select-String word file_name

Windows: Searching within Directories
    -Filter
    ls (optional location argument) -Recurse -Filter *.exe

Linux: Searching within Files
    grep command
    grep cow file_name
    grep cow *wildcard

Windows: Input, Output, and the Pipeline
    echo woof > dog.txt
        Creates a file called dog.txt and puts woof inside of it
        echo is alias for Write-Output
        echo prints out keyboard input to the screen
    > redirector operator, we can change where we want our standard output to go, THIS WILL EITHER CREATE THE FILE OR OVERWRITE
    >> appends information
    | operator, send output of one command to input of another command
        ex: cat words.txt | Select-String st
    All together:
        cat words.txt | Select-String st > st_words.txt
        gets all the strings with st inside words.txt and puts it inside st_words.txt
    Redirecting errors...
        rm secure_file 2> errors.txt
        all output streams are numbered: stdout is 1, stderr is 2
        redirect the standard error stream to the file instead of standard out
        can redirect standard error to $null if we don't care about error messages
        rm secure_file 2> $null
    More:
        Get-Help about_redirection

Linux: Input, Output and the Pipeline
    < standard input redirector is less than sign
        cat < file_input.txt
    /dev/null file is metaphorical blackhole, $null equivalent
        less /var/log/syslog 2> /dev/null
        filters output of error messages
    ls -la /etc | grep bluetooth   
        looks inside the /etc folder for everything with bluetooth

Users, Administrators, and Groups, Oh My!
    Computer Management Tool

Windows PowerShell Computer Management
    Get-LocalUser
    Get-LocalGroup
    Get-LocalGroupMember Administrators

Linux Computer Management
    cat /etc/group
    /etc/passwd is users on the machine

Windows: Passwords
    net user cindy *
        This causes the CLI to pause and ask you for your password hidden
    Force user to change their password on next logon
        net user victor /logonpasswordchg:yes

Linux: Passwords
    passwd cindy
        asks you type current password
    password is encrypted and store in this folder /etc/shadow that only root can see
    force user to change password at next logon
        sudo passwd -e cindy
        immediately expires the users password

Windows: Adding and Removing Users
    net user andrea * /add
        creates a new account for andrea and prompts you to type in a default password for them
    net user andrea /logonpasswordchg:yes
        flags her to immediately change her password
    combine two commands
        net user cesar pa5sw0rd /add /logonpasswordchg:yes
    net user andrea /del
        deletes account
    Remove-LocalUser cesar
        deletes account

Linux: Adding and Removing Users
    sudo useradd juan
    sudo userdel juan

Windows: File Permissions
    Access Control Lists
    Discretionary ACL
    System ACL
    Improved Change ACLs 
        icacls and file/folder name
        icacls /?

Windows: Modifying Permissions
    Right-Click; Properties; Security Tab; Edit Permissions
    icacls folder /grant 'Everyone:(OI)(CI)(R)'
        Replace Everyone w/ Authenticated Users
        /remove

Linux: Modifying Permissions
    chmod
        u+x
        u-x
        give owner executable or takes away
    multiple
        u+rx
    ugo (owner, user, everyone)
    or just user numbers   
        chmod 754
    chown changes owner
    chgrp changes group

    sudo chmod u+s
        s stands for setUID
    sudo chmod 4755
        prepend 4 stands for s
    sudo chmod g+s
    sudo chmod 2755
        prepend 2 stands for set gid (group)
    Sticky bit - a special permission bit used to allow a file to be written to by anyone but only removable by the owner or root
        /temp directory
        t bit
        sudo chmod +t
        sudo chmod 1755 1 stands for sticky bit


---
Software Distribution

Windows: Software Packages
    Installing packages via CLI
        just type in .exe name to run it

Linux: Software Packages
    dpkg -i .deb_file to install
    dpkg -r .deb_file to remove
    dpkg -l to list all files
    dpkg -l | grep atom 

Windows: Archive
    Compress-Archive -Path Folder newFolder.zip
    use 7zip

Linux:
    7zip
    7z e file.tar
        e stands for extract

Windows: Package Dependencies
    Sysinternals package 
    Find-Package sysinternals -IncludeDependencies
    Chocolatey package manager - all kinds of Windows software packages live here
    Register-PackageSource -Name chocolatey -ProviderName Chocolatey -Location http://chocolatey.org/api/v2
    Get-PackageSource

Linux: Package Dependencies
    dpkg doesn't automatically install dependencies for us
    apt does

---
Package Mangers

Windows: Package Manager
    chocolatey is a 3rd party package manager for microsoft
    sccm and puppet are configuration management tools
    Install-Package -Name sysinternals
    choco install sysinternals
    Install-Package -Name awesomesoftware -Source chocolatey

Linux:
    sudo apt install gimp
    sudo apt remove gimp
    /etc/APT/sources.list
    apt update
    apt upgrade

---
Device Software Management

Windows: Devices and Drivers
    Device Manager
    devmgmt.msc

Linux Devices and Drivers:
    Everything is considered a file, even devices
    located in /dev file
    b stands for block device
    c stands for character device
    /dev/sda or /sdb
    sd are mass storage devices
    /dev/sda, a just means something saw it first?

    To update drivers:
        some drivers are located in the kernel
        lots of hardware support is built in to the kernel
        often have kernel module - extend kernel's functionality without actually touching kernel

Windows: OS Updates

Linux: OS Updates
    uname -r
    sudo apt update
    sudo apt full-upgrade

---
Filesystem Types

NTFS
    Compatible with Windows and Linux

ext4
    Ubuntu, not compatible with Windows

FAT32
    Compatible with all
    Does not support larger than 4Gb, entire USB cannot be more than 32

Disk Anatomy
    Partitions: The piece of a disk that you can manage
    Volume: owhen you format a filesystem on a partition
    Volume is not the same a partition
    Partition Table: tells the OS how the disk is partitioned
        MBR 
            Master Boot Record
            2TB max volume size
            Primary partitions
            Only have 4 primary partitions on a disk
                Make a primary partition into an extended -> Logical
        GPT
            2TB or greater volume size
            One type of partition
            Unlimited partitions
    UEFI booting requires GPT partition

Windows: Disk Paritioning and Formatting a Filesystem
    Diskpart from CLI (command prompt)
        Diskpart
        list disk
        select disk 1
        clean
        create partition primary
        select partition 1
        active
        format FS=NTFS label=my-thumb-drive quick

Linux: Disk Paritioning and Formatting a Filesystem
    sudo parted -l
        lists the disks connected to the computer
        /dev/sda1
        /dev/sda2
    sudo parted /dev/sdb
        quit to exit
    print
    mklabel gpt
    print
    mkpart primary ext4 1MiB 5GiB
    print
    kibibyte is different from kilobyte
        kibibyte is 1024 and kilobyte is 1000
    quit
    sudo mkfs -t ext4 /dev/sdb1
    sudo parted -l

Linux: Mounting and Unmounting a Filesystem
    Need to mount the filesystem to begin interacting with the disk
    sudo parted -l
    sudo mount /dev/sdb1 /my_usb/
    sudo umount /my_usb/ OR /dev/sdb1   
    make sure you unmount before physically disconnecting the drive!
    can permenantely mount a drive
        cat /etc/fstab
        sudo blkid
        sudo nano /etc/fstab
        cd /my_usb/


Windows Swap
    VRAM
        Virtual memory is how our OS provides the physical memory available in our computer to the applications that run on the computer
        VRAM gives us the ability for our computer to use more memory than we physically hav einstalled
        If a particular page of data isn't being used by an application, it gets evicted, copied out of memory onto the hard drive
    WindowsOS uses The Memory manager
        pages saved to disk are stored in a special hidden file on the root partition of a volume called pagefile.sys
        System Properties allows you more control
            Control Panel - Systems and Security - System - Advanced system settings - Advanced - Performance Settings - Advanced 

Linux: Swap
    dedicated area of the hard drive used for virtual memory is known as swap space
    sudo parted /dev/sdb
    print
    mkpart primary linux-swap 5GiB 100%
    print
    quit
    sudo mkswap /dev/sdb2
    sudo swapon /dev/sdb2
    automatically mount , just do same as before

Windows: Files
    NTFS uses MFT, the Master File Table, to keep track of file metadata
        Typically 1-1 with number of files, although can be more MFT
        Identifier known as file record number
    Symbolic Links
        like shortcuts but at the file system level
        treated like a substitute by the OS 
        right click, create shortcut, rename file -> renaming breaks the link
    mklink file-1-symlink file-1.txt
    Hard Link
        points to the linked file record number, not the actual file
    mklink /H file-1-hardlink file-1.txt
        can change the file name and it'd still work

Linux: Files
    Inode is similar to NTFS MFT records
        metadata
    softlink like symlink
        ln -s important_file important_file_softlink
    hardlink like hardlink
        don't point to the file, point to an inode stored on an inode table
        ln important_file important_file_softlink
        hardlinks are great if you need to have the same file stored in different places, but you don't want to take up any additional space on the volume (think of it as pointing to a memory address)

Windows: Disk Usage
    there's a CLI tool
        https://support.microsoft.com/en-us/help/181701/how-to-start-disk-cleanup-by-using-the-command-line

Linux: 
    du -h command
        disk usage, defaults to current
        -h flag gives data measurements in human readable form
    df -h command
        disk free

Windows: Filesystem Repair
    fsutil repair query C:
    NTFS check disk utility if really bad sectors, disk failures, etc
    chkdsk -F D:

Linux: Filesystem Repair
    sudo fsck /dev/sda
        MAKE SURE IT IS UNMOUNTED, or else it will damage it
    



---
---
---
Life of a Process

Windows: taskkill utility
    give it the PID: taskkill /pid 5856


---
Managing Processes

Windows: Reading Process Information
    tasklist
    Get-Process
        https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-process?view=powershell-5.1

Linux: Reading Process Information
    ps -x
        R: Running
        T: stopped
        S: interruptible sleep
    ps -ef
        -e all processes, even by other users
        -f full details
    ps -ef | grep Chrome   
        search through output for Chrome
    ls -l /proc

Windows: Signals
    signal interrupt
    SIGINT
    ctrl+c in command prompt

Linux: Signals
    begin with sig
    can use sigint to interrupt
    ctrl+c
    
Windows: Managing Processes
    Process Explorer tool - download via microsoft (in my downloads)
    https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer

Linux: Managing Processes
    SIGTERM
    kill process
    gives it time to give up resources/clean up files that are used
    kill PID
    SIGKILL vs SIGTERM
        SIGKILL doesn't give it time to clean up
    kill -SIGKILL
        last resort
    SIGTSTP
        put process in suspended state
        kill -TSTP
        stop resource allocation, 
        ctrl+z
        SIGCONT
        kill -CONT 
            this will continue the process

Mobile App Management
--- 
Process Utilization

Windows: Resource Monitoring
    Resource Monitor tool
    Get-Process | Sort CPU -descending | Select -first 3 -Property ID ProcessName CPU

Linux: Resource Monitoring
    top
        shows top processes using most resources on machine
        q to quit
    uptime
        system load average: 1, 5, and 15 min averages
    lsof   
        list open files/processes


---
---
---
Remote Access

Remote Connection and SSH
    need account on remote computer and ip address
    ssh cindy@ipaddress

Remote Connections on Windows
    use putty GUI
    or powershell
        putty.exe -ssh user@ip port#
    also comes with plink that comes with putty
    to enable remote connections      
        my pc; right click properties; remote settings; remote desktop
    can use remote desktop client to connect to the computer from anywhere
        mstsc.exe
        or remote desktop connection
    can also launch remote desktop from command line if you want to specify more parameters (like admin credential)

Remote Connection File Transfer on Linux
    secure copy (scp) utilizes ssh to transfer data
    scp /filelocationpath ipwanttocopyfileto

Remote Connection File Transfer on Windows
    putty supports scp
    putty secure copy client
    pscp.exe
    pscp.exe /filelocationpath ipwanttocopyto
    shared folders
        right click; share with option; pick specific people
        to access: this pc; computer tab; map network drive option allows you to map the folder directly to your computer
        can share from command line using net share command (same as GUI)
    net share ShareMe=filelocation /grant:everyone full
        can also list currently shared folders on the computer w/o any arguments

---
Virtualization

---
Logging

System Monitoring
    typically a system in the background that constantly writes events to logs

The Windows Event Viewer
    eventvwr.msc or event viewer
    create custom view to create own filters

Linux Logs
    stored in /var/log directory   
        /var stands for variable, for files that are constantly changing
    auth.log
        authorization and security-logs
    kern.log
        kernel messages
    dmesg 
        system startup messages
    there are log files that combine the information of other log files
        typically very large h/o
        /var/log/syslog
    log rotation for storage space
        logrotate
        change settings to not delete logs that are x number of days old
    centralized logging if multiple devices


Working with Logs
    try to narrow down what the problem is to find the log category you're looking for
    grep error
    tail -f /var/log/syslog
        this forces it to stay open and keep running
        make a change, you'll see more logs pop up

--- 
Operating System Deployment

Imaging Software
    disk cloning tool
        clonezilla
        symantec ghost
    external hard drive dock
    linux command line tool dd used to clone a drive
        make sure drive unmounted
        sudo dd if=/dev/sdd of=filepath bs=100M
    can request images directly from the network
    can also use your own images (use automation to get going)

Operating Systems Deployment Methods

Mobile Device Resetting and Imaging

Interview Role Play: Operating Systems
