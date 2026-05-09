# MaNguonMo_BT2
Bài tập 02 của sinh viên: K225480106057 - Phạm Mạnh Quỳnh - Môn Phát triển ứng dụng mã nguồn mở.

# 1. TỔ CHỨC CSDL CHO HỆ THỐNG QUẢN LÝ TIỆM CẦM ĐỒ
<img width="576" height="1280" alt="z7807400450101_a79371f17b76dc193a06fcc5e06505da" src="https://github.com/user-attachments/assets/188c8bbc-fa3e-4151-807b-f9964ec58eb6" />

# 2. Cấu trúc thư mục

Em không thực hiện lại các bước cài dặt docker, docker-compose và cấu hình ssh nữa do đã làm ở bài trước rồi.

<img width="947" height="477" alt="Screenshot 2026-05-09 143944" src="https://github.com/user-attachments/assets/5b3ce79b-33f6-4f6b-9d53-beca1ed768bf" />

Project gồm:
  
  - docker-compose.yml
  - Dockerfile
  - requirements.txt
  - source Django

```text
pawnshop/
├── docker-compose.yml
├── django_app/
```

<img width="947" height="477" alt="Screenshot 2026-05-09 143944" src="https://github.com/user-attachments/assets/5e06dae2-5bb5-43c5-9fa1-a414782640db" />

# 3. Các bước thực hiện
## Bước 1: tạo các file cần thiết để chạy docker

Tạo các file Dockerfile và requirements.txt:

<img width="1918" height="947" alt="Screenshot 2026-05-09 145007" src="https://github.com/user-attachments/assets/e171ae25-65e3-4c25-a9b4-c96784a6d1ea" />
<img width="1132" height="466" alt="Screenshot 2026-05-09 145014" src="https://github.com/user-attachments/assets/f95da6bb-5f51-4661-b44c-ae88cadfffa0" />

Tạo file Docker-compose.yml:
<img width="1917" height="1031" alt="Screenshot 2026-05-09 145208" src="https://github.com/user-attachments/assets/b9d5d195-8dd2-4798-b2b9-4b068ba62aed" />

Chạy lệnh ```docker-compose up -d``` để build (trước đó cần cấu hình để lệnh docker chạy mà không cần sudo, đã hướng dẫn):
<img width="1025" height="308" alt="Screenshot 2026-05-09 145910" src="https://github.com/user-attachments/assets/d2abc662-eda6-4a46-b0fa-cda12694aac5" />

kiểm tra: 
<img width="1917" height="302" alt="Screenshot 2026-05-09 145929" src="https://github.com/user-attachments/assets/b405c70f-8e43-4867-9e3c-30188d96cffe" />

Do project Django chưa tồn tại nên container bị restart liên tục vì không tìm thấy manage.py.

tạm thời down docker bằng lệnh ``` docker-compose down```

sau đó:
- đổi CMD sang tail -f /dev/null
- vào container tạo project Django
- build lại container

kết quả:
<img width="1897" height="158" alt="Screenshot 2026-05-09 152633" src="https://github.com/user-attachments/assets/70d3a417-e6ea-47c2-95a2-a09b345a3a9d" />

hoàn thành bước 1.

## Bước 2: CẤU HÌNH DJANGO + TẠO MODEL

Chạy lệnh ``` sudo nano django_app/app/config/settings.py``` để sửa settings.py NGOÀI UBUNTU

thêm app pawn:
<img width="1918" height="1078" alt="Screenshot 2026-05-09 151426" src="https://github.com/user-attachments/assets/5c65e524-300a-423f-be9d-206e20131e31" />

sửa database: 
<img width="1907" height="851" alt="Screenshot 2026-05-09 151435" src="https://github.com/user-attachments/assets/11125334-3ace-462a-b30a-611d6c8ea815" />

tìm đoạn ```ALLOWED_HOSTS = []``` và sửa thành ```ALLOWED_HOSTS = ['*']```.

lưu file lại.

test config:
<img width="872" height="128" alt="Screenshot 2026-05-09 153512" src="https://github.com/user-attachments/assets/e02258af-d0c6-4892-a599-7488192e2f94" />



