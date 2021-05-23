# Cấu hình Pfsense HA trên OpenStack

# Mô hình
IP Planing trong bài và IP PUBLIC:
- HA-Pfsense-1:
  - `vtnet0`: 192.168.20.211 - external1 	fa:16:3e:35:1a:2d
  - `vtnet1`: 10.10.201.11 - LAN            fa:16:3e:66:a4:e7
  - `vtnet2`: 10.10.202.11 - SYNC           fa:16:3e:14:4b:db
- HA-Pfsense-2:
  - `vtnet0`: 192.168.20.212 - external1    fa:16:3e:ab:1c:e0
  - `vtnet1`: 10.10.201.12 - LAN            fa:16:3e:ca:b9:5c
  - `vtnet2`: 10.10.202.12 - SYNC           fa:16:3e:9d:d2:72

- VIP External: 192.168.20.10
- VIP LAN: 10.10.201.10

<img src ="..\images\pfsense\Screenshot_0.png">

# Chuẩn bị:
- 2 VM boot từ image Pfsense.

# 1. Cấu hình 2 node Pfsense 
> ### Thực hiện trên cả 2 node

## Cấu hình ban đầu
**Bước 1: Reset Factory:**

- Nhập `4`

    <img src ="..\images\pfsense\Screenshot_1.png">

- Chọn `y` để đồng ý reset về cài đặt ban đầu:

    <img src ="..\images\pfsense\Screenshot_2.png">

    -> Đợi một vài phút để VM reset cấu hình về ban đầu.

**Bước 2: Cấu hình WAN**

- Bỏ qua cấu hình VLAN tự động -> Nhập `n`

    <img src ="..\images\pfsense\Screenshot_3.png">

- Nhập tên card WAN. Ở đây là `vnet0`

    <img src ="..\images\pfsense\Screenshot_4.png">

- Bỏ qua cấu hình LAN. Để trống và nhấn Enter

    <img src ="..\images\pfsense\Screenshot_5.png">

**Lưu ý**: Xác định đúng card WAN. Có thể kiểm tra địa chỉ MAC của port gắn trên máy ảo để xác định chính xác

- Chọn `y`

    <img src ="..\images\pfsense\Screenshot_6.png">

    Chờ 3-5 phút để Pfsense thực hiện cấu hình WAN.

    <img src ="..\images\pfsense\Screenshot_7.png">

**Bước 3: Cấu hình IP tĩnh cho card WAN**

- Nhập `2` để chọn:

    <img src ="..\images\pfsense\Screenshot_8.png">

- Chọn `n`

    <img src ="..\images\pfsense\Screenshot_9.png">

- Nhập IP WAN của VM. Ở đây là `192.168.20.211`

    <img src ="..\images\pfsense\Screenshot_10.png">

    **Lưu ý:** Đối với VM 2 phải nhập IP WAN của VM 2. Trong tài liệu này sẽ là `192.168.20.212`

- Nhập số prefix. Ở đây là `24`:

    <img src ="..\images\pfsense\Screenshot_11.png">

- Nhập gateway dải WAN. Ở đây là `192.168.20.1`

    <img src ="..\images\pfsense\Screenshot_12.png">

- Bỏ qua cấu hình IPv6

    <img src ="..\images\pfsense\Screenshot_13.png">

    Nhấn Enter và chọn `n`

    <img src ="..\images\pfsense\Screenshot_14.png">

**Bước 4: Bật tính năng cho phép cấu hình Pfsense thông qua giao diện Web**

- Chọn `y`

    <img src ="..\images\pfsense\Screenshot_15.png">

- Nhập Enter để kết thúc cấu hình:

    <img src ="..\images\pfsense\Screenshot_16.png">

## Cấu hình Pfsense trên giao diện
> ### Thực hiện trên cả 2 node

Truy cập web theo địa chỉ IP WAN của 2 VM. Tài khoản mặc định là `admin/pfsense` Trong tài liệu này là:
- Pfsense-HA-01: http://192.168.20.211/
- Pfsense-HA-01: http://192.168.20.212/

**Bước 1: Thiết lập ban đầu trên web cho Pfsense:**

Thực hiện lần lượt các thao tác như các hình dưới:

<img src ="..\images\pfsense\Screenshot_17.png">

<img src ="..\images\pfsense\Screenshot_18.png">

<img src ="..\images\pfsense\Screenshot_19.png">

<img src ="..\images\pfsense\Screenshot_20.png">

<img src ="..\images\pfsense\Screenshot_21.png">

<img src ="..\images\pfsense\Screenshot_22.png">

<img src ="..\images\pfsense\Screenshot_23.png">

<img src ="..\images\pfsense\Screenshot_24.png">

<img src ="..\images\pfsense\Screenshot_25.png">

<img src ="..\images\pfsense\Screenshot_26.png">

