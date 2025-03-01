# DOCKER

## 1. Giới thiệu

### a. Containers
- Containers are lightweight, portable, and isolated software environments 
  - **Nhẹ (lightweight)**: Containers không tốn nhiều tài nguyên như các máy ảo (VM), vì chúng chia sẻ hệ điều hành của máy chủ thay vì chạy một hệ điều hành riêng biệt.
  - **Di động (portable)**: Containers có thể chạy trên nhiều nền tảng khác nhau (như máy chủ, máy tính cá nhân, hoặc trên đám mây) mà không gặp vấn đề tương thích.
  - **Tách biệt (isolated)**: Mỗi container chạy độc lập với nhau và không ảnh hưởng đến các container khác, giúp tránh xung đột phần mềm.
- Allow developers to run and package applications with their dependencies, consistently across different platforms.
  - Tất cả các phần mềm cần thiết (như thư viện, công cụ,...) để ứng dụng chạy đều được đóng gói cùng trong container, giúp đảm bảo ứng dụng sẽ hoạt động đúng trên mọi nền tảng.
    => Development, deployment dễ dàng không cần quan tâm đến sự khác biệt của môi trường hệ thống.

### b. Vì sao cần Container
- Solve the issue around inconsistent environments when working in large teams.
- Before containers or virtual environments, a lot of issues and time loss was caused by having to install and configure local environments to build projects shared by co-workers or friends.
  - => Containers giúp giải quyết vấn đề về sự không đồng nhất giữa các môi trường phát triển của các thành viên trong nhóm. Thay vì phải cài đặt và cấu hình mọi thứ từ đầu, mỗi người trong nhóm chỉ cần sử dụng một container đã được cấu hình sẵn, giúp tiết kiệm thời gian và giảm thiểu các lỗi liên quan đến môi trường phát triển.

### c. Bare Metal vs VM vs Containers
- **Bare Metal**: Chạy trực tiếp trên phần cứng, hiệu suất cao nhưng ít linh hoạt, chỉ chạy một ứng dụng.
- **Virtual Machines**: Cho phép chạy nhiều ứng dụng trên một máy chủ, nhưng tốn tài nguyên do giả lập phần cứng và yêu cầu nhiều hệ điều hành.
- **Containers**: Chạy nhiều ứng dụng trên một máy chủ mà không cần phần mềm ảo hóa, nhẹ và hiệu quả hơn so với máy ảo, nhưng cách ly không mạnh mẽ như máy ảo.

### d. Docker and OCI
OCI là một dự án do Linux Foundation sáng lập, nhằm mục đích tạo ra các chuẩn kỹ thuật cho việc xây dựng và chạy containers. Mục tiêu là đảm bảo tính tương thích và khả năng tương tác giữa các môi trường container khác nhau, giúp các nhà phát triển có thể sử dụng các công cụ và nền tảng khác nhau mà không lo lắng về sự không tương thích. 

## 2. Kiến thức nền tảng

