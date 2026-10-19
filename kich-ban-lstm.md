# Kịch bản thuyết trình LSTM (45 phút, 4 người)

**Mạch chung:** Cả lớp đã học CNN và RNN. Bài đi theo các câu hỏi: "RNN hay quên vì sao? → ai đã sửa và sửa bằng cách nào → bên trong ô LSTM có gì → vì sao nó nhớ lâu → thực tế tốt hơn RNN bao nhiêu → dùng LSTM thế nào". LSTM xuất hiện như lời giải cho một vấn đề cụ thể, không phải một định nghĩa đọc lên cho có. Không trình bày GRU.

**Nguyên tắc trình bày** (áp dụng cho cả slide lẫn lời thoại):

- **Nói bằng lời, không đọc ký hiệu.** Không nói "h t", "c t trừ 1", "W h", "xích-ma". Nói "bộ nhớ", "đầu ra của bước trước", "trọng số", "cổng quên". Công thức chỉ để nhỏ trên slide cho ai muốn đối chiếu với tài liệu.
- **Dùng từ tiếng Việt**, kèm tên tiếng Anh trong ngoặc ở lần đầu: trạng thái ô nhớ (*cell state*), trạng thái ẩn (*hidden state*), cổng quên / cổng ghi / cổng đọc (*forget / input / output gate*), nội dung mới (*candidate*), tín hiệu lỗi (*gradient*). Khi nói, giới thiệu thuật ngữ bằng "hay còn gọi là …" hoặc "tên tiếng Anh là …", không dùng "tên chuyên môn là …".
- **Từ viết tắt** được viết đầy đủ ngay lần đầu xuất hiện trên slide và trong lời thoại (MLP, CNN, RNN, LSTM, BPTT, CEC, ResNet, MAE, RMSE, ROC-AUC, GPU…).
- **Mỗi demo phải nói rõ đang thử điều gì**, mỗi thanh trượt có chú thích ý nghĩa (ví dụ "số bước cần nhớ lại = khoảng cách từ chữ 'Pháp' tới chỗ trống").
- **Luôn quay lại câu ví dụ** "Tôi lớn lên ở Pháp … nói thành thạo tiếng ___" để giải thích từ nào được ghi, giữ, xóa, đọc.

Code mẫu: **PyTorch** cho phần dùng thực tế; cấu trúc bên trong ô LSTM viết lại **chỉ bằng NumPy** (slide 18, 19, notebook `experiment/lstm_numpy.ipynb`).

**Phân bổ thời gian:**

| Phần | Người | Thời lượng | Mốc | Số slide |
|---|---|---|---|---|
| 1. Vì sao cần LSTM? + lịch sử ra đời | Người 1 · Nguyễn Phú Đại | ~10.5' | 0:00 → 10:30 | 8 |
| 2. Bên trong một ô LSTM | Người 2 · Trần Vũ Tiến Minh | ~11' | 10:30 → 21:30 | 8 |
| 3. Vì sao LSTM nhớ lâu + code NumPy + thí nghiệm so sánh với RNN | Người 3 · Trần Đình Hiếu | ~16.5' | 21:30 → 38:00 | 9 |
| 4. Mẹo cài đặt, ứng dụng, khi nào chọn LSTM, quy trình thực tế, tổng kết | Người 4 · Phạm Thanh An | ~10.5' | 38:00 → 48:30 | 6 |
| Hỏi đáp | Cả nhóm | ~2' | 48:30 → 50:30 | 1 |

Đã bỏ 4 slide trắc nghiệm cuối và chuyển slide code NumPy (tính xuôi) sang phần Người 3. Phần 4 thêm 2 slide (khi nào nên chọn LSTM, quy trình xây dựng mô hình thực tế): tổng dự kiến ~50.5 phút, vượt 45 phút khoảng 5 phút, cần rút gọn lời thoại ở một số slide.

---

## Phần 1 — Người 1: "Vì sao cần LSTM?" (~10.5 phút)

