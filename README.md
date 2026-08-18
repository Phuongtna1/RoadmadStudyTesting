# 🚀 Software Testing Roadmap & Checklist

Tài liệu này chứa lộ trình từ khóa (Keyword Map) chi tiết nhất về ngành Kiểm thử phần mềm (Software Testing). Được thiết kế dưới dạng checklist để theo dõi tiến độ học tập từ cơ bản đến nâng cao.

---

## 📋 Mục lục
1. [I. Mindset & Quy trình (Core Process)](#i-mindset--quy-trình-core-process)
2. [II. Manual Testing (Kỹ thuật cốt lõi)](#ii-manual-testing-kỹ-thuật-cốt-lõi)
3. [III. Automation Testing (Kỹ thuật lập trình)](#iii-automation-testing-kỹ-thuật-lập-trình)
4. [IV. Kiểm thử chuyên sâu (Advanced Testing)](#iv-kiểm-thử-chuyên-sâu-advanced-testing)
5. [V. Chứng chỉ & Xu hướng](#v-chứng-chỉ--xu-hướng)

---

## I. Mindset & Quy trình (Core Process)

- [ ] **SDLC (Software Development Life Cycle)**
  - [ ] Mô hình thác nước (Waterfall)
  - [ ] Mô hình chữ V (V-Model)
  - [ ] Mô hình Agile / Scrum
- [ ] **STLC (Software Testing Life Cycle)**
  - [ ] Requirement Analysis (Phân tích yêu cầu)
  - [ ] Test Planning (Lập kế hoạch)
  - [ ] Test Case Development (Thiết kế kịch bản)
  - [ ] Test Environment Setup (Chuẩn bị môi trường)
  - [ ] Test Execution (Chạy thử nghiệm)
  - [ ] Test Cycle Closure (Đóng giai đoạn)
- [ ] **7 Testing Principles (7 nguyên lý)**
  - [ ] Testing shows presence of defects (Chỉ ra lỗi, không chứng minh không có lỗi)
  - [ ] Exhaustive testing is impossible (Kiểm thử toàn bộ là bất khả thi)
  - [ ] Early testing (Kiểm thử càng sớm càng tốt)
  - [ ] Defect clustering (Lỗi thường tập trung một chỗ)
  - [ ] Pesticide paradox (Nghịch lý thuốc trừ sâu / Nhà mờ kịch bản)
  - [ ] Testing is context dependent (Kiểm thử phụ thuộc ngữ cảnh)
  - [ ] Absence-of-errors fallacy (Sai lầm về việc không có lỗi)
- [ ] **Agile / Scrum Framework**
  - [ ] User Story & Acceptance Criteria (Tiêu chí nghiệm thu)
  - [ ] Product Backlog & Sprint Backlog
  - [ ] Sprint Planning / Daily Scrum / Sprint Review / Retrospective
- [ ] **CI / CD (DevOps Pipeline)**
  - [ ] Continuous Integration (Tích hợp liên tục)
  - [ ] Continuous Delivery / Deployment (Triển khai liên tục)
- [ ] **Phân vai trò: QA vs QC vs Tester**
  - [ ] QA: Định hình quy trình (Process-oriented)
  - [ ] QC: Kiểm tra sản phẩm (Product-oriented)
  - [ ] Tester: Thực thi việc tìm lỗi

---

## II. Manual Testing (Kỹ thuật cốt lõi)

- [ ] **Đầu vào kiểm thử**
  - [ ] SRS (Software Requirement Specification)
  - [ ] BRD (Business Requirement Document)
  - [ ] Mockup / Wireframe / Design UI
- [ ] **Test Plan (Kế hoạch)**
  - [ ] Scope (In-scope & Out-of-scope)
  - [ ] Test Strategy (Chiến lược)
  - [ ] Schedule & Resource (Tiến độ & Nhân lực)
  - [ ] Risk & Mitigation (Rủi ro & Biện pháp giảm thiểu)
- [ ] **Cấu trúc Test Case**
  - [ ] Test Case ID / Title
  - [ ] Pre-conditions (Điều kiện tiên quyết)
  - [ ] Test Steps (Các bước thực hiện)
  - [ ] Test Data (Dữ liệu đầu vào)
  - [ ] Expected Result (Kết quả kỳ vọng)
  - [ ] Actual Result (Kết quả thực tế)
  - [ ] Status (Pass / Fail / Blocked / Skipped)
- [ ] **Black-box Test Techniques (Thiết kế kịch bản)**
  - [ ] Equivalence Partitioning (Phân vùng tương đương)
  - [ ] Boundary Value Analysis - BVA (Giá trị biên: 2-point, 3-point)
  - [ ] Decision Table Testing (Bảng quyết định logic)
  - [ ] State Transition Testing (Chuyển đổi trạng thái hệ thống)
  - [ ] Use Case Testing (Kiểm thử theo luồng người dùng)
- [ ] **Error-based Techniques (Dựa trên kinh nghiệm)**
  - [ ] Error Guessing (Đoán lỗi)
  - [ ] Exploratory Testing (Kiểm thử khám phá / Không kịch bản)
  - [ ] Checklist-based Testing
- [ ] **Quản lý lỗi (Bug Report)**
  - [ ] Severity (Độ nghiêm trọng: Critical, Major, Minor)
  - [ ] Priority (Độ ưu tiên: High, Medium, Low)
  - [ ] Bug Lifecycle (New -> Open -> Fixed -> Reopen -> Closed)
- [ ] **Công cụ quản lý**
  - [ ] JIRA (Quản lý dự án & Lỗi)
  - [ ] Trello / Redmine (Bảng Kanban tracking)

---

## III. Automation Testing (Kỹ thuật lập trình)

- [ ] **Chiến lược Automation**
  - [ ] Testing Pyramid (Unit Test -> Integration Test -> UI Test)
  - [ ] ROI Calculation (Tính toán lợi nhuận thu về so với chi phí bỏ ra)
- [ ] **Nền tảng Code cơ bản**
  - [ ] OOP (Encapsulation, Inheritance, Polymorphism, Abstraction)
  - [ ] Data Types, Loops, Control Statements (If-Else)
  - [ ] Exception Handling (Xử lý ngoại lệ / Lỗi code)
- [ ] **Định vị phần tử (Locators)**
  - [ ] ID / Name / Class Name
  - [ ] Link Text / Partial Link Text
  - [ ] CSS Selector
  - [ ] XPath (Absolute XPath & Relative XPath)
- [ ] **Web Automation Tools**
  - [ ] Selenium WebDriver
  - [ ] Playwright
  - [ ] Cypress
- [ ] **Mobile Automation Tools**
  - [ ] Appium
  - [ ] UiAutomator (Android) / XCUITest (iOS)
- [ ] **Automation Framework Architecture**
  - [ ] Page Object Model - POM (Tách biệt UI và Logic)
  - [ ] Data-Driven Testing (Đọc data từ Excel / CSV / JSON)
  - [ ] Keyword-Driven Testing
  - [ ] Behavior-Driven Development - BBD (Cucumber / Gherkin syntax)
  - [ ] Assertions (Kiểm tra kết quả: Hard Assert vs Soft Assert)

---

## IV. Kiểm thử chuyên sâu (Advanced Testing)

- [ ] **API Testing**
  - [ ] RESTful API & SOAP API
  - [ ] HTTP Methods (GET, POST, PUT, DELETE, PATCH)
  - [ ] HTTP Status Codes (2xx, 3xx, 4xx, 5xx)
  - [ ] Payload Format (JSON, XML)
  - [ ] Authentication (Bearer Token, API Key, OAuth 2.0)
- [ ] **API Tools**
  - [ ] Postman (Collection, Environment, Newman CLI)
  - [ ] REST Assured (Thư viện Java)
- [ ] **Performance Testing**
  - [ ] Load Testing (Kiểm thử tải thông thường)
  - [ ] Stress Testing (Kiểm thử giới hạn / Đánh sập hệ thống)
  - [ ] Endurance / Soak Testing (Kiểm thử độ bền qua thời gian dài)
  - [ ] Spike Testing (Kiểm thử lượng truy cập tăng đột biến)
- [ ] **Performance Tools**
  - [ ] JMeter (Thread Group, Sampler, Listener)
  - [ ] K6 (Viết script bằng JavaScript)
- [ ] **Security Testing (OWASP Top 10)**
  - [ ] SQL Injection (Chèn mã độc database)
  - [ ] Cross-Site Scripting - XSS (Chèn script phía Client)
  - [ ] Broken Authentication (Lỗi phân quyền)

---

## V. Chứng chỉ & Xu hướng

- [ ] **ISTQB (International Software Testing Qualifications Board)**
  - [ ] CTFL (Certified Tester Foundation Level)
  - [ ] CTAL (Advanced Level: Test Analyst, Technical Test Analyst, Test Manager)
- [ ] **AI-Driven Testing**
  - [ ] Prompt Engineering cho Tester (Sinh Test Case từ User Story)
  - [ ] AI Copilot / ChatGPT (Sinh mã script Automation, giải mã XPath phức tạp)