### a. Docker namespaces
- Docker sử dụng namespaces để tạo các môi trường cách ly cho các container. Điều này có nghĩa là mỗi container có thể hoạt động như thể nó đang có hệ thống tài nguyên riêng, không bị ảnh hưởng bởi các container khác hoặc hệ điều hành chủ (host).
- Các namespaces bao gồm:
  - **PID (Process ID)**: Cô lập các ID tiến trình của container, giúp mỗi container có danh sách tiến trình riêng biệt.  
    Ví dụ: Giả sử bạn có một container đang chạy một ứng dụng. Trong container, bạn có thể thấy PID là 1 cho tiến trình chính (chẳng hạn như nginx), nhưng trên hệ thống host, PID 1 có thể được sử dụng bởi một tiến trình khác (chẳng hạn như tiến trình init của hệ điều hành).
    Bạn có thể sử dụng lệnh `docker top <container_id>` để xem PID của các tiến trình trong container. Nếu bạn so sánh với các tiến trình trên hệ thống host, bạn sẽ thấy rằng PID trong container là khác nhau.
  - **NET (Network)**: Cô lập mạng của container, giúp mỗi container có thể có địa chỉ IP và giao diện mạng riêng biệt, không ảnh hưởng tới các container khác.  
    Ví dụ: Giả sử bạn chạy hai container, container1 và container2. Mỗi container sẽ có địa chỉ IP riêng và có thể giao tiếp với nhau thông qua cổng mà Docker tạo ra, nhưng sẽ không thể truy cập mạng ngoài trừ khi bạn cấu hình mạng chéo.
    - Bạn có thể kiểm tra địa chỉ IP của container bằng lệnh `docker inspect <container_id>`.
    - Ví dụ, bạn sẽ thấy container1 có địa chỉ IP là 172.17.0.2 và container2 có địa chỉ IP là 172.17.0.3, nhưng các container này không thể gây ảnh hưởng đến các dịch vụ khác đang chạy trên hệ thống host.
  - **MNT (Mount)**: Cô lập hệ thống tệp, mỗi container có thể gắn kết (mount) các tệp và thư mục riêng biệt mà không làm ảnh hưởng tới các container khác.  
    Ví dụ: Giả sử bạn chạy một container và gắn kết (mount) một thư mục vào container từ hệ thống host.
    ```bash
    docker run -v /host/data:/container/data my_image
    ```
    Trong ví dụ trên, Docker sẽ gắn kết thư mục `/host/data` của hệ thống host vào `/container/data` trong container. Mặc dù container có thể truy cập thư mục này, nhưng các container khác và hệ thống host không bị ảnh hưởng bởi các thay đổi trong thư mục này (nếu không được gắn kết).
  - **UTS (Unix Timesharing System)**: Cô lập các thông tin như tên hệ thống và tên máy chủ, giúp mỗi container có thể đặt tên hệ thống của riêng mình.  
    Ví dụ: Giả sử bạn có hai container, container1 và container2. Mỗi container có thể thay đổi tên hệ thống của nó độc lập:
    - Trong container1, bạn có thể thay đổi tên máy chủ thành `container1.local` bằng cách sử dụng lệnh `hostname`.
    - Trong container2, bạn có thể thay đổi tên máy chủ thành `container2.local` mà không làm ảnh hưởng đến container1 hay hệ thống host.
  - **IPC (InterProcess Communication)**: Cô lập giao tiếp giữa các tiến trình, giúp container không thể giao tiếp trực tiếp với tiến trình ngoài container của nó.  
    Ví dụ: Giả sử bạn có hai container đang chạy các ứng dụng cần giao tiếp với nhau qua shared memory hoặc message queues. IPC namespace giúp cô lập các kênh giao tiếp này giữa các container. Mỗi container sẽ có không gian IPC riêng, không thể giao tiếp với container khác mà không có cấu hình đặc biệt.
  - **USER**: Cô lập không gian người dùng, giúp Docker quản lý quyền truy cập người dùng và nhóm trong mỗi container.

### b. cgroups (control groups)
- Cgroups là một tính năng trong Linux kernel, cho phép hệ thống phân bổ và quản lý tài nguyên như CPU, bộ nhớ (memory), băng thông mạng (network bandwidth), I/O (input/output) cho các nhóm tiến trình (processes) đang chạy trên hệ thống. Cgroups cho phép bạn nhóm các tiến trình lại với nhau và kiểm soát lượng tài nguyên mà mỗi nhóm tiến trình có thể sử dụng.
- Cgroups với docker:
  - Docker sử dụng cgroups để giới hạn và kiểm soát tài nguyên mà các container có thể sử dụng, từ đó đảm bảo rằng mỗi container chỉ sử dụng một phần tài nguyên nhất định của hệ thống và không gây ảnh hưởng đến các container khác hay hệ điều hành chủ (host).
    - **Resource Isolation (Cô lập tài nguyên)**: Cgroups giúp đảm bảo rằng mỗi container không vượt quá mức tài nguyên đã được chỉ định, ngăn ngừa tình trạng một container chiếm dụng toàn bộ tài nguyên hệ thống và làm ảnh hưởng đến các container khác.
    - **Predictable and Consistent Behavior (Hành vi có thể dự đoán và nhất quán)**: Khi cgroups được sử dụng, Docker có thể đảm bảo rằng mỗi container sẽ có một lượng tài nguyên cố định và có thể dự đoán được, ngay cả khi chạy trên các hệ thống với cấu hình và tải khác nhau.

