---
Week 1
---
What is SysAdmin?
    server: software or a machine that provides services to other software/machines
    kvm switch: keyboard/video/mouse
        can use this to connect multiple computers to and control them using one keyboard/mouse/monitor
    
---
SysAdmin Tasks

Organizational Policies:
    Should users be allowed to install software?
    Should users hve complex password requirements?
    Should users be allowed to view non work related websites?
    If you hand a company phone to an employee, should you set a device password?

User and Hardware Provisioning
    When was it built?
    When was it first used?
    Did the org buy it brand new or was it used?
    Who maintained it before?
    How many users have used it in the current organization?
    What happens to this machine if someone needs a new one?

Router Maintenance
    Batch updates


---
Applying Changes

With Great Power Comes Great Responsibility
    Avoid using administrator rights fot tasks that don't require them
    Document!
        Linux: script ; use it to record a group of commands as they're being issued along with their output
        Windows PowerShell equivalent: Start-Transcript
        recordMyDesktop tool
    script

    In the case of script, you can call it like this:

            script session.log

    This will write the contents of your session to the session.log file. When you want to stop recording, you can write exit or press Ctrl-D.  The generated file will be in ANSI format which includes the colors that were displayed on screen.  In order to read them, you can use commands like ansi2txt or ansi2html to convert it to plain text or HTML respectively.

    Start-Transcript

    In the case of Start-Transcript, you can call it like this:

                Start-Transcript -Path C:\Transcript.txt

    This will write the contents of the session to C:\Transcript.txt. When you want to stop recording you need to call Stop-Transcript. The file created is a plain text file where the commands executed and their outputs are stored.

    Recording Graphical Sessions

    Performing system administration actions through a Graphical user interface is less common (as it’s harder to automate and to perform remotely), but it’s still something that may happen sometimes.

    If you are going to be performing an action that needs to be done graphically and you can document what you are doing, you can use a specialized tool like recordMyDesktop for Linux, or general video tools like OBS or VLC.

Never Test in Production

Fixing Things the Right Way
    Make sure you can recreate the error as you'll want to test your solution to make sure the problem is gone after you apply the fix
    Document!

---
Week 2 Network and Infrastructure Services
---
Intro to IT Infrastructure Services

Types of IT Infrastructure Services
    IaaS
    PaaS
    SaaS
    NaaS Networking
    DaaS

Virtualization
    cheaper to run services virtualized!

Remote Access Revisited
    Linux: OpenSSH is most popular 
    Remember: need to have SSH client on client machine, and SSH server on other machine
    client: sudo apt-get install openssh-client
    server: sudo apt-get install openssh-server
    ssh devan@ipaddress
    will ask for password

Network Services

FTP,SFTP,TFTP
    File Transfer Protocol, auth, no encryption
    Secure FTP, auth, encryption
    Trivial FTP, no auth, no encryption

NTP
    Kerberos and other important things rely on this

Network Support Services Revisited  
    Intranet
        only accessible on company network, an internal network
    proxy server - acts as an intermediary between a company's network and the internet

DNS for Web Servers

DNS for Internal Networks
    map internal computers to IP addresses
    /etc/hosts
    first checks local host files, then local DNS servers
    setup a local DNS server that contains all the computer names mapped to their IP addresses
        then change network settings so all computers to use that as a DNS server instead of one given by ISP
    Bind/powerdns

DHCP
    Have them working together so when DHCP gives an address, DNS logs it

Troubleshooting Network Services: Unable to Resolve a Hostname or Domain Name
    check actually connected: ping a website that you know the ip address of
    check dns: nslookup website
    /etc/hosts


Managing System Services

What do Services Look Like in Action
    daemons/services: the program doesn't need to interact with a user through the GUI or the CLI to provide the necessary service
    Services are usually configured to start when the machine boots, so that if there is a power outage or a similar event that causes the machine to reboot, you won't need a system administrator to manually restart the service

Managing Services in Linux
    NTP
    service ntp status
    sudo date -s "date" 
        if outside 120ms threshold, ntp will not adjust the time to reflect actual time
    can restart the service to make it catchup
        sudo service ntp stop
        sudo service ntp start
        date
    sudo service ntp restart   
        does a stop then a start