**Bước 2: Cấu hình firewall cho phép kết nối Pfsense qua IPPublic**

- Chọn `Firewall` -> `Rules`. chọn Tab `WAN`:

    <img src ="..\images\pfsense\Screenshot_27.png">

- Chọn `Add`:

    <img src ="..\images\pfsense\Screenshot_28.png">

- Kéo xuống phần `Destination` chọn `HTTP(80)` -> `Save`

    <img src ="..\images\pfsense\Screenshot_29.png">

- Thực hiện tương tự để add rule cho `HTTPS(443)`

    <img src ="..\images\pfsense\Screenshot_30.png">

- Chọn `Apply Change`

    <img src ="..\images\pfsense\Screenshot_31.png">

**Bước 3: Chuyển kết nối Dashboard mặc định Pfsense thành HTTPS**

- Chọn `System` -> `Advanced`

    <img src ="..\images\pfsense\Screenshot_32.png">

- Tại tab `Admin Access`, mục Protocol, tích chọn `HTTPS(SSL/TLS)`

    <img src ="..\images\pfsense\Screenshot_33.png">

- Chọn `Save`

    <img src ="..\images\pfsense\Screenshot_35.png">

- Đợi 20s, trang Dashboard sẽ tự redirect về HTTPS

    <img src ="..\images\pfsense\Screenshot_34.png">

## Cấu hình card dải LAN
Chọn `Interface` -> `Asignments`

<img src ="..\images\pfsense\Screenshot_36.png">

Chọn đúng vtnet của dải LAN. -> chọn `Add`

<img src ="..\images\pfsense\Screenshot_37.png">

Click chọn dải LAN:

<img src ="..\images\pfsense\Screenshot_38.png">

Cấu hình các mục dưới đây -> `Save`
- Tích chọn `Enable interface`
- `IPv4 Configuration Type` -> `Static IPv4`
- `IPv4 Address` -> Nhập IP dải LAN của Pfsense và số prefix. Lưu ý: 2 VM khác IP, cần nhập cho đúng IP

<img src ="..\images\pfsense\Screenshot_39.png">

<img src ="..\images\pfsense\Screenshot_40.png">

Chọn `Apply Change`

<img src ="..\images\pfsense\Screenshot_41.png">

## Cấu hình card dải SYNC
> ### Thực hiện tương tự dải LAN

## Kết quả sau khi cấu hình xong 2 node Pfsense
Sau khi cấu hình xong 3 dải của Pfsense, ta sẽ thấy kết nối hiển thị tại trang chủ của Dashboard

**Pfsense-HA-01:**

<img src ="..\images\pfsense\Screenshot_42.png">

**Pfsense-HA-02:**

<img src ="..\images\pfsense\Screenshot_43.png">

# 2. Cấu hình tạo Virtual IP cho VM trên OpenStack
### Khởi tạo SecurityGroup
Cần khởi tạo 1 Security Group cho phép gói tin multicast truyền thông qua giao thức VRRP.

Tạo 1 SecGroup mới trong Project của khách hàng:

- Chọn `Project` -> `Security Group`:

    <img src ="..\images\pfsense\Screenshot_44.png">

- Đặt tên cho SecGroup -> `Create Security Group`

    <img src ="..\images\pfsense\Screenshot_45.png">

- Trên SecGroup vừa tạo, chọn `Manage Rules`

    <img src ="..\images\pfsense\Screenshot_46.png">

- Chọn `Add Rule`:

    <img src ="..\images\pfsense\Screenshot_47.png">

- Thêm rule cho phép giao thức VRRP có protocol 112 được thực thi:

    <img src ="..\images\pfsense\Screenshot_48.png">

- Sau khi thêm ta sẽ thấy rule hiển thị trong SecGroup:

    <img src ="..\images\pfsense\Screenshot_49.png">

### Thêm Security Group cho VM
Các port cần thêm Security Group:
- Port WAN
- Port LAN

Các VM cấu hình virtual IP sẽ phải thêm security group cho các port sử dụng VIP này. Thao tác thêm security group
thực hiện đơn giản trên Dashboard bằng cách mở security group của VM:

- Trên VM chọn `Edit Port Security Group`:

    <img src ="..\images\pfsense\Screenshot_50.png">

- Trên port cần sử dụng VIP. Chọn `Edit Security Group`

    <img src ="..\images\pfsense\Screenshot_51.png">

- Add thêm Security Group sau đó chọn `Update`

    <img src ="..\images\pfsense\Screenshot_52.png">

    <img src ="..\images\pfsense\Screenshot_53.png">

Thực hiện tương tự trên port LAN và node Pfsense còn lại