### c. UnionFS

## 3. Kiến thức cơ bản

### a. Docker Engine
Docker Engine là thành phần cốt lõi chạy trên Linux, trong khi Docker Desktop là một ứng dụng đa nền tảng có tích hợp Docker Engine và cung cấp trải nghiệm người dùng hoàn thiện hơn.

### b. Ephemeral FS
- Storage trong Docker container là tạm thời và sẽ bị xóa khi container bị dừng hoặc bị xóa. Điều này được gọi là **ephemeral storage** (lưu trữ tạm thời).
- Docker container được thiết kế để không có trạng thái (**stateless**), điều này giúp việc triển khai ứng dụng trở nên nhanh chóng, nhất quán, và dễ dàng quản lý trên các môi trường khác nhau mà không cần phải quan tâm đến dữ liệu trước đó của container.
- Nếu bạn cần lưu trữ lâu dài, Docker khuyến khích sử dụng **volumes** (dung lượng lưu trữ bên ngoài container) để lưu trữ dữ liệu mà không lo bị mất khi container bị xóa.

### c. Volume Mounts, Bind Mounts

- **Volume Mounts**:
  - Volume mounts cho phép bạn gắn kết một thư mục hoặc tệp từ hệ thống máy chủ vào container, giúp dữ liệu được lưu trữ ngoài container và không bị mất khi container bị dừng hoặc xóa.
  - Dữ liệu được lưu trữ trong volume là lâu dài và có thể được chia sẻ giữa nhiều container. Điều này làm cho việc chia sẻ và đồng bộ dữ liệu giữa các container trở nên dễ dàng và hiệu quả.
  
- **Bind Mounts**:
  - Bind mount liên kết trực tiếp một thư mục hoặc tệp có sẵn trên máy chủ với container thông qua đường dẫn tuyệt đối. Đây là phương pháp đơn giản nhưng có ít tính năng quản lý và sự linh hoạt.
  - Volume là một phương pháp mạnh mẽ hơn để lưu trữ dữ liệu trong Docker, vì Docker quản lý thư mục lưu trữ và có thể cung cấp các tính năng như sao lưu, phục hồi và chia sẻ dữ liệu giữa các container.
  - Bind mounts có thể hữu ích khi bạn cần kết nối trực tiếp với các tệp hoặc thư mục trên hệ thống máy chủ, nhưng nếu bạn muốn Docker quản lý lưu trữ một cách hiệu quả hơn, bạn nên sử dụng volumes.

| Tiêu chí            | Bind Mounts           | Volumes                    |
|---------------------|-----------------------|----------------------------|
| Quản lý             | Người dùng quản lý trực tiếp | Docker quản lý             |
| Đường dẫn           | Cần chỉ định đường dẫn tuyệt đối trên máy chủ | Docker tạo và quản lý đường dẫn trong thư mục Docker |
| Bền vững            | Dữ liệu phụ thuộc vào hệ thống máy chủ | Dữ liệu được Docker quản lý và bền vững hơn |
| Chia sẻ dữ liệu     | Chỉ chia sẻ dữ liệu giữa host và container | Dễ dàng chia sẻ dữ liệu giữa nhiều container |
| Sao lưu & phục hồi  | Không hỗ trợ          | Hỗ trợ sao lưu và phục hồi |
| Khi sử dụng         | Khi cần truy cập dữ liệu trực tiếp từ máy chủ | Khi cần dữ liệu bền vững, quản lý tốt hơn và chia sẻ giữa nhiều container |

### d. Third Party Images