| # | Slide | Nội dung chính | Animation / tương tác |
|---|---|---|---|
| 1 | Mở đầu | Câu ví dụ "Tôi lớn lên ở Pháp, … (30 từ) …, nên tôi nói thành thạo tiếng ___". Mô hình phải nhớ chữ "Pháp" qua 30 bước. | Hiện đoạn chen giữa, chữ "Pháp" mờ dần |
| 2 | LSTM nằm ở đâu? | MLP (mạng nhiều lớp), CNN (mạng tích chập), RNN (mạng hồi quy): ba họ song song, mỗi họ hợp với một kiểu dữ liệu. LSTM là một thành viên của họ RNN, chỉ khác cách cập nhật bộ nhớ bên trong mỗi bước. | Các khối hiện dần |
| 3 | Bảng so sánh | Dữ liệu, dùng lại trọng số, bộ nhớ, nhớ được bao xa, số trọng số (LSTM gấp 4), ứng dụng. | Kết luận hiện theo click |
| 4 | Ôn RNN | RNN đọc từng từ, giữ một "bản tóm tắt" (trạng thái ẩn). Sơ đồ trải theo thời gian với các từ "Tôi", "lớn", "lên", …, "tiếng". BPTT (lan truyền ngược theo thời gian). | Các ô hiện từng bước |
| 5 | Vấn đề: RNN "quên" khi học | Tín hiệu lỗi (gradient) đi ngược từ cuối câu về chữ "Pháp", mỗi bước bị nhân với một hệ số. Hệ số < 1 → biến mất, > 1 → bùng nổ. Ví dụ trò chơi truyền tin. | **Thanh trượt:** số bước cần nhớ lại (khoảng cách "Pháp" → chỗ trống) và hệ số mỗi bước; hiện "còn lại x%" |
| 6 | Lịch sử: ai phát hiện và sửa | 1991 Hochreiter chứng minh bằng toán. Ý tưởng: ô nhớ "giữ nguyên rồi cộng thêm" (hệ số luôn bằng 1), gọi là CEC (băng chuyền giữ nguyên lỗi). Vấn đề lẫn lộn → thêm cổng như cái van. 1997 công bố LSTM. | 3 khối hiện theo click |
| 7 | Các mốc phát triển | 1997 chưa có cổng quên · 2000 thêm cổng quên · 2005 LSTM hai chiều · 2009 chữ viết tay · 2014 dịch máy (seq2seq) · 2015–2016 Google · 2017 Transformer. Ý nghĩa tên gọi. | Timeline theo click |
| 8 | Chuyển ý | "Mở ô LSTM ra xem bên trong" → bàn giao cho Người 2 | — |

---

## Phần 2 — Người 2: "Bên trong một ô LSTM" (~11 phút) — phần trọng tâm

| # | Slide | Nội dung chính | Animation / tương tác |
|---|---|---|---|
| 9 | Hai loại bộ nhớ | Trạng thái ô nhớ (băng chuyền, bộ nhớ dài hạn, chính là CEC) và trạng thái ẩn (đầu ra mỗi bước, bộ nhớ ngắn hạn). So sánh với RNN chỉ có một bộ nhớ bị viết lại mỗi bước. | Sơ đồ có nhãn tiếng Việt |
| 10 | Cổng quên | Số từ 0 đến 1 cho mỗi ô nhớ: 0 xóa sạch, 1 giữ nguyên. Sigmoid như độ mở của van. Ví dụ đổi sang người khác → xóa "Pháp". Bản 1997 chưa có. | Chỉ phần cổng quên sáng lên |
| 11 | Cổng ghi | Nội dung mới (ghi gì) + cổng ghi (ghi bao nhiêu). Tại "Pháp" mở, tại "chơi bóng đá" đóng. | Cổng ghi và nội dung mới sáng lên |
| 12 | Cập nhật bộ nhớ | bộ nhớ mới = cổng quên × bộ nhớ cũ + cổng ghi × nội dung mới. Xóa bớt rồi cộng thêm, không viết lại như RNN. | Băng chuyền sáng lên |
| 13 | Cổng đọc | đầu ra = cổng đọc × tanh(bộ nhớ). Cất "Pháp" 30 bước mà không làm nhiễu; đến "tiếng" mới đọc ra. | Cổng đọc sáng lên |
| 14 | Tổng hợp: 4 việc | Quên · chuẩn bị ghi · cập nhật · đọc; công thức để nhỏ bên dưới mỗi dòng; 4 bộ trọng số → gấp 4 lần RNN. | Mỗi click làm nổi một việc trên sơ đồ |
| 15 | **★ Mô phỏng một ô LSTM** | Mô hình đồ chơi 1 lớp, 1 nơ-ron, trọng số đặt tay (nói rõ khi trình bày). Bộ nhớ chỉ là 1 con số; đầu vào có nghĩa: +2 quan trọng, 0 bình thường, −2 hết ngữ cảnh. Dòng chữ báo ô đang GHI / GIỮ / XÓA. | Thanh trượt đầu vào và bộ nhớ, nút "Chạy bước" (dùng đầu vào đang chọn, rồi đặt sẵn giá trị tiếp theo của chuỗi mẫu), tự chỉnh cổng bằng tay |
| 16 | Ví dụ trên câu mẫu | Cổng ghi mở ở "Pháp", cổng quên mở ở các từ giữa, cổng đọc mở ở "tiếng" (số minh họa). | Bảng màu theo từng từ |