Managing Services in Windows
    Windows Update service
    Get-Service wuauserv
    Get-Service wuauserv | Format-List
    need to run as admin
        Stop-Service wuauserv
        Get-Service wuauserv
        Start-Service wuauserv
    Get-Service
    search services for services console


Configuring Services in Linux
    config files located in the /etc directory
    sudo apt install vsftpd
        service is already running
        service vsftpd status to confirm
    lftp localhost
        an ftp client program that allows us to connect to an ftp server
        ls
        exit
    /etc/vsftpd.conf to allow anon 
    sudo vim /etc/vsftpd.conf
        anonymous_enable to yes
    most services read their config when they start -> need to reload
        ongoing connections aren't interrupted, new connections will use a new configuration
    sudo service vsftpd reload
    lftp localhost

Configuring Services in Windows
    Internet Information Services
        Windows Control Panel - Turn Window features on and off - Add roles and features wizard - Server Roles - Web Server (IIS) - Install
    New option on server manager: IIS
        Right Click, select Internet Information Services Manager
        Select Sites
        Browser: Go to localhost
        Get a sample site, copy and paste to inetpub (default used)
        Enable in IIS Console
            Right click list of websites, select add website
            Find the file
            Choose 8080 or any port
            localhost:8080


Configuring Network Services

Configuring DNS with DNSmasq
    a program that provides DNS, DHCP, TFTP and PXE services in a simple package
    sudo apt install dnsmasq
    dig command lets us query DNS servers and see their answers
        dig www.example.com @localhost
            the part after the @ specifies what DNS to use
        run in debug mode to see behind the scenes
    sudo service dnsmasq stop
    sudo dnsmasq -d -q
        debug mode
        log queries
    use dig command again
    use it again
        you'll see it uses the cached query instead of querying the dns server
    cat myhosts.txt 
        ipaddresses  sitewanttohost
            ex: 111.1.1.111 oxygen.local
        sudo dnsmasq -d -q -H myhosts.txt
    dig hydrogen.local @localhost

Configuring DHCP with DNSmasq
    a DHCP server is usually set up on a machine or a device that has a STATIC IP addresses configured to the network interface which is being used to serve the DHCP queries
    eth_srv is configured to be the DHCP server's interface
    ip address show eth_srv
    eth_cli: the interface we'll use to simulate a client requesting an address using DHCP
    ip address show eth_cli
    cat dhcp.conf
    sudo dnsmasq -d -q -C dhcp.conf
    use second terminal to run dhcpclient
        sudo dhclient -i eth_cli -v
    ip address show eth_cli
    dig @localhost instance-1.example


---
Week 3 - Software and Platform Service
---

Software Services

Configurating Communication Services
    Internet Channel Relay/IRC
        Uses client/server model
        Free
    HipChat/Slack
        Cost
    OpenIM Protocols
        XMPP (Extensible Messaging and Presence protocol)   
            IoT uses this
        Ex: Pidgin/Padium
    Reading:
        https://zapier.com/blog/best-team-chat-app/
        https://jabber.at/clients/?os=any

Configurating Email Services
    Need a Domain Name setup for your company that you can use as your email domain
    Run own managed server - email service on a server, create a DNS record for your mail server
        MX mail exchange record
        Get email to work
        Protect email from spam
        Filter viruses
        More
    Use an email service provider
        GSuite
    Need to know email protocols when setting up accounts
        POP3 - Downloads emails from server to your local device
            To retrieve - can only view on one device
                If need to keep email storage under a quota
                Good for privacy - only one device 
                Storage + security
        IMAP - Download emails from server to multiple devices, keeps email on the server
        SMTP - Only protocol for sending email
    Reading:
        https://www.digitalocean.com/community/tutorials/why-you-may-not-want-to-run-your-own-mail-server
        https://blog.servermania.com/what-protocols-send-receive-email-with-the-mail-server/

