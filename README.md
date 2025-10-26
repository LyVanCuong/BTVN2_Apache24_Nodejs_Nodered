
==============================<br>
NGÀY GIAO: 19/10/2025<br>
==============================<br>
DEADLINE: 26/10/2025<br>
==============================<br>
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
-------------------------------------------------------------------------------------------------

Bài làm:
2.1. Cài đặt Apache web server:
- Vô hiệu hóa IIS: <br>
  <img width="640" height="376" alt="image" src="https://github.com/user-attachments/assets/51ffdd9a-fe4c-497e-b757-fd7b1f380c88" /><br>
  
- Tải Apache về và giải nén ra ổ D ( ở đây em giải nén ra ổ G): <br>
<img width="919" height="165" alt="image" src="https://github.com/user-attachments/assets/d8737122-a1fd-4a84-a3c0-36297006aa99" /><br>

 + Cấu hình file D:\Apache24\conf\httpd.conf:<br>
 <img width="328" height="88" alt="image" src="https://github.com/user-attachments/assets/7abace23-6ee9-4fff-b477-11e316c6a857" /><br>
 <img width="338" height="78" alt="image" src="https://github.com/user-attachments/assets/ab4eef4a-055b-4ac2-ad6b-381c1c806683" /><br>
 <img width="353" height="71" alt="image" src="https://github.com/user-attachments/assets/10ad08f3-858d-41a6-a1d5-7a4547f8c08a" /><br>
 <img width="365" height="113" alt="image" src="https://github.com/user-attachments/assets/547fc5be-f0d5-4555-bd0c-ad98767e5cbe" /><br>
 
 + Cấu hình file D:Apache24\conf\extra\httpd-vhosts.conf:<br>
 <img width="728" height="353" alt="image" src="https://github.com/user-attachments/assets/efcd2330-d103-4aeb-aab2-c7ae58b387e3" /><br>

 + Tạo 1 thư mục chứa code web (html,css,js) trong thư mục Apache24: <br>
 <img width="716" height="307" alt="image" src="https://github.com/user-attachments/assets/f59bb2a9-ea0f-4524-8129-b657b5c85f86" /><br>

+ Sử dung Nodepad chạy quyền Admin mở vị trí file c:\WINDOWS\SYSTEM32\Drivers\etc\hosts để fake ip 127.0.0.1 cho domain: <br>
<img width="954" height="427" alt="image" src="https://github.com/user-attachments/assets/148ffb5f-5e19-432b-9e20-d89aa3aacf80" /><br>
<img width="743" height="625" alt="image" src="https://github.com/user-attachments/assets/b4bd34f7-c72f-4e0e-b3fc-285960c53e6f" /><br>

+ Thao tác các dòng lệnh trên file D:\Apache24\bin\httpd.exe và khởi đồng web server Apache: <br>
<img width="391" height="107" alt="image" src="https://github.com/user-attachments/assets/9282d3df-e203-42e0-ab89-206f85e83ed3" /><br>

  <img width="995" height="348" alt="image" src="https://github.com/user-attachments/assets/04d26a78-05b6-4bd2-b1c7-406226d42ae6" /><br>

2.2. Cài đặt nodejs và nodered => Dùng làm backend:
- Cài đặt Nodejs: (từ link: https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi) chạy và cài vào thư mục G:\nodejs <br>

<img width="934" height="438" alt="image" src="https://github.com/user-attachments/assets/6057c2b3-b444-4f72-928d-953be5f5853d" /><br>

- Cài đặt nodered:
  + Chạy các lệnh trên CMD: npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered <br>
  <img width="817" height="259" alt="image" src="https://github.com/user-attachments/assets/5b9f06ad-7f62-413a-8e97-29227eb6895d" /><br>

+ download file: https://nssm.cc/release/nssm-2.24.zip, giải nén vào D:\nodejs\nodered\<br>
+ tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):<br>
<img width="1094" height="502" alt="image" src="https://github.com/user-attachments/assets/a74c9189-cb38-4d6c-86cd-aae1e217b22e" /><br>

+ mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`
+ cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"<br>
  <img width="1047" height="108" alt="image" src="https://github.com/user-attachments/assets/5cd31fa2-69f6-4adb-a823-e152d23c18c1" /><br>

+ chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`<br>
<img width="633" height="148" alt="image" src="https://github.com/user-attachments/assets/2318a88c-7f0a-4586-9521-d923af00169e" /><br>

2.3. Tạo csdl với đề tài là đặt lịch khám online: <br>
<img width="419" height="270" alt="image" src="https://github.com/user-attachments/assets/513f7315-519c-4123-a94f-7c1f2deba627" /><br>


2.4. Cài đặt thư viện trên nodered:

- truy cập url: http://localhost:1880 và cài các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus <br>

<img width="465" height="536" alt="image" src="https://github.com/user-attachments/assets/aaa7d3ad-77a4-4940-93b1-a70dbd339b73" /><br>

<img width="726" height="843" alt="image" src="https://github.com/user-attachments/assets/57a18476-756b-4087-9c8a-aad39fb9db70" /><br>

- Sửa file `D:\nodejs\nodered\work\settings.js` bằng nodepad quyền Admin:
  mật khẩu đã được em mã hóa qua tool: https://tms.tnut.edu.vn/pw.php <br>
  <img width="992" height="354" alt="image" src="https://github.com/user-attachments/assets/479999ff-28f3-47aa-8a47-0452ea402707" /><br>

- Chạy lại nodered qua CMD: <br>
<img width="652" height="134" alt="image" src="https://github.com/user-attachments/assets/9d31976b-03e0-40b9-a39d-52cc61507492" /><br>

- Truy cập lại url: http://localhost:1880 và đăng nhập với tài khoản admin: <br>
<img width="1270" height="1024" alt="image" src="https://github.com/user-attachments/assets/92a80aba-ed9c-4bbe-a1a9-166c47aac0df" /><br>

2.5. tạo api back-end bằng nodered:

<img width="891" height="546" alt="image" src="https://github.com/user-attachments/assets/d3ab39e0-7def-46a1-ab46-c387b1699735" /><br>

- Test tìm kiếm qua url: <br>
<img width="1737" height="616" alt="image" src="https://github.com/user-attachments/assets/9aa2358d-15b4-462a-9f44-9d870cf5d2a6" /><br>

- Test Get dữ liệu và nhập dữ liệu từ web vào database: <br>
 + Get: <br>
<img width="796" height="198" alt="image" src="https://github.com/user-attachments/assets/d9867065-4e39-4146-8687-5c539f14fbbc" /><br>
<img width="1120" height="802" alt="image" src="https://github.com/user-attachments/assets/63f378b7-6444-4d87-a056-dfe92e8dbf8c" /><br>

 + Nhập: <br>
 <img width="1122" height="866" alt="image" src="https://github.com/user-attachments/assets/12c7daa8-b469-4397-8f05-b4846659ee5b" /><br>
 <img width="1178" height="157" alt="image" src="https://github.com/user-attachments/assets/d8d5b78d-8b25-40b6-b090-e546605017f9" /><br>

<img width="1151" height="855" alt="image" src="https://github.com/user-attachments/assets/d0217e4f-60d6-4007-9e57-d7de7773f45c" /><br>

2.7. Nhận xét bài làm của mình:<br>
- Em đã hiểu được quá trình cài đặt các phần mềm web server Apache thay vì IIS để host file html,css,js
- Nodejs dùng để chạy nodered thông qua npm
- Cài đặt được các thư viện trên nodered để hỗ trợ kết nối tới SQL Server
- Sử dụng fetch API để gọi HTTP requests
- Hiểu cách gửi data qua GET(query params) và POST (JSON body)
- Biết cách xử lý async/await và Promise
- Hiểu cách parse JSON response từ backend
- Hiểu flow: User submit form → JS gửi request → Node-RED xử lý → SQL → Response → JS hiển thị kết quả






  







 

 

   

  
