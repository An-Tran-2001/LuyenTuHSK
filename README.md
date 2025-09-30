# HSK Vocabulary Trainer

Ứng dụng Tkinter hỗ trợ ôn luyện từ vựng HSK (HSK1–HSK6) với câu hỏi trắc nghiệm nhiều lựa chọn. Dữ liệu nguồn được lấy trực tiếp từ file Excel tổng hợp, mỗi sheet tương ứng với một cấp độ HSK. Chương trình theo dõi tiến độ, số câu trả lời đúng/sai, trái tim (mạng), đồng thời lưu lại lịch sử thi và cho phép xem lại chi tiết từng câu hỏi.

> Được tạo 100% bằng AI (chưa review hay xem gì vì lười tạo ra chỉ để học vẹt tiếng trung)

> Tải ở releases hoặc [ấn vào link](https://github.com/An-Tran-2001/LuyenTuHSK/releases/tag/v1.0)
## Yêu cầu hệ thống
- Python 3.10 trở lên (ứng dụng dùng kiểu chú thích PEP 585)
- Thư viện `pandas` kèm `openpyxl` để đọc file `.xlsx`
- Tkinter (đi kèm bản cài đặt Python chuẩn trên Windows/macOS/Linux)
- Môi trường hiển thị GUI (nếu chạy từ WSL cần cài thêm X server)

## Cài đặt
1. Tải hoặc clone mã nguồn vào một thư mục, bảo đảm file Excel và thư mục `assets/` nằm cùng cấp với `tk_vocab_quiz.py`.
2. (Khuyến nghị) Tạo môi trường ảo:
   ```bash
   python -m venv .venv
   .venv\Scripts\activate  # Windows
   source .venv/bin/activate # macOS/Linux
   ```
3. Cài các phụ thuộc chính:
   ```bash
   pip install pandas openpyxl
   ```

## Chuẩn bị dữ liệu
Ứng dụng đọc toàn bộ sheet trong workbook Excel và chuẩn hóa theo `COLUMN_MAP` trong mã nguồn:

| Cột trong Excel            | Ý nghĩa trong ứng dụng |
|---------------------------|------------------------|
| `Từ mới`                  | Từ vựng (chữ Hán)      |
| `Phiên âm`                | Phiên âm (pinyin)      |
| `Giải thích`              | Nghĩa tiếng Việt       |
| `Ví dụ (chữ hán)`         | Câu ví dụ chữ Hán      |
| `Phiên âm.1`              | Phiên âm câu ví dụ     |
| `Dịch`                    | Dịch nghĩa câu ví dụ   |

Mỗi sheet nên đặt tên trùng cấp độ (ví dụ `HSK1`, `HSK2`, …) để hiện thị đúng trong danh sách lựa chọn. Các hàng thiếu cột `Từ mới` sẽ bị bỏ qua.

## Khởi chạy nhanh
```bash
python tk_vocab_quiz.py
```

- Ứng dụng sẽ tự tìm file `FULL TỪ VỰNG HSK1- HSK6 (2).xlsx` trong cùng thư mục. Muốn dùng file khác, có thể sửa biến `workbook` ở cuối file hoặc khởi tạo `VocabQuizApp` từ script riêng:
  ```python
  from pathlib import Path
  from tk_vocab_quiz import VocabQuizApp
  import tkinter as tk

  root = tk.Tk()
  app = VocabQuizApp(root, Path('duong_dan/toi_file.xlsx'))
  root.mainloop()
  ```

## Hướng dẫn sử dụng
- **Trang chủ**: nút `Bắt đầu`, `Thoát`, ô thống kê Top lịch sử đã hoàn tất (giữ lại tối đa 100 phiên). Có thể tích chọn nhiều cấp độ HSK cùng lúc.
- **Bắt đầu luyện tập**: khi nhấn `Bắt đầu`, chương trình trộn ngẫu nhiên các mục từ, đặt lại điểm và 10 mạng (`MAX_LIVES = 10`).
- **Chế độ câu hỏi**: chọn `Phiên âm` hoặc `Nghĩa` để xác định loại đáp án cần chọn cho từ đang hiển thị. Mỗi câu có 4 phương án (1 đúng, 3 nhiễu) sinh từ danh sách dữ liệu hiện có.
- **Kiểm tra và tiếp tục**: chọn đáp án rồi nhấn `Kiểm tra`. Ứng dụng hiển thị phản hồi tức thời, bảng chi tiết đáp án đúng, thông tin từ vựng và (nếu trả lời sai) thêm mục “Bạn đã chọn”. Nhấn `Tiếp tục` để chuyển câu kế tiếp.
- **Mạng sống & tiến độ**: dải trái tim hiển thị số mạng còn lại. Mỗi lần trả lời sai trừ 1 mạng; hết 10 mạng phiên luyện sẽ kết thúc. Thanh tiến độ cập nhật theo tỷ lệ câu trả lời đúng.
- **Lịch sử phiên**: bảng ở trang chủ ghi lại từng câu (số thứ tự, từ, cấp độ, loại câu hỏi, đáp án đúng/chọn, kết quả). Nhấp đúp một dòng để chuyển sang trang chi tiết, nơi hiển thị đầy đủ pinyin, nghĩa, câu ví dụ.
- **Kết thúc**: phiên có thể kết thúc do hết từ, hết mạng hoặc người dùng chọn `Trở về màn hình chính`. Thông tin phiên (thời gian, số đúng/sai, lý do kết thúc) được lưu vào `session_records` để hiển thị ở Top lịch sử.

## Cấu trúc mã nguồn
- `sanitize_value` làm sạch dữ liệu thô từ Excel và trả về chuỗi.
- `load_vocabulary` đọc toàn bộ workbook, đổi tên cột theo `COLUMN_MAP`, loại bỏ dòng rỗng và gán trường `level` theo tên sheet.
- `VocabQuizApp` xây dựng toàn bộ giao diện và nghiệp vụ:
  - `setup_styles`, `build_widgets`, `show_home/quiz/detail` tùy biến giao diện Tkinter.
  - `apply_level_filter`, `refresh_unique_values` lọc dữ liệu theo cấp độ đã chọn và chuẩn bị nguồn sinh đáp án.
  - `next_question`, `update_options`, `check_answer` điều khiển vòng đời câu hỏi và logic chấm điểm.
  - `add_history_row`, `update_history_summary`, `record_session` quản lý thống kê, lịch sử và bảng điểm tổng hợp.
  - `show_answer_detail`, `populate_detail_view` dựng bảng chi tiết cho câu hiện tại hoặc lịch sử.
- `if __name__ == "__main__"` tạo cửa sổ Tkinter và khởi chạy ứng dụng.

## Kiểm thử nhanh
Script `__test_start_button.py` tạo ứng dụng ở chế độ ẩn (`root.withdraw()`), kích hoạt `start_quiz`, sau đó gọi `end_quiz` để kiểm tra nhanh trạng thái nút `Bắt đầu` và biến `session_active`. Có thể chạy trực tiếp:
```bash
python __test_start_button.py
```

## Tài nguyên giao diện
- Thư mục `assets/` chứa `app_icon.png`, `app_icon.ico` (đặt biểu tượng cửa sổ) và `background.png` (chưa được sử dụng trong giao diện hiện tại).
- Ứng dụng tự động bỏ qua lỗi tải icon (ví dụ chạy trên hệ điều hành không hỗ trợ định dạng `.ico`).

## Gợi ý phát triển thêm
- Bổ sung chế độ nhập tự do (typing) hoặc flashcard.
- Ghi lại chi tiết câu trả lời vào file CSV/JSON để thống kê dài hạn.
- Hỗ trợ lọc nâng cao (ví dụ chọn phạm vi điểm số hoặc chủ đề) và tìm kiếm trong lịch sử.
- Thêm chức năng luyện tập theo lượt (ví dụ giới hạn 20 câu) hoặc lặp lại những câu sai.