---

## Phần 3 — Người 3: "Vì sao LSTM nhớ lâu, và thực tế ra sao?" (~16.5 phút)

| # | Slide | Nội dung chính | Animation / tương tác |
|---|---|---|---|
| 17 | Vì sao LSTM học được những gì ở xa? | RNN: tín hiệu đi qua bản tóm tắt, mỗi bước × trọng số × đạo hàm tanh (0.9⁵⁰ ≈ 0.005). LSTM: đi dọc băng chuyền, mỗi bước chỉ × cổng quên (0.99⁵⁰ ≈ 0.6). Vẫn còn "đường vòng" giống RNN, nhưng chỉ cần còn một đường thẳng. | Hai khối và ghi chú hiện theo click |
| 18 | **Viết thành code (NumPy)** | Ô LSTM = ghép 5 hàm sơ cấp (affine, sigmoid, tanh, nhân, cộng), không dùng PyTorch/Keras; `lstm_cell` và vòng lặp theo thời gian; gộp lại thành một biểu thức. Bản đầy đủ: `experiment/lstm_numpy.ipynb`. | Dòng code sáng lần lượt theo click |
| 19 | Trong code: con đường thẳng ở đâu? | Phần đi ngược (lan truyền ngược) viết bằng NumPy: 5 hàm đạo hàm sơ cấp; trong `lstm_cell_back`, dòng `dc_prev = dfc * f` chỉ nhân với cổng quên, còn RNN mỗi bước luôn qua đạo hàm tanh và trọng số. Đã kiểm tra khớp sai phân hữu hạn và PyTorch. | Dòng 5 sáng lên, RNN và kiểm tra hiện theo click |
| 20 | **★ Đo trên mạng thật** | Mạng NumPy thật: 2 số đầu vào → 1 lớp RNN/LSTM 64 nơ-ron ẩn → 1 nơ-ron đầu ra (LSTM 17 217 trọng số, RNN 4 353), học bài toán cộng 100 bước. Đo phần trăm tín hiệu lỗi về tới từng bước: sau khi học, LSTM giữ ~23% tới tận 99 bước, RNN lùi 50 bước còn ~10⁻⁹ %. (`experiment/lstm_numpy.ipynb`) | **Nút** lúc mới khởi tạo / sau khi học, **thanh trượt** số bước đi ngược (`SignalReach`) |
| 21 | Ý tưởng được dùng lại trong ResNet | Lớp chưa học ResNet → giới thiệu ngắn: mạng nhận dạng ảnh 2015, cũng gặp vấn đề tín hiệu lỗi yếu dần qua nhiều lớp. Cách giải: đầu ra = đầu vào + phần thay đổi. LSTM: lối tắt theo thời gian. LSTM có ý tưởng này trước 18 năm. | Kết luận theo click |
| 22 | Thí nghiệm 1: đơn hàng, chuỗi 8 bước | Dữ liệu Pakistan (`experiment/lstm_ecommerce.ipynb`). LSTM hơn ở tỉ lệ đoán đúng và ROC-AUC (5/5 lần) nhưng rất ít, kém hơn ở loại đơn bị trả lại. Lý do: không có gì ở xa. | Kết luận theo click |
| 23 | Thí nghiệm 2: cổ phiếu, chuỗi 60 bước | `experiment/lstm_stock.ipynb`. LSTM hơn ở mọi chỉ số, cả 5/5 lần; lần tệ nhất của LSTM vẫn tốt hơn lần tốt nhất của RNN. | **Chọn chỉ số** để xem từng lần chạy (`SeedStrip`) |
| 24 | Vì sao LSTM hơn ở cổ phiếu? | Ảnh hưởng của ngày đầu tiên: RNN 0.014%, LSTM 1.1%. Xóa bớt ngày cũ; cho xem 20 / 60 / 120 ngày (RNN 120 ngày y hệt 60 ngày; 20 ngày tốt nhất cho cả hai). | **3 tab** (`StockReach`) |
| 25 | Thí nghiệm 3: bài toán cộng, chuỗi 100 bước | Phải nhớ số được đánh dấu qua 50 bước. RNN đứng ở mức đoán mò, LSTM học được. Tổng hợp: chuỗi càng dài, LSTM càng hơn rõ. | **Chuỗi 10 số / 100 số** |

> ⚠️ Slide 22–25: **huấn luyện trước bằng PyTorch và lưu kết quả ra JSON** (`slides/data/lstm_vs_rnn.json`, `slides/data/compare.json`), không huấn luyện trực tiếp lúc thuyết trình.

