# Raspberry Pi Team 2 - Nextcloud Setup

Project Overview

This project involves setting up a Nextcloud server on a Raspberry Pi 400 for file sharing and collaboration. The goal is to configure users, set up a shared folder, enable external access, and ensure security settings are properly configured.

System Details

Device: Raspberry Pi 400

Operating System: Raspberry Pi OS Lite

IP Address: 192.168.1.118

Hostname: raspberrypi
---

## 1️⃣ Raspberry Pi OS Installation
- Installed **Raspberry Pi OS (Lite version recommended)** on the device.
- Ensured the OS boots correctly and connected to the network.
- Verified system updates:
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

---

## 2️⃣ Configuring Users, Groups & Permissions
- Created users and groups for controlled access:
  ```bash
  sudo useradd -m team2
  sudo passwd team2
  ```
- Verified user list:
  ```bash
  sudo cat /etc/passwd | grep team2
  ```
- Set up group permissions:
  ```bash
  sudo groupadd teamshare
  sudo usermod -aG teamshare team2
  ```

---

## 3️⃣ Setting Up a Shared Folder
- Created a shared directory `/teamshare`:
  ```bash
  sudo mkdir /teamshare
  sudo chown -R www-data:www-data /teamshare
  sudo chmod -R 775 /teamshare
  ```
- Ensured users can read/write files but only owners can delete their own files:
  ```bash
  sudo setfacl -m d:u::rwx,d:g::r-x,d:o::- /teamshare
  ```
- Verified permissions:
  ```bash
  ls -ld /teamshare
  ```

---

## 4️⃣ Enabling SSH Access for Remote Management
- Installed OpenSSH Server:
  ```bash
  sudo apt install openssh-server -y
  ```
- Enabled and started SSH service:
  ```bash
  sudo systemctl enable ssh
  sudo systemctl start ssh
  sudo systemctl status ssh
  ```
- Allowed SSH through the firewall:
  ```bash
  sudo ufw allow 22
  sudo ufw enable
  ```

---

## 5️⃣ Installing & Configuring Nextcloud
- Installed dependencies:
  ```bash
  sudo apt install apache2 mariadb-server libapache2-mod-php php php-mysql php-gd php-xml php-mbstring php-curl php-zip php-intl -y
  ```
- Downloaded and set up Nextcloud:
  ```bash
  wget https://download.nextcloud.com/server/releases/nextcloud-latest.zip
  unzip nextcloud-latest.zip
  sudo mv nextcloud /var/www/
  sudo chown -R www-data:www-data /var/www/nextcloud
  sudo chmod -R 755 /var/www/nextcloud
  ```
- Configured Apache for Nextcloud:
  ```bash
  sudo nano /etc/apache2/sites-available/nextcloud.conf
  ```
  **Configuration:**
  ```apache
  <VirtualHost *:80>
      ServerAdmin admin@yourdomain.com
      DocumentRoot /var/www/nextcloud
      ServerName 192.168.1.118
      Redirect permanent / https://yourdomain.com/
      <Directory /var/www/nextcloud>
          Require all granted
          AllowOverride All
          Options FollowSymLinks MultiViews
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
      CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined
  </VirtualHost>
  ```
- Enabled Apache configuration:
  ```bash
  sudo a2ensite nextcloud.conf
  sudo systemctl restart apache2
  ```

---

## 6️⃣ Enabling External Storage
- Enabled **files_external** app:
  ```bash
  sudo -u www-data php /var/www/nextcloud/occ app:enable files_external
  ```
- Mounted local storage `/teamshare` in Nextcloud:
  ```bash
  sudo -u www-data php /var/www/nextcloud/occ files_external:create /teamshare local none --config datadir="/teamshare"
  ```
- Verified mount point:
  ```bash
  sudo -u www-data php /var/www/nextcloud/occ files_external:list
  ```

---

## 7️⃣ Checking Open & Reachable Ports
- Checked if ports 22 (SSH), 80 (HTTP), and 443 (HTTPS) are open:
  ```bash
  sudo netstat -tulnp | grep -E '22|80|443'
  ```
- Tested local connectivity:
  ```bash
  nc -zv 127.0.0.1 22
  nc -zv 127.0.0.1 443
  ```
- Tested remote access from another computer:
  ```bash
  nc -zv 192.168.1.118 22
  nc -zv 192.168.1.118 443
  ```

---

## 8️⃣ Pushing Configuration to GitHub Repository
- Installed Git:
  ```bash
  sudo apt install git -y
  ```
- Created a repository:
  ```bash
  mkdir ~/team2-setup && cd ~/team2-setup
  git init
  ```
- Copied configuration files:
  ```bash
  cp /etc/apache2/sites-available/nextcloud.conf .
  cp /var/www/nextcloud/config/config.php .
  cp /etc/ssl/certs/nextcloud.crt .
  cp /etc/ssl/private/nextcloud.key .
  ```
- Committed & pushed files:
  ```bash
  git add .
  git commit -m "Added Nextcloud configuration files"
  git branch -M main
  git remote add origin https://github.com/Ramzi-444/team2-setup.git
  git push -u origin main
  ```

---

## ✅ Final Deliverables
- **Fully configured Raspberry Pi infrastructure** ✅
- **Centralized knowledge base hosted via Nginx (Pending)** 🚧
- **Functional team-based collaboration & networked services** ✅

---

## 📌 Future Improvements
- Implement SSL certificates with **Let's Encrypt** for better security.
- Set up **fail2ban** to protect against SSH brute-force attacks.
- Automate **Nextcloud backups** to prevent data loss.

---

## 🔗 References
- [Nextcloud Documentation](https://docs.nextcloud.com/)
- [Apache Configuration](https://httpd.apache.org/docs/2.4/)
- [GitHub Setup Guide](https://docs.github.com/en/get-started/quickstart/create-a-repo)

🚀 **Team 2 - Nextcloud Setup Completed!** 🎉
