# Bai2Web
# Phạm Duy Tiến Minh - K225480106048 
# Đề bài:
1. Sử dụng github để ghi lại quá trình làm, tạo repo mới, để truy cập public, edit file `readme.md`:  
   chụp ảnh màn hình (CTRL+Prtsc) lúc đang làm, paste vào file `readme.md`, thêm mô tả cho ảnh.  
2. NỘI DUNG BÀI TẬP:  
2.1. Cài đặt Apache web server:  
- Vô hiệu hoá IIS: nếu iis đang chạy thì mở cmd quyền admin để chạy lệnh: iisreset /stop  
- Download apache server, giải nén ra ổ D, cấu hình các file:  
  + D:\Apache24\conf\httpd.conf  
  + D:Apache24\conf\extra\httpd-vhosts.conf  
  để tạo website với domain: fullname.com  
  code web sẽ đặt tại thư mục: `D:\Apache24\fullname` (fullname ko dấu, liền nhau)  
- sử dụng file `c:\WINDOWS\SYSTEM32\Drivers\etc\hosts` để fake ip 127.0.0.1 cho domain này  
  ví dụ sv tên là: `Đỗ Duy Cốp` thì tạo website với domain là fullname ko dấu, liền nhau: `doduycop.com`  
- thao tác dòng lệnh trên file `D:\Apache24\bin\httpd.exe` với các tham số `-k install` và `-k start` để cài đặt và khởi động web server apache.  
2.2. Cài đặt nodejs và nodered => Dùng làm backend:  
- Cài đặt nodejs:  
  + download file `https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi`  (đây ko phải bản mới nhất, nhưng ổn định)  
  + cài đặt vào thư mục `D:\nodejs`  
