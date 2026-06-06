# bai5-PTUD
  - lý thuyết: 
    + docker là gì? 
    + các keyword được sử dụng trong docker-compose.yml
      để mô tả 1 service, network, volume,...
      liệt kê + ý nghĩa của từ khoá đó + ví dụ minh hoạ
    + ưu điểm khi triển app sử dụng docker là gì?
    + dùng docker: tạo app, test app OK trên laptop cá nhân
      giờ muốn triển khai app này trên máy chủ thật ko có internet
      thì các bước cần làm là?
  - thực hành áp dụng: APP MONITOR + ALERT DATA REALTIME
    sử dụng docker compose có nhiều serivce 
    và các thành phần cần thiết để tạo thành ứng dụng:
     + nodered liên tục lấy dữ liệu từ nguồn nào đó (chứng khoán, thời tiết, giá vàng,...)
       nguồn thực tế, số liệu luôn động sau thời gian ngắn
     + nodered lưu trữ dữ liệu vào 2 database: mariadb để lưu giá trị tức thời
       lưu lịch sử vào influxdb
     + sử dụng grafana để trực quan hoá dữ liệu: vẽ biểu đồ
     + sử dụng nginx để làm webserver
       chạy 1 trang web html+js+css làm front-end
       js: lấy dữ liệu tức thời trong mariadb qua (ajax | socket) 
           gọi api (api tự build bằng Flask giống bt1)
           api trả về giá trị tức thời trong mariadb
           hiển thị lên web, auto hiển thị số mới khi thay đổi
       sử dụng iframe để gọi grafana
       hiển thị biểu đồ dữ liệu lịch sử của thông số đã lưu
     + QUAN SÁT DỮ LIỆU LỊCH SỬ => GIÁ TRỊ BẤT THƯỜNG
       (VD MIỀN A..B: OK, DƯỚI A: ALERT LOW, TRÊN B: ALERT HIGH)
     + nodered: kết hợp bot Telegram
       khi dữ liệu not OK, thì gửi tin nhắn từ bot => group trên telegram
       group đã add bot vào: (nhóm đã có 2 người), add thêm 1875746636 thành 3 người
       mỗi khi bot gửi dữ liệu vào nhóm: mọi member of group đều nhận đc
       nội dung alert: tường minh, có value gây alert

     xuất tất cả các container ra file nén.
     xoá mọi container đang chạy
     load lại các container  từ file nén để khôi phục các container đã xoá

    ## BÀI LÀM
- lý thuyết: 
  + docker là gì?

      Docker là một nền tảng mã nguồn mở cho phép đóng gói ứng dụng cùng toàn bộ thư viện, cấu hình và môi trường chạy vào các container. Nhờ đó, ứng dụng có thể hoạt

      động giống nhau trên nhiều môi trường khác nhau như máy cá nhân, máy chủ hoặc cloud, giúp giảm lỗi do khác biệt cấu hình hệ thống.
      
  + các keyword được sử dụng trong docker-compose.yml
      để mô tả 1 service, network, volume,...
      liệt kê + ý nghĩa của từ khoá đó + ví dụ minh hoạ

 Ví dụ : 
 version: "3.9"

 services:
 
  web:
  
    build: .
    
    container_name: myweb
    
    ports:
    
      - "8000:8000"
      
    environment:
    
      - DEBUG=True
      
    volumes:
    
      - .:/app
      
    depends_on:
    
      - db
      
    networks:
    
      - backend

  db:
    image: postgres:16
    
    environment:

      POSTGRES_DB: mydb
      
      POSTGRES_USER: admin
      
      POSTGRES_PASSWORD: 123456
      
    volumes:
    
      - db_data:/var/lib/postgresql/data
      
    networks:
    
      - backend

networks:

  backend:

volumes:

  db_data:

   + Các keyword thường dùng 
     + services: Khai báo các service/container.
     + image: Chỉ định image sử dụng.
     + build: Build image từ Dockerfile.
     + ports: Ánh xạ cổng giữa host và container.
     + environment: Khai báo biến môi trường.
     + volumes: Lưu trữ và chia sẻ dữ liệu.
     + networks: Kết nối các container với nhau.
     + depends_on: Khai báo service phụ thuộc.
     + restart: Thiết lập chế độ tự khởi động lại.
     + container_name: Đặt tên container.
     + command: Ghi đè lệnh mặc định khi chạy container.
     + healthcheck: Kiểm tra trạng thái hoạt động của container.
  
 + ưu điểm khi triển app sử dụng docker là gì?
     +  Đồng nhất môi trường phát triển và triển khai.
     + Dễ dàng cài đặt và triển khai ứng dụng.
     + Cô lập các ứng dụng, tránh xung đột thư viện.
     + Tiết kiệm tài nguyên hơn máy ảo (VM).
     + Dễ dàng sao lưu và khôi phục dữ liệu.
     + Hỗ trợ mở rộng hệ thống nhanh chóng.
     + Dễ dàng nâng cấp và cập nhật phiên bản.
     + Tăng tính di động của ứng dụng.
 + dùng docker: tạo app, test app OK trên laptop cá nhân
   giờ muốn triển khai app này trên máy chủ thật ko có internet
      thì các bước cần làm là?

    + Bước 1: Build image trên laptop
       
       docker compose build


    + Bước 2: Kiểm tra chạy OK
      
      docker compose up

    + Bước 3: Export image ra file
      
     docker save -o myapp.tar myapp:v1


    + Bước 4: Copy sang server

     Ví dụ: scp myapp.tar root@server:/root

    + Bước 5: Import image trên server
      
     docker load -i myapp.tar

     Kiểm tra: docker images
    + Bước 6: Copy source cần thiết

     Dockerfile
   
     docker-compose.yml
   
    .env
   
     sang server.

    + Bước 7: Chạy ứng dụng
      
      docker compose up -d hoặc docker run -d myapp:v1
    + Bước 8: Kiểm tra

  - thực hành áp dụng: APP MONITOR + ALERT DATA REALTIM

1. Docker Setup 1,2345
   - Tạo thư mục dự án : mkdir monitor-alert
     <img width="772" height="344" alt="image" src="https://github.com/user-attachments/assets/21511931-777c-48b0-a18d-22a26b07b297" />

   - Tạo file docker-compose.yml : nano docker-compose.yml
   - Khởi động hệ thống : docker compose up -d
   - Kiểm tra container : docker ps
   - Truy cập Node-RED
2. Cấu hình Node-RED 6789 10 11 12
   
3. MariaDB 13 14 15 
   
4. InfluxDB 16 17 18 19 
   
5. Grafana 20 21 22 23 24 25 26 
    
6. Flask API 27 28 
    
7. Frontend + Nginx  29 30 
    
8. Telegram Alert 31 32
    
9. Backup & Restore 33
    

    