- **database**:
  - Docker là một công cụ cho phép bạn đóng gói, phân phối, và chạy các ứng dụng trong các container (bao bọc ứng dụng). Container này là một môi trường tách biệt và độc lập với hệ điều hành chủ, giúp ứng dụng hoạt động nhất quán trên các hệ thống khác nhau.
  - Chạy cơ sở dữ liệu trong Docker giúp đơn giản hóa quá trình phát triển (ví dụ như việc cài đặt và cấu hình cơ sở dữ liệu) và triển khai (khi bạn cần mang cơ sở dữ liệu từ môi trường phát triển sang môi trường sản xuất).
  - Docker Hub là một kho lưu trữ trực tuyến nơi bạn có thể tìm và tải về các images (hình ảnh) Docker, là các cấu hình và phần mềm đã được đóng gói sẵn, giúp tiết kiệm thời gian. Bạn có thể tìm thấy rất nhiều image cho các cơ sở dữ liệu phổ biến như MySQL, PostgreSQL, và MongoDB, tức là bạn có thể dễ dàng triển khai các cơ sở dữ liệu này chỉ với vài lệnh Docker mà không phải cài đặt thủ công.

- **Interactive Test Environments**:
  - Docker cho phép bạn tạo ra các môi trường tách biệt (isolated environments). Điều này có nghĩa là mỗi ứng dụng hoặc dịch vụ chạy trong một container riêng biệt, và chúng không ảnh hưởng đến nhau hoặc hệ thống chính của bạn.
  - Những môi trường **disposable** (tạm thời, có thể xóa được) có thể bị xóa đi ngay khi bạn hoàn thành thử nghiệm. Điều này rất hữu ích khi bạn cần thử nghiệm với một phần mềm hay cấu hình mới mà không cần lo lắng về việc làm hỏng hệ thống của bạn. Sau khi thử nghiệm xong, bạn có thể dễ dàng xóa container để giải phóng tài nguyên và giữ cho hệ thống sạch sẽ.
  - Docker giúp bạn dễ dàng làm việc với phần mềm của bên thứ ba (third-party software), thử nghiệm các phụ thuộc (dependencies) khác nhau hoặc các phiên bản phần mềm mà không cần phải thay đổi hoặc phá vỡ cấu hình hiện tại trên máy tính của bạn.
  - Việc thử nghiệm nhanh chóng và không lo ảnh hưởng đến hệ thống chính giúp giảm thiểu rủi ro và giúp bạn linh hoạt hơn trong việc phát triển, kiểm tra và thử nghiệm các tính năng mới mà không sợ làm hỏng môi trường phát triển ban đầu.

- **Command Line Utilities**

    - Docker images có thể chứa các công cụ dòng lệnh hoặc ứng dụng hoàn chỉnh, và bạn có thể chạy chúng trong các container để thử nghiệm hoặc triển khai mà không làm ảnh hưởng đến môi trường chính của hệ thống

### e. Container Images

- **Dockerfile**
  - Dockerfile là một tệp chứa các hướng dẫn để Docker tạo ra Docker images, và mỗi hướng dẫn trong tệp Dockerfile thêm một lớp mới (layers) vào image. Sau khi hoàn thành, bạn có thể tạo và chạy containers từ image này.
  - Các **instruction** phổ biến trong Dockerfile bao gồm:
  ```
      FROM: Chỉ định image cơ sở mà bạn sẽ xây dựng từ đó (ví dụ: FROM ubuntu).
      RUN: Thực thi lệnh trong container (ví dụ: RUN apt-get update).
      COPY: Sao chép tệp từ hệ thống chủ vào trong container (ví dụ: COPY ./app /app).
      ADD: Tương tự như COPY, nhưng có thể giải nén tệp hoặc tải dữ liệu từ URL (ví dụ: ADD ./source.tar.gz /app).
      CMD: Chỉ định lệnh mặc định mà container sẽ chạy khi bắt đầu (ví dụ: CMD ["python", "app.py"]).
      ENTRYPOINT: Định nghĩa chương trình chính mà container sẽ thực thi khi khởi động.
      ENV: Thiết lập biến môi trường trong container (ví dụ: ENV PATH /usr/local/bin:$PATH).
      EXPOSE: Chỉ định cổng mà container sẽ sử dụng để giao tiếp với bên ngoài.
  ```
  => cách tối ưu hơn
  ```
      # Sử dụng Alpine Linux base image
      FROM alpine:latest

      # Cài đặt curl và sao chép ứng dụng, kết hợp các lệnh thành một layer duy nhất
      RUN apk add --no-cache curl && \
          mkdir /app && \
          cp -r ./myapp /app
  ```
