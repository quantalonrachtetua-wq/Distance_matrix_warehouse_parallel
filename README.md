# Distance_matrix_warehouse
Ma trận khoảng cách trong kho hàng , xếp theo từng dãy kệ đối lưng và song song
<img width="1149" height="614" alt="image" src="https://github.com/user-attachments/assets/e99b1666-0ec4-47b8-bcdb-12fe8d7d04bb" />
## 🧠 Quy tắc Di chuyển & Logic Tính toán (Movement Logic)

Dự án này mô phỏng và tính toán khoảng cách di chuyển thực tế trong kho hàng dựa trên cấu trúc vật lý cụ thể, không phải khoảng cách đường chim bay (Euclidean).

### 1. Thông số Kho hàng
Các tham số kích thước được định nghĩa cố định (đơn vị: mét):
- **`d_slot` (1.2m):** Chiều dài mỗi ô kệ (theo phương ngang).
- **`d_row` (0.3m):** Bề dày vách ngăn giữa 2 dãy kệ.
- **`d_aisle` (1.0m):** Chiều rộng lối đi giữa 2 dãy đối mặt.
- **`d_cross` (2.0m):** Khoảng cách (khe hở) giữa các Cụm kệ (Block A-B, B-C).
- **`BLOCK_WIDTH`:** Chiều rộng vật lý của một cụm = `6 * d_slot`.

### 2. Nguyên lý Di chuyển

Hệ thống chia làm 3 trường hợp tính toán chính:

#### Trường hợp A: Di chuyển Nội bộ Dãy (Cùng Cụm, Cùng Dãy)
* **Logic:** Di chuyển thẳng dọc theo dãy kệ.
* **Công thức:** `|Vị trí 1 - Vị trí 2| * d_slot`

#### Trường hợp B: Di chuyển Nội bộ Cụm (Cùng Cụm, Khác Dãy)
Đây là phần phức tạp nhất với logic **"Chọn đường ngắn nhất"** và xử lý **"Cặp dãy đối lưng"**:

1.  **Logic cơ bản (Đi vòng):**
    * Để đi từ Dãy `n1` sang Dãy `n2`, nhân viên phải đi ra đầu dãy (Cửa z=1 hoặc Cửa z=6), di chuyển dọc hành lang, rồi đi vào.
    * Thuật toán sẽ tính cả 2 phương án (thoát ra cửa 1 và thoát ra cửa 6) rồi chọn giá trị nhỏ nhất (`min`).

2.  **Logic Cặp dãy (Back-to-Back vs Facing):**
    * Kho được bố trí theo cặp lưng đấu lưng: (Dãy 1-2), (Dãy 3-4)...
    * **Lưng đấu lưng (Back-to-Back):** Ví dụ Dãy 1 sang Dãy 2. Không thể đi xuyên qua. Bắt buộc phải đi vòng ra đầu dãy.
    * **Đối mặt (Facing):** Ví dụ Dãy 2 sang Dãy 3. Giữa chúng là lối đi (`d_aisle`).
    * **Tối ưu hóa (Cross-Aisle):** Nếu 2 dãy nằm đối mặt nhau (Dãy chẵn -> Dãy lẻ liền kề), thuật toán cho phép **đi băng ngang qua lối đi** (cộng thêm `d_aisle`) thay vì phải đi vòng ra đầu dãy.

#### Trường hợp C: Di chuyển Khác Cụm (Inter-Block)
Áp dụng logic **"Xuyên ngang" (Horizontal Pass-through)**:

* **Mô hình:** 3 Cụm A, B, C xếp hàng ngang.
* **Di chuyển:** Khi đi từ Cụm A sang Cụm C, nhân viên sẽ đi xuyên ngang qua lòng Cụm B (tại cùng số Dãy) thay vì phải đi vòng lên đầu kho.
* **Quy trình:**
    1.  Thoát ra mép của cụm hiện tại (Hướng về phía cụm đích).
    2.  Băng qua các khoảng trống (`d_cross`) và xuyên qua chiều rộng các cụm trung gian (`BLOCK_WIDTH`).
    3.  Tiếp cận mép của cụm đích.
    4.  Di chuyển dọc tại mép cụm đích để đến đúng Dãy.
    5.  Đi vào vị trí kệ.

---
*Logic này đảm bảo mô phỏng chính xác hành vi lấy hàng thực tế của con người trong kho nhiều lối đi (Multi-aisle Warehouse).*
**Future Improvements" (Cải tiến trong tương lai)**
Tùy chỉnh số lượng dãy, số lượng cụm, số lượng kệ mỗi dãy
