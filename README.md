# D2 · Logistics Control Room

Demo ý tưởng cho đề D2 (Hiển thị – Dự báo – Mô phỏng năng lực logistics) tại DENSO Factory Hacks 2026.

> **Lưu ý:** dữ liệu trong demo là **dữ liệu tổng hợp (synthetic)**. Năng lực nguồn lực, tỷ lệ dòng hàng giữa các zone và chi phí là giả định để minh họa phương pháp, chưa phải số liệu thực của nhà máy.

## Ý tưởng

Gom dữ liệu logistics theo từng khu vực (zone) và chạy một vòng khép kín:

1. **Predict:** dự báo khối lượng pallet/giờ cho 24 giờ tới, kèm khoảng tin cậy.
2. **Detect:** so tải thực với kỳ vọng và nhìn trước 4 giờ để báo nút thắt sớm.
3. **Simulate:** mô phỏng hàng đợi theo zone, what-if và Monte Carlo.
4. **Optimize:** tìm cấu hình nguồn lực rẻ nhất mà vẫn đủ năng lực.

## Chạy thử

Không cần cài đặt. Mở file `index.html` bằng trình duyệt, hoặc chạy server cục bộ:

```bash
python -m http.server 8000
# mở http://localhost:8000
```

## Cấu trúc

```
index.html   # toàn bộ demo (HTML + CSS + JS, không phụ thuộc thư viện ngoài)
README.md
```

## Phương pháp

| Bước | Phương pháp |
|---|---|
| Dự báo | `ŷ(h) = s(h)·r`: hồ sơ theo mùa 7 ngày × hệ số level 3 ngày; khoảng tin cậy ±1.28σ; kiểm định hold-out so với baseline "hôm qua = hôm nay" |
| Phát hiện | Lệch tải thực so với kỳ vọng > 15 điểm phần trăm; mô phỏng trước 4 giờ |
| Mô phỏng | Hàng đợi theo zone: `b(t) = max(0, b(t−1) + λ(t)·s_z − μ_z(x))`; Monte Carlo 200 lần |
| Tối ưu | ILP: `min Σ c_k·x_k` với `μ_z(x) ≥ đỉnh P90 · s_z / 0.9`; duyệt toàn bộ 6.864 tổ hợp (nghiệm tối ưu chính xác) |

## Thay bằng dữ liệu thật

Trong `index.html`, sửa các mục sau:

| Cần thay | Tìm trong code |
|---|---|
| Lịch sử sản lượng (14 ngày × 24 giờ, pallet/h) | dòng `const D=[]...` |
| Zone và công thức năng lực | mảng `Z` |
| Nguồn lực hiện có | `R0` |
| Chi phí mỗi đơn vị nguồn lực | `CO` |

## Giới hạn

- Dữ liệu và thông số là giả định, cần kiểm chứng bằng dữ liệu thật.
- Xe nâng, AMR, cửa dock dùng chung nhiều zone nhưng mô hình hiện tính riêng từng zone.
- Bước thời gian theo giờ nên chưa bắt được dao động ngắn hạn.
- Dự báo mới kiểm định trên một ngày hold-out; cần backtest nhiều ngày.

## Demo trực tuyến

(Điền link GitHub Pages sau khi bật: `https://<username>.github.io/d2-logistics-control-room/`)