Configuring User Productivity Services
    Software used as a consumer is not the same as software used as a business
    Licenses
    Cloud based?

Configuring Security Services
    To enable TLS, you need to get a digital certificate of trust from a CA saying it trusts that you control the web server and you are who you say you are



File Services
    Cloud Storage Providers Reading: https://www.cloudwards.net/comparison/

Network File Storage
    NFS - Network File System - A protocol that enables files to be shared over a network.
        FAT system is compatible on all major OS. 
        Easiest way to setup an NFS server is using a Linux environment.
        Have NFS service running in the background of the server. 
    Samba - similar to NFS, all major OS can use, works better w/ Windows OS, also includes other services (printer) that can be integrated
    SMB - a protocol Samba uses
    Affordable: use a Network Attached Storage
        Computers optimized for file storage. OS is stripped down in order to just serve files over a network. Lots of storage space.
    Reading:    
        NTFS: www.linuxfromscratch.org/blfs/view/cvs/basicnet/nfs-utils.html
        SMB: https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831795(v=ws.11)?redirectedfrom=MSDN


Configurating Print Services
    Setting up printers depends on how many printers you have and how many people are in your company.
    To setup a print server, you need to install a print service on a server.
    Linux: CUPS (Common UNIX Printing System)
    Windows: Print and Document Services
    Need to add the printer to the client machine

Reading: 
Supplemental Reading for Print Services
Printers

Sometimes you just need to create a hard copy of something on a computer. You need to be able to pass it around, mark it up, or store something as a physical copy. This is where a printer comes in! Printers work in a lot of different ways. In each case, the printer uses some type of printing technology to apply an image to a printing substrate such as paper, plastic, cloth, or just about any sort of surface you can imagine!
Printer technologies

Over time, many types of printing technologies have been developed. Here are some of the most common types:

Inkjet printersuse arrays of very small nozzles to spray ink onto the printing substrate. These are very versatile printers that can print onto a lot of different surfaces.

    https://en.wikipedia.org/wiki/Inkjet_printing
    https://computer.howstuffworks.com/inkjet-printer.htm

Laser printersuse a laser to draw an image in static electricity on a photosensitive drum. The statically charged image on the drum attracts a powdered pigment called toner, which is transferred onto the paper and fused in place!

    https://en.wikipedia.org/wiki/Laser_printing
    https://computer.howstuffworks.com/laser-printer.htm

Impact printerswork sort of like a typewriter. A dot-matrix printer, for example, has an array of small pins that press against the paper through an inked ribbon. Dot-matrix printers used to be very common, but now are only used in special situations. One example of this is when you need to print on carbon (or carbon-less) copy paper.

    https://en.wikipedia.org/wiki/Dot_matrix_printer

Thermal printersapply heat to special thermochromatic paper. Thermochromatic paper changes color when it is heated, so thermal printers don't require any ink! Thermal printers are very commonly used as receipt printers.

    https://en.wikipedia.org/wiki/Thermal_printing

3D printersdon't apply an image to a substrate. 3D printers slowly layer small amounts of material at a time to create 3-dimensional objects! There are a lot of types of 3D printing technologies, and you need not only drivers, but other special software to build the instructions for your specific 3D printer.

    https://en.wikipedia.org/wiki/3D_printing
    https://en.wikipedia.org/wiki/3D_printing_processes
    https://3dinsider.com/3d-printer-types/

Viewing your printers

To see what printers are already installed in your operating system, navigate to the OS's printer settings. You can also add new printers, and manage existing printers from there.

    In Windows, you will go to one of two places, depending on the version of Windows that installed. You will go to either Settings > Devices > Printers & Scanners, or to Control Panel > Printers and Devices.
    In MacOS, navigate to System Preferences > Printers & Scanners.
    There are a lot of different utilities for configuring printer settings in Linux. Take a look at the documentation for your version of Linux to be sure. Just as an example, for one common distribution of Linux, Ubuntu, you will navigate to Activities > Printers.

