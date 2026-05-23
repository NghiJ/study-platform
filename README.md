# Study Platform - Microservices

## Phân tích nghiệp vụ AutoWash

### 1) Phạm vi hệ thống
AutoWash là hệ thống quản lý vận hành rửa xe tự động, tập trung vào:
- Đặt lịch dịch vụ rửa xe.
- Tiếp nhận xe và theo dõi trạng thái xử lý theo thời gian thực.
- Điều phối nhân sự, làn rửa, và gói dịch vụ.
- Thanh toán và xuất hóa đơn.
- Báo cáo vận hành, doanh thu, chất lượng dịch vụ.

### 2) Tác nhân chính
- **Khách hàng**: đặt lịch, theo dõi trạng thái, thanh toán, đánh giá dịch vụ.
- **Lễ tân/Operator**: tiếp nhận xe, xác nhận lịch hẹn, điều phối quy trình.
- **Kỹ thuật viên/Nhân viên rửa xe**: thực hiện các bước dịch vụ.
- **Quản lý/Admin**: cấu hình dịch vụ, giá, ca làm, giám sát KPI.
- **Hệ thống thanh toán**: xử lý thanh toán online và đối soát.
- **Hệ thống thông báo**: gửi SMS/email/app notification.

## User Stories (US)

### Nhóm khách hàng
- **US-01**: Là khách hàng, tôi muốn xem các gói rửa xe để chọn dịch vụ phù hợp.
- **US-02**: Là khách hàng, tôi muốn đặt lịch theo thời gian mong muốn để giảm thời gian chờ.
- **US-03**: Là khách hàng, tôi muốn nhận xác nhận đặt lịch ngay sau khi tạo đơn.
- **US-04**: Là khách hàng, tôi muốn theo dõi trạng thái xe (chờ xử lý, đang rửa, hoàn tất) theo thời gian thực.
- **US-05**: Là khách hàng, tôi muốn thanh toán linh hoạt (tiền mặt/chuyển khoản/ví) để thuận tiện.
- **US-06**: Là khách hàng, tôi muốn xem lịch sử dịch vụ để theo dõi chi phí bảo dưỡng/rửa xe.
- **US-07**: Là khách hàng, tôi muốn đánh giá chất lượng sau dịch vụ để phản hồi trải nghiệm.

### Nhóm vận hành
- **US-08**: Là lễ tân, tôi muốn check-in xe nhanh để tránh ùn tắc tại điểm tiếp nhận.
- **US-09**: Là operator, tôi muốn gán xe cho làn rửa và nhân viên để tối ưu công suất.
- **US-10**: Là operator, tôi muốn xử lý lịch trễ/hủy/đổi giờ để duy trì lịch làm việc ổn định.
- **US-11**: Là nhân viên rửa xe, tôi muốn nhận danh sách việc cần làm theo thứ tự ưu tiên.
- **US-12**: Là quản lý, tôi muốn theo dõi hiệu suất theo ca để cải thiện vận hành.

### Nhóm quản trị/tài chính
- **US-13**: Là admin, tôi muốn cấu hình gói dịch vụ, bảng giá, khuyến mãi theo thời gian.
- **US-14**: Là quản lý, tôi muốn theo dõi doanh thu theo ngày/tuần/tháng.
- **US-15**: Là kế toán, tôi muốn đối soát thanh toán với hệ thống cổng thanh toán để đảm bảo chính xác.
- **US-16**: Là admin, tôi muốn xem audit log thao tác quản trị để truy vết khi có sự cố.
- **US-17**: Là quản lý, tôi muốn xem tỷ lệ đúng giờ và thời gian phục vụ trung bình để đánh giá chất lượng.

## Use Cases (UC)

### UC-01: Đặt lịch rửa xe
- **Tác nhân**: Khách hàng
- **Tiền điều kiện**: Khách hàng có tài khoản hợp lệ hoặc cung cấp thông tin liên hệ.
- **Luồng chính**:
  1. Khách hàng chọn gói dịch vụ.
  2. Chọn thời gian và địa điểm.
  3. Nhập thông tin xe.
  4. Xác nhận đặt lịch.
  5. Hệ thống tạo lịch hẹn và gửi thông báo.
- **Kết quả**: Lịch hẹn được tạo ở trạng thái `Booked`.

### UC-02: Check-in khi xe đến
- **Tác nhân**: Lễ tân/Operator
- **Tiền điều kiện**: Có lịch hẹn hợp lệ hoặc đơn vãng lai.
- **Luồng chính**:
  1. Lễ tân tra cứu lịch hẹn.
  2. Xác nhận biển số và thông tin khách.
  3. Cập nhật trạng thái xe sang `Checked-in`.
  4. Hệ thống đưa xe vào hàng chờ điều phối.
- **Ngoại lệ**: Không tìm thấy lịch hẹn thì tạo đơn walk-in.
- **Kết quả**: Xe sẵn sàng vào quy trình rửa.

### UC-03: Điều phối và thực hiện dịch vụ
- **Tác nhân**: Operator, Nhân viên rửa xe
- **Tiền điều kiện**: Xe đã check-in.
- **Luồng chính**:
  1. Operator gán làn rửa và nhân viên.
  2. Nhân viên bắt đầu xử lý và cập nhật tiến độ.
  3. Hoàn tất từng bước theo checklist.
  4. Cập nhật trạng thái `Completed`.
