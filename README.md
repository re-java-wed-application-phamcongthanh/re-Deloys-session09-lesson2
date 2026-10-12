# Bài 2: Khởi Chạy Web Server Nginx Với Port Mapping

## 🎯 1. Mục Tiêu
- Khởi chạy Web Server Nginx dạng container ở chế độ chạy ngầm (detached mode `-d`).
- Thực hành ánh xạ cổng mạng (`-p 8080:80`) giữa máy host và container.
- Kiểm tra phản hồi HTTP từ Web Server thông qua công cụ `curl`.
- Dọn dẹp an toàn container sau khi hoàn tất thử nghiệm.

---

## 📋 2. Quy Trình Thực Hiện & Giải Thích Các Tham Số

### Bước 1: Khởi chạy container Nginx Alpine
Sử dụng image siêu nhẹ `nginx:alpine`, đặt tên container là `my-web` và ánh xạ cổng `8080` của máy tính tới cổng `80` của container:
```bash
docker run -d -p 8080:80 --name my-web nginx:alpine
```

**Giải thích các cờ (flags):**
- `-d` (*detached mode*): Chạy container ngầm dưới nền, giải phóng Terminal ngay lập tức.
- `-p 8080:80` (*port publishing*): Ánh xạ cổng host `8080` vào cổng container `80` (`<Host_Port>:<Container_Port>`).
- `--name my-web`: Đặt tên định danh dễ nhớ cho container thay vì tên ngẫu nhiên.
- `nginx:alpine`: Sử dụng base image Nginx chạy trên nền Alpine Linux tối ưu dung lượng (~40MB).

**Kết quả:**
```text
5c63b00a109c168a3bc3a53626f1ed38088e077af961305d24cecc8182f5d181
```

---

### Bước 2: Kiểm tra trạng thái hoạt động của container
```bash
docker ps --filter "name=my-web"
```

**Kết quả hiển thị:**
```text
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                     NAMES
5c63b00a109c   nginx:alpine   "/docker-entrypoint.…"   6 seconds ago   Up 5 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   my-web
```
Container đang ở trạng thái `Up`, cổng `8080` đã được bind thành công.

---

### Bước 3: Gửi truy vấn HTTP kiểm tra trang web
```bash
curl http://localhost:8080
```

**Kết quả phản hồi:**
```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
> ✅ **Xác nhận**: Trang mặc định của Nginx trả về tiêu đề và dòng chữ `"Welcome to nginx!"`.

---

### Bước 4: Dọn dẹp tài nguyên (Stop & Remove container)
Dừng container và gỡ bỏ để tránh xung đột tài nguyên:
```bash
docker stop my-web && docker rm my-web
```

**Kết quả:**
```text
my-web
my-web
```

Kiểm tra lại để đảm bảo container đã được xóa sạch:
```bash
docker ps -a --filter "name=my-web"
```
*(Không còn container nào tồn tại trong danh sách)*

---

## 🔒 3. Kết Luận
- Web server Nginx đã được triển khai độc lập, an toàn và nhanh chóng thông qua Docker.
- Việc ánh xạ cổng (port forwarding) giúp truy cập ứng dụng web chạy trong container trực tiếp từ trình duyệt hoặc `curl` ở máy chủ host một cách liền mạch.
