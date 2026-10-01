# Bai 3: HTTP Basic Authentication cho /admin

## 1. File nop
- `nginx-admin.conf` — Server Block Nginx, copy len Droplet tai `/etc/nginx/sites-available/default`
  hoac `/etc/nginx/conf.d/default.conf` tuy distro.
- `.htpasswd` — file mau, vi tri chuan tren server la `/etc/nginx/.htpasswd`
  (KHONG dat trong `/var/www/` de tranh bi tai truc tiep qua web).
  - user: `admin_user`
  - password mau: `Admin@123`
  - hash apache apr1 tao bang: `openssl passwd -apr1 Admin@123`
  - tren Droplet that lam nhu de bai:
    ```bash
    sudo apt install apache2-utils -y
    sudo htpasswd -c /etc/nginx/.htpasswd admin_user
    # nhap password khi duoc hoi
    cat /etc/nginx/.htpasswd
    sudo nginx -t && sudo systemctl reload nginx
    ```

## 2. Tu test local khong can Droplet (Docker Desktop)

```powershell
cd homework/session_03/ex3
docker compose up -d
curl.exe -I http://localhost:8080/admin/
# -> HTTP/1.1 401 Unauthorized + WWW-Authenticate: Basic realm="Restricted Admin Area"

curl.exe -u admin_user:Admin@123 http://localhost:8080/admin/
# -> HTTP/1.1 200 OK (co index.html). Neu xoa index.html thi 404, nhung KHONG duoc 401.

docker compose down
```

## 3. Cau hinh quan trong (trong nginx-admin.conf)
```nginx
location /admin {
    auth_basic "Restricted Admin Area";
    auth_basic_user_file /etc/nginx/.htpasswd;
    try_files $uri $uri/ =404;
}
```

## 4. Luu y cham bai
- Request thuong `/admin` phai 401 + header `WWW-Authenticate`.
- Request kem `-u admin_user:<PASSWORD>` dung phai 200 (hoac 404 neu chua co index, nhung khong 401).
- Doi `server_name` thanh IP Droplet khi nop len server that neu can.