- **Efficient Layer Caching**
    - Khi Docker xây dựng **container images**, nó cache các layers (lớp) mới tạo ra. Mỗi layer tương ứng với một bước trong quá trình xây dựng image từ **Dockerfile** (ví dụ như cài đặt phần mềm, sao chép tệp, v.v.).

    - **Docker caching** cho phép Docker tái sử dụng các layers đã có thay vì phải xây dựng lại từ đầu mỗi lần. Điều này giúp giảm thời gian xây dựng và tiết kiệm băng thông vì Docker không phải tải lại hoặc xây dựng lại các layers giống nhau
    - Nếu một **instruction** không thay đổi từ lần build trước, Docker sẽ tái sử dụng **layer** cũ (mà không phải xây dựng lại), giúp tiết kiệm thời gian và tài nguyên.

- **Reducing Image Size**

  - Sử dụng các base image tối giản như Alpine Linux
  - Sử dụng multi-stage builds để loại bỏ các công cụ xây dựng không cần thiết
  - Xóa các tệp và gói không cần thiết
  - Giảm số lượng layers bằng cách kết hợp các lệnh 
  ``` 
      # Sử dụng Ubuntu base image
      FROM ubuntu:latest

      # Cài đặt các gói phần mềm
      RUN apt-get update && apt-get install -y curl

      # Sao chép ứng dụng vào container
      COPY ./myapp /app
  ```


### f. Container Registries
- **DockerHub**
  - Docker Hub là một dịch vụ lưu trữ đám mây (cloud-based registry service), nơi bạn có thể lưu trữ và quản lý Docker images. Đây là repository chính thức và công khai của Docker, nơi người dùng có thể chia sẻ và phân phối các Docker images.

  - **Docker images** là các gói phần mềm chứa tất cả mọi thứ cần thiết để chạy một ứng dụng trong Docker container. Docker Hub cung cấp nơi lưu trữ cho những images này, giúp người dùng dễ dàng truy xuất và sử dụng chúng.

  - Docker Hub cung cấp hai loại repositories:

      1. **Public repositories** (kho lưu trữ công khai) miễn phí, nơi bất kỳ ai cũng có thể tải lên và tải xuống images.
      2. **Private repositories** (kho lưu trữ riêng tư) có phí, nơi người dùng có thể lưu trữ các images riêng tư chỉ dành cho họ hoặc nhóm người sử dụng nhất định.

  - Tích hợp với **Docker CLI**: Docker Hub dễ dàng tích hợp với Docker CLI (giao diện dòng lệnh của Docker), giúp người dùng dễ dàng push (đẩy) các images lên Docker Hub và pull (kéo) chúng về từ Docker Hub thông qua các lệnh đơn giản trong terminal.

  - Docker Hub có các **images** chính thức (official images) được duy trì bởi các nhà cung cấp phần mềm (như MySQL, PostgreSQL, Nginx, v.v.), giúp người dùng dễ dàng tìm và sử dụng các Docker images phổ biến, được đảm bảo chất lượng.

  - **Automated builds** (xây dựng tự động): Docker Hub hỗ trợ xây dựng tự động, liên kết với các source code repositories (kho mã nguồn) để tự động tạo Docker images khi có thay đổi trong mã nguồn.

  - **Webhooks**: Docker Hub cung cấp tính năng webhooks, cho phép người dùng thiết lập hành động tự động khi có sự kiện xảy ra trong kho lưu trữ (ví dụ như khi có một image mới được đẩy lên). Điều này giúp tích hợp với các hệ thống CI/CD hoặc thực hiện các quy trình tự động hóa khác.