- **Kết quả**: Dịch vụ hoàn thành và chờ thanh toán/bàn giao.

### UC-04: Thanh toán và xuất hóa đơn
- **Tác nhân**: Khách hàng, Hệ thống thanh toán, Lễ tân
- **Tiền điều kiện**: Dịch vụ đã hoàn tất.
- **Luồng chính**:
  1. Hệ thống tính tiền theo gói/khuyến mãi.
  2. Khách hàng chọn phương thức thanh toán.
  3. Hệ thống ghi nhận kết quả thanh toán.
  4. Xuất hóa đơn/biên nhận.
- **Ngoại lệ**: Thanh toán thất bại thì chuyển trạng thái `Payment Pending`.
- **Kết quả**: Đơn hàng được thanh toán và đóng.

### UC-05: Hủy hoặc đổi lịch
- **Tác nhân**: Khách hàng, Operator
- **Tiền điều kiện**: Lịch hẹn chưa bắt đầu xử lý.
- **Luồng chính**:
  1. Người dùng chọn hủy/đổi lịch.
  2. Hệ thống kiểm tra điều kiện và chính sách.
  3. Cập nhật lịch mới hoặc trạng thái hủy.
  4. Gửi thông báo xác nhận.
- **Kết quả**: Lịch được cập nhật đúng trạng thái.

### UC-06: Báo cáo vận hành
- **Tác nhân**: Quản lý/Admin
- **Tiền điều kiện**: Có dữ liệu giao dịch và xử lý dịch vụ.
- **Luồng chính**:
  1. Chọn khoảng thời gian.
  2. Hệ thống tổng hợp số lượt xe, doanh thu, thời gian xử lý, tỷ lệ đúng giờ.
  3. Hiển thị dashboard và cho phép xuất báo cáo.
- **Kết quả**: Có báo cáo phục vụ vận hành và ra quyết định.

## Functional Requirements

- FR-01: Quản lý danh mục gói dịch vụ, bảng giá, phụ phí.
- FR-02: Quản lý lịch hẹn theo khung giờ và sức chứa từng điểm rửa.
- FR-03: Hỗ trợ đơn có lịch hẹn và đơn vãng lai.
- FR-04: Quản lý vòng đời đơn: `Booked` → `Checked-in` → `In Progress` → `Completed` → `Paid`/`Closed`.
- FR-05: Điều phối tự động hoặc thủ công theo làn rửa và nhân sự.
- FR-06: Theo dõi tiến độ theo từng bước checklist dịch vụ.
- FR-07: Tính phí theo gói, mã giảm giá, khuyến mãi theo thời gian.
- FR-08: Tích hợp thanh toán đa kênh và xử lý trạng thái thanh toán.
- FR-09: Gửi thông báo xác nhận, nhắc lịch, hoàn tất, thất bại thanh toán.
- FR-10: Quản lý lịch sử giao dịch và lịch sử dịch vụ theo khách hàng/xe.
- FR-11: Dashboard realtime về công suất, thời gian chờ, thời gian phục vụ.
- FR-12: Xuất báo cáo doanh thu, hiệu suất theo điểm rửa/ca/nhân viên.
- FR-13: Audit log cho thao tác cấu hình và cập nhật trạng thái quan trọng.
- FR-14: Phân quyền theo vai trò (Khách hàng, Lễ tân, Nhân viên, Quản lý, Admin).

## Non-functional Requirements

- NFR-01 (Bảo mật): Mã hóa dữ liệu nhạy cảm, phân quyền theo nguyên tắc tối thiểu.
- NFR-02 (Hiệu năng): Truy vấn trạng thái đơn và dashboard phản hồi nhanh.
- NFR-03 (Sẵn sàng): Hệ thống hoạt động ổn định trong giờ cao điểm.
- NFR-04 (Mở rộng): Có khả năng thêm điểm rửa, gói dịch vụ, kênh thanh toán.
- NFR-05 (Tin cậy): Đảm bảo không mất dữ liệu trạng thái đơn và thanh toán.
- NFR-06 (Quan sát): Có logs, metrics, alerts cho luồng đặt lịch, check-in, thanh toán.
- NFR-07 (Tích hợp): API/webhook có versioning, timeout và retry phù hợp.
- NFR-08 (Khả dụng): UI rõ ràng, thao tác nhanh cho operator tại quầy.

## Business Rules

- BR-01: Không cho phép đặt vượt sức chứa khung giờ tại mỗi điểm rửa.
- BR-02: Chỉ cho đổi/hủy lịch trong khoảng thời gian cho phép theo chính sách.
- BR-03: Chỉ operator/admin mới được chỉnh sửa trạng thái khi xe đã check-in.
- BR-04: Không thể đóng đơn khi thanh toán chưa thành công (trừ đơn nợ được duyệt).
- BR-05: Mọi thay đổi giá/gói dịch vụ phải được ghi audit log.
- BR-06: Mọi hoàn tiền phải có lý do và quyền phê duyệt hợp lệ.
- BR-07: Ưu tiên xe đã đặt lịch trước so với đơn vãng lai khi quá tải.
