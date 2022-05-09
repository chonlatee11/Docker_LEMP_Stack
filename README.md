# Docker_LEMP_Stack
Settting LEMP in Docker \
LEMP เป็นกลุ่มของ Open Source Software สำหรับเขียน Website ด้วยภาษา PHP บน Docker Container
- L = Linux OS
- E = (E)Nginx Web Server
- M = MariaDB
- P = PHP

# Part 1 config and command for Apache

1. docker run --name webserver1 -p 80:80 httpd:2.4.41-alpine
    + ติดตั้ง Apache Web Server ใช้ httpd:2.4.41-alpine | ตั้งชื่อว่า webserver1 | -p คือการกำหนด port forword **พอร์ตของเครื่อง:พอร์ตของ container** |
    + เช็คด้วยการเข้า locallhost ที่ port 80 | localhost:80 | 
2. Config Docker-compose เพื่อใช้งาน Apache Web Server Container แทนการใช้คำสั่ง Docker run
    + สร้าง folder เพื่อเก็บไฟล์ html และ ไฟล์ docker-compose
    + สร้างไฟล์ docker-compose.yml | สร้าง floder เพื่อเก็บไฟล์ index.html
    + แก้ไขไฟล์ docker-compose
    + สร้าง Bridge network โดยตั้งชื่อเป็น web_network
    + รัน Container ด้วย docker-compose | สามารถดู Container ที่กำลังรันทั้งหมดที่ docker-compose.yml ดูแลได้ ใช้คำสั่ง docker-compose ps |
*หากต้องการ stop/delete เข้าไปที่ docker exec -it httpd /bin/bash และใช้คำสั่ง docker-compose down --rmi all*


# Part 2 config and command for Nginx

* สร้าง folder เพื่อเก็บไฟล์ html และ ไฟล์ docker-compose
    + สร้างไฟล์ docker-compose.yml | สร้าง floder เพื่อเก็บไฟล์ index.html |
    + แก้ไขไฟล์ docker-compose | ใช้ port 81 เพราะ 80 ใช้ไปกับ Apache แล้ว |
    + รัน container ด้วย docker-compose | docker-compose up -d |
*หากต้องการ stop/delete ใช้คำสั่ง docker-compose down --rmi all*

# Part 3 Config Nginx และ php FPM Container

* สร้าง folder เพื่อเก็บไฟล์ docker-compose , html , Nginx
* แก้ไขไฟล์ docker-compose.yml | ใช้ port 82 |
* แก้ไขไฟล์ index.php
* แก้ไขไฟล์ nginx.conf
* แก้ไขไฟล์ default.conf เราได้ตั้งค่า Nginx ให้รันใน *Folder /var/www/html* และกำหนด index file โดยใช้คำสั่ง *index index.php;* ส่วนที่จะรัน php เราได้กำหนด *fastcgi_pass php:9000* เพื่อให้ Nginx ส่ง Script ไปยัง php FPM ซึ่งเรา Config ให้เปิด Port 9000 ไว้ใน php Container ของไฟล์ docker-compose.yml 
# Nginx + php + MariaDB
  * สร้าง folder สำหรับเก็บไฟล์ sql 
  * แก้ไขไฟล์ docker-compose.yml นำไฟล์ "titanic.sql" มา Import ลงใน Mariadb โดยเมื่อรัน Container แล้ว Mariadb จะ Import ไฟล์ ".sql" ใน Folder /docker-entrypoint-initdb.d ให้อัตโนมัติถ้ามันพบว่าภายใน /var/lib/mysql/ ของ lemp_mariadb Container ยังไม่มีข้อมูล
  * สร้าง Dockerfile โดยติดตั้ง mysqli เพื่อเรียก Mariadb จาก php 
    * FROM php:7.4-fpm-alpine\
      RUN docker-php-ext-install mysqli
  * แก้ไขไฟล์ index.php
  * สร้าง php Image ด้วยคำสั่ง *docker-compose build* 
  * รัน container 
