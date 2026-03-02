# Demo-EC2-Security-Group-Misconfiguration

Bước 1: Tạo EC2
Vào AWS Management Console → chọn EC2.

Nhấn Launch Instance.

Đặt tên instance (ví dụ: Demo-SG-Misconfig).

Chọn Amazon Linux 2 AMI (Free tier eligible).

Chọn loại máy: t2.micro (Free tier).

Key pair: tạo mới hoặc dùng key pair có sẵn để SSH.

Network settings: tạo Security Group mới.





Cách chỉnh Security Group sai cấu hình (demo misconfig)
Trong phần Firewall (security groups), chọn Create security group.

Đặt tên (ví dụ: sg-misconfig-demo).

Ở mục Inbound rules, thêm rule:

Type: All traffic

Protocol: All

Port range: All

Source type: Anywhere

Source: 0.0.0.0/0

Lưu lại.
👉 Đây là cấu hình cực kỳ nguy hiểm vì mở toàn bộ cổng cho tất cả mọi người trên Internet.





Bước 3: Phân tích rủi ro
Port scanning: Hacker có thể quét toàn bộ cổng để tìm dịch vụ đang chạy.

Brute force SSH: Nếu SSH mở ra toàn Internet, kẻ tấn công có thể thử mật khẩu liên tục.

Exploit services: Nếu bạn chạy web server hoặc database, chúng có thể bị khai thác lỗ hổng

Tôi cần 1 mô phỏng thực hành nhanh , lab bạn ơi để kèm lý thuyết chứ ko phải lý thuyết riêng
