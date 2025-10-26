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

Tải Apache về và giải nén ra ổ D ( ở đây em giải nén ra ổ G):  

Cấu hình file D:\Apache24\conf\httpd.conf:  

Cấu hình file D:Apache24\conf\extra\httpd-vhosts.conf:    

Tạo 1 thư mục chứa code web (html,css,js) trong thư mục Apache24:   

Sử dung Nodepad chạy quyền Admin mở vị trí file c:\WINDOWS\SYSTEM32\Drivers\etc\hosts để fake ip 127.0.0.1 cho domain:   

Thao tác các dòng lệnh trên file D:\Apache24\bin\httpd.exe và khởi đồng web server Apache:   

2.2. Cài đặt nodejs và nodered => Dùng làm backend:  

Cài đặt Nodejs: (từ link: https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi) chạy và cài vào thư mục G:\nodejs  

Cài đặt nodered:  
Chạy các lệnh trên CMD: npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered  

download file: https://nssm.cc/release/nssm-2.24.zip, giải nén vào D:\nodejs\nodered<br>  

tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):  

mở cmd, chuyển đến thư mục: D:\nodejs\nodered  

cài đặt service a1-nodered bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"  

chạy service a1-nodered bằng lệnh: nssm start a1-nodered  

2.3. Tạo csdl với đề tài là đặt lịch khám online:  

2.4. Cài đặt thư viện trên nodered:  

truy cập url: http://localhost:1880 và cài các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus  

Sửa file D:\nodejs\nodered\work\settings.js bằng nodepad quyền Admin: mật khẩu đã được em mã hóa qua tool: https://tms.tnut.edu.vn/pw.php  

Chạy lại nodered qua CMD:  

Truy cập lại url: http://localhost:1880 và đăng nhập với tài khoản admin:  

2.5. tạo api back-end bằng nodered:  

Test tìm kiếm qua url:  

Test Get dữ liệu và nhập dữ liệu từ web vào database:  

Get:  
 
Nhập:  

2.7. Nhận xét bài làm của mình:  
