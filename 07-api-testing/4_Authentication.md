# 📁 07. API Testing

*Mục tiêu: Làm chủ quy trình kiểm thử tầng tích hợp hệ thống, xử lý thuần thục các giao thức truyền thông HTTP, giải mã cấu trúc Request/Response, bẻ gãy các cơ chế xác thực bảo mật và thiết kế kịch bản kiểm toán API chuyên sâu.*

# **7.4. API Authentication Mechanisms**

## 📌 Mục lục nội bộ (Chặng 07)

- [ ] [**7.1. API Fundamentals**](./1_APIFundamentals.md)
- [ ] [**7.2. HTTP Protocol in API**](./2_HTTPProtocol.md)
- [ ] [**7.3. Request & Response Anatomy**](./3_RequestResponse.md)
- [ ] [**7.4. API Authentication Mechanisms**](./4_Authentication.md)
  - [ ] [7.4.1. Basic Auth vs API Key](./4_Authentication.md#741-basic-auth-vs-api-key)
  - [ ] [7.4.2. Bearer Token & JWT (JSON Web Token)](./4_Authentication.md#742-bearer-token--jwt-json-web-token)
  - [ ] [7.4.3. OAuth 2.0 Flow & Session/Cookie Auth](./4_Authentication.md#743-oauth-20-flow--sessioncookie-auth)
- [ ] [**7.5. API Test Scenarios Designing**](./5_APITestingStrategy.md)
- [ ] [**7.6. API Testing Tooling**](./6_Tools.md)

---

## 🗺️ Bản đồ Tiến trình Từ Nền tảng Giao tiếp đến Kiểm toán Tích hợp API

Sơ đồ đơn sắc dưới đây mô tả cách thức Tester bóc tách các trường phái kiến trúc mạng, mổ xẻ cấu trúc gói tin HTTP và áp dụng các kịch bản kiểm thử biên để bẻ gãy lớp bảo mật/logic của tầng trung gian:

```mermaid
graph TD
    A[Phân tích Kiến trúc Mạng: REST, SOAP, GraphQL] --> B(Mổ xẻ gói tin HTTP: Headers, Methods, Parameters)
    B --> C[Phá vỡ cơ chế xác thực: Basic Auth, JWT, OAuth 2.0]
    C --> D(Thiết kế Kịch bản API Testing chuyên sâu)
    D -->|Boundary, Rate Limiting, Idempotency| E[Kiểm toán phản hồi Response Body JSON/XML via Postman]
    E --> F(Ứng dụng Công cụ Tự động hóa: Postman Script & REST Assured)
```

---

# 7.4.1. Basic Auth vs API Key

Trong kiểm thử an ninh API (API Security Testing), việc xác thực danh tính là chốt chặn đầu tiên để bảo vệ tài nguyên hệ thống khỏi các truy cập bất hợp pháp. **Basic Authentication (Xác thực cơ bản)** và **API Key (Khóa ứng dụng)** là hai cơ chế xác thực sơ khai, gọn nhẹ nhưng được áp dụng rộng rãi. Thành thạo việc phân tích bản chất vận hành ngầm của hai cơ chế này giúp Tester thiết kế chính xác các ca kiểm thử phá hủy, lật tẩy các lỗ hổng rò rỉ mã hóa hoặc phân quyền lỏng lẻo của Backend Server.

> ⚠️ **Nguyên lý phơi bày thông điệp xác thực (Credentials Exposure Principle):** Cả Basic Auth và API Key đều là các hình thức xác thực tĩnh (Static Credentials) và không có cơ chế mã hóa đầu-cuối tự thân. Nếu hệ thống API không được ép buộc chạy trên giao thức bảo mật HTTPS, toàn bộ chuỗi ký tự xác thực truyền qua mạng sẽ bị các công cụ bắt gói tin (Sniffer) đọc trộm dưới dạng văn bản thô.

---

## 🛠️ Luồng Xử lý Bóc tách và Xác thực Danh tính tại Lớp Trung gian (Authentication Middleware Lifecycle)

Sơ đồ đơn sắc dưới đây mô tả chính xác chu trình lớp bảo mật trung gian (Middleware) đánh chặn gói tin HTTP Request để thẩm định chuỗi mã hóa trước khi cho phép truyền tải dữ liệu:

```mermaid
graph TD
    A[Client gửi HTTP Request đính kèm Headers xác thực] --> B[Middleware bảo mật đánh chặn gói tin]
    B --> C{Headers chứa mã hợp lệ?}
    C -- No --> D[Chặn đứng hành động và ném mã lỗi 401 Unauthorized]
    C -- Yes --> E[Giải mã thông điệp và đối chiếu quyền trong DB]
    E --> F[Chuyển tiếp yêu cầu vào Controller xử lý tài nguyên]
    D & F --> G[Trả về kết quả phản hồi API Response cho QA]
```

---

## 📊 Ma trận Phân rã Kỹ thuật Cơ chế Xác thực Cơ bản và Khóa Ứng dụng (QA Mindset)

Dưới đây là ma trận hệ thống hóa bản chất vận hành và phương thức bẻ gãy hai cơ chế xác thực cấp thấp, bóc tách theo quy chuẩn vi mô thực chiến:

| Giải pháp xác thực | Vị trí truyền tải ngầm trong HTTP | Cơ chế mã hóa vật lý | Trọng tâm kiểm thử (QA Focus) | Kịch bản lỗi điển hình thực chiến (Defect) |
| :--- | :--- | :--- | :--- | :--- |
| **1. Basic Auth** | Truyền qua Header duy nhất:<br>`Authorization: Basic <chuỗi_mã_hóa>` | Chuỗi văn bản thô `username:password` được chuyển đổi qua thuật toán **Base64** (Không phải mã hóa, có thể giải mã ngược trong 1 giây). | **Kiểm thử đánh cắp danh tính.** Lấy chuỗi mã hóa Base64 nạp vào bộ giải mã thô xem có đọc được mật khẩu gốc không. Ép lỗi gửi sai thông tin. | Hệ thống ném lỗi `500 Internal Error` lộ log thô kèm câu lệnh SQL truy vấn user khi QA cố tình gửi sai cấu trúc username. |
| **2. API Key** | Linh hoạt: Truyền qua Custom Header (Ví dụ: `X-API-Key`), Query Parameter trên URL, hoặc Request Body. | Chuỗi ký tự băm ngẫu nhiên (UUID/Hash) đại diện cho một ứng dụng hoặc một đối tác tích hợp cố định. | **Kiểm thử rò rỉ và thu hồi.** Xác thực cờ vô hiệu hóa khóa. Thử nghiệm gửi API Key cũ đã bị xóa xem Server có chặn lại không. | API Key truyền qua URL (`?apikey=xyz`) bị ghi lại thô bạo vào hệ thống Log của Proxy, tạo điều kiện cho hacker chiếm đoạt khóa. |

---

## 🧠 Chiến lược Thực chiến QA: Thiết kế Ca kiểm thử Phá hủy Hệ thống Xác thực

Một Tester thực chiến sử dụng tư duy phản biện để săn lùng các điểm mù trong lập trình xác thực, ngăn chặn nguy cơ tin tặc bypass tầng an ninh:

*   **Bẻ gãy chốt chặn Basic Auth bằng kỹ thuật giải mã ngược:** Khi kiểm thử API quản trị nội bộ `GET /api/v1/admin/config`. Bạn thấy Postman yêu cầu nhập Username/Password ở tab Authorization. Khi bấm gửi, hãy mở tab **Headers** thô. Bạn thấy dòng lệnh: `Authorization: Basic YWRtaW46c2VjcmV0MTIz`. Hãy sao chép chuỗi `YWRtaW46c2VjcmV0MTIz` nạp vào công cụ giải mã Base64 (Như `atob()` trong Console). Nếu kết quả trả về văn bản thô `"admin:secret123"`, điều đó chứng tỏ mật khẩu đang bay lơ lửng trên mạng. QA bắt buộc phải kiểm tra cấu hình hạ tầng mạng: Nếu API này chạy trên cổng HTTP thường (không có SSL/TLS), lập tức log Bug Critical yêu cầu khóa chặt cổng.
*   **Săn lỗi phân quyền lỏng lẻo của API Key (Privilege Escalation):** Hãy tưởng tượng bạn được cấp một khóa API Key dành riêng cho môi trường thử nghiệm với quyền hạn "Chỉ đọc" (`Read-only`). Bạn bắn một Request đọc dữ liệu `GET /api/v1/products` kèm khóa này, hệ thống trả về mã `200 OK` (Đúng thiết kế). Thử nghiệm đổi phương thức sang `DELETE /api/v1/products/10` nhưng vẫn giữ nguyên khóa API Key Read-only đó. Nếu máy chủ Backend vẫn thực thi lệnh xóa và trả về `200 OK / 204 No Content`, bạn đã bắt được một lỗi bảo mật nghiêm trọng: Backend Server chỉ kiểm tra xem khóa API Key có tồn tại hay không mà bỏ quên việc phân quyền thao tác cho khóa (Broken Object Level Authorization).

---

## 📚 References
* [ISTQB® Certified Tester Foundation Level (CTFL) Syllabus v4.0](https://istqb.org) - Section 2.2.1: Component Integration Testing (Security Authentication and Interface Interface Flaws).
* [OWASP API Security Top 10 Standards](https://owasp.org) - API2:2023 Broken Authentication & API5:2023 Broken Object Level Authorization.


# 7.4.2. Bearer Token & JWT (JSON Web Token)

Trong kiểm thử an ninh API hiện đại, **JWT (JSON Web Token)** phối hợp với cơ chế truyền tải **Bearer Token** là chốt chặn bảo mật tối thượng được áp dụng rộng rãi trong kiến trúc phân tán (Microservices). Thành thạo việc mổ xẻ cấu trúc 3 phần mã hóa của JWT giúp Tester chủ động thực hiện các ca kiểm thử bẻ gãy bộ lọc an ninh, lật tẩy các lỗ hổng giả mạo chữ ký hoặc cấu hình thời gian hết hạn lỏng lẻo của Backend Server.

> ⚠️ **Nguyên lý bất khả xâm phạm chữ ký số (Signature Tampering Principle):** Chuỗi JWT là một chuỗi mã hóa phi trạng thái tự thân (Self-contained). Máy chủ Backend không cần truy vấn Database vẫn có thể tin tưởng Client nếu chữ ký số hợp lệ. Việc Backend bỏ quên bước thẩm định chữ ký số (Signature Verification) sẽ mở toang cánh cửa cho hacker tự chế Token để chiếm đoạt tài khoản.

---

## 🛠️ Luồng Khởi tạo, Mã hóa và Thẩm định Mã Token JWT (JWT Token Lifecycle)

Sơ đồ đơn sắc dưới đây mô tả chính xác chu trình hệ thống cấp phát mã JWT sau khi đăng nhập và cơ chế Middleware bóc tách, thẩm định chữ ký số ở các request tiếp theo:

```mermaid
graph TD
    A[Client gửi thông tin Đăng nhập hợp lệ] --> B[Server tạo chuỗi JWT gồm 3 phần ký số]
    B --> C[Client lưu JWT và đính vào Header Bearer Token]
    C --> D[Middleware Backend bóc tách chuỗi và giải mã chữ ký]
    D -->|Chữ ký bị sửa đổi / Hết hạn| E[Chặn đứng và ném mã lỗi 401 Unauthorized]
    D -->|Chữ ký trùng khớp| F[Cho phép chuyển tiếp yêu cầu lấy tài nguyên]
```

---

## 📊 Ma trận Phân rã Cấu trúc Vi mô 3 Thành phần của Chuỗi JWT (QA Mindset)

Mỗi chuỗi JWT truyền qua mạng được phân tách rõ ràng bằng hai dấu chấm (`.`). Dưới đây là ma trận hệ thống hóa cấu trúc vi mô bên trong mã Token, giúp Tester định hình kịch bản khai thác lỗi:

| Thành phần JWT | Cơ chế mã hóa vật lý | Nội dung thông tin lưu trữ | Trọng tâm kiểm thử (QA Focus) | Kịch bản lỗi điển hình thực chiến (Defect) |
| :--- | :--- | :--- | :--- | :--- |
| **1. Header<br>(Phần đầu)** | Chuỗi Base64Url mã hóa văn bản thô JSON. | Định nghĩa loại Token (`JWT`) và thuật toán ký số bảo mật (Ví dụ: `HS256`, `RS256`). | **Kiểm thử tấn công thuật toán.** Sửa đổi giá trị thuật toán trong JSON thành `"none"` xem Server có bị lừa không. | **Lỗ hổng JWT None Algorithm:** Server ngây thơ chấp nhận Token có thuộc tính `alg: none` và cho phép vượt qua lớp bảo mật không cần chữ ký. |
| **2. Payload<br>(Phần thân)** | Chuỗi Base64Url chứa thông tin thô dạng JSON. | Chứa các xác bố dữ liệu (*Claims*) như thông tin người dùng (`sub`, `role`) và thời gian hết hạn (`exp`). | **Kiểm thử thao tác thời gian.** Thay đổi thời gian hết hạn `exp` về tương lai hoặc sửa vai trò `role` từ user lên admin. | **Lỗi không check thời gian:** Token đã hết hạn từ 3 ngày trước nhưng Backend Server vẫn chấp nhận thực thi lệnh do thiếu logic hậu kiểm cờ `exp`. |
| **3. Signature<br>(Chữ ký số)** | Chuỗi băm mật mã tạo ra bằng cách gộp `Header + Payload + Khóa bí mật (Secret Key)`. | Mã băm độc bản để bảo vệ tính toàn vẹn, chống lại mọi hành vi chỉnh sửa thông tin từ phía Client. | **Kiểm thử tính toàn vẹn độc bản.** Thử cố tình sửa một ký tự bất kỳ trong phần Payload, giữ nguyên chữ ký số rồi bấm gửi. | **Lỗi bỏ quên chữ ký:** Server vẫn giải mã phần Payload và cho phép đăng nhập thành công mặc dù chữ ký số hoàn toàn bị sai lệch (Bug an ninh nghiêm trọng). |

---

## 🧠 Chiến lược Thực chiến QA: Thiết kế Ca kiểm thử Giả mạo Dữ liệu Payload

Một Tester thực chiến sử dụng tab **Headers** của Postman và công cụ giải mã (như jwt.io) để tiến hành tiêm lỗi, kiểm thử phá hủy bộ lọc Token Bearer:

*   **Kịch bản khai thác:** Bạn đăng nhập tài khoản cấp thấp (`role: "USER"`), nhận về chuỗi JWT: `Header.Payload.Signature`.
*   **Hành động QA:** Sao chép chuỗi này nạp vào công cụ bóc tách. Thay đổi giá trị thô trong phần Payload từ `"role": "USER"` thành `"role": "ADMIN"`. Công cụ sẽ tự động sinh ra chuỗi Payload mã hóa Base64Url mới. Ghép chuỗi Payload mới này với phần Header cũ và phần Chữ ký số cũ để tạo ra một Token giả mạo. Đính Token này vào Header Request: `Authorization: Bearer <token_giả_mạo>` rồi bắn API xóa dữ liệu hệ thống.
*   **Đối chiếu kết quả (Expected Result):** Bộ não Middleware của Backend Server bắt buộc phải quét qua phần chữ ký số. Vì Payload đã bị QA chỉnh sửa, mã băm chữ ký số tính toán lại tại Server sẽ không thể trùng khớp với chữ ký số cũ đính kèm. Server bắt buộc phải từ chối và ném mã lỗi `401 Unauthorized`. Nếu Server lỏng lẻo, chấp nhận thực thi lệnh xóa, bạn đã bắt được siêu Bug bảo mật mức độ phá hủy hệ thống (Critical Defect - Privilege Escalation).

---

## 📚 References
* [ISTQB® Certified Tester Foundation Level (CTFL) Syllabus v4.0](https://istqb.org) - Section 2.2.1: Component Integration Testing (Security Token Verification and Identity Spoofing).
* [OWASP API Security Top 10 Standards](https://owasp.org) - API2:2023 Broken Authentication & RFC 7519: JSON Web Token (JWT) Global Specifications.

# 7.4.3. OAuth 2.0 Flow & Session/Cookie Auth

Trong kiểm thử an ninh và tích hợp liên hệ tầng, Tester cần làm chủ hai trường phái quản lý quyền hạn và phiên làm việc khác biệt: **OAuth 2.0 (Giao thức ủy quyền bên thứ ba)** và **Session/Cookie Authentication (Xác thực trạng thái phiên làm việc)**. Việc bóc tách bản chất vận hành ngầm của hai cơ chế này giúp QA định hình chính xác chiến lược kiểm thử, bẻ gãy các luồng điều hướng gói tin và ngăn chặn các lỗ hổng rò rỉ phiên làm việc nghiêm trọng trên Server.

> ⚠️ **Nguyên lý ranh giới ủy quyền và trạng thái (Authorization & State Boundary Principle):** OAuth 2.0 không phải là giao thức xác thực danh tính (Authentication) mà là giao thức ủy quyền hạn đặc quyền (Authorization) thông qua chuỗi Access Token tĩnh. Ngược lại, Session-based Auth là cơ chế lưu trạng thái (Stateful) trên bộ nhớ máy chủ. Việc Backend xử lý lỗi các mốc thời gian hết hạn hoặc mã hóa lỏng lẻo chuỗi ID của hai cơ chế này sẽ tạo điều kiện cho tin tặc chiếm đoạt toàn bộ phiên làm việc của người dùng.

---

## 🛠️ Luồng Ủy quyền Gói tin qua Giao thức OAuth 2.0 (Authorization Code Flow)

Sơ đồ đơn sắc dưới đây mô tả chính xác chu kỳ 5 bước luân chuyển gói tin, cấp phát Authorization Code và hoán đổi Access Token giữa Client, Máy chủ ủy quyền (Authorization Server) và API Resource Server:

```mermaid
graph TD
    A[Người dùng đồng ý ủy quyền đặc quyền tại Client] --> B[Ứng dụng nhận mã Authorization Code tạm thời]
    B --> C[Client gửi Code đính kèm Client Secret lên Authorization Server]
    C --> D[Hệ thống xác thực mã và cấp phát mã Access Token]
    D --> E[Client dùng Access Token gọi API lấy dữ liệu bảo mật]
    E --> F[Resource Server đối chiếu mã Token và phản hồi dữ liệu]
```

---

## 📊 Ma trận Phân rã Cơ chế Ủy quyền OAuth 2.0 và Xác thực Session/Cookie (QA Mindset)

Dưới đây là ma trận hệ thống hóa bản chất kỹ thuật của hai giải pháp kiểm soát quyền hạn phiên, bóc tách theo quy chuẩn vi mô thực chiến của một chuyên gia QA:

| Cơ chế quản lý phiên | Bản chất vận hành ngầm | Trọng tâm kiểm thử (QA Focus) | Kịch bản lỗi điển hình thực chiến (Defect) |
| :--- | :--- | :--- | :--- |
| **1. OAuth 2.0 Flow** | Hệ thống sử dụng một chuỗi mã tạm thời (*Authorization Code*) hoán đổi lấy một mã định danh đặc quyền ngắn hạn (*Access Token*) để truy cập tài nguyên của bên thứ ba mà không cần lộ mật khẩu gốc. | **Kiểm thử đánh tráo mã (Code Interchange).** Thử nghiệm lấy mã Code của tài khoản A nạp vào luồng xử lý của tài khoản B để kiểm tra xem Server có chặn không. | **Lỗi chiếm đoạt Token:** Lập trình viên Backend không cấu hình tham số bảo mật `state` trên URL điều hướng, tạo điều kiện cho hacker tấn công giả mạo yêu cầu chéo trang (CSRF). |
| **2. Session/Cookie** | Cơ chế Stateful: Máy chủ tạo một file Session lưu trên RAM/DB và trả về cho Client một mã ID duy nhất (`SessionID`) nằm gọn trong Cookie. Trình duyệt tự động gửi đính kèm Cookie này trong các Request tiếp theo. | **Kiểm tra cấu hình cờ bảo mật Cookie.** Sử dụng công cụ Chrome DevTools để rà soát xem các thuộc tính `HttpOnly`, `Secure` và `SameSite` của Cookie chứa SessionID có được kích hoạt đầy đủ. | **Lỗi rò rỉ phiên làm việc:** Cookie nhạy cảm bị bỏ quên cờ `HttpOnly`, tạo điều kiện cho các đoạn mã độc Javascript (`XSS`) dễ dàng đọc trộm chuỗi SessionID để đánh cắp tài khoản khách hàng. |

---

## 🧠 Chiến lược Thực chiến QA: Thiết kế Ca kiểm thử Đột tử Phiên và bypass bảo mật

Một Tester thực chiến sử dụng tư duy phản biện để săn lùng các điểm mù trong lập trình xác thực, ngăn chặn nguy cơ tin tặc bypass tầng an ninh:

*   **Săn lỗ hổng thu hồi mã Token (Token Revocation Flaw) trong OAuth 2.0:** Hãy tưởng tượng người dùng bấm nút "Đăng xuất" hoặc "Hủy liên kết ứng dụng bên thứ ba" trên giao diện UI. Ứng dụng xóa Token khỏi bộ nhớ Client và chuyển hướng người dùng ra màn hình đăng nhập. 
    * Tư duy phản biện của QA: Bạn hãy sao chép chuỗi `Access Token` đó từ trước khi bấm nút. Sau khi hệ thống báo hủy liên kết thành công, hãy mở công cụ Postman, tự tạo một Request thô, đính chuỗi Access Token cũ vào Header và bắn thẳng vào API Resource Server. 
    * Nếu Server vẫn chấp nhận lệnh và trả về dữ liệu `200 OK`, bạn đã bắt được Bug bảo mật nghiêm trọng: Hệ thống Backend chỉ xóa Token ở phía Client chứ quên không gọi lệnh vô hiệu hóa chuỗi Token đó trong Database của Authorization Server.
*   **Săn lỗi Session Fixation (Cố định phiên làm việc) qua Cookie:** Quy chuẩn an ninh an toàn yêu cầu: Mỗi khi người dùng thực hiện hành động chuyển đổi trạng thái (Từ chưa đăng nhập sang đăng nhập thành công), hệ thống máy chủ **bắt buộc phải hủy bỏ mã SessionID cũ** và cấp phát một mã SessionID mới hoàn toàn để chống lại hành vi cố định phiên làm việc. 
    * QA thực hiện kịch bản test: Mở trình duyệt, sao chép mã SessionID khi chưa đăng nhập. Tiến hành nhập Username/Password để đăng nhập thành công. 
    * Kiểm tra lại giá trị SessionID trong tab Application: Nếu chuỗi ID này vẫn giữ nguyên giá trị cũ không thay đổi, hệ thống dính lỗ hổng bảo mật nghiêm trọng `Session Fixation`, cho phép tin tặc ghim sẵn mã ID rác vào máy nạn nhân và âm thầm chiếm quyền điều khiển ngay khi họ vừa đăng nhập xong.

---

## 📚 References
* [ISTQB® Certified Tester Foundation Level (CTFL) Syllabus v4.0](https://istqb.org) - Section 2.2.1: Component Integration Testing (Security Authentication Protocol Flaws and Stateful Interface Flaws).
* [OWASP API Security Top 10 Standards](https://owasp.org) - API2:2023 Broken Authentication & RFC 6749: The OAuth 2.0 Authorization Framework.

