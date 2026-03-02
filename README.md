# Demo-EC2-Security-Group-Misconfiguration

## Introduction
This project highlights the risks of EC2 Security Group misconfiguration. By exposing SSH to the entire Internet and enabling password authentication with weak credentials, i want demonstrate how attackers can exploit these mistakes using brute force tools like Hydra. The goal is to raise awareness of cloud security best practices and show how small misconfigurations can lead to serious vulnerabilities.

## Tạo EC2

Vào AWS Management Console → chọn EC2.
Nhấn Launch Instance.

![image alt](https://github.com/ImSAM-S/Demo-EC2-Security-Group-Misconfiguration/blob/4801b6ebc7ebf514610f694ccb6ac0155b75969d/01_Starting_launch_instance.png)

Name the instance
Select Amazon Linux 2 AMI (Free tier eligible).
Select machine type: t2.micro (Free tier).
Key pair: create a new one or use an existing key pair for SSH.

Network settings: create a new Security Group:
-> How to correct a misconfigured Security Group (demo misconfig). In the Firewall (security groups) section, select Create security group. In the Inbound rules section, add the follow
. Type: All traffic
. Protocol: All
. Port range: All
. Source type: Anywhere
. Source: 0.0.0.0/0

![image alt](https://github.com/ImSAM-S/Demo-EC2-Security-Group-Misconfiguration/blob/4801b6ebc7ebf514610f694ccb6ac0155b75969d/02_Settings.png)

Saved.

![image alt](https://github.com/ImSAM-S/Demo-EC2-Security-Group-Misconfiguration/blob/4801b6ebc7ebf514610f694ccb6ac0155b75969d/03_Instance_done.png)

👉 This is an extremely dangerous configuration because it opens all the ports to everyone on the Internet.

*) Risk Analysis
. Port scanning: Hackers can scan all ports to find running services
. Brute force SSH: If SSH is open to the entire internet, attackers can try passwords repeatedly
. Exploit services: If you run a web server or database, they can be exploited

👉 Here's a scenario I'll simulate:

## First, create a conducive environment for practice:
🔹 Step 1: SSH into by key pair
In local computer:

```bash
chmod 400 KeytodemoMisconfig3.pem
ssh -i KeytodemoMisconfig3.pem ec2-user@<Public-IP>
```
![image alt](https://github.com/ImSAM-S/Demo-EC2-Security-Group-Misconfiguration/blob/4801b6ebc7ebf514610f694ccb6ac0155b75969d/04_access_to_instance_BF.png)

👉 If the AMI is Ubuntu, the default username is ubuntu. ec2-user is for Linux.

🔹 Step 2: Turn on password login
In EC2:

```bash
sudo nano /etc/ssh/sshd_config
```

Đổi:

Code
PasswordAuthentication no

To:

Code
PasswordAuthentication yes

![image alt](https://github.com/ImSAM-S/Demo-EC2-Security-Group-Misconfiguration/blob/4801b6ebc7ebf514610f694ccb6ac0155b75969d/05_No_to_yes.png)

Save file.

Restart SSH service:

```bash
sudo systemctl restart sshd
sudo systemctl enable sshd
sudo systemctl status sshd
```
→ Need to see Active: active (running).

🔹 Step 3: Creat user test

```bash
sudo adduser demo
sudo passwd demo
```
👉 Make weak password  (123456).

![image alt](https://github.com/ImSAM-S/Demo-EC2-Security-Group-Misconfiguration/blob/4801b6ebc7ebf514610f694ccb6ac0155b75969d/06_Change_password.png)

## Situation when you are attacked by a hacker (using Hydra brute force)
In EC2, if PasswordAuthentication is set to 'yes' and not 'no', with protocol 'all', port range 'all', and Source 0.0.0.0/0 (Anywhere), the attacker only needs to know the EC2 username and public IP address to gain access to the victim's server with the following command:
 
                                    `hydra -l demo -P passwords.txt -t 4 ssh://<Public-IP-EC2>`

. The passwords.txt file contains the passwords used to guess the victim's server password.

## Khắc phục
Simply put, you do the opposite of everything I did above.

Fix `PasswordAuthentication no`
Restart `sshd`
Remove user test:
```bash
sudo deluser demo
```

## Key Lessons
Security Groups act as the first firewall for EC2. If port 22 is open to the entire Internet (0.0.0.0/0), anyone can attempt to connect.
PasswordAuthentication is dangerous. Enabling password login with weak credentials makes brute force attacks trivial.
Hydra is only a demonstration tool. It shows how attackers can quickly guess passwords if misconfiguration exists.

Best Practices:
. Restrict SSH access to trusted IPs (VPN, corporate network)
. Always use key pairs instead of passwords
. Never expose port 22 to the whole Internet
. Monitor logs and roll back insecure settings after testing

## Project Files
```tree
EC2-SG-Misconfiguration/
├── 01_Starting_launch_instance.png
├── 02_Settings.png
├── 03_Instance_done.png
├── 04_access_to_instance_BF.png
├── 05_No_to_yes.png
├── 06_Change_password.png
├── README.md
└── passwords.txt
```

## Author
ImSAM-S
