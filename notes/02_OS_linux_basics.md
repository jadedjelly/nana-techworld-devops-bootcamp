# OS & Linux Basics
## Linux File System


- Hierarchical tree structure
    - / (root) is at the top, roots home dir is also located here
        - /bin
            - this is the location with binaries, things like cat, ls, grep, etc are here
        - /sbin
            - this is for binaries that require sudo / root (and also ran by the system) to run like, adduser, chgpasswd, etc
        - /etc
            - sys configs are located here, so like network config, firewall configs, etc
            - system wide configs
        - /home
            - This is where standard users home files are located (think of it as c:\users\ in windows)
        - /boot
            - contains files used for booting the system
        - /dev
            - location of device files, keyboard, hard drive, etc
            - apps & drives will access this location not users
        - /lib
            - holds the libraries for /bin & /sbin
        - /mnt
            - for internal drives
        - /media
            - this is where USB drives / ext / network drives are, so when you add a usb drive, you would navigate like: cd /mnt/s
        - /tmp
            - resources required for some processes, similar to the c:\temp
        - /usr
            - this used to be the location for user home folders, but changed as time went on, its now the location for user useable programs and data (it can grow quite large, but is very important none the less)
            - a folder called /local also resides in here, and this is where programs **you** install are located
        - /opt
            - 3rd party programs you install
                - the difference between /opt & /usr/local?
                    - Some applications don’t split their code / files in different places, so binaries in bin folder, etc (examples: IDEs (installed to /opt))

[image usr/local vs opt]

        - /var 
            - contains logs
            - /var/logs contains syslog (everything you do on your machine is logged here)
            - /var/cache
                - contains cached data from apps

There’s also hidden files, these are used to prevent important data from being accidently deleted

Automatically created by programs & OS

they are denoted with a . (dot) infront of their names

        - .gitignore

        - .bashrc

        - .bash_history