- **Image Tagging Best Practices**

  - _Semantic versioning_  theo dạng ```MAJOR.MINOR.PATCH``` (ví dụ: 1.2.3).
      - **MAJOR**: Thay đổi có thể gây ra sự không tương thích ngược.
      - **MINOR**: Thêm tính năng mới nhưng vẫn duy trì tính tương thích.
      - **PATCH**: Sửa lỗi nhỏ mà không ảnh hưởng đến tính tương thích.
  
  - Triển khai chiến lược phân biệt giữa các môi trường. Ví dụ: ```myapp:1.0.0-dev```, ```myapp:1.0.0-prod```.

  - Sử dụng thẻ mô tả cho các biến thể (variants), Ví dụ: ```myapp:1.0.0-arm64```, ```myapp:1.0.0-x86_64```. 
  
  - Tài liệu hóa các quy tắc gán thẻ: giúp đảm bảo tính nhất quán trong cách đặt thẻ cho Docker images trong tổ chức.

### g. Running Containers

- ```docker run```
  - là lệnh dùng để tạo và khởi động một container mới từ một image đã chỉ định. Lệnh này kết hợp hai lệnh **docker create** và **docker start** thành một lệnh duy nhất, đồng thời cung cấp nhiều tùy chọn để tùy chỉnh môi trường chạy của container:
    
    - **Tạo và khởi động container**: Lệnh này không chỉ tạo ra một container mới từ một image, mà còn khởi động nó ngay lập tức.
    - **Tùy chỉnh môi trường chạy của container**: Bạn có thể thiết lập các biến môi trường, ánh xạ cổng (ports), ánh xạ thư mục (volumes), cấu hình kết nối mạng, và giới hạn tài nguyên cho container.
    - **Chế độ chạy**: Lệnh hỗ trợ chế độ detached (chạy ở chế độ nền) hoặc chế độ interactive (cung cấp shell truy cập vào container).
    - **Ghi đè lệnh mặc định**: Bạn có thể ghi đè lệnh mặc định mà image đã định nghĩa để chạy lệnh tùy chỉnh khi container được khởi động.    
    - Các flag quan trọng:
      - **-d**: Chạy container ở chế độ detached (nền).
      - **-p**: Ánh xạ cổng giữa máy chủ và container.
      - **-v**: Gắn kết (mount) volume (thư mục) từ hệ thống máy chủ vào container.
      - **--name**: Đặt tên tùy chỉnh cho container
- ```docker compose```

  - là công cụ cho phép bạn quản lý các ứng dụng Docker gồm nhiều container (multi-container). Thay vì chạy từng container riêng biệt bằng lệnh **docker run**, docker compose giúp bạn tổ chức và chạy tất cả các container của ứng dụng trong 1 lần duy nhất.
  - ```docker-compose.yml```: là file cấu hình dạng yaml, trong đó mô tả các thành phần của ứng dụng, bao gồm:
    - **Services**: các container trong ứng dụng, mỗi service có thể đại diện cho một container hoặc một nhóm các container có liên quan.
    - **Networks**: Mạng mà các container sẽ kết nối với nhau, giúp chúng có thể giao tiếp.
    - **Volumes**: các thứ mục hoặc dữ liệu mà muốn chia sẻ giữa các container hoặc giữa container và máy chủ.

- **Docker runtime configuration options**

  - các tùy chọn cấu hình runtime của Docker cung cấp khả năng tinh chỉnh chi tiết môi trường của container, từ tài nguyên hệ thống đến bảo mật, mạng và logging, giúp bạn tối ưu hóa hiệu suất và bảo mật cho các container, cũng như đảm bảo chúng chạy ổn định và nhất quán trong mọi môi trường triển khai.
### h. Container Security

- **Image security**
- **Runtime Security**  
  
### i. Docker CLI

- **Container**

  - 
### k. Developer Experience
### l. Deploying Containers

