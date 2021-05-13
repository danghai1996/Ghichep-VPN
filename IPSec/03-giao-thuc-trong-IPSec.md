# Các giao thức trong IPSec

Để trao đổi và thỏa thuận các thông số nhằm tạo nên một môi trường bảo mật giữa 2 đầu cuối, IPSec dùng 3 giao thức:
- IKE (Internet Key Exchange) - RFC 2409
- ESP (Encapsulation Security Payload) - RFC 2406
- AH (Authentication Header) - RFC 2402

## 1. IKE (Internet Key Exchange)
Là giao thức thực hiện quá trình trao đổi khóa và thỏa thuận các thông số bảo mật như: 
- Thuật toán mã hóa được áp dụng
- Khoảng thời gian khóa cần được thay đổi.

Sau khi thỏa thuận xong thì sẽ thiết lập "hợp đồng" giữa 2 bên, khi đó IPSec SA được tạo ra.

Ngoài ra IKE còn dùng 2 giao thức khác để chứng thực đầu cuối và tạo khóa: ISAKMP (Internet Security Association and Key Management Protocol) và Oakley.

- ISAKMP: là giao thức thực hiện việc thiết lập, thỏa thuận và quản lý chính sách bảo mật SA.

- Oakley: là giao thức làm nhiệm vụ chứng thực khóa, bản chất là dùng thuật toán Diffie-Hellman để trao đổi khóa bí mật thông qua môi trường chưa bảo mật.

Giao thức IKE dùng **UDP port 500**

## 2. ESP (Encapsulation Security Payload)
ESP là giao thức cung cấp sự an toàn, toàn vẹn, chứng thực nguồn dữ liệu và những tùy chọn khác. 

ESP cung cấp gần như toàn bộ tính năng của IPSec, ngoài ra nó còn cung cấp tính năng mã hóa dữ liệu. Do đó, ESP được sử dụng phổ biến trong IPSec VPN.

Các tính năng của ESP:
- Tính bảo mật (Data confidentiality)
- Tính toàn vẹn dữ liệu (Data integrity)
- Chứng thực nguồn dữ liệu (Data origin authentication)
- Tránh trùng lặp (Anti-replay)

Những tính năng trên cũng là những tính năng đặc trưng và chính yếu nhất của IPSec.

ESP sử dụng **IP protocol number 50**.

## 3. AH (Authentication Header)
AH là giao thức cung cấp sự toàn vẹn, chứng thực nguồn gốc dữ liệu và một số tùy chọn khác. Nhưng khác với ESP, nó không cung cấp chức năng bảo mật.

AH đảm bảo dữ liệu không bị thay đổi trong quá trình truyền dẫn nhưng không mã hóa dữ liệu.

# Tài liệu tham khảo:
- https://sirpremier.wordpress.com/2012/05/17/giao-thuc-ipsec/