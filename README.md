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

1. Khởi tạo môi trường Docker Compose
   - Tạo cấu trúc thư mục dự án
     <img width="772" height="344" alt="image" src="https://github.com/user-attachments/assets/809557fa-d6d2-442a-a6b6-e32cd6cdb5bb" />

   - Tạo file Docker Compose
     <img width="722" height="228" alt="image" src="https://github.com/user-attachments/assets/6a50a9ab-cddd-4053-8291-4c4d57fb7261" />

   - Khởi động hệ thống bằng Docker Compose
     <img width="718" height="176" alt="image" src="https://github.com/user-attachments/assets/b7a47f78-c2e8-4f7f-80e7-a825d64587aa" />

   - Kiểm tra các container đang hoạt động
     <img width="803" height="1007" alt="image" src="https://github.com/user-attachments/assets/dcad6fbe-204f-4fde-b7b7-b9143809e313" />

2. Xây dựng luồng thu thập dữ liệu bằng Node-RED 
   - Truy cập giao diện Node-RED
    <img width="785" height="992" alt="image" src="https://github.com/user-attachments/assets/caef522d-2bb7-43c1-8efc-b64cb248847d" />

   - Tạo luồng lấy dữ liệu thời tiết
     <img width="789" height="971" alt="image" src="https://github.com/user-attachments/assets/df7167dc-5249-4a3f-b3f8-b40b3083884b" />

   - Kiểm tra dữ liệu từ API thời tiết
     <img width="940" height="1057" alt="image" src="https://github.com/user-attachments/assets/01712213-a2ae-45b4-bc0a-ed01dd54b52d" />

   - Kiểm tra dữ liệu nhận được trong Node-RED
     <img width="940" height="1072" alt="image" src="https://github.com/user-attachments/assets/b1ba90df-6b82-4221-8643-46270fbcd60e" />

   - Tạo luồng lưu dữ liệu vào MariaDB
     <img width="940" height="1070" alt="image" src="https://github.com/user-attachments/assets/edb30f37-cea9-47aa-adb6-f1186a062a96" />

   - Tạo luồng lưu dữ liệu vào InfluxDB
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/12a002cd-8d7a-49fc-9a10-d1f6750d0ee1" />

3. Lưu trữ dữ liệu thời gian thực bằng MariaDB 
  - Tạo cơ sở dữ liệu MariaDB
    <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/d429608c-7c2b-4808-9364-76b5cd1c6568" />

  - Kiểm tra dữ liệu trong bảng weather
    <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/34e3bb86-390d-4360-9b2b-f86c5dc4fa4c" />

4. Lưu trữ dữ liệu lịch sử bằng InfluxDB 
   - Tạo Organization và Bucket trong InfluxDB
     <img width="755" height="703" alt="image" src="https://github.com/user-attachments/assets/5238b504-43da-4437-815a-0818598d62e3" />

   - Kiểm tra dữ liệu trong Data Explorer
     <img width="773" height="717" alt="image" src="https://github.com/user-attachments/assets/c0b0276f-aeda-4f7b-9e69-9e2087d8a300" />

5. Trực quan hóa dữ liệu bằng Grafana 
   - Cấu hình Data Source cho Grafana
     <img width="793" height="724" alt="image" src="https://github.com/user-attachments/assets/15ddda05-7560-435e-9235-4612cffff740" />

   - Tạo Dashboard giám sát dữ liệu
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/1c6ba93a-9261-4469-b331-856defb22156" />

   - Tạo biểu đồ nhiệt độ
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/5b6377d1-2edc-4efc-a1ed-38f76499102e" />

   - Tạo biểu đồ độ ẩm
     <img width="940" height="990" alt="image" src="https://github.com/user-attachments/assets/d6000935-3a2d-446e-8af7-d40eda3ab15a" />


6. Xây dựng REST API bằng Flask 
   - Xây dựng API lấy dữ liệu bằng Flask
     <img width="940" height="877" alt="image" src="https://github.com/user-attachments/assets/a903db7c-c7d3-4a63-8254-0a824773198f" />

   - Kiểm tra API trả về dữ liệu JSON
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/2e4f3f6a-3131-4233-bce0-a8dcab634b38" />

7. Xây dựng giao diện Web bằng Nginx 
   - Xây dựng giao diện Web bằng Nginx
     <img width="940" height="921" alt="image" src="https://github.com/user-attachments/assets/9b771db0-ac2a-4d36-8e86-919da02f84c8" />

   - Cấu hình AJAX lấy dữ liệu thời gian thực
     <img width="940" height="956" alt="image" src="https://github.com/user-attachments/assets/ef009ae4-0a14-4079-9efa-3845aed50c96" />

   - Hiển thị dữ liệu thời gian thực trên Website
     <img width="940" height="594" alt="image" src="https://github.com/user-attachments/assets/59c79015-b98e-422b-b7a6-2ad192370fd2" />

   - Nhúng Dashboard Grafana vào Website
     <img width="940" height="726" alt="image" src="https://github.com/user-attachments/assets/4cd741e5-ba03-4285-96e5-671f644ffdd9" />

8. Cấu hình hệ thống cảnh báo Telegram
   - Tạo luồng giám sát và phát hiện cảnh báo
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/e34d80db-56d9-4e26-bb0c-51b660884aca" />

   - Cấu hình Function Node xử lý cảnh báo
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/84ef380d-1e11-41e4-8bac-e345008e4c76" />

   - Cấu hình Telegram Bot
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/6115a9fc-9a0e-47bf-9783-bcca7c832087" />

   - Thêm Bot vào nhóm Telegram
     <img width="940" height="983" alt="image" src="https://github.com/user-attachments/assets/59f5bc6f-cc7c-4830-a5e7-92a0ff17966c" />

   - Kiểm tra cảnh báo vượt ngưỡng (ALERT HIGH)
   - Kiểm tra cảnh báo dưới ngưỡng (ALERT LOW)
     <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/e71abe09-ef95-4263-97ec-6cd51a0f6a07" />

9. Sao lưu và khôi phục hệ thống Docker 
    - Thực hiện sao lưu Docker Container
      <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/dee23b6d-752d-4051-9ca2-98b023515586" />

    - Xóa các Container đang hoạt động
      <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/eaf2c836-afc6-4d8a-b56b-8cfc9e89b1b9" />

    - Khôi phục hệ thống từ bản sao lưu
      <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/961c32c9-bd12-47f6-8b32-6be462f2014c" />

      <img width="654" height="1454" alt="image" src="https://github.com/user-attachments/assets/cdf8960a-2c0d-4cc1-8c3a-5bca3662973a" />