# 3. Tạo port VIP và allow VIP trên các port của 2 node Pfsense
Cần thực hiện tạo port cho các VIP.
- Dải external - VIP WAN: `pfsense-wan-vip` : `192.168.20.210`
- Dải LAN - VIP LAN: `pfsense-lan-vip` : `10.10.201.10`

### Tạo port VIP cho dải WAN
Chọn `Networks` -> Chọn dải WAN. Ở đây là `external1`

<img src ="..\images\pfsense\Screenshot_54.png">

Chọn tab `Port` -> `Create Port`

<img src ="..\images\pfsense\Screenshot_55.png">

Nhập thông tin port như hình:

<img src ="..\images\pfsense\Screenshot_56.png">

Sang phần `Security Groups`, thực hiện thêm SecGroup đã tạo ở trên:

<img src ="..\images\pfsense\Screenshot_57.png">

Sau khi tạo, ta sẽ thấy port được tạo và ở trạng thái Down:

<img src ="..\images\pfsense\Screenshot_58.png">

### Tạo port VIP cho dải LAN
Thực hiện tương tự như với dải WAN. Kết quả:

<img src ="..\images\pfsense\Screenshot_59.png">

### Allow port VIP
Ta sẽ thực hiện allow VIP trên các port sử dụng VIP tương ứng của 2 node Pfsense.
- 2 port WAN -> allow port `pfsense-wan-vip`
- 2 port LAN -> allow port `pfsense-lan-vip`

**Allow VIP WAN cho 2 port WAN của 2 node:**

- Chọn port WAN trong giao diện port của dải WAN:

    <img src ="..\images\pfsense\Screenshot_60.png">

- Sang tab `Allowed Address Pairs` -> Chọn Add `Allowed Address Pairs`

    <img src ="..\images\pfsense\Screenshot_61.png">

- Nhập IP của WAN VIP (`192.168.20.210`) -> `Submit`

    <img src ="..\images\pfsense\Screenshot_62.png">

- Kết quả: Ta sẽ thấy WAN VIP được thêm vào port WAN của node Pfsense 1

    <img src ="..\images\pfsense\Screenshot_63.png">

- Thực hiện tương tự với port WAN của node Pfsense2

    <img src ="..\images\pfsense\Screenshot_64.png">

**Allow VIP LAN cho 2 port LAN của 2 node:**
> Thực hiện tương tự như với port WAN ở trên

- Port LAN trên node Pfsense 1

    <img src ="..\images\pfsense\Screenshot_65.png">

- Porta LAN trên node Pfsense 2

    <img src ="..\images\pfsense\Screenshot_66.png">

# 4. Cấu hình Pfsense HA

## 4.1. Cấu hình Pfsync
### Bước 1: Cấu hình Rule cho interface SYNC
> Thực hiện trên cả 2 node

Cấu hình Allow All trên Interface `SYNC`

Chọn `Firewall` -> `Rules`

<img src ="..\images\pfsense\Screenshot_67.png">

Chọn `SYNC` -> `Add`

<img src ="..\images\pfsense\Screenshot_68.png">

Cấu hình Rule như sau:

- `Action` > `Pass`
- `Interface` > `SYNC`
- `Address Family` > `IPv4`
- `Protocol` > `Any`
- Chọn `Save`

<img src ="..\images\pfsense\Screenshot_69.png">

<img src ="..\images\pfsense\Screenshot_70.png">

Chọn `Apply Change`

<img src ="..\images\pfsense\Screenshot_71.png">

Kết quả:

<img src ="..\images\pfsense\Screenshot_72.png">

**=> Thực hiện tương tự trên node còn lại**

### Bước 2: Cấu hình HA trên node Pfsense 1
> Thực hiện trên node Pfsense 1 192.168.20.211

Chọn `System` ->` High Avail. Sync`

<img src ="..\images\pfsense\Screenshot_73.png">

Cấu hình theo các giá trị dưới đây:

- Tích chọn: `Synchronize states` > `pfsync transfers state insertion, update, and deletion messages between firewalls.`
- `Synchronize Interface` -> `SYNC`
- `pfsync Synchronize Peer IP`: IP dải SYNC của node Pfsense 2 -> `10.10.202.12`
- `Synchronize Config to IP`: IP dải SYNC của node Pfsense 2 -> `10.10.202.12`
- `Remote System Username`: tài khoản quản trị node pfsense 2 -> `admin`
- `Remote System Password`: Mật khẩu tài khoản quản trị admin của node pfsense2
- Tích chọn `Synchronize admin` để đồng bộ 2 tài khoản admin giống nhau. (Có thể không chọn. Khi đó, nếu 2 node có pass admin khác nhau thì khi truy cập bằng VIP sẽ sử dụng pass của node đang làm master)
- `Select options to sync` -> Chọn `Toggle All`
- Chọn `Save` để lưu lại

<img src ="..\images\pfsense\Screenshot_74.png">

