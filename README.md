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

xong cấu hình Django.

tiếp theo đến phần tạo model

chạy lệnh ``` sudo nano django_app/app/pawn/models.py``` để vào file model.py

```
from django.db import models


class KhachHang(models.Model):

    ho_ten = models.CharField(max_length=100)

    cccd = models.CharField(max_length=20)

    sdt = models.CharField(max_length=15)

    dia_chi = models.TextField()

    def __str__(self):
        return self.ho_ten


class TaiSanCam(models.Model):

    ten_tai_san = models.CharField(max_length=100)

    mo_ta = models.TextField()

    gia_tri_uoc_tinh = models.FloatField()

    tinh_trang = models.CharField(max_length=100)

    khach_hang = models.ForeignKey(
        KhachHang,
        on_delete=models.CASCADE
    )

    def __str__(self):
        return self.ten_tai_san


class HopDongCam(models.Model):

    khach_hang = models.ForeignKey(
        KhachHang,
        on_delete=models.CASCADE
    )

    tai_san = models.ForeignKey(
        TaiSanCam,
        on_delete=models.CASCADE
    )

    ngay_cam = models.DateField()

    ngay_het_han = models.DateField()

    so_tien_cam = models.FloatField()

    lai_suat = models.FloatField()

    trang_thai = models.CharField(max_length=50)

    def __str__(self):
        return f"HopDong {self.id}"


class ThanhToan(models.Model):

    hop_dong = models.ForeignKey(
        HopDongCam,
        on_delete=models.CASCADE
    )

    ngay_thanh_toan = models.DateField()

    so_tien = models.FloatField()

    def __str__(self):
        return f"ThanhToan {self.id}"
```

Vào container: ```docker-compose exec django bash```

Tạo migration: ```python manage.py makemigrations```

<img width="897" height="195" alt="Screenshot 2026-05-09 154100" src="https://github.com/user-attachments/assets/99dfd38f-1079-4af6-9ad1-f16e2c4fe9f8" />

apply migration: ```python manage.py migrate```

<img width="1095" height="566" alt="Screenshot 2026-05-09 154354" src="https://github.com/user-attachments/assets/9837d50f-e969-4f5d-ba0e-b031e80aefd6" />

mục tiêu: để Django tự tạo bảng trong MariaDB.

tiếp theo, tạo admin user: ```python manage.py createsuperuser```

điền các thông tin yêu cầu vào, sau dó nhấn enter.

<img width="995" height="232" alt="Screenshot 2026-05-09 154543" src="https://github.com/user-attachments/assets/19afbc24-e11a-46e5-9890-f44a2ba3febe" />

xong bước 2.

## Bước 3: ĐĂNG KÝ MODEL VÀO DJANGO ADMIN

sửa admin.py: ```sudo nano django_app/app/pawn/admin.py```

```
from django.contrib import admin

# Register your models here.
from .models import *

admin.site.register(KhachHang)
admin.site.register(TaiSanCam)
admin.site.register(HopDongCam)
admin.site.register(ThanhToan)
```

lưu lại.

Ý nghĩa:

Đăng ký model để:
- thêm
- sửa
- xoá

trên Django Admin.

tiếp theo, ĐỔI Dockerfile VỀ RUNSERVER: ``` sudo nano django_app/Dockerfile```

sửa dòng cuối thành ```CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]```

lưu lại.

build lại project: ```docker-compose up -d --build```

truy cập http://192.168.56.10:8000/ để kiểm tra
<img width="1918" height="1078" alt="Screenshot 2026-05-09 162344" src="https://github.com/user-attachments/assets/c1b6ea1f-21fb-4480-99a9-c161c85b9b6d" />

trang admin: 
<img width="1915" height="1075" alt="Screenshot 2026-05-09 162453" src="https://github.com/user-attachments/assets/35cca16e-d99d-4235-b745-8e6a9392c32f" />
<img width="1918" height="933" alt="Screenshot 2026-05-09 162717" src="https://github.com/user-attachments/assets/cd7c8dd5-4538-476b-b5af-6459acef755b" />

