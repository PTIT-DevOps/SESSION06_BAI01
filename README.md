# BT01 - Khảo sát FHS và Phân quyền File/Folder nâng cao (FHS and Advanced File Permissions)

## 1. Tạo cấu trúc thư mục

```bash
devops@0310263186:~$ sudo mkdir -p /var/www/my-app/public
devops@0310263186:~$ sudo mkdir -p /var/www/my-app/logs
devops@0310263186:~$ ls -la /var/www/my-app
total 16
drwxr-xr-x 4 root   root     4096 Oct  6 09:12 .
drwxr-xr-x 3 root   root     4096 Oct  6 09:12 ..
drwxr-xr-x 2 root   root     4096 Oct  6 09:12 logs
drwxr-xr-x 2 root   root     4096 Oct  6 09:12 public
```

## 2. Phân quyền thư mục public bằng số (octal)

Yêu cầu: Owner đọc/ghi, Group chỉ đọc, Others không có quyền.

```bash
devops@0310263186:~$ sudo chmod 750 /var/www/my-app/public
```

`7` = `rwx` cho Owner, `5` = `r-x` cho Group, `0` = `---` cho Others.

## 3. Phân quyền thư mục logs bằng ký tự (symbolic)

Yêu cầu: Owner và Group toàn quyền, Others không có quyền.

```bash
devops@0310263186:~$ sudo chmod u=rwx,g=rwx,o= /var/www/my-app/logs
```

`u=rwx,g=rwx,o=` tương đương `770`, tức `drwxrwx---`.

## 4. Đổi chủ sở hữu và nhóm sở hữu

Chủ sở hữu là tài khoản non-root `devops`, nhóm sở hữu là nhóm web `www-data`.

```bash
devops@0310263186:~$ getent group www-data
www-data:x:33:
devops@0310263186:~$ sudo chown -R devops:www-data /var/www/my-app
```

## 5. Kiểm tra kết quả

```bash
devops@0310263186:~$ ls -la /var/www/my-app
total 16
drwxr-xr-x 4 devops www-data 4096 Oct  6 09:14 .
drwxr-xr-x 3 root   root     4096 Oct  6 09:12 ..
drwxrwx--- 2 devops www-data 4096 Oct  6 09:14 logs
drwxr-x--- 2 devops www-data 4096 Oct  6 09:14 public

devops@0310263186:~$ stat -c "%a %U:%G %n" /var/www/my-app/public /var/www/my-app/logs
750 devops:www-data /var/www/my-app/public
770 devops:www-data /var/www/my-app/logs
```

## 6. Kiểm tra quyền của người dùng khác (Others)

```bash
devops@0310263186:~$ sudo -u nobody ls /var/www/my-app/public
ls: cannot open directory '/var/www/my-app/public': Permission denied

devops@0310263186:~$ sudo -u www-data ls /var/www/my-app/public
devops@0310263186:~$ sudo -u www-data ls /var/www/my-app/logs
```

Group `www-data` đọc được `public` và truy cập được `logs`, `nobody` (Others) bị từ chối.
