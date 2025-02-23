# POSTGRESQL
- RDBMS + OODBMS = ORDBMS 
- Domains: 
    - lệnh: 
    ```sql
    CREATE DOMAIN <domain_name> AS <data_type> [constraints];
    ```
    - ví dụ: 
    ```sql
    CREATE DOMAIN name_domain AS TEXT CHECK (LENGTH(VALUE) <= 100) NOT NULL;
    CREATE TABLE employees (
        id SERIAL PRIMARY KEY,
        name name_domain,  -- Sử dụng domain name_domain
        email VARCHAR(255) NOT NULL
    );
    ```
- Tuples = record
- Exclusion: helps maintain data integrity by preventing conflicts in data
- null # 0/"" : any arithmetic operation involving a null results in a null, and comparisons with null using standard operators return unknown rather than true or false.To handle null values, PostgreSQL provides specific functions and constructs such as IS NULL, IS NOT NULL, and the COALESCE function, which returns the first non-null value in its arguments
- ACID = atomicity + consistency + isolation + durability
    - atomicity: đảm bảo rằng 1 transaction được xử lý như 1 đơn vị duy nhất = transaction phải hoàn thành toàn bộ hoặc không thực hiện gì cả.Ví dụ: Nếu bạn thực hiện một thao tác chuyển tiền từ tài khoản A sang tài khoản B, có hai bước cần thực hiện: trừ tiền từ tài khoản A và cộng tiền vào tài khoản B. Nếu một trong hai bước không thành công, toàn bộ transaction sẽ bị hủy bỏ và không có thay đổi nào được thực hiện.
    - consistency: luôn tuân thủ theo các quy tắc và ràng buộc ( ràng buộc khóa chính, khóa ngoại,...)
    - isolation: các transaction đang được thực hiện không ảnh hưởng đến nhau. Ví dụ: Nếu hai người đồng thời thực hiện các thao tác thay đổi số dư tài khoản trong hệ thống ngân hàng, cơ sở dữ liệu sẽ xử lý sao cho mỗi transaction sẽ không làm sai lệch dữ liệu của transaction khác, ngay cả khi chúng xảy ra đồng thời
    - durability: khi transaction đã được commit thì dữ liệu được lưu một cách vĩnh viễn trong cơ sở dữ liệu. Ví dụ: Nếu bạn thực hiện một transaction chuyển tiền và hệ thống thông báo rằng transaction đã thành công, dữ liệu sẽ được lưu lại ngay lập tức và không bị mất, ngay cả khi hệ thống gặp sự cố sau đó.
- MVCC: allow multiple transactions to access the same data concurrently without conflicts or delays.  It ensures that each transaction has a consistent snapshot of the database and can operate on its own version of the data.
- deadlocks (chết chùm)
- wal: The WAL records all changes made to the database in a sequential log before they are written to the actual data files. In case of a crash, PostgreSQL can use the WAL to bring the database back to a consistent state without losing any crucial data. This provides durability and crash recovery capabilities for your database.
- Query processing: Parsing -> Parse Tree -> Query Optimization -> Execution Plan -> Query Execution
- Join: inner join, left join, right join, full outer join
- Cơ chế Update bản ghi: 
    + Tạo một bản ghi mới: Khi bạn thực hiện UPDATE, PostgreSQL không sửa trực tiếp bản ghi cũ mà thay vào đó nó sẽ tạo ra một bản ghi mới, có các giá trị đã được thay đổi.
    + Chỉ mục không thay đổi ngay lập tức: Mặc dù bản ghi được cập nhật, nhưng bản ghi cũ sẽ không bị xóa ngay lập tức. Thay vào đó, nó sẽ được đánh dấu là "đã xóa" (tức là sẽ bị loại bỏ khi thực hiện VACUUM). Bản ghi mới sẽ được thêm vào cuối bảng và có thể có một giá trị ctid mới, làm cho nó không còn giữ vị trí cũ.
    + Đẩy xuống dưới cùng: Nếu bạn sử dụng một SELECT mà không có chỉ mục phù hợp, PostgreSQL có thể sắp xếp lại các bản ghi và dẫn đến việc bản ghi đã cập nhật không còn nằm ở vị trí ban đầu, đặc biệt nếu bản ghi này có một id nhỏ. Điều này là do PostgreSQL thường xuyên thực hiện việc thay đổi thứ tự của các bản ghi trong bảng khi có các bản ghi mới được tạo ra.
- ctid trong PostgreSQL là một mã định danh (identifier) duy nhất được PostgreSQL sử dụng để xác định vị trí vật lý của một bản ghi trong bảng: 
- ctid là một tuple bao gồm hai thành phần:
Block ID (ID khối): Được sử dụng để chỉ vị trí khối trên đĩa nơi bản ghi hiện tại được lưu trữ.
- Tuple ID (ID tuple): Được sử dụng để chỉ vị trí cụ thể của bản ghi trong khối.
- Cấu trúc của ctid trông giống như sau: (block_number, tuple_index), ví dụ: (0, 1)
Tác dụng:
    - Dùng để xác định vị trí bản ghi: ctid cho phép PostgreSQL biết chính xác vị trí của một bản ghi trên đĩa, điều này rất hữu ích trong các thao tác như UPDATE và DELETE. Khi một bản ghi được cập nhật, PostgreSQL sẽ tạo một bản sao mới của bản ghi đó, và bản ghi cũ sẽ bị đánh dấu là đã xóa (tạm thời). Tuy nhiên, bản ghi mới sẽ có một ctid mới, trong khi bản ghi cũ vẫn giữ ctid cũ cho đến khi thực hiện dọn dẹp (VACUUM).
    - Quản lý MVCC: Trong cơ chế Multi-Version Concurrency Control (MVCC) của PostgreSQL, nhiều phiên bản của một bản ghi có thể tồn tại trong bảng. ctid giúp PostgreSQL phân biệt giữa các phiên bản khác nhau của một bản ghi, đặc biệt là khi một bản ghi được cập nhật nhiều lần.
    - Không phải là chỉ mục: Mặc dù ctid giúp xác định vị trí của bản ghi, nó không phải là chỉ mục và không được sử dụng trong các chỉ mục mặc định của PostgreSQL. Nó chỉ có ý nghĩa trong phạm vi phiên bản cụ thể của bản ghi.  
- PostgreSQL sử dụng heap để lưu trữ dữ liệu trong các trang heap, nơi các bản ghi được lưu trữ mà không có thứ tự cố định và có thể được di chuyển khi cần. 
- InnoDB (MySQL) sử dụng pages để lưu trữ dữ liệu và chỉ mục, nơi mà các bản ghi được lưu trong các trang dữ liệu với kích thước cố định (16KB), và được sắp xếp theo các chỉ mục.