test chức nang admin:

thêm khách hàng: 
<img width="1918" height="876" alt="Screenshot 2026-05-09 162912" src="https://github.com/user-attachments/assets/cf1e1b29-1427-4a77-bbf7-94954ba11c65" />

sửa thông tin:
<img width="1507" height="795" alt="Screenshot 2026-05-09 163131" src="https://github.com/user-attachments/assets/cdbc9250-5853-447b-a723-cb427812f22c" />
<img width="1523" height="541" alt="Screenshot 2026-05-09 163137" src="https://github.com/user-attachments/assets/fbf7e2bb-6373-462f-bef4-7ab37ad138be" />

xóa khách hàng:
<img width="1467" height="535" alt="Screenshot 2026-05-09 163227" src="https://github.com/user-attachments/assets/6a27dc4f-e458-484d-bdb7-a1c25084764f" />
<img width="1662" height="785" alt="Screenshot 2026-05-09 163241" src="https://github.com/user-attachments/assets/ba5e90b8-ce96-4a9b-aa7b-6f1eb6ebf437" />

test admin xong.

tiếp theo đến phần sử dụng template (file html, sử dụng cú pháp jinja2), lấy context từ 1 view home_page, để tạo trang liệt kê các con nợ đến hạn mà chưa trả tiền.

vào folder app: ```cd ~/pawnshop/django_app/app```

tạo folder: ```mkdir templates```
tạo file html: ```sudo nano templates/home.html```

code file:
```
<!DOCTYPE html>
<html>
<head>
    <title>Danh sách con nợ</title>
</head>
<body>

    <h1>Danh sách con nợ đến hạn chưa trả</h1>

    <table border="1" cellpadding="10">

        <tr>
            <th>Khách hàng</th>
            <th>Tài sản</th>
            <th>Số tiền vay</th>
            <th>Ngày hết hạn</th>
        </tr>

        {% for hopdong in danh_sach_no %}

        <tr>
            <td>{{ hopdong.khach_hang.ho_ten }}</td>
            <td>{{ hopdong.tai_san.ten_tai_san }}</td>
            <td>{{ hopdong.so_tien_vay }}</td>
            <td>{{ hopdong.ngay_het_han }}</td>
        </tr>

        {% endfor %}

    </table>

</body>
</html>
```
<img width="1918" height="1078" alt="Screenshot 2026-05-09 163810" src="https://github.com/user-attachments/assets/e348322e-1f95-4ed0-b8c6-3db3d8db20e7" />


sửa settings.py: ```sudo nano config/settings.py```

tìm ```'DIRS': [],``` sửa thành ```'DIRS': [BASE_DIR / 'templates'],1```
<img width="1085" height="137" alt="Screenshot 2026-05-09 164109" src="https://github.com/user-attachments/assets/5f81a8d8-feac-4640-981b-6253d800e57d" />

tạo views: ```sudo nano pawn/views.py```
<img width="1337" height="652" alt="Screenshot 2026-05-09 164239" src="https://github.com/user-attachments/assets/ae41847c-bde0-43f1-b9a7-03467df6603c" />

url app: ```sudo nano pawn/urls.py```
<img width="1125" height="522" alt="Screenshot 2026-05-09 164429" src="https://github.com/user-attachments/assets/99b70b64-362f-4c30-bc47-617fb08287dd" />

url project: ```sudo nano config/urls.py```
<img width="1013" height="693" alt="Screenshot 2026-05-09 164651" src="https://github.com/user-attachments/assets/aae3f17f-c082-4c5f-8cbe-d496ba2ca897" />

nhớ lưu toàn bộ lại. sau đó restart lại django: ```docker-compose restart django```

test: 

<img width="1918" height="793" alt="Screenshot 2026-05-09 173202" src="https://github.com/user-attachments/assets/368c7c6f-09aa-4f4a-a619-17b8339e8b5f" />

