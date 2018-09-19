# 1. Nhận diện
## a. Trên giao diện giám sát SOC

![](/Picture/SOC_PIC001/1.png)

## b.	Trên giao diện Kibana

![](/Picture/SOC_PIC001/2.png)

# 2. Mô tả
Cảnh báo đưa ra mô tả việc mật khẩu (password) của người dùng từ bên trong vùng được giám sát (inside) ra vùng bên ngoài vùng được giám sát (outside) không được mã hóa Base64. Mức độ vi phạm bảo mật của cảnh báo được đánh giá thông qua nội dung password được gửi đi.

# 3. Các thông tin cần thu thập
* Xác định IP nguồn và IP đích của cảnh báo


* Xác định nội dung payload được gửi đi trên giao diện Kibana bằng cú pháp lệnh
> agent.name: **[tên sensor]** AND srcip: **[IP nguồn]** AND dstip: **[IP đích]** AND ips.msg: "ET POLICY Outgoing Basic Auth Base64 HTTP Password detected unencrypted"

### Ví dụ
> agent.name: **sensor-a15-80** AND srcip: **10.168.10.55** AND dstip: **115.146.126.146** AND ips.msg: "ET POLICY Outgoing Basic Auth Base64 HTTP Password detected unencrypted"

* Lấy nội dung trường payload (_**ips.payload**_) trong thông tin cảnh báo

![](/Picture/SOC_PIC001/4.png)

* Giải mã nội dung mã hóa bằng decode base64 thông qua việc sử dụng website decode online (https://www.base64decode.org) hoặc tool decode base64

# 4. Phân tích và xử lý
## a. Phân tích
* Nếu nội dung giải mã không chứa password thì cảnh báo được đánh giá là false positive
* Nếu nội dung giải mã là password của đăng nhập các hệ thống, của các ứng dụng chứa thông tin quan trọng thì cảnh báo được đánh giá **mức CAO (High) trở lên**
* Nếu nội dung giải mã là password random (password rác hay không quan trọng) thì cảnh báo được đánh giá **mức WARNING trở xuống**

## b. Xử lý
* Nếu cảnh báo là **false positive** thì bỏ qua
* Nếu cảnh báo là **positive** (tức là nội dung giải mã có chứa password) thì cần xác minh làm rõ IP nguồn, IP đích và nội dung password gửi đi để có hình thức xử lý tiếp theo là:
    * Yêu cầu đổi password đối với **cảnh báo mức CAO trở lên**
    * Thông báo nhắc nhở hoặc tiếp tục theo dõi (tùy theo tình hình thực tế) đối với **cảnh báo mức WARNING trở xuống**

# 5. Sơ đồ xử lý

![](/Picture/SOC_PIC001/5.png)
