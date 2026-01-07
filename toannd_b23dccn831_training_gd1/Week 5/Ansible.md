**I - Ansible**
    **a. Khái niệm**
    - Là công cụ mã nguồn mở IaC(Infratructer as code) phổ biến, hỗ trợ người dùng trong việc tự động hóa việc cài đặt, quản lí các server từ xa
    **b. Chức năng**
    - Provisioning: Ansible có thể khởi tạo VM, Container hoạt động trong môi trường cloud thông qua các API (OpenStack, AWS, Google Cloud,....)
    - Configuration Management: Quản lí cấu hình tập trung, từ đó giúp người dùng không phải đi cấu hình từng server
    - Application Deployment: Deploy ứng dụng hàng loạt, quản lí hiệu quả vòng đời của ứng dụng từ giai đoạn dev tới production
    - Security and Compiance: Quản lý các chính sách về an toàn thông tin một cách đồng bộ trên nhiều môi trường và sản phẩm khác nhau
    **c. Cách thức hoạt động**
    - Nhờ kiên trúc "Agentless", người dùng sẽ không cần cài đặt agent ở trên các server. Thay vào đó, Ansible sẽ kết nối đến thiết bị thông qua kết nối SSH hoặc API, sau đó thực thi việc cài đặt, quản lý các thiết bị

**II - Ansible Configuration**
    - ansible.cfg là tệp cấu hình cho Ansible, giúp tùy chỉnh cách Ansible hoạt động. Nếu không có tệp này, Ansible sẽ sử dụng cấu hình mặc định
    
    - Thông tin về một file ansible.cfg

   ```
        [defaults]
        inventory = ./inventory.ini         # chỉ định các host mặc định trong inventory
        remote_user = ansible-bot           # tên User mặc định để SSH đến
        private_key_file = ~/.ssh/ansible   # nơi chứa SSH key mặc định
        host-key-checking = False           # tắt kiểm tra SSH
        timeout = 30                        # timeout cho ssh   
        retry_files_enabled = False         # không lưu file retry khi bị lỗi

**III - Inventory **