<img src ="..\images\pfsense\Screenshot_76.png">

<img src ="..\images\pfsense\Screenshot_77.png">

### Bước 3: Cấu hình HA trên node Pfsense 2
> Thực hiện trên node Pfsense 1 192.168.20.212

Chọn `System` ->` High Avail. Sync`

<img src ="..\images\pfsense\Screenshot_78.png">

Trên node Pfsense 2 này chỉ cần cấu hình 3 option:

- Tích chọn: `Synchronize states` > `pfsync transfers state insertion, update, and deletion messages between firewalls.`
- `Synchronize Interface` -> `SYNC`
- `pfsync Synchronize Peer IP`: IP dải SYNC của node Pfsense 1 -> `10.10.202.11`
- Chọn `Save` để lưu

<img src ="..\images\pfsense\Screenshot_79.png">

<img src ="..\images\pfsense\Screenshot_80.png">

### Bước 4: Kiểm tra đồng bộ:
> Thực hiện trên node Pfsense 1

Chọn `Firewall` -> `Aliases`

<img src ="..\images\pfsense\Screenshot_81.png">

Add 1 alisase `test` vào. Chọn `Add`:

<img src ="..\images\pfsense\Screenshot_82.png">

Nhập 1 alisase `testsync` -> `Save`

<img src ="..\images\pfsense\Screenshot_83.png">

Chọn `Apply Change`

<img src ="..\images\pfsense\Screenshot_84.png">

Kết quả: trên node pfsense 1:

<img src ="..\images\pfsense\Screenshot_85.png">

Kiểm tra trên node pfsense 2:

<img src ="..\images\pfsense\Screenshot_86.png">

**Lưu ý:**
- Nếu cấu hình đúng, khi tạo mới config Firewall, VPN trên node pfsense1 các cấu hình sẽ tự được đồng bộ sang pfsense2
- Nếu cấu hình sai, kiểm tra cấu hình Firewall interface SYNC

## 4.2. Cấu hình VIP trên 2 node Pfsense
> Thực hiện cấu hình trên node Pfsense 1

### Bước 1: Cấu hình VIP cho đường WAN
Chọn `Firewalls` -> `Virtual IPs`:

<img src ="..\images\pfsense\Screenshot_87.png">

Chọn `Add`:

<img src ="..\images\pfsense\Screenshot_88.png">

Tạo VIP với các thông tin lần lượt sau:

- `Type`: `CARP`
- `Interface`: `WAN`
- `Addresses`: `192.168.20.210 / 24`
- `Virtual IP Password`: mật khẩu cho CARP VIP
- `VHID Group`: `210` (Chọn giống với giá trị octet cuối của VIP)
- `Description`: `VIP WAN`

<img src ="..\images\pfsense\Screenshot_89.png">

Chọn `Apply Change`:

<img src ="..\images\pfsense\Screenshot_90.png">

Kết quả:

<img src ="..\images\pfsense\Screenshot_91.png">

**Lưu ý:** Không cần cấu hình trên node Pfsense 2. Config sẽ tự đồng bộ sang.

> Kiểm tra trên node Pfsense 2

<img src ="..\images\pfsense\Screenshot_92.png">

**Lưu ý:**
- Giá trị `SKEW` mặc định trên node Pfsense 1 sẽ là 0
- Giá trị `SKEW` mặc định trên node Pfsense 2 sẽ là 100

<img src ="..\images\pfsense\Screenshot_93.png">

### Bước 3: Cấu hình VIP cho đường LAN
> Thực hiện trên node Pfsense 1

Thực hiện tương tự như VIP cho đường WAN.

<img src ="..\images\pfsense\Screenshot_94.png">

Nhập các thông số của dải LAN:
- `Type`: `CARP`
- `Interface`: `LAN`
- `Addresses`: `10.10.201.10 / 24`
- `Virtual IP Password`: mật khẩu cho CARP VIP
- `VHID Group`: `10` (Chọn giống với giá trị octet cuối của VIP)
- `Description`: `VIP WAN`

<img src ="..\images\pfsense\Screenshot_95.png">

Chọn `Apply Change`:

<img src ="..\images\pfsense\Screenshot_96.png">

Kết quả:

<img src ="..\images\pfsense\Screenshot_97.png">

> Kiểm tra trên node Pfsense 2

<img src ="..\images\pfsense\Screenshot_98.png">

### Bước 4: Kiểm tra trạng thái VIP
> Thực hiện trên node Pfsense 1

Chọn `Status` -> `CARP (failover)`

<img src ="..\images\pfsense\Screenshot_99.png">

Trạng thái VIP trên Pfsense 1 sẽ là `MASTER`:

<img src ="..\images\pfsense\Screenshot_100.png">

> Kiểm tra trên Pfsense 2 sẽ là BACKUP:

