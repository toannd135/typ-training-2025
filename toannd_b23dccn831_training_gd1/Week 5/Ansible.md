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
   [defaults]                             # group mặc đinh, cấu hình chung
      inventory = ./inventory.ini         # chỉ định các host mặc định trong inventory
      remote_user = ansible-bot           # tên User mặc định để SSH đến
      private_key_file = ~/.ssh/ansible   # nơi chứa SSH key mặc định
      host_key_checking = False           # tắt kiểm tra SSH
      timeout = 30                        # timeout cho ssh   
      retry_files_enabled = False         # không lưu file retry khi bị lỗi
      
   [privilege_escalation]             # cấu hình leo thang quyền, cho phép Ansible chạy task với quyền cao hơn
      become = True                   # Tự động dùng sudo cho tất cả các task
      become_method = sudo            # Ubuntu / CentOS / Debian dùng sudo
      become_user = root              # User sẽ chuyển sang sau khi sudo
      become_ask_pass = False         # không hỏi mật khẩu
   
## III - Inventory
* **Inventory** là nơi chứa danh sách các host mà Ansible sẽ thực thi trên đó
* Hỗ trợ hai định dạng chính là INI và YAML
* Tổ chức các máy chủ thành các groups, dễ quản và thực thi
* Lấy ra danh sách Inventory
  ```bash
     ansible-inventory -i inventory.ini --list
* hoặc
  ```bash
      ansible-inventory -i inventory.ini --graph

* **Quản lý nhóm**
   * Ansible gom nhóm các host để dễ quản lý
   * Ví dụ: nhóm web-server
     ```bash
     [web-servers]
      web-01 ansible_host=192.168.1.10
      web-02 ansible_host=192.168.1.11
   * Nhóm lồng nhau chứa hậu tố children
     ```bash
        [infrastructure:children]
         loadbalancers
         web-servers
         db-servers
* **Biến trong Inventory**
  * Biến host 
  * Cấu trúc: <hostname> <tên_biến>=<giá_trị>
   * Ví dụ:
   ```bash
      web-01 ansible_host=192.168.1.10 
   * Biến nhóm: dùng section [ten_nhom:vars]
   
* **Các tham số kết kết nối**
     * ansible_host: IP hoặc hostname muốn kết nối đến
     * ansible_port: Cổng SSH (mặc định là 22)
     * ansible_user: Username để đăng nhập SSH
     * ansible_password: Mật khẩu SSH
     * ansible_ssh_private_key_file: Đường dẫn đến nơi chứ private key
     * ansible_connection: loại kết nối (mặc định là ssh)
     * ansible_become: yes bật chế độ sudo
     * ansible_become_user: User muốn chuyển sang (thường là root)
     * ansible_become_password: Mật khẩu khi sudo
   
 ## IV - Ad-Hoc commands trong Ansible
 * Là những lệnh dùng để thực thi nhanh từ 1 host hoặc nhiều host mà không cần tạo playbook
 * Cú pháp:
   ```bash
      ansible [pattern] -m [module] -a "[module_options]" [options]
### a. Quản lý hệ thống 
   ```bash
   # Kiểm tra kết nối
   ansible all -m ping

   # Kiểm tra uptime
   ansible all -m command -a "uptime"
   
   # Kiểm tra disk space
   ansible all -m shell -a "df -h | grep /dev/sda1"
   
   # Kiểm tra memory
   ansible all -m shell -a "free -m"
   
   # Thu thập thông tin hệ thống
   ansible all -m setup
   ansible all -m setup -a "filter=*ipv4*"
```
### b. Quản lý package
```bash
   # Cài đặt package
   ansible ubuntu-servers -m apt -a "name=nginx state=present"
   ansible centos-servers -m yum -a "name=httpd state=latest"
   
   # Gỡ cài đặt
   ansible all -m apt -a "name=apache2 state=absent"
   
   # Cập nhật tất cả package
   ansible all -m apt -a "upgrade=dist update_cache=yes"
```
### c. Quản lí file thư mục
```bash
   # Tạo thư mục
   ansible all -m file -a "path=/opt/myapp state=directory mode=0755"
   
   # Tạo file
   ansible all -m file -a "path=/tmp/test.txt state=touch"
   
   # Sao chép file
   ansible webservers -m copy -a "src=./config.conf dest=/etc/app/"
   
   # Download file từ URL
   ansible all -m get_url -a "url=http://example.com/file.tar.gz dest=/tmp/"
   
   # Thay đổi ownership
   ansible all -m file -a "path=/opt/myapp owner=appuser group=appgroup"
 ```

### d. Quản lí service
   ``` bash
      # Khởi động service
      ansible webservers -m service -a "name=nginx state=started"
      
      # Dừng service
      ansible webservers -m service -a "name=nginx state=stopped"
      
      # Khởi động lại
      ansible webservers -m service -a "name=nginx state=restarted"
      
      # Bật tự động khởi động
      ansible webservers -m service -a "name=nginx enabled=yes"
  ```
### e. Quản lý User
   ```bash
      # Tạo user
      ansible all -m user -a "name=johndoe password={{ 'mypassword' | password_hash('sha512') }}"
      
      # Xóa user
      ansible all -m user -a "name=johndoe state=absent remove=yes"
      
      # Thêm user vào group
      ansible all -m user -a "name=johndoe groups=wheel append=yes"
  ```
### f. Hạn chế của Ad-Hoc Commands
   1. Không có idempotency mặc định - Một số module cần tự đảm bảo
   2. Không có cấu trúc phức tạp - Không thể xử lý flow phức tạp
   3. Không thể tái sử dụng - Chỉ phù hợp cho tác vụ một lần
   4 .Không hỗ trợ handler - Không thể trigger các hành động sau
   5.  Giới hạn về error handling - Xử lý lỗi thủ công
