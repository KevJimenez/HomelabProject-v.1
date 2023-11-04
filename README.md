# HomelabTestProject-v.1

## Project Description

Repurposing my old laptop to a test server running multiple services (ie. JellyFin, Pihole, NextCloud, etc.) using an Ubuntu Server.

## Table of Contents

- [HomelabTestProject-v.1](#homelabtestproject-v.1)
- [Project Description](#project-description)
- [Table of Contents](#table-of-contents)
- [Features](#features)
- [Homelab Diagram](#homelab-diagram)
- [Technologies Used](#technologies-used)
    - [Hardware](#hardware)
    - [Software](#software)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
- [Usage](#usage)
- [Challenges](#challenges)
- [Future Improvements](#things-that-can-be-improved-for-the-next-version-of-my-homelab)
- [Acknowledgments](#acknowledgments)

## Features

- Offline media server (Jellyfin)
- Self-hosted cloud storage accessible to the internet (NextCloud)
- Web ad-blocker (DNS Filtering) (Pi-hole)
- Recursive DNS for added internet security (Unbound)
- Monitor server status (Htop)
- App packager to avoid dependency conflict. Easier to run than VMs. (Docker)
- VPN for encryption when using public network and to access local services remotely (Tailscale)

## Homelab Diagram
![diagram](https://github.com/KevJimenez/HomelabTestProject-v.1/blob/main/hldiagram.png)

## Technologies Used

### Hardware:
<img src="https://github.com/KevJimenez/HomelabTestProject-v.1/blob/main/photo_2023-10-11_11-27-36.jpg?raw=true" alt="dell7447" width="300" height="500"/>

 **Server Specs:**
 
 - Laptop Model: Dell Inspiron 14 7447
   - Processor: `Intel i7-4710HQ (4C | 8T)`
   - Memory: `8GB DDR3L 1600mhz`
   - Graphics: `Intel HD 4600 (Integrated), Nvidia GTX 850M (Dedicated)`
   - Storage: `128GB SSD (OS Drive), 140GB HDD (Storage Drive)`
         
**Others:**

- Host PC/Laptop
- Cat 5e LAN Cables (For 1 gigabit transfer speeds)
- Huawei Echolife EG8145V5 (Router)
  
 ### Software:
- Ubuntu Server 22.04.3 LTS (Bare Metal)
- Docker
- Htop
- OpenSSH
- Jellyfin
- Pi-hole
- Unbound
- NextCloud
- Tailscale
- MariaDB
- Apache
- WinSCP
  
## Getting Started

### Prerequisites

- [Ubuntu Server](https://ubuntu.com/server/docs/installation)
- [Docker](https://docs.docker.com/engine/install/ubuntu/#prerequisites)
- [Jellyfin](https://jellyfin.org/docs/general/administration/hardware-selection/)
- [Pi-Hole](https://docs.pi-hole.net/main/prerequisites/)
- Unbound
  - Device running Pi-Hole
  - Access to SSH
- [NextCloud](https://docs.nextcloud.com/server/latest/admin_manual/installation/system_requirements.html)
- [Tailscale](https://tailscale.com/kb/1017/install/)
- [Apache](https://docs.oracle.com/cd/E19879-01/821-0182/fwboh/index.html)
- [WinSCP](https://winscp.net/eng/docs/requirements)

### Installation
#### Ubuntu 22.04 Server OS Installation:
1. Download Ubuntu Server OS [here](https://ubuntu.com/download/server).
2. Create a bootable USB drive for Ubuntu OS (In this case, I used Ventoy).
3. Insert bootable USB into the server PC
4. Boot into BIOS in the server PC.
5. Change boot priority to the Ubuntu USB Drive (You can also boot override if your BIOS has that).
6. Follow on-screen instructions for Ubuntu install.
   
> **_NOTE:_** Be sure to include OpenSSH during installation to access server remotely.
    
**Htop Setup:**
1. Run apt update and apt upgrade in the CLI

   ```bash
   sudo apt update && sudo apt upgrade
   ```
   
2. Install htop using the apt command

   ```bash
   sudo apt install htop
   ```
   
**Docker Setup:**
1. Uninstall old docker packages (It is highly recommended to run this even though you're on a fresh OS)
   
   ```bash
   for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
   ```
   
2. Install the Docker Repository

   ```bash
    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    
    # Add the repository to Apt sources:
    echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```
   
3. Install Docker Package

   ```bash
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```
   
4. Verify that Docker is installed

    ```bash
    sudo docker run hello-world
    ```

**Jellyfin Setup: (Docker method)**
1. Install docker
2. Using the CLI, download the latest docker image for Jellyfin
   
   ```bash
   docker install jellyfin/jellyfin
   ```
   
3. Create cache,config, and media directories as per Jellyfin container requirements:
   
   ```bash
   mkdir /home/kev/jellyfin
   cd /home/kev/jellyfin
   mkdir cache config media
   ```
   
4. Auto mounting the internal hard drive with media content

   4.1 Run to know the media storage details
   
   ```bash
   lsblk -o NAME,FSTYPE,UUID,MOUNTPOINTS
   ```
   
   4.2 Edit the fstab file
   
   ```bash
   sudo nano /etc/fstab
   ```
   
   4.3 Input the UUID, Mountpoint, and drive type of the media storage in this format
   
   ```bash
   UUID=<UUID> <PATH_TO_MOUNT> <DRIVE_TYPE>  defaults        0       0
   ```
   *In my case:*
   
   ```bash
   UUID=35d41abe-a9ae-4f96-9951-a6d406efe569 /home/kev/jellyfin/media ext4 defaults 0 0
   ```
   
   4.4 Restart Ubuntu Server to apply changes
   
   ```bash
   sudo reboot
   ```

   4.5 Check if media storage is mounted properly
   
   ```bash
   lsblk
   ```
   
6. Run Jellyfin

   ```bash
   docker run -d \
   --name jellyfin \
   --user 1000:1000 \
   --net=host \
   --volume /home/kev/jellyfin/config:/config \
   --volume /home/kev/jellyfin/cache:/cache \
   --mount type=bind,source=/home/kev/jellyfin/media,target=/media \
   --restart=unless-stopped \
   jellyfin/jellyfin
   ```

**Pi-hole and Unbound Setup:**
1. Setup a static IP Address for your server
2. Install Pi-hole using this command
   
   ```bash
   curl -sSL https://install.pi-hole.net | bash
   ```

3. Follow on-screen instructions
4. Changing the default Pi-hole password

   ```bash
   pihole -a -p <insert new password>
   ```
  
5. Verify if Pi-hole is running by using the static IP address that has been set earlier (use your browser).
   
   *In my case:*
   ```
   192.168.100.74/admin
   ```
> We will configure it later, we need to install Unbound first so that we can just configure all of it at once.

6. Install unbound
   
   ```
   sudo apt install unbound
   ```

7. Create a configuration file for Unbound

   ```
   sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
   ```

8. Write and save this in the nano editor

   ```bash
   server:
    # If no logfile is specified, syslog is used
    # logfile: "/var/log/unbound/unbound.log"
    verbosity: 0

    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes

    # May be set to yes if you have IPv6 connectivity
    do-ip6: no

    # You want to leave this to no unless you have *native* IPv6. With 6to4 and
    # Terredo tunnels your web browser should favor IPv4 for the same reasons
    prefer-ip6: no

    # Use this only when you downloaded the list of primary root servers!
    # If you use the default dns-root-data package, unbound will find it automatically
    #root-hints: "/var/lib/unbound/root.hints"

    # Trust glue only if it is within the server's authority
    harden-glue: yes

    # Require DNSSEC data for trust-anchored zones, if such data is absent, the zone becomes BOGUS
    harden-dnssec-stripped: yes

    # Don't use Capitalization randomization as it known to cause DNSSEC issues sometimes
    # see https://discourse.pi-hole.net/t/unbound-stubby-or-dnscrypt-proxy/9378 for further details
    use-caps-for-id: no

    # Reduce EDNS reassembly buffer size.
    # IP fragmentation is unreliable on the Internet today, and can cause
    # transmission failures when large DNS messages are sent via UDP. Even
    # when fragmentation does work, it may not be secure; it is theoretically
    # possible to spoof parts of a fragmented DNS message, without easy
    # detection at the receiving end. Recently, there was an excellent study
    # >>> Defragmenting DNS - Determining the optimal maximum UDP response size for DNS <<<
    # by Axel Koolhaas, and Tjeerd Slokker (https://indico.dns-oarc.net/event/36/contributions/776/)
    # in collaboration with NLnet Labs explored DNS using real world data from the
    # the RIPE Atlas probes and the researchers suggested different values for
    # IPv4 and IPv6 and in different scenarios. They advise that servers should
    # be configured to limit DNS messages sent over UDP to a size that will not
    # trigger fragmentation on typical network links. DNS servers can switch
    # from UDP to TCP when a DNS response is too big to fit in this limited
    # buffer size. This value has also been suggested in DNS Flag Day 2020.
    edns-buffer-size: 1232

    # Perform prefetching of close to expired message cache entries
    # This only applies to domains that have been frequently queried
    prefetch: yes

    # One thread should be sufficient, can be increased on beefy machines. In reality for most users running on small networks or on a single machine, it should be unnecessary to seek performance enhancement by increasing num-threads above 1.
    num-threads: 1

    # Ensure kernel buffer is large enough to not lose messages in traffic spikes
    so-rcvbuf: 1m

    # Ensure privacy of local IP ranges
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10
   ```

9. Restart unbound
    
    ```
    sudo service unbound restart
    ```

10. Login to Pi-hole
11. Go to Pi-hole Settings then click the DNS settings
12. Untick all Upstream DNS Servers except for "Custom 1 (IPV4)"
13. Set "Custom 1 (IPV4)" to 127.0.0.1#5335
14. Scroll down to hit save
15. You can choose to set your DNS servers through the main router or you can do it by device. I will be doing it per device for testing purposes.

**Tailscale Setup:**
1. Login to Tailscale Web (I used my GitHub account.)
2. Install Tailscale on my Ubuntu server.
3. Run this in the CLI
   
   ```bash
    curl -fsSL https://tailscale.com/install.sh | sh
   ```
   
5. Run Tailscale on Ubuntu Server as an Exit node and accept Pi-Hole DNS Server
   
   ```bash
   tailscale up --accept-dns=false --advertise-exit-node
   ```
   
6. Register your devices to Tailscale (see [link](https://tailscale.com/download/) to follow steps according to your device).
   
*Tailscale using Pi-Hole as DNS Server Setup*

1. Go to Tailscale home and click DNS to set DNS Server to Pi-Hole.
2. Under Global nameservers, add nameserver.
3. Click on custom then enter the IPV4 address provided by Tailscale. (Do not put in your local IPV4 address as Tailscale provides it's own IPV4 address.)
4. Toggle Override local DNS
   
*Tailscale as a 'commercial' VPN Setup*

1. Go to the Machines tab
2. Click on the Ubuntu Server's settings
3. Edit route settings > Toggle "Use as Exit Node".

**Nextcloud Setup:**
1. Check if you have wget.
   
   ```bash
     which wget
   ``` 
> 1.1 If you don't have wget, run this:
  ```bash
    sudo apt install wget
  ```
2. Run this command to download the Nextcloud zip file.

   ```bash
     wget https://download.nextcloud.com/server/releases/latest.zip
   ```

3. Setup MariaDB and check if it runs

   ```bash
     sudo apt install mariadb-server
   ```
   ```bash
     systemctl status mariadb
   ```

4. Run the secure installation script and follow the promts.

   ```bash
   sudo mysql_secure_installation
   ```

5. Creating the Nextcloud DB. Access the mariadb console first:

   ```bash
   sudo mariadb
   ```

6. Creating the Database and setting permissions:

   ```bash
   CREATE DATABASE nextcloud;
   ```
   ```bash
   SHOW DATABASES;
   ```
   ```bash
   GRANT ALL PRIVILEGES ON nextcloud.* TO 'kev'@'localhost' IDENTIFIED BY '*insert your password*';
   ```
   ```bash
   FLUSH PRIVILEGES;
   ```

7. Setting up Apache by first installing it:

   ```bash
   sudo apt install apache2
   ```

8. Check apache status

   ```bash
   systemctl status apache2
   ```

9. Enable PHP extensions

    ```bash
    sudo phpenmod bcmath gmp imagick intl
    ```

10. Check if you have unzip:

    ```bash
    which unzip
    ```
> 10.1 If you don't have wget, run this:

  ```bash
    sudo apt install unzip
  ```

11. Unzip the NextCloud zip file downloaded earlier

    ```bash
    unzip latest.zip
    ```

12. Remove the zip file 

     ```bash
    rm latest.zip
    ```
     
13. Move the nextcloud folder and set pemissions

    ```bash
    sudo chown -R www-data:www-data nextcloud
    ```
    ```bash
    sudo mv nextcloud /var/www
    ```
    
15. Disable the Apache default website
    
    ```bash
    sudo a2dissite 000-default.conf
    ```

16. Setup the Apache to serve Nextcloud instance. Make a conf file.

    ```bash
    sudo nano /etc/apache2/sites-available/nextcloud.conf
    ```

17. Add this to the newly made conf file.

    ```bash 
    <VirtualHost *:80>
    DocumentRoot "/var/www/nextcloud"
    ServerName nextcloud

     <Directory "/var/www/nextcloud">
        Options MultiViews FollowSymlinks
        AllowOverride All
        Order allow,deny
        Allow from all
     </Directory>

     TransferLog /var/log/apache2/nextcloud.log
     ErrorLog /var/log/apache2/nextcloud.log

    </VirtualHost>
    ```

18. Enable the site:

    ```bash
    sudo a2ensite apache-config-nextcloud.conf
    ```

19. Edit the PHP file

    ```bash
    sudo nano/etc/php/8.1/apache2/php.ini
    ```

20. Replace the following to these values and uncomment certain lines using nano.

    ```
    memory_limit = 512M
    upload_max_filesize = 200M
    max_execution_time = 360
    post_max_size = 200M
    date.timezone = America/Detroit
    opcache.enable=1
    opcache.interned_strings_buffer=8
    opcache.max_accelerated_files=10000
    opcache.memory_consumption=128
    opcache.save_comments=1
    opcache.revalidate_freq=1
    ```

21. Enable these PHP mods for Apache

    ```bash
    sudo a2enmod dir env headers mime rewrite ssl
    ```

22. Restart Apache

    ```bash
    sudo systemctl restart apache2
    ```

23. Login to NextCloud using local ip address.

### Usage

 [![usage](https://img.youtube.com/vi/koYdHIqc3Ng/0.jpg)](https://www.youtube.com/watch?v=koYdHIqc3Ng)
 

### Challenges:

**JellyFin Install**
- Folder permissions (Changed Jellyfin permissions)
- Jellyfin keeps restarting (Changed the file path of cache and config files during execution)

**NextCloud Install**
- Can't seem to fix the Apache install (Solutions: https://askubuntu.com/questions/912638/error-module-php7-0-does-not-exist, and changing the port number of lighttpd to 8080)
- Enabling cron automation by appending '*/5  *  *  *  * php -f /var/www/nextcloud/cron.php --define apc.enable_cli=1' to 'sudo crontab -u root -e' or crontab.

### Things that can be improved for the next version of my homelab:

- Self-hosted VPN server like wireguard (Needs its own public IP to run, but my network is behind CGNAT, so the only option is to rent a Virtual Machine and run wireguard there).
- NextCloud access through the public internet (Bit of skleptical on this one since my VPN setup is much more secure. Also requires a CGNAT internet, costs will be much higher. Can be done in the future).
- NextCloud AIO (Will be installed in the docker container, much lighter to run, stable, and reliable).
- Run services in docker for easy management and light resources (Did not run some services in docker in this version to learn and to practice troubleshooting)
- Installing a reverse proxy if ever that I intended to run my services on the public internet without the use of VPN.
- A new server build with power efficient parts, more storage, more RAM, and a better processor with Quick Sync for video encoding/decoding in Jellyfin.
- Will run Proxmox in the new build for a much more versatile Hypervisor and run Virtual Machines and containers.
- A router that can run OPNsense and a managed switch for VLANs and better firewall.
- UPS is also a nice to have for sudden power outages and failure.


### Acknowledgments

I would like to thank the following as it helped me build and learn much more about self-hosting and the linux operating system:

- https://www.youtube.com/@LearnLinuxTV
- https://www.youtube.com/@WolfgangsChannel
- https://www.youtube.com/@TechnoTim
- https://www.youtube.com/@NetworkChuck
- https://www.youtube.com/@TechHut
- https://www.reddit.com/r/homelab/
- https://www.reddit.com/r/HomeNetworking/
- https://www.reddit.com/r/selfhosted/
