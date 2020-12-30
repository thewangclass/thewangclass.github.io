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
Week 3
Software and Platform Services

---

---
Week 4 Directory Services
---

---
Week 5 Data Recovery & Backups
---

---
Week 6
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