Each printer in your OS has a print queue, or print spool. If you send multiple print jobs to a printer, those jobs will line up in the queue to be handled, one at a time. Print jobs can be reordered or cancelled while they are in the print queue.

    Windows - View the print queue
    MacOS - Use the Dock on your Mac to check on a printer or print job
    Ubuntu - Cancel, pause or release a print job

Your operating system will have a default printer. If you only have one printer, then that will be the default printer. If you have multiple printers configured, then you can select one to be used, well, by default!

    Windows - How to set a default printer in Windows 10
    MacOS - Change the default printer or a printer’s name on your Mac
    Ubuntu - Set the default printer

Installing a printer

Printers can be pretty complicated devices, with lots of settings. There are dozens of common printer brands and thousands of printer models. Your operating system has a printer service, and knows how to talk to many printers, but it might not know how to talk to your printer. Operating systems have generic printer device drivers that will work for many common styles of printers. Beyond this, major operating systems will also understand how to search catalogs of device drivers in order to find the correct driver for a given printer. If your operating system does not automatically locate a driver for the printer you are trying to install, then the best place to look is on the printer manufacturer's support website. Remember, device drivers are specific to your operating system, so be sure to use the correct drivers for your OS.

    Windows - How to install a printer in Windows 10
    MacOS - How to add a printer on your Mac
    Use your Mac to print to a printer connected to a Windows computer
    Ubuntu - Printing

One thing you may notice when you are looking at printer device drivers is that some printers can speak more than one page description language. The most common of these languages are PostScript (PS), and Printer Command Language (PCL). Some printers will work better with one language than another. Most of the time, whatever is default or recommended by the printer manufacturer is what you should go with. Sometimes, the applications that you are printing from will prefer one language over another. If your printer supports multiple languages and it is failing to print certain documents, or failing to print from certain applications, you might try a different language.
Virtual Printers

What do you do if there is an important document that you want to save, but you don't need a paper copy? You can use a virtual printer. A virtual printer is a printer driver that looks like a real printer to the operating system, but instead of printing print jobs onto paper, it creates a file! Virtual printers have names like "Print to PDF", or "Print to File". You can use virtual printers to create documents like PDFsor XPS files, or just about any type of image file!

    Microsoft XPS Document Writer
    Save a document as a PDF on Mac
    Ubuntu - Print to file

Printer Sharing

What if you have a printer attached to your computer, and you want to share that printer with someone who is using a different computer? You can! You can share your printer! When you share your printer, you are making it available to other computers as a shared printer. With a shared printer, other computers will send print jobs across the network to the computer that is attached to the printer. Take a look at these instructions on how to share your printer, and connect to the shared printer:

    Windows - How to share your network printer
    MacOS - How to share your printer on Mac
    MacOS - How to add a printer on Mac

Network Printers

Some printers can be directly attached to the network without having to be shared by a computer's operating system. These are standalone network printers. You can add network printers to your computers in a very similar way as a shared printer:

    Windows - How to install a printer in Windows 10
    MacOS - How to add a printer on Mac
    Network printing from Ubuntu

Watch out! Some network printers contain hard drives or other storage that are used to hold jobs in a print queue. This storage can end up holding on to some pretty sensitive information! Make sure to control access to this storage. Destroy the storage or securely delete any data from this storage before servicing, selling, or disposing of a network printer!
Print Servers

What if you have just a few printers, and a several people who need to share those printers? You might need a print server! Print servers work similarly to a local printer share, but on a larger scale. They can accept many print jobs at once, and will queue or spool the print jobs so they can be processed one at a time by the printer(s).

    Print and Document Services Overview
    Ubuntu - CUPS Print Server


Platform Services
    - Platform services provide a platform for developers to completely build and deploy software applications, without having to deal with OS maintenance, server hardware, networking or other services that are needed to use the platform tools
    - A web server that we deploy our web apps to or the development software that we use to code our applications are examples
    - Apache HTTP Server is free and open source, responsible for a large percentage of web pages on the internet
        Install on linux and can run
        sudo apt-get install apache2 -y
        can now start hosting web content
        127.0.0.1 or localhost refers to the machine you're on
        enter in web browser - will set the default page
        to replace - just navigate to where it is stored and replace
        need to use DNS to let the world know our web server exists
    - Reading: https://stackshare.io/stackups/apache-httpd-vs-microsoft-iis-vs-nginx

