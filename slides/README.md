# Slide LSTM (Slidev)

```bash
npm install          # lần đầu
npm run dev          # trình chiếu: http://localhost:3030 (presenter mode: /presenter, có lời thoại)
npm run notes        # xuất lời thoại trong slides.md ra ../thuyet-trinh.md
npm run export       # xuất PDF (cần: npx playwright install chromium)
```

- `slides.md`: nội dung 34 slide; lời thoại nằm trong comment `<!-- -->` cuối mỗi slide. Nguyên tắc viết (nói bằng lời, không đọc ký hiệu, viết đầy đủ từ viết tắt lần đầu): xem `../kich-ban-lstm.md`
- `components/`: LstmCell (sơ đồ ô LSTM), LstmSimulator (slide 16), GradientDecay (slide 5), GateHeatmap (slide 17), SignalReach (tín hiệu lỗi đo trên mạng NumPy thật, slide 20), SeedStrip (kết quả từng lần chạy, slide 23), StockReach (ảnh hưởng theo ngày / xóa bớt ngày cũ / số ngày đưa vào, slide 24), CompareChart (slide 25), Quiz + QuizSet (trắc nghiệm, mỗi câu một slide: 30–33, `<QuizSet :n="1" />`)
- `data/lstm_vs_rnn.json` (thí nghiệm e-commerce và cổ phiếu, slide 22–24): kết quả thật sinh bởi `../experiment/lstm_ecommerce.ipynb` và `../experiment/lstm_stock.ipynb` (khoảng 30 + 40 phút CPU), sau đó chạy `python ../experiment/export_slide_data.py` để gom vào file này
- `data/numpy_lstm.json` (slide 20): sinh bởi `experiment/lstm_numpy.ipynb` (LSTM và RNN tự cài đặt chỉ bằng NumPy, khoảng 4 phút CPU)
- `data/compare.json` (bài toán cộng, slide 25): sinh bởi `experiment/lstm_adding.ipynb` (seed 0; notebook có thêm kiểm tra seed 1–3, ghi vào `experiment/results/lstm_adding.json`)

**Điều khiển bằng chuột** (`global-bottom.vue`): chuột trái = bước/slide tiếp theo, chuột phải = quay lại. Bấm vào thanh trượt, nút, checkbox trong các demo (slide 5, 16, 20, 23, 24, 25, 30–33) sẽ không chuyển slide. Bàn phím (→ ←, Space) vẫn dùng bình thường.
