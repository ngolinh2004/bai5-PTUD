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

 1. <img width="772" height="344" alt="image" src="https://github.com/user-attachments/assets/171dc54f-f0aa-4c56-91d8-7a2f89599588" />


   2.  <img width="722" height="228" alt="image" src="https://github.com/user-attachments/assets/876fbc76-a41d-4db7-9da5-22a635913714" />


   3. <img width="718" height="176" alt="image" src="https://github.com/user-attachments/assets/e95fafb5-dad8-4a12-a395-0eb5f6ca74c1" />


    4. <img width="803" height="1007" alt="image" src="https://github.com/user-attachments/assets/94b0cba1-1242-4602-a51f-fba50c6648b0" />


    5.<img width="785" height="992" alt="image" src="https://github.com/user-attachments/assets/986118e2-44c6-440c-bcb5-b000ca55d7c8" />


   
   6. <img width="789" height="971" alt="image" src="https://github.com/user-attachments/assets/6365feff-4aec-4c8b-a9b8-0c9b7f68214b" />
   
  7.  <img width="940" height="1057" alt="image" src="https://github.com/user-attachments/assets/b686f683-cc71-477e-be90-a8b7bbe47473" />



    8.<img width="940" height="1072" alt="image" src="https://github.com/user-attachments/assets/9c0f756b-7a13-4736-a523-677e040bf189" />

9. <img width="940" height="1070" alt="image" src="https://github.com/user-attachments/assets/a6d70eba-720a-48e0-a4c9-79f1f360513d" />

10 <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/f8f90b85-fbda-420d-b6bc-fee434b2928c" />

11.   <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/a58330b0-b2d7-4987-8fa5-83ff7b50e02f" />

12.<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/7319227c-0851-4251-b84f-ae8aeb181821" />

13. <img width="755" height="703" alt="image" src="https://github.com/user-attachments/assets/3c9927c9-c10e-4f56-8fa0-032a4dbcf264" />

14.  <img width="773" height="717" alt="image" src="https://github.com/user-attachments/assets/ce4ceab3-7776-4ddd-a132-9ba0faa5dc82" />

15.<img width="793" height="724" alt="image" src="https://github.com/user-attachments/assets/997ceda3-a84e-41a1-8bd0-5da44c6f7f3b" />

16. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/ccb873cf-1cdf-49b0-abf8-5d32b3255124" />

17. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/294d9405-4dee-4bea-814e-0934ab153b47" />

18. <img width="940" height="990" alt="image" src="https://github.com/user-attachments/assets/95e3158f-0f43-4132-9b69-a208ea2359c8" />

19. <img width="940" height="877" alt="image" src="https://github.com/user-attachments/assets/5e8183fc-1888-4926-a9b9-148ca9bc8a6f" />

20. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/e25c5c7a-74b8-4a4c-8fcb-393de449b034" />

21.<img width="940" height="921" alt="image" src="https://github.com/user-attachments/assets/029c056c-67ea-4212-bab5-59023ef295df" />

22.<img width="940" height="956" alt="image" src="https://github.com/user-attachments/assets/8f1009c7-329d-4cc5-9679-04381c74a347" />

23.  <img width="940" height="594" alt="image" src="https://github.com/user-attachments/assets/f85ca21d-4143-4ad9-96bc-fc942f13bd7d" />

24.<img width="940" height="726" alt="image" src="https://github.com/user-attachments/assets/7c845fb4-2b54-48ed-b33c-91308795acf5" />

25.  <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/e814b26e-292f-40b6-ac55-873aa3a3b609" />
    26.<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/4688dd93-1320-4f46-9c39-55cbf2ba99ef" />
   27. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/db5cc4de-615c-4556-adf1-90050b0146ad" />
   28. <img width="940" height="983" alt="image" src="https://github.com/user-attachments/assets/b008aae0-6315-43fb-aec5-428924129a95" />
  29.  <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/afd9c516-1656-49c6-aa24-37098e306c2b" />
   30. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/289b7192-6be1-499e-b74d-9f94f1ee31a8" />
   31. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/1536581c-e544-4eef-9ab0-08a044868945" />
   32. <img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/d119c3d0-1349-4fd8-91cc-5ad4d490dcfc" />





























    
    