---

## Phần 4 — Người 4: "LSTM trong thực tế" (~10.5 phút) + Hỏi đáp

| # | Slide | Nội dung chính | Animation / tương tác |
|---|---|---|---|
| 26 | Hai mẹo khi cài đặt | Cho cổng quên hơi mở từ đầu (độ lệch = 1); vẫn cần giới hạn độ lớn tín hiệu lỗi (gradient clipping) vì "đường vòng" có thể bùng nổ. | Mục 2 hiện theo click |
| 27 | Xếp chồng và đọc hai chiều | Nhiều lớp LSTM; hai LSTM đọc xuôi và ngược; code PyTorch (`num_layers`, `bidirectional`, `dropout`), đầu ra hai chiều dài gấp đôi. | — |
| 28 | Ứng dụng và giới hạn | Cả chuỗi → một nhãn, mỗi bước một nhãn, chuỗi → chuỗi, chuỗi thời gian, sinh dữ liệu. Giới hạn: đọc lần lượt nên không chạy song song trên GPU → Attention, Transformer. | Mũi tên sang Transformer theo click |
| 29 | Khi nào nên chọn LSTM? | Nên dùng: dữ liệu có thứ tự, quan hệ dài hạn quan trọng, dữ liệu không quá lớn, xử lý tuần tự theo thời gian thực. Không nhất thiết: chuỗi ngắn (thí nghiệm 1), dữ liệu không có thứ tự, chuỗi rất dài cần huấn luyện song song. Không chọn LSTM chỉ vì có yếu tố thời gian: bắt đầu từ baseline rồi so sánh. | Kết luận hiện theo click |
| 30 | Quy trình xây dựng mô hình LSTM thực tế | Pipeline 8 bước: dữ liệu thô → làm sạch → chia train/validation/test → chuẩn hóa → cửa sổ thời gian → huấn luyện → đánh giá → triển khai. Lưu ý: chia theo thời gian, chuẩn hóa chỉ học từ train, chọn cửa sổ bằng validation, so với baseline. | 4 lưu ý hiện theo click, bước tương ứng trên pipeline sáng lên |
| 31 | Tổng kết | 3 ý chính: bộ nhớ dài hạn, 3 cổng, phép cộng | — |
| 32 | Hỏi đáp | ~2 phút, có sẵn gợi ý trả lời trong lời thoại | — |

---

## Ghi chú cho cả nhóm

- **Bàn giao giữa các người nói:** mỗi phần kết thúc bằng một câu hỏi mà phần sau trả lời (Người 1: "bên trong ô LSTM có gì?", Người 2: "vì sao phép cộng giúp LSTM học được những gì ở xa?", Người 3: "dùng LSTM thế nào trong thực tế?").
- **Sợi chỉ xuyên suốt:** ý tưởng "giữ nguyên rồi cộng thêm" của Hochreiter (slide 6) → băng chuyền bộ nhớ (slide 9) → bộ nhớ = xóa bớt + cộng thêm (slide 12) → con đường thẳng cho tín hiệu lỗi (slide 17, trong code ở slide 18–19, đo thật ở slide 20) → lối tắt của ResNet (slide 21). Mỗi người nên nhắc lại ý này một lần.
- **Lời thoại cho từng slide:** đặt trong speaker notes của Slidev (`<!-- ... -->`), xuất ra `thuyet-trinh.md` bằng `npm run notes`. Khoảng 130 từ/phút; nói như đang giải thích cho bạn cùng lớp, không đọc công thức.
- **Các slide khó làm nhất:** slide 5 (thanh trượt tín hiệu lỗi theo công thức), slide 18 và 19 (code NumPy), slide 15 (mô phỏng ô LSTM), slide 20 (đo trên mạng thật), slide 22–25 (thí nghiệm).

## Nguồn tham khảo cho phần lịch sử

- S. Hochreiter (1991). *Untersuchungen zu dynamischen neuronalen Netzen*. Diploma thesis, TU München.
- Y. Bengio, P. Simard, P. Frasconi (1994). *Learning long-term dependencies with gradient descent is difficult*. IEEE TNN.
- S. Hochreiter, J. Schmidhuber (1997). *Long Short-Term Memory*. Neural Computation 9(8).
- F. Gers, J. Schmidhuber, F. Cummins (2000). *Learning to Forget: Continual Prediction with LSTM*. Neural Computation 12(10).
- A. Graves, J. Schmidhuber (2005). *Framewise phoneme classification with bidirectional LSTM*. Neural Networks.
