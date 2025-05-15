"Step-by-Step Guide to Install, Configure, and Use MySQL on an EC2 Instance by Jackline Chepngetich"

📌 Why Set Up MySQL on an EC2 Instance?

While AWS offers Amazon RDS as a managed service, self-hosting MySQL on an EC2 instance gives you:

🔧 **Full Control**: Configure `my.cnf`, install extensions, and tune performance manually.  
💰 **Cost Efficiency**: Ideal for multi-database setups on a single instance.  
💾 **Custom Backup Strategies**: Implement snapshots or scripts.  
🔒 **Advanced Security Setup**: Control firewall rules, VPNs, and encryption.  
💡 **OS & Version Flexibility**: Choose specific MySQL and OS versions.

When to Use RDS Instead:
- You need automatic backups, patching, and scaling.
- You prefer a fully managed service with minimal configuration.

🚀 EC2 + MySQL Setup Guide

✅ Tasks Covered
- Launch EC2 instance
- Install and configure MySQL
- Secure installation
- Create and manage databases/tables
- Insert and query data

🔧 Step-by-Step Instructions

Step 1: Launch EC2 Instance**

1. Log into AWS Management Console.
2. Navigate to **EC2** → **Instances** → **Launch Instance**.
3. **Name**: `dbhost`
4. **AMI**: Select **Amazon Linux 2**
5. **Instance Type**: `t3.micro`
6. **Key Pair**: Create a new key pair  
   - Name: `dbkey`  
   - Type: RSA  
   - Format: `.pem`
7. **Network Settings** → Click **Edit**
   - Enable auto-assign Public IP
   - Create a new security group:  
     - Name: `dbsecuritygroup`  
     - Description: `Security Group to allow traffic to EC2`
     - Inbound Rules:
       SSH– Port 22
       MySQL/Aurora– Port 3306  
8. Click **Launch Instance** (leave all else as default).
9. Wait until **Instance State = Running** and **Status Checks = 2/2 passed**.

---

Step 2: SSH into EC2**

1. Go to **Instances** → Select `dbhost` → Click **Connect**.
2. Use **EC2 Instance Connect** and click **Connect**.
3. Terminal window opens in a new browser tab.

---

Step 3: Install MySQL**

```bash
sudo rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-5.noarch.rpm
sudo yum install -y mysql-community-server --nogpgcheck
```
Step 4: Start and Enable MySQL**

```bash
sudo systemctl start mysqld
sudo systemctl enable mysqld
sudo systemctl status mysqld
```

Step 5: Retrieve Temporary Root Password**

```bash
sudo grep 'temporary password' /var/log/mysqld.log
```

Step 6: Secure MySQL Installation**

```bash
sudo mysql_secure_installation
```

Prompts:
- Enter temporary password
- Set new root password
- Remove anonymous users: `Y`
- Disallow remote root login: `N` (optional)
- Remove test database: `N` (optional)
- Reload privilege tables: `Y`

---

Step 7: Log into MySQL

```bash
mysql -u root -p
```

Step 8: Create Database and Tables**
```sql
CREATE DATABASE mydatabase;
USE mydatabase;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    product_name VARCHAR(100) NOT NULL,
    quantity INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

---

Step 9: Insert Sample Data**

```sql
INSERT INTO users (username, email, password)
VALUES
  ('john_doe', 'john@example.com', 'hashed_password_123'),
  ('jane_doe', 'jane@example.com', 'hashed_password_456');

INSERT INTO orders (user_id, product_name, quantity)
VALUES
  (1, 'Laptop', 1),
  (1, 'Mouse', 2),
  (2, 'Keyboard', 1);
```
Step 10: Query Data**

```sql
SELECT * FROM users;
SELECT * FROM orders;
```
Step 11: Exit MySQL**

```sql
EXIT;
```
🎉 Wrapping Up

You've successfully:
- Launched an EC2 instance
- Installed and configured MySQL
- Created databases and tables
- Inserted and queried data

This setup is perfect for developers seeking full control of their database environment on AWS.

