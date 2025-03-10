# 🚀 Raspberry Pi Team 2 - Nextcloud Setup

## 📌 Project Overview
This project involves setting up a **Nextcloud server** on a **Raspberry Pi 400** for file sharing and collaboration. The objective is to:
- Configure users and groups for controlled access.
- Set up a shared folder for team collaboration.
- Enable external access and secure the server.

---

## 🖥️ System Details
| Component          | Details                    |
|------------------|--------------------------|
| **Device**      | Raspberry Pi 400          |
| **OS**         | Raspberry Pi OS Lite      |
| **IP Address** | `192.168.1.118`           |
| **Hostname**   | `raspberrypi`             |

---

## 📌 1️⃣ Raspberry Pi OS Installation
- Installed **Raspberry Pi OS Lite**.
- Verified system updates:
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

---

## 👥 2️⃣ Configuring Users, Groups & Permissions
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

## 📂 3️⃣ Setting Up a Shared Folder
- Created and secured a shared directory `/teamshare`:
  ```bash
  sudo mkdir /teamshare
  sudo chown -R www-data:www-data /teamshare
  sudo chmod -R 775 /teamshare
  ```
- Ensured restricted delete permissions:
  ```bash
  sudo setfacl -m d:u::rwx,d:g::r-x,d:o::- /teamshare
  ```
- Verified folder permissions:
  ```bash
  ls -ld /teamshare
  ```

---

## 🔑 4️⃣ Enabling SSH Access for Remote Management
- Installed and enabled **OpenSSH Server**:
  ```bash
  sudo apt install openssh-server -y
  sudo systemctl enable ssh
  sudo systemctl start ssh
  sudo systemctl status ssh
  ```
- Allowed SSH through firewall:
  ```bash
  sudo ufw allow 22
  sudo ufw enable
  ```

---

## ☁️ 5️⃣ Installing & Configuring Nextcloud
- Installed necessary dependencies:
  ```bash
  sudo apt install apache2 mariadb-server libapache2-mod-php php php-mysql php-gd php-xml php-mbstring php-curl php-zip php-intl -y
  ```
- Downloaded and set up **Nextcloud**:
  ```bash
  wget https://download.nextcloud.com/server/releases/nextcloud-latest.zip
  unzip nextcloud-latest.zip
  sudo mv nextcloud /var/www/
  sudo chown -R www-data:www-data /var/www/nextcloud
  sudo chmod -R 755 /var/www/nextcloud
  ```
- Configured **Apache for Nextcloud**:
  ```bash
  sudo nano /etc/apache2/sites-available/nextcloud.conf
  ```
  **Apache Configuration:**
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

## 🔗 6️⃣ Enabling External Storage
- Enabled **files_external** app:
  ```bash
  sudo -u www-data php /var/www/nextcloud/occ app:enable files_external
  ```
- Mounted **local storage `/teamshare` in Nextcloud**:
  ```bash
  sudo -u www-data php /var/www/nextcloud/occ files_external:create /teamshare local none --config datadir="/teamshare"
  ```
- Verified the mount point:
  ```bash
  sudo -u www-data php /var/www/nextcloud/occ files_external:list
  ```

---

## 🔍 7️⃣ Checking Open & Reachable Ports
- Checked if ports **22 (SSH), 80 (HTTP), and 443 (HTTPS)** are open:
  ```bash
  sudo netstat -tulnp | grep -E '22|80|443'
  ```
- Tested local and remote access:
  ```bash
  nc -zv 127.0.0.1 22
  nc -zv 127.0.0.1 443
  nc -zv 192.168.1.118 22
  nc -zv 192.168.1.118 443
  ```

---

## 📁 8️⃣ Pushing Configuration to GitHub Repository
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
✔️ Fully configured **Raspberry Pi infrastructure**  
🚧 Centralized knowledge base hosted via **Nginx (Pending)**  
✔️ Functional **team-based collaboration & networked services**  

---

## 🌐 Accessing Nextcloud
- Open a browser and enter:
  ```
  https://192.168.1.118
  ```
- Login with team credentials:
  - **Admin:** `admin`
  - **Team accounts:** `team1, team3, team4, etc.`

---

## 📖 References
- 📄 [Nextcloud Documentation](https://docs.nextcloud.com/)
- 🌐 [Apache Configuration](https://httpd.apache.org/docs/2.4/)
- 🛠️ [GitHub Setup Guide](https://docs.github.com/en/get-started/quickstart/create-a-repo)

🚀 **Team 2 - Nextcloud Setup Completed!** 🎉

