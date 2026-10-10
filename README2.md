# D2 · Logistics Co-pilot tự hiệu chỉnh

Demo cho đề D2 (Simulate & Forecast Logistics, Recommend Actions) tại DENSO Factory Hacks 2026.

Toàn bộ dữ liệu trong demo là **mô phỏng**: nhà máy giả lập và lịch sử nhu cầu tổng hợp. Các hệ số năng lực, tỷ lệ dòng hàng và chi phí là giả định để minh họa phương pháp, chưa phải số liệu thực của nhà máy.

## Năm trụ

| Trụ | Phương pháp | Nơi xem trong demo |
|---|---|---|
| 1. Digital twin tự đồng bộ | Bộ lọc hạt (400 hạt), trạng thái ẩn: backlog và hiệu suất từng zone, quan sát: sản lượng | Tab Digital twin |
| 2. Dự báo có bảo đảm | Conformal thích nghi (ACI): α<sub>t+1</sub> = α<sub>t</sub> + γ(α − err<sub>t</sub>) | Tab Dự báo |
| 3. Điều khiển cuộn | Mỗi 15 phút chọn cấu hình rẻ nhất có CVaR₉₀ backlog ≤ 20 pallet trên 30 kịch bản | Tab Vận hành, Tab Điều khiển cuộn |
| 4. Học đối sách | Thompson sampling (Beta–Bernoulli) theo bối cảnh zone × ca, prior là luật giả định | Tab Học đối sách |
| 5. Giải thích nguyên nhân | Giá trị Shapley trên 3 nguyên nhân (8 lần mô phỏng) | Tab Giải thích |

## Chạy thử

Không cần cài đặt. Mở `index.html` bằng trình duyệt, hoặc chạy server cục bộ:

```bash
python -m http.server 8000
# mở http://localhost:8000
```

## Kết quả trên mô phỏng (trung bình 4 bộ nhiễu: 42, 7, 11, 23)

| Chỉ số | Không co-pilot | Co-pilot |
|---|---|---|
| Backlog đỉnh (pallet) | 510 | 62 |
| Số bước zone vượt 20 pallet | 148 | 19 |
| Pallet-giờ tồn đọng | 3.358 | 268 |
| Chi phí nguồn lực/ngày (điểm) | 1.044 | 1.065 |
| Độ phủ khoảng dự báo (mục tiêu 80%) | 64,7% (cố định) | 77,7% (ACI) |
| MAPE dự báo nhu cầu | 12,9% (cùng giờ hôm qua) | 6,8% |

Bộ lọc hạt được chọn tham số trên hai bộ nhiễu (42, 7) và kiểm chứng trên hai bộ còn lại (11, 23).

## Giới hạn

- Dữ liệu là mô phỏng; cần kiểm chứng bằng dữ liệu thật.
- Bảo đảm conformal là về tỷ lệ vi phạm trung bình dài hạn.
- CVaR tính trên tập kịch bản rời rạc; trung bình khoảng 5 bước/ngày không có cấu hình khả thi và dùng cấu hình tối đa.
- Hiệu suất η chỉ ước lượng được khi có hàng đợi.
- Học đối sách dùng phản hồi giả lập; chưa chứng minh hiệu quả thật của từng đối sách.