- Cài đặt nodered:  
  + chạy cmd, vào thư mục `D:\nodejs`, chạy lệnh `npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"`  
  + download file: https://nssm.cc/release/nssm-2.24.zip  
    giải nén được file nssm.exe  
    copy nssm.exe vào thư mục `D:\nodejs\nodered\`  
  + tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):  
@echo off  
REM fix path  
set PATH=D:\nodejs;%PATH%  
REM Run Node-RED  
node "D:\nodejs\nodered\node_modules\node-red\red.js" -u "D:\nodejs\nodered\work" %*  
  + mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`  
  + cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"  
  + chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`  
2.3. Tạo csdl tuỳ ý trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name  
2.4. Cài đặt thư viện trên nodered:  
- truy cập giao diện nodered bằng url: http://localhost:1880  
- cài đặt các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus  
- Sửa file `D:\nodejs\nodered\work\settings.js` :   
  tìm đến chỗ adminAuth, bỏ comment # ở đầu dòng (8 dòng), thay chuỗi mã hoá mật khẩu bằng chuỗi mới  
    adminAuth: {  
        type: "credentials",  
        users: [{  
            username: "admin",  
            password: "chuỗi_mã_hoá_mật_khẩu",  
            permissions: "*"  
        }]  
    },     
   với mã hoá mật khẩu có thể thiết lập bằng tool: https://tms.tnut.edu.vn/pw.php  
- chạy lại nodered bằng cách: mở cmd, vào thư mục `D:\nodejs\nodered` và chạy lệnh `nssm restart a1-nodered`  
  khi đó nodered sẽ yêu cầu nhập mật khẩu mới vào được giao diện cho admin tại: http://localhost:1880  
2.5. tạo api back-end bằng nodered:  
- tại flow1 trên nodered, sử dụng node `http in` và `http response` để tạo api  
- thêm node `MSSQL` để truy vấn tới cơ sở dữ liệu  
- logic flow sẽ gồm 4 node theo thứ tự sau (thứ tự nối dây):   
  1. http in  : dùng GET cho đơn giản, URL đặt tuỳ ý, ví dụ: /timkiem  
  2. function : để tiền xử lý dữ liệu gửi đến  
  3. MSSQL: để truy vấn dữ liệu tới CSDL, nhận tham số từ node tiền xử lý  
  4. http response: để phản hồi dữ liệu về client: Status Code=200, Header add : Content-Type = application/json  
  có thể thêm node `debug` để quan sát giá trị trung gian.  
- test api thông qua trình duyệt, ví dụ: http://localhost:1880/timkiem?q=thị  
2.6. Tạo giao diện front-end:  
- html form gồm các file : index.html, fullname.js, fullname.css  
  cả 3 file này đặt trong thư mục: `D:\Apache24\fullname`  
  nhớ thay fullname là tên của bạn, viết liền, ko dấu, chữ thường, vd tên là Đỗ Duy Cốp thì fullname là `doduycop`  
  khi đó 3 file sẽ là: index.html, doduycop.js và doduycop.css  
- index.html và fullname.css: trang trí tuỳ ý, có dấu ấn cá nhân, có form nhập được thông tin.  
- fullname.js: lấy dữ liệu trên form, gửi đến api nodered đã làm ở bước 2.5, nhận về json, dùng json trả về để tạo giao diện phù hợp với kết quả truy vấn của bạn.  
2.7. Nhận xét bài làm của mình:  
- đã hiểu quá trình cài đặt các phần mềm và các thư viện như nào?  
- đã hiểu cách sử dụng nodered để tạo api back-end như nào?  
- đã hiểu cách frond-end tương tác với back-end ra sao?  
==============================

# Bài làm:  
2.1. Cài đặt Apache web server:  
Vô hiệu hóa IIS:  
<img width="457" height="131" alt="image" src="https://github.com/user-attachments/assets/e83d9f85-30a1-4304-9efa-9492847fc3c7" />

Tải Apache về và giải nén ra ổ D :  
<img width="977" height="249" alt="image" src="https://github.com/user-attachments/assets/8841e61f-c5a5-446b-b38c-6c39d8d7f3e2" />

Để cấu hình file httpd.conf ta mở Notepad và open file đó và chỉnh sửa:  
<img width="425" height="99" alt="image" src="https://github.com/user-attachments/assets/46fb0061-3e82-4524-ae77-e4aa1158701c" />  
<img width="818" height="261" alt="image" src="https://github.com/user-attachments/assets/908932fc-b5d8-41d5-ae60-8b869facd501" />  

Cấu hình file httpd-vhosts.conf tương tự như làm với file trên:  
<img width="647" height="219" alt="image" src="https://github.com/user-attachments/assets/2177682a-1aec-4118-acce-514dbfee8cd4" />

Tạo 1 thư mục chứa code web (html,css,js) trong thư mục Apache24:   
<img width="752" height="155" alt="image" src="https://github.com/user-attachments/assets/8dc73d58-b399-40fe-8bae-b8dfe5c19ce8" />

Sử dung Nodepad chạy quyền Admin mở vị trí file c:\WINDOWS\SYSTEM32\Drivers\etc\hosts để fake ip 127.0.0.1 cho domain:   
<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/069b788f-95de-40ec-ac0b-7be40e3668ff" />

Thao tác các dòng lệnh trên file D:\BT\New\Apache24\bin\httpd.exe và khởi đồng web server Apache:   
<img width="710" height="286" alt="image" src="https://github.com/user-attachments/assets/b91eeda4-1ad9-4db4-b28a-9bf3de0bc301" />
<img width="311" height="144" alt="image" src="https://github.com/user-attachments/assets/7a3577f0-bdfb-4a24-abd4-a33ca36b5868" />

2.2. Cài đặt nodejs và nodered => Dùng làm backend:  

Cài đặt Nodejs: (từ link: https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi) chạy và cài vào thư mục D:BT\New\nodejs  
<img width="614" height="479" alt="image" src="https://github.com/user-attachments/assets/d508b02b-9670-4a53-a9dc-e2afc1a473bb" />

Cài đặt nodered:  
Chạy các lệnh trên CMD: npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered  
<img width="800" height="295" alt="image" src="https://github.com/user-attachments/assets/465bba09-4a60-4ba3-8759-dcdbcaa0fc51" />

download file: https://nssm.cc/release/nssm-2.24.zip, giải nén vào D:\nodejs\nodered<br>  
<img width="881" height="155" alt="image" src="https://github.com/user-attachments/assets/dce727b3-629c-42a1-87e0-4d5738f124d0" />

tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):  
<img width="947" height="752" alt="image" src="https://github.com/user-attachments/assets/e44ae3cc-cd35-4911-8049-98aa987f801c" />

mở cmd, chuyển đến thư mục: D:\nodejs\nodered  
<img width="824" height="416" alt="image" src="https://github.com/user-attachments/assets/d5767dda-8958-4bb6-9e65-8d2e0f285169" />  

cài đặt service a1-nodered bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"  
<img width="709" height="483" alt="image" src="https://github.com/user-attachments/assets/5d25b433-f7ea-40be-b958-42ec18b92859" />  
  
  + mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`  
  + cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"  
  + chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`
<img width="768" height="628" alt="image" src="https://github.com/user-attachments/assets/afa88881-1560-4951-8d28-3664ab50f5fc" />
2.3. Tạo csdl SQL Server 2022:
Database:
<img width="920" height="401" alt="image" src="https://github.com/user-attachments/assets/3d67c9a7-0496-4a86-85c7-9d1b6595dce6" />
Table và các thuộc tính:
<img width="660" height="268" alt="image" src="https://github.com/user-attachments/assets/e3c39d67-a5ca-4d55-b2ee-3f9659128432" />
Dữ liệu:
<img width="677" height="330" alt="image" src="https://github.com/user-attachments/assets/377020b5-1988-4f8a-83da-1887aa11a780" />

2.4. Cài đặt thư viện trên nodered:  

Truy cập url: http://localhost:1880 và cài các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus  
<img width="1293" height="883" alt="image" src="https://github.com/user-attachments/assets/b3f899db-05ae-4b9a-8a4c-791ad31aef48" />  
Sửa file D:\nodejs\nodered\work\settings.js bằng nodepad quyền Admin: mật khẩu đã được mã hóa qua tool: https://tms.tnut.edu.vn/pw.php  
<img width="812" height="213" alt="image" src="https://github.com/user-attachments/assets/882ad365-8c1b-460a-9f45-197468d462be" />  
Chạy lại nodered qua CMD:  
<img width="549" height="326" alt="image" src="https://github.com/user-attachments/assets/526f12cd-e63f-4aef-a53b-77a9d9f6d7a3" />