Database Server
    - Need a web server that serves web pages that clients have requested
    - May also need to store information - into a db!
    - mySQL and postgreSQL
    - Reading:
        https://www.bls.gov/ooh/computer-and-information-technology/database-administrators.htm
        https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems


Troubleshooting Platform Services
    -Reading:
        Chrome DevTools: https://developers.google.com/web/tools/chrome-devtools
        HTTP Status Codes: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes

Managing Cloud Resources
    Load balancer - when there's a request for your website, a different VM will be picked to serve the response
        Can be configured to spin out more VMs when there are lots of queries and shut them down when queries goes down
        Known as autoscaling - good because owner only pays for cost of the machine that are in use at any given time

Reading:
If you are considering hosting some services in the Cloud, you’ll need to learn what the different terms used to configure the services mean.

When deploying a service to the cloud, you will typically create a number of virtual machines that will be the servers in charge of hosting your service. In the usual case, you would start by creating a single machine that will run the service, creating the configuration associated with the machine, verifying that it works, and then turning this into a template that can be used for the creation of many machines as needed.

In order to do this, you’ll make use of both Autoscaling and Load Balancing.  Autoscaling means being able to automatically create new instances when the load increases and automatically turn them down when the load decreases.  In order for this to be possible, you need to ensure that your instances can be completely configured automatically, and that there’s no data being kept in the instances themselves (data can be stored in a database, or in separate drives).

