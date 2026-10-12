# BÁO CÁO KỸ THUẬT: VM VÀ CONTAINER

## 1. So sánh kiến trúc VM và Container

Máy ảo (Virtual Machine - VM) và Container đều được sử dụng để triển khai ứng dụng trong môi trường độc lập. Tuy nhiên, hai công nghệ này khác nhau về kiến trúc và cách sử dụng tài nguyên.

VM sử dụng Hypervisor để tạo và quản lý các máy ảo. Mỗi VM thường có hệ điều hành khách (Guest OS), kernel riêng và các thư viện cần thiết để chạy ứng dụng. Ngược lại, Container Engine như Docker tạo các container sử dụng chung kernel của hệ điều hành máy chủ, đồng thời cô lập tiến trình và tài nguyên của từng container.

| Tiêu chí | Máy ảo (VM) | Container |
|---|---|---|
| Dung lượng Image | Thường lớn do chứa hệ điều hành khách và các thành phần hệ thống | Thường nhỏ hơn vì chủ yếu chứa ứng dụng, thư viện và các phụ thuộc |
| Thời gian khởi động | Thường lâu hơn do cần khởi động hệ điều hành khách | Thường nhanh hơn vì không cần khởi động kernel riêng |
| Hao phí OS | Mỗi VM có hệ điều hành khách riêng, tiêu tốn thêm CPU, RAM và dung lượng lưu trữ | Các container trên cùng máy chủ dùng chung kernel nên giảm phần tài nguyên dành cho hệ điều hành |
| Mức độ cô lập | Thường có ranh giới cô lập mạnh hơn nhờ lớp Hypervisor và kernel riêng | Cô lập ở cấp tiến trình và tài nguyên; mức độ bảo vệ phụ thuộc cấu hình và bảo mật kernel |
| Khả năng mở rộng | Có thể mở rộng nhưng việc tạo VM mới thường tốn nhiều tài nguyên hơn | Có thể tạo, thay thế và nhân bản container nhanh, thuận lợi cho triển khai nhiều bản sao ứng dụng |

Nhìn chung, VM phù hợp khi cần hệ điều hành riêng hoặc ranh giới cô lập mạnh. Container phù hợp với các ứng dụng cần triển khai nhanh, đóng gói nhất quán và mở rộng linh hoạt. Container không thay thế hoàn toàn VM; trong thực tế, Docker thường chạy bên trong VM hoặc trên máy chủ vật lý.

## 2. Cơ chế cô lập và quản lý tài nguyên của Linux

### 2.1. Namespaces

Namespaces là cơ chế của Linux giúp tạo ra các góc nhìn tài nguyên riêng cho từng nhóm tiến trình. Nhờ đó, một container có thể hoạt động như một môi trường độc lập dù vẫn sử dụng kernel của máy chủ.

- **PID Namespace:** Cô lập không gian định danh tiến trình. Tiến trình trong container có thể nhìn thấy PID khác với PID được quan sát từ hệ thống máy chủ.
- **Network Namespace:** Cô lập giao diện mạng, bảng định tuyến, địa chỉ IP và một số tài nguyên mạng. Docker có thể kết nối các namespace mạng thông qua mạng container và cơ chế ánh xạ cổng.
- **Mount Namespace:** Cô lập góc nhìn về các điểm gắn kết hệ thống tệp (mount points), giúp container có cây thư mục riêng theo cấu hình.

Namespaces góp phần cô lập môi trường thực thi, nhưng không phải là cơ chế bảo mật tuyệt đối. Quyền Linux, capabilities, seccomp và các cấu hình bảo mật khác cũng có vai trò quan trọng.

### 2.2. Control Groups (cgroups)

Control Groups là cơ chế của Linux dùng để tổ chức, theo dõi và kiểm soát mức sử dụng tài nguyên của các nhóm tiến trình.

- **Giới hạn CPU:** Có thể phân bổ tỷ lệ CPU hoặc giới hạn thời gian CPU mà một container được sử dụng. Điều này giúp tránh trường hợp một ứng dụng chiếm phần lớn tài nguyên xử lý.
- **Giới hạn RAM:** Có thể đặt giới hạn bộ nhớ cho container. Khi vượt ngưỡng, hệ thống có thể từ chối cấp phát hoặc kích hoạt cơ chế xử lý bộ nhớ, bao gồm OOM kill tùy tình huống và cấu hình.

Namespaces chủ yếu giúp cô lập góc nhìn tài nguyên, trong khi cgroups chủ yếu kiểm soát và theo dõi mức tiêu thụ tài nguyên. Hai cơ chế phối hợp để hỗ trợ Docker chạy nhiều ứng dụng trên cùng một máy chủ.

## 3. Thông số Docker Engine

Kết quả kiểm tra trên môi trường thực hành:

- **Server Version:** [Điền Server Version]
- **Cgroup Driver:** [Điền Cgroup Driver]
- **Containers:** [Điền tổng số Containers]
- **Images:** [Điền tổng số Images]

Các thông số trên được lấy từ đầu ra lệnh `docker info`. Lệnh `docker version` được sử dụng để kiểm tra phiên bản Docker Client và Server, đồng thời xác nhận khả năng kết nối với Docker Engine.

## 4. Kết luận

Doanh nghiệp có thể chuyển từ mô hình triển khai từng ứng dụng trực tiếp trên VPS sang Docker để chuẩn hóa môi trường đóng gói, giảm phần tài nguyên dành cho các hệ điều hành khách riêng biệt và tăng tốc độ triển khai. Container cũng hỗ trợ CI/CD, rollback và mở rộng số lượng bản sao ứng dụng. Tuy nhiên, việc chuyển đổi cần đi kèm quản lý dữ liệu bền vững, giám sát, sao lưu và cấu hình bảo mật phù hợp. Docker giúp tối ưu cách đóng gói và vận hành ứng dụng, nhưng không loại bỏ hoàn toàn nhu cầu sử dụng VM hoặc các biện pháp bảo mật hệ thống.
