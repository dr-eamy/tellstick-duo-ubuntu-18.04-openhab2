# Instaling tellstick duo on ubuntu 18.04 and openhab2 #
After searching for install tutorials and find nothing, i did my own write up

## first folow this : ##
presume you have a working ubuntu/kubuntu 18.04 and Openhab 2.3 instalation, we ned to install telldus-core folow the description in git reposetory : 
https://github.com/soderlundf/telldus

i used the file http://download.telldus.com/pool/main/t/telldus-core/telldus-core_2.1.3-beta1.orig.tar.gz

reboot...

## Plugg inn the stick  ##
Now we need to find what serial number the tellstick duo have

dmesg or lsusb and look for it, i got min from old telldus.conf file

## Config telstick ##

make the config file 


    sudo nano /etc/tellstick.conf

Then copy paste this :

    user = "nobody"
    group = "plugdev"
    ignoreControllerConfirmation = "false"
    controller {
      id = 2
      # name = ""
      type = 2
      serial = "*******"
    }
    

------------

add your tellstick serial number in serial = "*******"


## Starting the telldus-core ##

make systemctl script..

-------------------------

    sudo nano /lib/systemd/system/telldusd.service
    
Copy paste the folowing :

    [Unit]
    Description=Tellstick service daemon
    After=multi-user.target
    
    [Service]
    Type=forking
    ExecStart=/usr/local/sbin/telldusd
    
    [Install]
    WantedBy=multi-user.target
    

enabeling the service at boot


    sudo systemctl start telldusd.service

Reloding files

    sudo systemctl daemon-reload
Starting tettdus service

    sudo systemctl start telldusd.service

reboot and then check

    systemctl status telldusd.service
    

My output are :

    openhab@openhabserver:~$ systemctl status telldusd.service
    ● telldusd.service - Tellstick service daemon
       Loaded: loaded (/lib/systemd/system/telldusd.service; enabled; vendor preset: enabled)
       Active: active (running) since Fri 2018-08-17 13:50:16 CEST; 47min ago
      Process: 1114 ExecStart=/usr/local/sbin/telldusd (code=exited, status=0/SUCCESS)
     Main PID: 1116 (telldusd)
    Tasks: 6 (limit: 2316)
       CGroup: /system.slice/telldusd.service
       └─1116 /usr/local/sbin/telldusd
    
    aug. 17 13:50:16 yggdrasil systemd[1]: Starting Tellstick service daemon...
    aug. 17 13:50:16 yggdrasil telldusd[1116]: telldusd daemon starting up
    aug. 17 13:50:16 yggdrasil systemd[1]: Started Tellstick service daemon.
    aug. 17 13:50:16 yggdrasil telldusd[1116]: Connecting to TellStick (1781/C31) with serial A703AIN1
    openhab@openhabserver:~$


## Now the openhab2 part ##
in openhab2 we need to install the telldusbinding.

under things create a new thing Telldus Core gateway

add path to so file : /usr/local/share/telldus-core/

now openhab shud discover things in inbox

"# tellstick-duo-ubuntu-18.04-openhab2" 
