Here's a README file outlining the steps to configure Zabbix on an AWS instance using Ubuntu 24.04. You can save this as `README.md` in your project directory.

---

# Zabbix Installation on Ubuntu 24.04 (AWS Instance)

This guide provides step-by-step instructions for installing and configuring Zabbix on an AWS instance running Ubuntu 24.04.

## Prerequisites

- An AWS account with an EC2 instance running Ubuntu 24.04.
- Sufficient permissions to install software and configure services.

## Steps to Install Zabbix

### 1. Update System Packages

Log in to your AWS instance and update the package index:

```bash
sudo apt update
```

### 2. Install Required Packages

Install `wget` if it’s not already installed:

```bash
sudo apt install wget -y
```

### 3. Download and Install Zabbix Repository

Download the Zabbix repository package:

```bash
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest+ubuntu24.04_all.deb
```

Install the Zabbix repository:

```bash
sudo dpkg -i zabbix-release_latest+ubuntu24.04_all.deb
```

### 4. Update Package Index Again

After adding the Zabbix repository, update the package index:

```bash
sudo apt update
```

### 5. Install Zabbix Server, Frontend, and Agent

Install the Zabbix server, frontend, and agent packages:

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent -y
```

### 6. Install MySQL Server

Install MySQL server:

```bash
sudo apt install mysql-server -y
```

### 7. Configure MySQL

Log in to the MySQL server:

```bash
mysql -uroot
```

Run the following commands to set up the Zabbix database:

```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
SET GLOBAL log_bin_trust_function_creators = 1;
QUIT;
```

### 8. Import Initial Zabbix Schema and Data

Import the initial schema and data:

```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

### 9. Update MySQL Settings

Log back into MySQL:

```bash
mysql -uroot
```

Reset the `log_bin_trust_function_creators` variable:

```sql
SET GLOBAL log_bin_trust_function_creators = 0;
QUIT;
```

### 10. Configure Zabbix Server

Edit the Zabbix server configuration file:

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

Set the database password:

```
DBPassword=password  # Replace 'password' with your actual password
```

### 11. Configure PHP for Zabbix Frontend

Edit the Nginx configuration file:

```bash
sudo nano /etc/zabbix/nginx.conf
```

Uncomment and set the `listen` and `server_name` directives appropriately.

### 12. Restart Services

Restart the Zabbix server, agent, Nginx, and PHP services:

```bash
sudo systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
```

### 13. Enable Services on Boot

Enable Zabbix server, agent, Nginx, and PHP to start on boot:

```bash
sudo systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
```

## Accessing Zabbix Frontend

Once the installation is complete, you can access the Zabbix frontend by navigating to your server’s IP address in a web browser:

```
http://<your_server_ip>/zabbix
```

Follow the on-screen instructions to complete the frontend setup.

## Conclusion

You have successfully installed and configured Zabbix on your AWS instance running Ubuntu 24.04. For further configuration and customization, refer to the official [Zabbix documentation](https://www.zabbix.com/documentation/current/manual).
