# Slide LSTM (Slidev)

```bash
npm install          # lần đầu
npm run dev          # trình chiếu: http://localhost:3030 (presenter mode: /presenter, có lời thoại)
npm run notes        # xuất lời thoại trong slides.md ra ../thuyet-trinh.md
npm run export       # xuất PDF (cần: npx playwright install chromium)
```

- `slides.md`: nội dung 32 slide; lời thoại nằm trong comment `<!-- -->` cuối mỗi slide. Nguyên tắc viết (nói bằng lời, không đọc ký hiệu, viết đầy đủ từ viết tắt lần đầu): xem `../kich-ban-lstm.md`
- `components/`: LstmCell (sơ đồ ô LSTM), LstmSimulator (slide 15), GradientDecay (slide 5), GateHeatmap (slide 16), SignalReach (tín hiệu lỗi đo trên mạng NumPy thật, slide 20), SeedStrip (kết quả từng lần chạy, slide 23), StockReach (ảnh hưởng theo ngày / xóa bớt ngày cũ / số ngày đưa vào, slide 24), CompareChart (slide 25)
- `data/lstm_vs_rnn.json` (thí nghiệm e-commerce và cổ phiếu, slide 22–24): kết quả thật sinh bởi `../experiment/lstm_ecommerce.ipynb` và `../experiment/lstm_stock.ipynb` (khoảng 30 + 40 phút CPU), sau đó chạy `python ../experiment/export_slide_data.py` để gom vào file này
- `data/numpy_lstm.json` (slide 20): sinh bởi `experiment/lstm_numpy.ipynb` (LSTM và RNN tự cài đặt chỉ bằng NumPy, khoảng 4 phút CPU)
- `data/compare.json` (bài toán cộng, slide 25): sinh bởi `experiment/lstm_adding.ipynb` (seed 0; notebook có thêm kiểm tra seed 1–3, ghi vào `experiment/results/lstm_adding.json`)

- `global-top.vue`: footer (phần đang trình bày, người nói, số trang, thanh tiến độ). Slide đầu mỗi phần khai báo `part: 1..4` trong frontmatter; `style.css`: giao diện chung (tiêu đề, bảng, ô ghi chú, thẻ)

**Điều khiển bằng chuột** (`global-bottom.vue`): chuột trái = bước/slide tiếp theo, chuột phải = quay lại. Bấm vào thanh trượt, nút, checkbox trong các demo (slide 5, 15, 20, 23, 24, 25) sẽ không chuyển slide. Bàn phím (→ ←, Space) vẫn dùng bình thường.
