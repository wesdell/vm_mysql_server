# Remote MySQL Server Setup Using VirtualBox

This project describes how to set up a MySQL database server on a Linux virtual machine (VM) using VirtualBox, and connect to it from the host computer.

## 📘 Table of Contents

[Objective](#objective)

[Requirements](#requirements)

[Step-by-Step Guide](#step-by-step-guide)

[Troubleshooting](#troubleshooting)

[Sample Query](#sample-query)

[Team Members](#authors)

## Objective

Simulate a production environment where a database server runs on a Linux machine and remote clients (e.g., applications or developer tools) can connect to it securely.

## Requirements

- VirtualBox installed on host
- A Linux-based guest OS (Ubuntu/Debian preferred)
- MySQL installed in the guest OS
- Basic command-line knowledge
- (Optional) Docker or MySQL client installed on the host

## Step-by-Step Guide

### 1. Install MySQL on the VM

Update and install:

```bash
sudo apt update
sudo apt install mysql-server
```

### 2. Configure MySQL to Accept Remote Connections

Edit the configuration file (for MySQL):

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Find and replace:

```ini
bind-address = 127.0.0.1
```

With:

```ini
bind-address = 0.0.0.0
```

Save and exit, then restart the service:

```bash
sudo systemctl restart mysql
```

### 3. Create Database Users

Enter MySQL console:

```bash
sudo mysql
```

Create an admin user:

```sql
CREATE USER 'admin'@'%' IDENTIFIED BY 'admin_password';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
```

Create a read-only user:

```sql
CREATE USER 'reader'@'%' IDENTIFIED BY 'reader_password';
GRANT SELECT ON your_database_name.* TO 'reader'@'%';
```

Create a test database and table:

```sql
CREATE DATABASE test_db;
USE test_db;

CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    age INT CHECK (edad >= 0)
);
```

Then, exit:

```sql
EXIT;
```

### 4. Configure VirtualBox Network

- Shutdown the VM.
- Go to VirtualBox settings > Network.
- Set Adapter 1 to: Bridged Adapter
- Choose your real network adapter (e.g., Wi-Fi).
- Check “Cable connected”.

Start the VM again.

### 5. Check VM IP Address

Inside the VM:

```bash
sudo ip link set enp0s3 up
ip a
```

Look for the IP under enp0s3 (e.g., 192.168.100.1)

### 6. Allow Firewall (Optional)

If using UFW firewall in the VM:

```bash
sudo ufw allow 3306/tcp
```

### 7. Connect from Host

🔹 Option A: Install MySQL client on host

From Windows CMD:

```bash
mysql -h 192.168.100.11 -u admin -p
```

🔹 Option B: Use Docker (no client installation required)

From host terminal:

```bash
docker run -it --rm mysql mysql -h 192.168.100.11 -u admin -p
```

## Troubleshooting

- Use `sudo ss -tulnp | grep 3306` to verify MySQL is listening on 0.0.0.0:3306
- Check that users are created with host = '%' ('%' means that you can connect from any IP)
- Ensure the VM IP is reachable from the host (`ping 192.168.100.1`)

## Sample Query

To test your setup, log in and run:

```sql
USE test_db;
SELECT * FROM students;
```

## Authors

- Franciss Cartagena
- Mayerli Chávez
- Jenner Simbaña
- Franciel Tipantuña
- Sebastián Guerrero
