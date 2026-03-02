# Demo-EC2-Security-Group-Misconfiguration


## Tạo EC2

Vào AWS Management Console → chọn EC2.
Nhấn Launch Instance.
Đặt tên instance (ví dụ: Demo-SG-Misconfig).
Chọn Amazon Linux 2 AMI (Free tier eligible).
Chọn loại máy: t2.micro (Free tier).
Key pair: tạo mới hoặc dùng key pair có sẵn để SSH.
Network settings: tạo Security Group mới.


### Cách chỉnh Security Group sai cấu hình (demo misconfig)
Trong phần Firewall (security groups), chọn Create security group.

Ở mục Inbound rules, thêm rule:
. Type: All traffic
. Protocol: All
. Port range: All
. Source type: Anywhere
. Source: 0.0.0.0/0

Lưu lại.
👉 Đây là cấu hình cực kỳ nguy hiểm vì mở toàn bộ cổng cho tất cả mọi người trên Internet.

*) Phân tích rủi ro
. Port scanning: Hacker có thể quét toàn bộ cổng để tìm dịch vụ đang chạy
. Brute force SSH: Nếu SSH mở ra toàn Internet, kẻ tấn công có thể thử mật khẩu liên tục
. Exploit services: Nếu bạn chạy web server hoặc database, chúng có thể bị khai thác lỗ hổng

👉 Sau đây tôi sẽ mô phỏng 1 tình huống:

## Tạo môi trường thuận lợi thực hành:
🔹 Bước 1: SSH vào bằng key pair
Trên máy local:

```bash
chmod 400 KeytodemoMisconfig3.pem
ssh -i KeytodemoMisconfig3.pem ec2-user@<Public-IP>
```
👉 Nếu AMI là Ubuntu thì username mặc định là ubuntu.

🔹 Bước 2: Bật password login
Trong EC2:

```bash
sudo nano /etc/ssh/sshd_config
```

Đổi:

Mã
PasswordAuthentication no

Thành:

Mã
PasswordAuthentication yes
Lưu file.

Restart dịch vụ SSH:

```bash
sudo systemctl restart sshd
sudo systemctl enable sshd
sudo systemctl status sshd
```
→ cần thấy Active: active (running).

🔹 Bước 3: Tạo user test

```bash
sudo adduser demo
sudo passwd demo
```

👉 Đặt mật khẩu yếu (ví dụ 123456).

## Tình huống khi bạn bị tấn công bởi Hacker (use Hydra brute force)
Vậy trong EC2 là PasswordAuthentication yes không phải PasswordAuthentication no thì với protocol all, port range all và Source 0.0.0.0/0 (Anywhere) thì attacker chỉ cần biết username và Public IP của EC2 thì chỉ với lệnh sau là vào được máy chủ của nạn nhân:
 
                                    `hydra -l demo -P passwords.txt -t 4 ssh://<Public-IP-EC2>`

. Với passwords.txt là chứa mật khẩu để đoán mật khẩu của máy chủ nạn nhân.

## Khắc phục
Rất đơn giản là bạn làm ngược lại mọi thứ tôi làm như trên

Sửa lại `PasswordAuthentication no`
Restart `sshd`
Xóa user test:
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

## Project 
