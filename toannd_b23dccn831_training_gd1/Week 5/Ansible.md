## I. Tổng quan về Ansible

### a. Khái niệm
* **Ansible** là một công cụ mã nguồn mở cung cấp giải pháp **IaC (Infrastructure as Code)** phổ biến. 
* Nó hỗ trợ người dùng tự động hóa việc cài đặt, cấu hình và quản lý hàng loạt server từ xa một cách hiệu quả.

### b. Chức năng chính
* **Provisioning:** Khởi tạo máy ảo (VM), Container trong môi trường Cloud thông qua các API (OpenStack, AWS, Google Cloud, v.v.).
* **Configuration Management:** Quản lý cấu hình tập trung, giúp thiết lập thông số đồng bộ mà không cần can thiệp thủ công vào từng server.
* **Application Deployment:** Triển khai ứng dụng hàng loạt, quản lý vòng đời ứng dụng xuyên suốt từ giai đoạn Development đến Production.
* **Security and Compliance:** Thiết lập và thực thi các chính sách an toàn thông tin đồng bộ trên nhiều môi trường khác nhau.

### c. Cách thức hoạt động
* **Kiến trúc Agentless:** Đây là điểm mạnh nhất của Ansible. Người dùng không cần cài đặt bất kỳ phần mềm (agent) nào lên các server đích.
* **Kết nối:** Ansible thực thi công việc bằng cách kết nối đến các thiết bị thông qua giao thức `SSH` (cho Linux), `WinRM` (cho Windows) hoặc qua `API`.
## II - Ansible Configuration
* ansible.cfg là tệp cấu hình cho Ansible, giúp tùy chỉnh cách Ansible hoạt động. Nếu không có tệp này, Ansible sẽ sử dụng cấu hình mặc định.
 * Thông tin về một file ansible.cfg

   ```bash
   [defaults]                          # group mặc đinh, cấu hình chung
      inventory = ./inventory.ini         # chỉ định các host mặc định trong inventory
      remote_user = ansible-bot           # tên User mặc định để SSH đến
      private_key_file = ~/.ssh/ansible   # nơi chứa SSH key mặc định
      host_key_checking = False         # tắt kiểm tra SSH
      timeout = 30                        # timeout cho ssh   
      retry_files_enabled = False         # không lưu file retry khi bị lỗi
      
      [privilege_escalation]          # cấu hình leo thang quyền, cho phép Ansible chạy task với quyền cao hơn
      become = True                   # Tự động dùng sudo cho tất cả các task
      become_method = sudo            # Ubuntu / CentOS / Debian dùng sudo
      become_user = root              # Yser sẽ chuyển sang sau khi sudo
      become_ask_pass = False         # không hỏi mật khẩu
   
## III - Inventory