Load Balancing means distributing the load among many servers.  There’s different approaches to doing load balancing, but the main concept is that there’s a load balancing service that will route traffic to the servers in a way that they each get to serve a portion of users, without the users realizing that they are connecting to different machines.  In other words, the users will access a single address (e.g. http://www.example.com), which can be served by different servers, in different parts of the world, without the users having to care about that.

Once you have your service set up to scale automatically and balance the load, you’ll want to also setup Monitoring and Alerting for it.  Monitoring means checking that the service is healthy, that it’s responding to queries as expected and not generating unusual errors.  Alerting means sending alerts when things don’t happen as expected.

For a simple service, you might go with the monitoring that is already built in by the cloud provider, which will allow you to check that your instance is healthy, but is likely not going to go into much detail as to whether the content is being served correctly.  If your service is more complex, you might want to invest more time into making it possible to monitor additional parameters of your service.

Depending on the specific service you are deploying, there might be more concepts that you need to understand before you can actually do it.  We recommend reading the documentation offered by the cloud provider you have chosen to figure out what you need to do.

Here are some links with more information from some of the biggest cloud providers:

    https://azure.microsoft.com/en-us/overview/what-is-cloud-computing/
    https://aws.amazon.com/getting-started/
    https://cloud.google.com/docs/overview/

    https://cloud.google.com/docs/overview/

---
Week 4 Directory Services
---
Directory Server
    - Contains a lookup service that provides mapping between network resources and their network addresses
        Like a phone book
    - Useful for organizing data and making is seaerchable for an organization
        OU - organizational units

Implementing Directory Services
    - DAP and LDAP and DSP and DISP and DOP
    - Open standards, so lots of vendors for them
    - Microsoft AD Active Directory
    - OpenLDAP

LDAP Authenticationc
    - Can make some directories public and others private
    - Add/remove/modify
        - Also a bind operation
    - Three ways to authenticate
        Anonymous: Not actually authenticating. Anyone could potentially access that directory
        Simple: Directory name and password sent in plaintext
        SASL - Requires client and directory server to authenticate using some method - Kerberos is a common way


Active Directory
    Active Directory Administration Center (ADAC)

    Policies: Not meant to be changed, even by local admins
    Preferences: A template, local user can change

Group Policy Management Console
    Server Manager - Tools Menu
    !!! Make backup before making changes!


---
Week 5 Data Recovery & Backups
---
Rsync isn't explicitly a backup tool, but it's very commonly used as one. It's a file transfer utility that's designed to efficiently transfer and synchronize files between locations or computers. Rsync supports compression and you can use SSH to securely transfer data over a network.
Using SSH, it can also synchronize files between remote machines making it super useful for simple automated backups

iOS: Time Machine
Micrsofot: Backup and Restore



---
Week 6
---
Question 1

Learning Goals:

    Use the systems administration concepts you learned in the course to provide technical improvements to current processes.
    Implement solutions based on an organization’s restrictions, like financial resources, number of users, etc.

Overview: You’ll take what you learned in the System Administration and IT Infrastructure Services course and apply that knowledge to real-world situations.

Assignment: For this writing project, you’ll be presented with three scenarios for different companies. You’ll be doing the systems administration for each company’s IT infrastructure. For each scenario, present improvements to processes based on the company’s needs and current restrictions. There’s no right or wrong answer to your consultation, but your responses should explain the problem, the improvement, and the rationale behind them. Please write a 200-400 word process review for each company presented to you.

Scenario 1: 

You’re doing systems administration work for Network Funtime Company. Evaluate their current IT infrastructure needs and limitations, then provide at least five process improvements and rationale behind those improvements. Write a 200-400 word process review for this consultation. Remember, there’s no right or wrong answer, but make sure to provide your reasoning.

Software Company:

Network Funtime Company is a small company that builds open-source software. The company is made up software engineers, a few designers, one person in Human Resources (HR), and a small sales team. Altogether, there are 100 employees. They recently hired you as a system administrator to come in and become their IT department.

When a new person is hired on, the HR person purchases a laptop for them to do their work. The HR representative is unfamiliar with what type of hardware is out there; if a new employee requests a laptop, the HR person will purchase the cheapest option for a laptop online. Because of this, almost everyone has a different laptop model. The company doesn’t have too much revenue to spend, so they don’t order laptops until someone gets hired at the company. This leads to a few days of wait time from when someone starts to when they can actually work on a laptop.

The company doesn’t label their computers with anything, so if a computer is missing or stolen, there’s no way to audit it. There’s no inventory system to keep track of what’s currently in the fleet.

Once a computer is purchased, the HR person hands it to the new employee to set up. Software engineers that use Linux have to find a USB drive and add their preferred distribution to the laptop. Anytime someone needs something from HR -- whether it’s office related or tech related -- they email the HR representative directly.

When a new employee gets a machine, they’re given logins to use cloud services. They get a personal orientation with HR to make sure they can login. This requires the HR person to block off a few hours for every new employee. If an employee forgets the login to their machine, they have no way to retrieve a password and they have to reimagine their machine. Employees don’t have a strict password requirement to set for their computers.

The company currently has many of their services in the cloud, such as email, word processors, spreadsheet applications, etc. They also use the application, Slack, for instant communication.


---
Scenario 2:

You’re doing systems administration work for W.D. Widgets. Evaluate their current IT infrastructure needs and limitations, then provide at least five process improvements and rationale behind those improvements. Please write a 200-400 word process review for this consultation. Remember, there’s no right or wrong answer, but make sure to provide your reasoning.

Sales Company:

W.D. Widgets is a small company that sells widgets. They’re mostly made up of salespeople who work with lots of clients. You’ve taken over as the sole IT person for this company of 80-100 people.

When HR tells you to provision a machine for a new employee, you order the hardware directly from a business vendor. You keep one or two machines in stock, in case of emergency. The users receive a username that you generate for them. You then give them an orientation on how to login when they start. You currently manage all of your machines using Windows Active Directory. The company uses only Windows computers. When a new computer is provisioned, you have to install lots of sales-specific applications manually onto every machine. This takes a few hours of your time for each machine. When someone has an IT-related request, they email you directly to help them.

Almost all software is kept in-house, meaning that you’re responsible for the email server, local machine software, and instant messenger. None of the company’s services are kept on the cloud.

Customer data is stored on a single file server. When a new salesperson starts, you also map this file server onto their local machine, so that they can access it like a directory. Whoever creates a folder on this server owns that folder and everything in it. There are no backups to this critical customer data. If a user deletes something, it may be lost for everyone.

The company generates a lot of revenue and is rapidly growing. They’re expecting to hire hundreds of new employees in the next year or so, and you may not be able to scale your operations at the pace you’re working.
---
You’re doing systems administration work for Dewgood. Evaluate their current IT infrastructure needs and limitations, then provide at least five process improvements and rationale behind those improvements. Please write a 200-400 word process review for this consultation. Remember, there’s no right or wrong answer, but make sure to provide your reasoning.

Non-profit Company:

Dewgood is a small, local non-profit company of 50 employees. They hired you as the sole IT person in the company. The HR person tells you when they need a new computer for an employee. Currently, computers are purchased directly in a physical store on the day that an employee is hired. This is due to budget reasons, as they can’t keep extra stock in the store.

The company has a single server with multiple services on it, a file server, and email. They don’t currently have a messaging system in place. When a new employee is hired, you have to do an orientation with them for login. You’re also responsible for installing all the software they need on their machine, and mapping the file server to their computer. The computers are managed through Windows Active Directory. When an employee leaves, they’re currently not disabled in the directory service.

The company uses an open-source ticketing system to handle all internal requests as well as external non-profit requests. But the ticketing system is confusing and difficult to use, so lots of the employees reach out to you directly to figure out how to do things. In fact, so many things are difficult to find that employees typically ask around when they have a question.

There are nightly backups in place of the file server. You store this information on a disk backup and take it home with you everyday to keep it safe in case something happens onsite. There’s also a small company website that’s hosted on the single server at the company. This website is a single html page that explains the mission of the company and provides contact information. The website has gone down many times, and no one knows what to do when it happens.
---


There are two goals outlined for this project:

    Use the systems administration concepts you learned in the course to provide technical improvements to current processes.
    Implement solutions based on an organization’s restrictions, like financial resources, number of users, etc.

Here is the first question:

You’re doing systems administration work for Network Funtime Company. Evaluate their current IT infrastructure needs and limitations, then provide at least five process improvements and rationale behind those improvements. Write a 200-400 word process review for this consultation. Remember, there’s no right or wrong answer, but make sure to provide your reasoning to be eligible for full credit.

Software Company:

Network Funtime Company is a small company that builds open-source software. The company is made up software engineers, a few designers, one person in Human Resources (HR), and a small sales team. Altogether, there are 100 employees. They recently hired you as a system administrator to come in and become their IT department.

When a new person is hired on, the HR person purchases a laptop for them to do their work. The HR representative is unfamiliar with what type of hardware is out there; if a new employee requests a laptop, the HR person will purchase the cheapest option for a laptop online. Because of this, almost everyone has a different laptop model. The company doesn’t have too much revenue to spend, so they don’t order laptops until someone gets hired at the company. This leads to a few days of wait time from when someone starts to when they can actually work on a laptop.

The company doesn’t label their computers with anything, so if a computer is missing or stolen, there’s no way to audit it. There’s no inventory system to keep track of what’s currently in the fleet.

Once a computer is purchased, the HR person hands it to the new employee to set up. Software engineers that use Linux have to find a USB drive and add their preferred distribution to the laptop. Anytime someone needs something from HR — whether it’s office related or tech related — they email the HR representative directly.

When a new employee gets a machine, they’re given logins to use cloud services. They get a personal orientation with HR to make sure they can login. This requires the HR person to block off a few hours for every new employee. If an employee forgets the login to their machine, they have no way to retrieve a password and they have to reimage their machine. Employees don’t have a strict password requirement to set for their computers.

The company currently has many of their services in the cloud, such as email, word processors, spreadsheet applications, etc. They also use the application, Slack, for instant communication.

My Response:

This company is in desperate need of infrastructure management.
First, purchasing computers from the internet when they are needed is a very inefficient process. This results in lost time and productivity. In a company this size, having at least three spare laptops ready to go into service would help everyone, especially in emergencies. Also, buying the cheapest laptop is always a bad idea. Company assets need to be documented and inventoried, including all computers, printers, and networking gear.

Even if the entire company only uses cloud services, I think they would benefit from having a domain controller and some kind of central management service, such as OpenLDAP to track computers connected to the domain. This could be used to provision software to any computers joined to the domain, and would implement some kind of strict password management.
This company is large enough to justify the time and expense to set this up.

Question Two:

Sales Company:

W.D. Widgets is a small company that sells widgets. They’re mostly made up of salespeople who work with lots of clients. You’ve taken over as the sole IT person for this company of 80-100 people.

When HR tells you to provision a machine for a new employee, you order the hardware directly from a business vendor. You keep one or two machines in stock, in case of emergency. The users receive a username that you generate for them. You then give them an orientation on how to login when they start. You currently manage all of your machines using Windows Active Directory. The company uses only Windows computers. When a new computer is provisioned, you have to install lots of sales-specific applications manually onto every machine. This takes a few hours of your time for each machine. When someone has an IT-related request, they email you directly to help them.

Almost all software is kept in-house, meaning that you’re responsible for the email server, local machine software, and instant messenger. None of the company’s services are kept on the cloud.

Customer data is stored on a single file server. When a new salesperson starts, you also map this file server onto their local machine, so that they can access it like a directory. Whoever creates a folder on this server owns that folder and everything in it. There are no backups to this critical customer data. If a user deletes something, it may be lost for everyone.

The company generates a lot of revenue and is rapidly growing. They’re expecting to hire hundreds of new employees in the next year or so, and you may not be able to scale your operations at the pace you’re working.

My Response:

This company would probably benefit from setting up GPOs and assigning roles to users. This would allow you to push software out to machines based on what role they have been assigned in Active Directory. This would keep you from having to install software manually on any new machine.

You should be creating backups of the fileserver both locally and through a cloud service. It may also be a good idea to re-assign file permissions and security groups, so that only domain admins have full control of files and directories. All users could be assigned permissions based on roles in the company, and corresponding GPOs.

Finally, you should hire an assistant now, before the workload increases beyond what you can handle alone. You may want to hire someone to take on a defined part of the IT responsibilities, such as low-level helpdesk/support tech, or you could hire a “counterpart” who would be able to also do everything that you do.

Question 3:

Non-profit Company:

Dewgood is a small, local non-profit company of 50 employees. They hired you as the sole IT person in the company. The HR person tells you when they need a new computer for an employee. Currently, computers are purchased directly in a physical store on the day that an employee is hired. This is due to budget reasons, as they can’t keep extra stock in the store.

The company has a single server with multiple services on it, a file server, and email. They don’t currently have a messaging system in place. When a new employee is hired, you have to do an orientation with them for login. You’re also responsible for installing all the software they need on their machine, and mapping the file server to their computer. The computers are managed through Windows Active Directory. When an employee leaves, they’re currently not disabled in the directory service.

The company uses an open-source ticketing system to handle all internal requests as well as external non-profit requests. But the ticketing system is confusing and difficult to use, so lots of the employees reach out to you directly to figure out how to do things. In fact, so many things are difficult to find that employees typically ask around when they have a question.

There are nightly backups in place of the file server. You store this information on a disk backup and take it home with you everyday to keep it safe in case something happens onsite. There’s also a small company website that’s hosted on the single server at the company. This website is a single html page that explains the mission of the company and provides contact information. The website has gone down many times, and no one knows what to do when it happens.

My Response:

I think the most troubling thing about this company is the backup system. Making a disk backup isn’t bad, but someone driving around with it seems like an accident being invited to happen. Make a cloud backup, a disk backup that stays onsite, and a third that the IT person can drive around with.
The ticket system should be replaced or the employees should be trained to use it. If it is there, it needs to be in use and effective for people to use.
Again, I think this org should reconfigure AD with roles and GPOs for users so that they can have software automatically installed on their machines at login. And the IT person needs to be maintaining accounts and deleting users when they leave the organization.
Running the website on your own server, especially for a single HTML page, makes no sense at this time. Pay the $10/year for your page to be hosted by a hosting service and enjoy the 99%+ uptime.