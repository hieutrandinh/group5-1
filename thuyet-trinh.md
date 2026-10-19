# Lời thoại thuyết trình LSTM (45 phút, 4 người)

> File được sinh tự động từ speaker notes trong `slides/slides.md` (lệnh `npm run notes` trong thư mục `slides`). Muốn sửa lời thoại thì sửa trong `slides.md` rồi chạy lại lệnh, để slide và file này luôn khớp nhau.
>
> Ký hiệu: **[click]** = bấm sang bước animation tiếp theo · **(Demo)** = thao tác trên slide tương tác.
>
> Phân công: Người 1 = Nguyễn Phú Đại · Người 2 = Trần Vũ Tiến Minh · Người 3 = Trần Đình Hiếu · Người 4 = Phạm Thanh An.

---

## Phần 1 — Vì sao cần LSTM? + lịch sử ra đời (Người 1)

### Slide 1 · LSTM — Long Short-Term Memory  
*Người 1 · ~1 phút*

Xin chào thầy và các bạn. Hôm nay nhóm mình trình bày về LSTM, viết tắt của Long Short-Term Memory. Dịch sát nghĩa thì hơi khó nghe, nhưng ý của cái tên là: một mạng nơ-ron có trí nhớ ngắn hạn, nhưng giữ được lâu.

Mở đầu bằng một trò chơi nhỏ. Các bạn đọc câu này: "Tôi lớn lên ở Pháp, nên tôi nói thành thạo tiếng gì?" Ai cũng trả lời ngay: tiếng Pháp.

[click] Bây giờ mình chèn vào giữa câu một đoạn dài không liên quan: học tiểu học, chuyển nhà, chơi bóng đá… Các bạn vẫn trả lời được, vì các bạn nhớ chữ "Pháp" ở đầu câu.

[click] Nhưng với một mạng nơ-ron hồi quy cơ bản, chữ "Pháp" mờ dần sau mỗi từ, giống như trên màn hình. Đến cuối câu thì mạng gần như đã quên nó. Bài hôm nay trả lời ba câu hỏi: vì sao mạng hồi quy cơ bản hay quên, LSTM nhớ lâu bằng cách nào, và trong thực tế LSTM tốt hơn bao nhiêu.

### Slide 2 · LSTM nằm ở đâu trong các loại mạng nơ-ron?  
*Người 1 · ~1.5 phút*

Trước khi vào LSTM, mình đặt nó vào bức tranh chung các loại mạng nơ-ron mà lớp đã học.

[click] Đầu tiên là MLP, viết tắt của Multi-Layer Perceptron, tức mạng nơ-ron nhiều lớp thông thường. Đầu vào là một vector có kích thước cố định, ví dụ một dòng dữ liệu trong bảng.

[click] Thứ hai là CNN, mạng nơ-ron tích chập, các bạn đã làm ở bài tập trước. CNN dành cho ảnh: cùng một bộ lọc được quét qua mọi vị trí trên ảnh.

[click] Thứ ba là họ RNN, mạng nơ-ron hồi quy, dành cho dữ liệu dạng chuỗi như một câu văn hay chuỗi giá cổ phiếu. RNN đọc lần lượt từng phần tử và mang theo một "bộ nhớ" từ bước này sang bước sau. Ba họ này song song với nhau, mỗi họ hợp với một kiểu dữ liệu, không họ nào sinh ra từ họ nào.

[click] LSTM không phải một họ mới. Nó là một thành viên trong họ RNN: vẫn đọc chuỗi từng bước, vẫn dùng lại cùng một bộ trọng số ở mọi bước, vẫn huấn luyện giống RNN. Điểm khác duy nhất nằm bên trong mỗi bước: LSTM có thêm một bộ nhớ dài hạn và ba "cổng" để quyết định nhớ gì, quên gì.

### Slide 3 · So sánh MLP · CNN · RNN · LSTM  
*Người 1 · ~1 phút*

Bảng này tóm tắt bốn loại mạng. Mình chỉ lưu ý ba dòng.

Dòng bộ nhớ: MLP và CNN không có bộ nhớ, mỗi đầu vào được xử lý riêng. RNN có một bộ nhớ, nhưng bộ nhớ này bị viết lại ở mỗi bước. LSTM có thêm một bộ nhớ dài hạn, và đây là nhân vật chính của cả bài.

Dòng nhớ được bao xa: trong thực tế, RNN cơ bản chỉ học được mối liên hệ trong khoảng 10 bước. Bài báo gốc năm 1997 cho thấy LSTM học được mối liên hệ cách nhau hơn 1000 bước.

Dòng số trọng số: với cùng kích thước, LSTM có gấp 4 lần số trọng số của RNN, vì nó có ba cổng cộng một phần tạo nội dung mới, mỗi phần cần một bộ trọng số riêng.

[click] Tóm lại: LSTM giữ nguyên cách làm việc của RNN, và sửa điểm yếu lớn nhất của RNN là hay quên những gì ở xa.

### Slide 4 · Ôn lại RNN: đọc từng từ, cập nhật một "bản tóm tắt"  
*Người 1 · ~1.5 phút*

Ôn lại nhanh RNN mà các bạn đã làm. Ý tưởng rất đơn giản: RNN đọc câu từng từ một, và luôn giữ một "bản tóm tắt" những gì đã đọc. Bản tóm tắt này hay còn gọi là trạng thái ẩn, tên tiếng Anh là hidden state.

Ở mỗi bước, bản tóm tắt mới được tính từ hai thứ: bản tóm tắt cũ, và từ đang đọc. Cả hai được nhân với trọng số, cộng lại, rồi đưa qua hàm tanh để ép về khoảng từ trừ 1 đến 1. Công thức trên slide chỉ nói đúng điều đó.

[click] Đọc chữ "Tôi", ra bản tóm tắt thứ nhất. [click] Đọc chữ "lớn", bản tóm tắt được viết lại. [click] Cứ thế tiếp tục, [click] cho tới chữ "tiếng", lúc đó mạng phải dựa vào bản tóm tắt để đoán chữ tiếp theo là "Pháp".

Các bạn để ý mũi tên màu cam: thông tin từ đầu câu muốn tới cuối câu thì phải đi qua tất cả các ô, và ở mỗi ô nó lại bị trộn với từ mới rồi viết lại.

[click] Một điều nữa: mọi ô dùng chung một bộ trọng số. Khi huấn luyện, sai số ở cuối câu được truyền ngược lại từng bước về đầu câu để sửa trọng số. Cách làm này gọi là BPTT, lan truyền ngược theo thời gian. Chính quãng đường đi ngược dài này gây ra vấn đề ở slide sau.

### Slide 5 · Vấn đề: RNN "quên" khi học  
*Người 1 · ~2 phút*

Đây là vấn đề chính của RNN. Khi huấn luyện, nếu mạng đoán sai ở chỗ trống, ta cần một tín hiệu lỗi đi ngược lại để sửa trọng số. Tín hiệu này có tên là gradient, các bạn hiểu đơn giản là nó cho biết cần chỉnh trọng số theo hướng nào và chỉnh bao nhiêu.

Muốn mạng học được rằng chữ "Pháp" quyết định chữ cuối câu, tín hiệu lỗi phải đi ngược về tận chữ "Pháp". Vấn đề là mỗi lần lùi lại một bước, tín hiệu bị nhân với một hệ số, do trọng số và hàm tanh quyết định. Đi ngược 50 bước thì bị nhân 50 lần.

Nếu hệ số nhỏ hơn 1, tín hiệu teo dần về 0. Ta gọi là gradient biến mất. Nếu hệ số lớn hơn 1, tín hiệu phình to, gọi là gradient bùng nổ. Giống như trò chơi truyền tin qua 50 người: chỉ cần mỗi người nói nhỏ đi một chút, đến cuối hàng thì không còn nghe thấy gì.

**(Demo)** Mình thử trên biểu đồ. Thanh trượt trên cùng là số bước cần nhớ lại, tức khoảng cách từ chữ "Pháp" tới chỗ trống, đang là 50. Thanh thứ hai là hệ số mỗi bước, đang là 0.9, tức mỗi bước tín hiệu chỉ giữ lại 90%. Nghe thì có vẻ nhiều, nhưng sau 50 bước, tín hiệu chỉ còn khoảng nửa phần trăm. Kéo số bước lên 100 thì gần như bằng 0. Còn nếu kéo hệ số lên 1.1, đường cong vọt lên trên: đó là bùng nổ.

Ta không thể giữ hệ số đúng bằng 1, vì trọng số thay đổi liên tục trong quá trình học. Đó là lý do RNN không nối được chữ "Pháp" với chữ "tiếng".

Lưu ý: biểu đồ này vẽ thẳng từ công thức, hệ số mỗi bước do mình tự chọn, chưa phải một mạng nơ-ron cụ thể. Ở slide 20, bạn Hiếu sẽ đo trên một mạng thật đã huấn luyện, có số lớp và số nơ-ron rõ ràng.

### Slide 6 · Ai phát hiện ra vấn đề và sửa nó?  
*Người 1 · ~2 phút*

Vậy ai đã nhìn ra vấn đề này và sửa nó?

[click] Năm 1991, Sepp Hochreiter, khi đó là sinh viên Đại học Kỹ thuật München ở Đức, làm luận văn tốt nghiệp với thầy Jürgen Schmidhuber. Trong luận văn này, ông là người đầu tiên chứng minh bằng toán vì sao RNN hay quên, chính là phép nhân lặp đi lặp lại mà mình vừa demo. Ba năm sau, nhóm của Bengio cũng độc lập tìm ra kết quả tương tự.

[click] Cách sửa của ông rất tự nhiên. Tín hiệu yếu dần vì bị nhân với một số khác 1 nhiều lần. Vậy thì làm một ô nhớ mà ở mỗi bước, nó chỉ giữ nguyên giá trị cũ rồi cộng thêm thông tin mới. Hệ số khi đó luôn đúng bằng 1, nên tín hiệu đi qua bao nhiêu bước cũng không bị yếu đi. Ông đặt tên cho ô nhớ này là CEC, viết tắt của Constant Error Carousel, tạm hiểu là "băng chuyền giữ nguyên lỗi".

[click] Nhưng làm vậy lại sinh ra vấn đề mới: từ nào cũng được cộng vào ô nhớ, kể cả những từ không quan trọng, nên ô nhớ bị lẫn lộn. Giải pháp là thêm các cổng. Các bạn hình dung cổng như một cái van, mở từ 0 đến 1, quyết định lúc nào được ghi vào ô nhớ và lúc nào được đọc ra. Năm 1997, Hochreiter và Schmidhuber công bố bài báo "Long Short-Term Memory", và bài báo này trở thành một trong những bài được trích dẫn nhiều nhất của ngành học sâu.

### Slide 7 · Các mốc phát triển của LSTM  
*Người 1 · ~1 phút*

Điểm qua nhanh hành trình của LSTM.

[click] Bản đầu tiên năm 1997 có cổng ghi và cổng đọc, nhưng chưa có cổng quên. Nghĩa là ô nhớ chỉ được cộng thêm, không có cách nào xóa bớt.

[click] Năm 2000, Felix Gers và cộng sự thêm cổng quên, để mạng tự học khi nào cần xóa bớt nội dung cũ. LSTM mà các bạn dùng trong PyTorch ngày nay chính là phiên bản có cổng quên này.

[click] Năm 2005, LSTM được huấn luyện theo cách đầy đủ hơn, và có thêm LSTM hai chiều, đọc câu cả xuôi lẫn ngược. [click] Năm 2009, LSTM thắng các cuộc thi nhận dạng chữ viết tay. [click] Năm 2014, người ta dùng hai LSTM để dịch máy: một LSTM đọc câu tiếng Anh, một LSTM viết ra câu tiếng Pháp. [click] Khoảng 2015, 2016, nhận dạng giọng nói của Google và Google Dịch đều chuyển sang LSTM. [click] Đến 2017, Transformer, nền tảng của ChatGPT, ra đời và dần thay thế LSTM trong xử lý ngôn ngữ.

[click] Còn cái tên: trí nhớ ngắn hạn là những gì mạng đang giữ trong lúc đọc câu. LSTM là trí nhớ ngắn hạn nhưng giữ được lâu.

### Slide 8 · (không tiêu đề)  
*Người 1 · ~0.5 phút*

Tóm lại phần của mình: RNN hay quên vì tín hiệu lỗi bị nhân đi nhân lại qua rất nhiều bước. Hochreiter sửa bằng một ô nhớ chỉ giữ nguyên rồi cộng thêm, cùng với các cổng để điều khiển nó. Tiếp theo, mời bạn Minh mở ô LSTM ra xem bên trong có gì.

---

## Phần 2 — Bên trong một ô LSTM (Người 2)

### Slide 9 · Hai loại bộ nhớ: trạng thái ẩn và trạng thái ô nhớ  
*Người 2 · ~1 phút*

Cảm ơn Đại. Mình bắt đầu bằng bức tranh tổng thể.

Bên trái là RNN: chỉ có một bộ nhớ, chính là bản tóm tắt. Ở mỗi bước, bản tóm tắt này bị trộn với từ mới, đi qua hàm tanh, và được viết lại hoàn toàn.

Bên phải là LSTM. Các bạn để ý đường màu xanh nằm ngang phía trên. Đó là bộ nhớ dài hạn, hay còn gọi là trạng thái ô nhớ, tên tiếng Anh là cell state.

[click] Đường này chạy thẳng qua ô giống như một băng chuyền. Trên đường đi chỉ có hai thao tác: xóa bớt một phần, và cộng thêm thông tin mới. Không có gì viết lại toàn bộ. Đây chính là ô nhớ giữ nguyên của Hochreiter mà bạn Đại vừa kể.

[click] Ngoài ra LSTM vẫn có trạng thái ẩn, là đầu ra ở mỗi bước, giống bản tóm tắt của RNN. Nó được đọc ra từ ô nhớ, và đóng vai trò bộ nhớ ngắn hạn.

Ba cổng tiếp theo điều khiển ba việc trên băng chuyền: xóa, ghi, và đọc. Mình đi lần lượt từng cổng.

### Slide 10 · Cổng 1 · Cổng quên (*forget gate*): giữ lại bao nhiêu?  
*Người 2 · ~1.5 phút*

Cổng đầu tiên là cổng quên, tiếng Anh là forget gate, tô màu đỏ trên sơ đồ.

Cổng quên nhìn vào hai thứ: từ đang đọc, và đầu ra của bước trước. Từ đó nó quyết định giữ lại bao nhiêu phần bộ nhớ cũ. Kết quả là một con số từ 0 đến 1 cho mỗi ô nhớ. Bằng 0 nghĩa là xóa sạch, bằng 1 nghĩa là giữ nguyên, còn 0.7 nghĩa là giữ lại 70 phần trăm.

[click] Để có con số từ 0 đến 1, cổng dùng hàm sigmoid. Các bạn cứ hình dung nó như độ mở của một cái van: 0 là đóng hẳn, 1 là mở hết.

[click] Ví dụ, khi đoạn văn chuyển sang kể về một người khác, sinh ra ở Nhật chẳng hạn, thì thông tin "Pháp" không còn đúng nữa. Lúc đó mạng học cách đóng cổng quên về gần 0 để xóa nó đi.

[click] Như bạn Đại đã kể, bản năm 1997 chưa có cổng này. Khi đó bộ nhớ chỉ được cộng thêm mà không bao giờ bị xóa, giống một cái tủ chỉ cho thêm đồ vào mà không cho lấy ra.

### Slide 11 · Cổng 2 · Cổng ghi (*input gate*): ghi gì, ghi bao nhiêu?  
*Người 2 · ~1.5 phút*

Cổng thứ hai là cổng ghi, tiếng Anh là input gate, màu xanh lá. Phần này có hai thành phần làm việc cùng nhau.

Thành phần thứ nhất tạo ra nội dung mới, tức thông tin định ghi vào ô nhớ. Nó dùng hàm tanh, nên giá trị nằm trong khoảng từ trừ 1 đến 1, có thể làm ô nhớ tăng lên hoặc giảm xuống.

Thành phần thứ hai là cổng ghi, lại là một cái van từ 0 đến 1, quyết định ghi bao nhiêu phần nội dung đó.

[click] Phần thật sự được ghi vào ô nhớ là cổng ghi nhân với nội dung mới.

[click] Vì sao phải tách làm hai? Vì không phải từ nào cũng đáng nhớ. Tại chữ "Pháp", cổng ghi mở gần hết để ghi thông tin quan trọng này vào. [click] Còn tại những từ như "chơi bóng đá", "mê truyện tranh", cổng ghi gần như đóng, để không ghi đè lên chữ "Pháp" đã lưu. Đây chính là vấn đề lẫn lộn mà Hochreiter giải quyết bằng cổng.

### Slide 12 · Cập nhật bộ nhớ: xóa bớt rồi cộng thêm  
*Người 2 · ~1 phút*

Bây giờ ghép hai cổng lại. Đây là công thức quan trọng nhất của LSTM, và mình giải thích nó bằng một câu: bộ nhớ mới sẽ bằng tổng của cổng quên nhân bộ nhớ cũ và cổng ghi nhân nội dung mới. Nói cách khác: xóa bớt một phần cái cũ, rồi cộng thêm một phần cái mới.

[click] So với RNN: ở RNN, mỗi bước bản tóm tắt bị viết lại hoàn toàn, nhân với trọng số rồi đi qua tanh.

[click] Còn ở LSTM, bộ nhớ cũ không bị viết lại. Nó chỉ bị xóa bớt theo cổng quên, rồi được cộng thêm phần mới. Nếu cổng quên mở hết và cổng ghi đóng, bộ nhớ đi qua bước đó y nguyên.

[click] Lát nữa bạn Hiếu sẽ cho thấy chính phép cộng đơn giản này là lý do LSTM học được những mối liên hệ ở xa.

### Slide 13 · Cổng 3 · Cổng đọc (*output gate*): đọc ra bao nhiêu?  
*Người 2 · ~1 phút*

Cổng cuối cùng là cổng đọc, tiếng Anh là output gate, màu tím.

Những gì được ghi vào ô nhớ chỉ nằm bên trong LSTM; bên ngoài không nhìn thấy trực tiếp. Thứ bên ngoài nhận được ở mỗi bước, và dùng để đoán từ tiếp theo, là đầu ra. Cổng đọc quyết định lấy bao nhiêu từ bộ nhớ ra làm đầu ra. Giống như một cuốn sổ ghi chú: mình ghi nhiều thứ vào sổ, nhưng lúc nói chuyện chỉ đọc ra phần đang cần.

Cụ thể có hai bước: đưa bộ nhớ qua hàm tanh để ép về khoảng từ trừ 1 đến 1, rồi nhân với cổng đọc. Cổng đọc cũng là một cái van từ 0 đến 1: bằng 0 là không cho gì ra, bằng 1 là cho ra hết.

[click] Nhờ vậy, mạng có thể cất chữ "Pháp" trong ô nhớ suốt 30 bước mà không làm nhiễu đầu ra ở các bước giữa: cổng đọc đóng thì thông tin vẫn nằm yên trong ô nhớ.

[click] Đến chữ "tiếng", khi cần đoán tên ngôn ngữ, cổng đọc mới mở ra để lấy thông tin "Pháp" ra dùng.

### Slide 14 · Tổng hợp: một ô LSTM làm 4 việc  
*Người 2 · ~1 phút*

Đây là toàn bộ LSTM trên một slide. Một ô LSTM làm đúng bốn việc, mình bấm lần lượt để các bạn đối chiếu với sơ đồ.

[click] Việc một: quên. Cổng quên quyết định giữ lại bao nhiêu bộ nhớ cũ.

[click] Việc hai: chuẩn bị ghi. Tạo nội dung mới, và cổng ghi quyết định ghi bao nhiêu.

[click] Việc ba: cập nhật bộ nhớ. Giữ lại phần cũ sau khi đã xóa bớt, rồi cộng thêm phần mới vừa ghi vào.

[click] Việc bốn: đọc. Cổng đọc quyết định lấy ra bao nhiêu từ bộ nhớ để làm đầu ra.

Công thức nằm dưới mỗi dòng cho bạn nào muốn đối chiếu với tài liệu. Các bạn để ý ba cổng và phần nội dung mới đều có cùng một dạng, mỗi phần có một bộ trọng số riêng. Bốn bộ trọng số, nên LSTM có gấp 4 lần trọng số so với RNN cùng kích thước, đúng như bảng so sánh lúc đầu.

### Slide 15 · Thử tay: theo dõi bộ nhớ của một ô LSTM  
*Người 2 · ~3 phút*

Bây giờ mình chạy thử một ô LSTM thu nhỏ để các bạn nhìn thấy các cổng hoạt động. Nói rõ kích thước: đây là 1 lớp, chỉ có 1 nơ-ron, nên bộ nhớ chỉ là một con số. Nó làm đúng bốn việc ở slide trước: quên, chuẩn bị ghi, cập nhật, đọc.

Trọng số của ô này nhóm đặt sẵn, không phải học, sao cho đầu vào có ý nghĩa dễ hiểu, như ô chú thích bên phải: đầu vào bằng 2 nghĩa là "đây là thông tin quan trọng, hãy ghi nhớ", giống chữ "Pháp". Đầu vào bằng 0 là một từ bình thường. Đầu vào bằng trừ 2 nghĩa là "hết ngữ cảnh rồi, hãy xóa đi".

**Demo 1, kéo thanh "Đầu vào" (chưa bấm chạy, sơ đồ cập nhật ngay):**
- Đặt bằng 2: cổng ghi mở gần hết, bộ nhớ tăng lên gần 1. Dòng chữ phía dưới sơ đồ báo ô sẽ GHI.
- Đặt bằng trừ 2: cổng quên đóng về gần 0 nên bộ nhớ cũ bị XÓA, đồng thời cổng ghi cũng đóng nên không có gì mới được ghi vào. Kết quả là bộ nhớ mới về gần 0.

**Demo 2, bấm "Làm lại" rồi bấm "Chạy bước" liên tục.** Chuỗi mẫu là: một từ quan trọng, ba từ bình thường, một tín hiệu xóa, rồi một từ bình thường. Sau mỗi lần bấm, thanh đầu vào tự đặt sẵn giá trị tiếp theo của chuỗi mẫu; nếu muốn thử khác thì kéo thanh trước khi bấm, mô phỏng sẽ dùng đúng giá trị mình chọn.
- Bước 1, từ quan trọng: bộ nhớ lên khoảng 0.99, mạng ghi nhớ.
- Bước 2 đến 4, từ bình thường: cổng ghi gần như đóng nên không ghi gì mới, cổng quên mở khoảng 96% nên bộ nhớ chỉ giảm rất chậm: 0.96, 0.93, 0.89. Các bạn nhìn cột "bộ nhớ" trong bảng bên phải: thông tin được GIỮ.
- Bước 5, tín hiệu xóa: cổng quên đóng nên bộ nhớ cũ bị xóa, cổng ghi cũng đóng nên không ghi gì mới. Bộ nhớ rơi về gần 0: mạng đã XÓA thông tin "từ quan trọng" lúc trước.

**Demo 3, tích ô "Tự chỉnh các cổng bằng tay":** kéo cổng quên lên 1, cổng ghi về 0. Khi đó bộ nhớ mới bằng đúng bộ nhớ cũ, không mất gì cả. Đây chính là ô nhớ giữ nguyên của Hochreiter năm 1997.

Trong mạng thật, các trọng số này không do mình đặt mà được học từ dữ liệu.

### Slide 16 · Ví dụ: các cổng mở / đóng thế nào trên câu mẫu?  
*Người 2 · ~1 phút*

Quay lại câu ví dụ ở đầu bài. Giả sử LSTM đã học tốt, thì khi đọc câu này, ở mỗi từ ba cổng sẽ mở hay đóng như trong bảng. Màu càng đậm thì cổng càng mở. Các con số ở đây do nhóm tự đặt để minh họa ý tưởng, không phải đo từ một mô hình thật.

[click] Tại chữ "Pháp", cổng ghi mở để ghi thông tin vào bộ nhớ.

[click] Ở các từ ở giữa, cổng quên mở để giữ nguyên bộ nhớ, còn cổng ghi đóng để không ghi đè lên.

[click] Tại chữ "tiếng", cổng đọc mở để lấy chữ "Pháp" ra và đoán từ tiếp theo.

Đến đây là toàn bộ cách một ô LSTM hoạt động khi đọc câu. Câu hỏi còn lại là: vì sao phép cộng đơn giản này lại giúp LSTM học được những gì ở xa? Mời bạn Hiếu.

---

## Phần 3 — Vì sao LSTM nhớ lâu + thí nghiệm so sánh với RNN (Người 3)

### Slide 17 · Vì sao LSTM học được những gì ở xa?  
*Người 3 · ~2.5 phút*

Cảm ơn Minh. Phần của mình trả lời câu hỏi: vì sao LSTM học được những gì ở xa, còn RNN thì không?

Khi huấn luyện, mạng đoán sai ở chữ cuối, và tín hiệu lỗi phải đi ngược về tận chữ "Pháp" để mạng biết chữ "Pháp" quan trọng. RNN và LSTM cho tín hiệu này đi theo hai con đường khác nhau.

[click] Ở RNN, tín hiệu chỉ có một đường là đi ngược qua các bản tóm tắt. Mỗi lần lùi một bước, nó bị nhân với trọng số và với đạo hàm của hàm tanh. Hệ số này rất khó giữ đúng bằng 1. Giả sử nó là 0.9, thì sau 50 bước chỉ còn khoảng 0.005, tức nửa phần trăm. Đến chữ "Pháp" thì tín hiệu gần như không còn gì.

[click] Ở LSTM, nhớ lại công thức bộ nhớ của bạn Minh: bộ nhớ mới bằng cổng quên nhân bộ nhớ cũ, cộng phần mới. Vì là phép cộng, khi đi ngược dọc theo băng chuyền bộ nhớ, tín hiệu chỉ bị nhân với cổng quên, không có trọng số, không có tanh. Mà cổng quên là thứ mạng tự học được. Khi cần nhớ, mạng học cách giữ cổng quên gần 1. Với cổng quên bằng 0.99, sau 50 bước tín hiệu vẫn còn khoảng 60 phần trăm.

[click] Nói cho công bằng: LSTM vẫn còn những đường vòng giống RNN, đi qua đầu ra của bước trước, và các đường này vẫn có thể yếu dần. Nhưng tín hiệu là tổng của tất cả các đường. Chỉ cần còn một con đường thẳng qua bộ nhớ là tín hiệu vẫn về tới chữ "Pháp". Đây chính là ý tưởng "giữ nguyên rồi cộng thêm" của Hochreiter năm 1991.

Vậy con đường thẳng này nằm ở đâu? Để thấy tận mắt, mình viết LSTM thành code.

### Slide 18 · Viết thành code: ô LSTM = ghép các hàm sơ cấp  
*Người 3 · ~2 phút*

Bốn việc mà bạn Minh vừa trình bày, viết thành code thì trông thế nào? Nhóm tự viết lại LSTM chỉ bằng thư viện NumPy, không dùng PyTorch hay Keras, để thấy rằng bên trong không có gì bí ẩn: cả ô LSTM chỉ là ghép năm hàm sơ cấp lại với nhau, hay còn gọi là elementary functions.

Bên trái là năm hàm đó. [click] Hàm thứ nhất: nhân đầu vào với trọng số rồi cộng độ lệch. [click] Hai hàm tiếp theo là sigmoid, cho ra số từ 0 đến 1 giống độ mở của van, và tanh, nén số về khoảng từ trừ 1 đến 1. [click] Hàm thứ tư nhân từng phần tử với nhau. [click] Hàm cuối cùng cộng từng phần tử.

Bên phải là một ô LSTM. [click] Dòng đầu tiên: ghép đầu vào hiện tại với đầu ra của bước trước, rồi nhân với trọng số. Bốn bộ trọng số của bốn phần được đặt cạnh nhau trong một ma trận lớn, nên chỉ cần một phép nhân. [click] Bốn dòng tiếp theo cắt kết quả thành bốn đoạn: ba cổng dùng sigmoid, nội dung mới dùng tanh. [click] Dòng cập nhật bộ nhớ: nhân cổng quên với bộ nhớ cũ, nhân cổng ghi với nội dung mới, rồi cộng lại. Đúng câu "xóa bớt rồi cộng thêm". [click] Dòng đọc ra: cổng đọc nhân với tanh của bộ nhớ.

[click] Hàm cuối cùng chạy ô LSTM lần lượt qua từng bước của chuỗi, và ở mọi bước đều dùng lại đúng một bộ trọng số.

[click] Nếu viết gộp lại, cả ô LSTM chỉ là một biểu thức lồng các hàm sơ cấp vào nhau, như dòng ở dưới cùng.

Vì mỗi hàm sơ cấp đều có đạo hàm đơn giản, nên khi học, ta chỉ cần đi ngược lại đúng các dòng này. Đó là slide tiếp theo, và cũng là chỗ con đường thẳng xuất hiện.

### Slide 19 · Trong code: con đường thẳng nằm ở đâu?  
*Người 3 · ~2 phút*

Slide trước là phần tính xuôi của LSTM viết bằng NumPy. Khi học, máy phải đi ngược lại để tính tín hiệu lỗi. Vì ô LSTM chỉ là ghép năm hàm sơ cấp, nên phần đi ngược cũng chỉ là năm hàm đạo hàm, như bên trái. Mỗi hàm nhận tín hiệu lỗi ở đầu ra và trả về tín hiệu lỗi ở đầu vào. Ví dụ đạo hàm của phép cộng là chia đều tín hiệu cho cả hai vế, đạo hàm của phép nhân là nhân chéo.

Bên phải là phần đi ngược của một ô LSTM. Các bạn để ý nó đi ngược đúng các dòng của phần tính xuôi, từ dòng đọc ra, lên dòng cập nhật bộ nhớ, lên các cổng, cuối cùng là phép nhân trọng số.

[click] Dòng quan trọng nhất là dòng thứ năm. Tín hiệu lỗi gửi về bộ nhớ của bước trước bằng tín hiệu ở bộ nhớ hiện tại nhân với cổng quên. Chỉ vậy thôi: không có trọng số, không có tanh. Đây chính là con đường thẳng mà mình đã nói ở slide 17, giờ các bạn thấy nó nằm ở đâu trong code.

[click] So với RNN: mỗi bước lùi chỉ có hai dòng, nhưng tín hiệu về bước trước luôn phải đi qua đạo hàm của tanh rồi qua trọng số. Không có đường nào tránh được hai thứ đó.

[click] Để chắc code đúng, nhóm kiểm tra hai cách: so với đạo hàm tính bằng cách nhích từng trọng số một chút rồi xem sai số thay đổi bao nhiêu, và so với PyTorch. Cả hai đều khớp gần như tuyệt đối. Code trên slide đã rút gọn phần lưu dữ liệu trung gian, bản đầy đủ nằm trong notebook.

### Slide 20 · Đo trên mạng thật: tín hiệu lỗi về được bao xa?  
*Người 3 · ~2 phút*

Ba slide trước là lý thuyết và code. Bây giờ mình đo thật. Nói rõ mô hình: mỗi bước nhận vào 2 số, đi qua 1 lớp RNN hoặc 1 lớp LSTM có 64 nơ-ron ẩn, cuối cùng là 1 nơ-ron cho ra kết quả. LSTM có khoảng 17 nghìn trọng số, RNN khoảng 4 nghìn 4, vì LSTM có 4 bộ trọng số. Cả hai được huấn luyện bằng chính code NumPy vừa rồi, trên bài toán cộng với chuỗi 100 bước. Bài toán này mình sẽ nói kỹ ở thí nghiệm 3; ở đây chỉ cần biết mô hình phải nhớ một con số ở nửa đầu chuỗi cho tới cuối chuỗi.

Cách đo: ở bước cuối, gửi một tín hiệu lỗi ngược về, rồi xem ở từng bước trong quá khứ còn lại bao nhiêu phần trăm so với ở bước cuối. Trục dọc chia theo cấp số nhân, mỗi vạch nhỏ đi một trăm nghìn lần.

**(Demo)** Bấm "Lúc mới khởi tạo", chưa học gì. Kéo thanh về 50 bước: RNN gần như bằng 0, LSTM cũng đã rất nhỏ, khoảng năm phần trăm của một phần trăm. Lúc chưa học, LSTM cũng quên, vì cổng quên mới ở khoảng 0.73.

Bấm "Sau khi học". Đường xanh của LSTM gần như nằm ngang: từ khoảng 10 bước trở về trước, tín hiệu giữ ở khoảng 20 đến 25 phần trăm, kéo tới 99 bước vẫn còn. Mạng đã tự học cách giữ bộ nhớ: trong 64 nơ-ron, có một nơ-ron có cổng quên trung bình 0.96, cao hẳn các nơ-ron còn lại, nó làm nhiệm vụ mang con số được đánh dấu tới cuối chuỗi. Đường cam của RNN thì không đổi: lùi 50 bước chỉ còn khoảng một phần tỷ của một phần trăm.

Kết quả học khớp với điều đó: sai số của RNN đứng ở mức đoán mò, còn LSTM nhỏ hơn khoảng 160 lần. Đây cũng là câu trả lời cho slide 5: hiện tượng tín hiệu lỗi biến mất không chỉ là công thức, mà đo được trên mạng thật.

### Slide 21 · Về sau, ResNet cũng dùng lại ý tưởng này  
*Người 3 · ~1.5 phút*

Ý tưởng "giữ nguyên rồi cộng thêm" này hay đến mức về sau được dùng lại ở chỗ khác. Ví dụ nổi tiếng nhất là ResNet, viết tắt của Residual Network. Lớp mình chưa học mô hình này nên mình chỉ nói ý chính: ResNet là một mạng nhận dạng ảnh ra đời năm 2015. Trước đó, mạng càng nhiều lớp thì càng khó huấn luyện, cũng vì tín hiệu lỗi yếu dần khi đi ngược qua từng lớp, giống hệt vấn đề của RNN.

Bên trái là cách ResNet giải quyết. Mỗi khối tính đầu ra bằng đầu vào cộng với một phần thay đổi. Nhờ phép cộng đó, tín hiệu lỗi luôn có một lối tắt đi thẳng qua khối mà không bị nhân với trọng số. Nhờ vậy ResNet huấn luyện được mạng hơn 100 lớp.

Bên phải là LSTM: bộ nhớ mới bằng cổng quên nhân bộ nhớ cũ, cộng phần mới. Khi cổng quên bằng 1, nó giống hệt lối tắt của ResNet.

[click] Hai mô hình dùng cùng một nguyên lý: phép cộng tạo ra một lối tắt cho tín hiệu lỗi. ResNet làm theo chiều sâu, từ lớp này sang lớp sau. LSTM làm theo chiều thời gian, từ bước này sang bước sau. Và LSTM có ý tưởng này trước ResNet tận 18 năm.

Lý thuyết là vậy. Bây giờ mình kiểm tra trên dữ liệu thật.

### Slide 22 · Thí nghiệm 1: đơn hàng, chuỗi ngắn (8 bước)  
*Người 3 · ~1.5 phút*

Nhóm so sánh LSTM với RNN trên ba thí nghiệm, xếp theo độ dài chuỗi tăng dần: 8 bước, 60 bước, rồi 100 bước. Ở mọi thí nghiệm, hai mô hình giống hệt nhau về kích thước và cách huấn luyện; điểm khác duy nhất là thay RNN bằng LSTM. Mỗi mô hình được huấn luyện 5 lần với khởi tạo ngẫu nhiên khác nhau, để chắc chắn kết quả không phải do may rủi.

Hai thí nghiệm đầu dùng lại đúng hai bộ dữ liệu của bài A6 đã làm. Mô hình RNN ở đây cũng chính là mô hình RNN của bài A6; nhóm chỉ thay RNN bằng LSTM rồi so sánh.

Thí nghiệm thứ nhất dùng bộ dữ liệu đơn hàng của bài A6: khoảng 400 nghìn đơn hàng của một sàn thương mại điện tử ở Pakistan. Khi khách đặt một đơn, mô hình đoán đơn đó sẽ được giao xong, bị hủy, hay bị trả lại, dựa trên 8 đơn gần nhất của khách đó.

Kết quả: LSTM đoán đúng nhiều hơn, và ROC-AUC cao hơn. ROC-AUC là chỉ số đo khả năng xếp đúng loại đơn lên trước, 0.5 là đoán mò, 1 là hoàn hảo. LSTM hơn ở cả 5 lần chạy. Nhưng mức chênh rất nhỏ: chỉ khoảng 0.3 phần trăm. Và ở hai chỉ số liên quan tới đơn bị trả lại, LSTM còn kém hơn một chút.

[click] Lý do là chuỗi ở đây rất ngắn, chỉ 8 đơn. Thông tin quan trọng nằm ở chính đơn đang đặt và vài đơn gần nhất. Không có gì ở xa cần nhớ, nên bộ nhớ dài hạn của LSTM không có đất dụng võ.

Vậy nếu chuỗi dài hơn thì sao? Đó là thí nghiệm thứ hai.

### Slide 23 · Thí nghiệm 2: cổ phiếu, chuỗi dài (60 bước)  
*Người 3 · ~2 phút*

Thí nghiệm thứ hai là bộ dữ liệu cổ phiếu của bài A6: giá cổ phiếu theo ngày của Apple, Microsoft và HP. Mô hình nhìn 60 ngày giao dịch gần nhất và dự báo ngày mai giá sẽ dao động mạnh hay nhẹ, tức khoảng cách giữa giá cao nhất và thấp nhất trong ngày. Nhóm chọn bài toán này vì độ dao động có tính "nhớ": giai đoạn sôi động thường kéo dài nhiều tuần. Mô hình học trên dữ liệu từ 1995 đến 2016, và được kiểm tra trên 6 năm từ 2020 đến 2025.

Bảng bên trái là trung bình 5 lần chạy. LSTM tốt hơn ở mọi chỉ số. Sai số trung bình, MAE, giảm từ 0.727 xuống 0.709. Chỉ số R², tức mô hình giải thích được bao nhiêu phần sự lên xuống, tăng từ 0.35 lên 0.38. Và LSTM đoán đúng ngày mai dao động mạnh hơn hay nhẹ hơn nhiều hơn khoảng 1 phần trăm.

**(Demo: bấm qua các chỉ số)** Hình bên phải vẽ riêng từng lần chạy, mỗi chấm là một lần. Năm chấm xanh của LSTM và năm chấm cam của RNN tách hẳn nhau, không chồng lên nhau.

[click] Nghĩa là: lần chạy tệ nhất của LSTM vẫn tốt hơn lần chạy tốt nhất của RNN. Kết quả của LSTM cũng ổn định hơn giữa các lần. Cái giá phải trả là LSTM có gấp 4 lần trọng số và chạy chậm hơn khoảng một lần rưỡi.

Vậy vì sao LSTM tốt hơn ở đây?

### Slide 24 · Vì sao? LSTM dùng được cả những ngày ở xa  
*Người 3 · ~1.5 phút*

Để trả lời, nhóm đo xem mỗi ngày trong 60 ngày ảnh hưởng tới dự báo bao nhiêu: thay đổi dữ liệu của một ngày một chút, rồi xem dự báo thay đổi bao nhiêu.

**(Demo: tab 1)** Trục ngang là 60 ngày, ngày 60 là hôm nay. Trục dọc là mức ảnh hưởng so với hôm nay. Đường cam của RNN tụt rất nhanh: ngày đầu tiên chỉ còn ảnh hưởng khoảng một phần mười nghìn so với hôm nay. Đường xanh của LSTM tụt chậm hơn nhiều, ngày đầu tiên vẫn còn khoảng một phần trăm, gấp khoảng 75 lần RNN. Bảng bên phải: chỉ 3.7 phần trăm ảnh hưởng của RNN đến từ 40 ngày cũ, còn ở LSTM là 25 phần trăm.

**(Demo: tab 2)** Nhóm thử xóa dữ liệu của các ngày cũ rồi cho dự báo lại. Với RNN, giữ 20 ngày hay 60 ngày cũng như nhau, các ngày cũ không đóng góp gì. Với LSTM thì ngược lại: nếu chỉ giữ 5 hay 10 ngày, LSTM còn sai nhiều hơn RNN, vì nó đã quen dựa vào những ngày cũ, bỏ đi thì nó mất thông tin. Từ 20 ngày trở lên thì LSTM tốt hơn RNN.

**(Demo: tab 3)** Nhóm huấn luyện lại với 20, 60 và 120 ngày. RNN với 120 ngày cho kết quả y hệt 60 ngày: 60 ngày thêm vào hoàn toàn vô ích, vì tín hiệu lỗi không đi ngược xa được tới đó. Một điểm cần lưu ý: với dữ liệu này, cho xem 20 ngày là tốt nhất ở cả hai mô hình, vì thông tin quan trọng nhất nằm trong khoảng một tháng gần đây. Nhưng ở cả ba trường hợp, LSTM đều tốt hơn RNN.

[click] Tóm lại: RNN gặp đúng hiện tượng tín hiệu lỗi biến mất ở slide 5, còn LSTM nhờ con đường thẳng qua bộ nhớ nên vẫn nghe được tín hiệu từ những ngày xa.

### Slide 25 · Thí nghiệm 3: phải nhớ rất xa (bài toán cộng)  
*Người 3 · ~1.5 phút*

Thí nghiệm thứ ba đẩy độ dài lên mức cực đoan. Đây là bài toán cộng, một bài toán kinh điển có trong chính bài báo năm 1997. Mô hình đọc một chuỗi số ngẫu nhiên, trong đó có đúng hai số được đánh dấu: một số ở nửa đầu, một số ở nửa sau. Nhiệm vụ là trả lời tổng của hai số đó. Với chuỗi 100 số, mô hình phải nhớ số được đánh dấu đầu tiên qua ít nhất 50 bước.

Đường nét đứt là mức "đoán mò": luôn trả lời tổng bằng 1, là giá trị trung bình. Mô hình nào nằm trên đường này là chưa học được gì.

**(Bấm "Chuỗi 10 số")** Với chuỗi ngắn, cả hai mô hình đều học được, sai số giảm xuống dưới mức đoán mò. Giống thí nghiệm 1: khi không cần nhớ xa thì RNN cũng làm được.

**(Bấm "Chuỗi 100 số")** Với chuỗi dài, đường cam của RNN nằm yên ở mức đoán mò suốt quá trình học: RNN không học được gì. LSTM cũng loay hoay ở mức đó một thời gian, rồi đến khoảng giữa quá trình thì "vỡ lẽ", sai số tụt xuống gần 0. Sai số cuối cùng của LSTM thấp hơn RNN khoảng 50 lần.

[click] Tóm lại ba thí nghiệm: chuỗi càng dài thì LSTM càng hơn RNN rõ. 8 bước: hơn rất ít. 60 bước: hơn ở mọi chỉ số, cả 5 lần chạy. 100 bước: RNN không học được, LSTM học được. Đổi lại, LSTM có gấp 4 lần trọng số và chạy chậm hơn. Sang phần cuối, bạn An sẽ nói về cách dùng LSTM trong thực tế.

---

## Phần 4 — LSTM trong thực tế + tổng kết (Người 4)

### Slide 26 · Hai mẹo khi tự cài đặt LSTM  
*Người 4 · ~1.5 phút*

Cảm ơn Hiếu. Phần cuối nói về cách dùng LSTM trong thực tế, bắt đầu từ hai mẹo khi cài đặt, rút ra trực tiếp từ những gì các bạn vừa nghe.

Mẹo thứ nhất: cho cổng quên hơi mở ngay từ đầu. Lúc mới khởi tạo, trọng số còn nhỏ nên cổng quên thường nằm quanh 0.5, tức mỗi bước mạng mất một nửa bộ nhớ, và tín hiệu lỗi cũng yếu đi một nửa mỗi bước. Nếu đặt sẵn độ lệch, tức bias, của cổng quên bằng 1, thì cổng quên khởi đầu ở khoảng 0.73, mạng có xu hướng nhớ ngay từ đầu. Đây là khuyến nghị của một nhóm nghiên cứu ở Google năm 2015. Trong PyTorch, độ lệch của bốn phần được xếp liền nhau theo thứ tự: cổng ghi, cổng quên, nội dung mới, cổng đọc, nên phần của cổng quên là đoạn thứ hai.

[click] Mẹo thứ hai: vẫn phải giới hạn độ lớn của tín hiệu lỗi, gọi là gradient clipping. Con đường thẳng qua bộ nhớ chỉ chữa được việc tín hiệu biến mất. Các đường vòng giống RNN vẫn có thể làm tín hiệu phình to, tức bùng nổ. Hàm clip grad norm làm đúng một việc: nếu tín hiệu lớn hơn ngưỡng, ở đây là 1, thì thu nhỏ nó lại. Nhờ đó quá trình học không bị hỏng giữa chừng. Cả ba thí nghiệm của nhóm đều dùng cách này.

### Slide 27 · Xếp chồng và đọc hai chiều  
*Người 4 · ~1.5 phút*

Hai cách mở rộng LSTM hay dùng nhất.

Thứ nhất là xếp chồng nhiều lớp. Đầu ra ở từng bước của lớp LSTM thứ nhất trở thành đầu vào của lớp thứ hai. Giống CNN nhiều lớp, lớp càng cao thì hiểu ý nghĩa càng trừu tượng.

Thứ hai là đọc hai chiều. Dùng hai LSTM riêng: một cái đọc câu từ trái sang phải, một cái đọc từ phải sang trái, rồi ghép kết quả lại. Như vậy mỗi từ được hiểu nhờ cả phần trước lẫn phần sau của câu. Cách này hợp với các bài toán có sẵn cả câu, ví dụ phân loại một bình luận là khen hay chê.

Bên phải là code PyTorch. Muốn có hai cách mở rộng này, chỉ cần đặt số lớp bằng 2 và bật chế độ hai chiều. Thêm một tham số nữa là dropout, giúp mô hình đỡ học vẹt: trong lúc học, mỗi lần mô hình bỏ qua ngẫu nhiên khoảng 20 phần trăm tín hiệu đi từ lớp dưới lên lớp trên, nên nó không thể chỉ dựa vào vài đường cố định.

Một chỗ hay gây lỗi khi viết code: đọc hai chiều thì ở mỗi bước có hai kết quả, một của chiều xuôi, một của chiều ngược, và PyTorch nối chúng lại thành một. Nên đầu ra dài gấp đôi: nếu mỗi chiều cho 64 số thì đầu ra có 128 số. Lớp phía sau phải được khai báo nhận 128 số chứ không phải 64, nếu không chương trình sẽ báo lỗi sai kích thước.

### Slide 28 · LSTM dùng để làm gì, và giới hạn của nó  
*Người 4 · ~1.5 phút*

LSTM được dùng ở đâu? Có thể chia theo dạng đầu vào và đầu ra. Cả chuỗi cho ra một nhãn, ví dụ đọc một bình luận rồi đoán là khen hay chê, hoặc như thí nghiệm 1 của nhóm: đọc lịch sử đơn hàng rồi đoán đơn có bị hủy không. Mỗi bước một nhãn, ví dụ nhận dạng giọng nói. Chuỗi sang chuỗi, ví dụ dịch máy: một LSTM đọc câu nguồn, một LSTM viết câu đích, Google Dịch từng dùng cách này. Ngoài ra LSTM được dùng rất nhiều cho chuỗi thời gian và để sinh dữ liệu như văn bản hay nhạc.

LSTM cũng có giới hạn. Lớn nhất là phải đọc lần lượt: muốn tính bước sau thì phải có kết quả bước trước, nên không tận dụng được sức chạy song song của card đồ họa. Với chuỗi rất dài, mọi thứ phải nhồi vào một bộ nhớ có kích thước cố định. Và nó có gấp 4 lần trọng số so với RNN.

[click] Đó là lý do Attention, rồi Transformer năm 2017 ra đời: bỏ cách đọc lần lượt, mỗi từ nhìn thẳng vào mọi từ khác trong câu. Đó là chủ đề cho một buổi khác. Dòng phát triển của các mô hình xử lý chuỗi là: RNN, rồi LSTM, rồi Transformer.

### Slide 29 · Khi nào nên chọn LSTM?  
*Người 4 · ~2 phút*

Sau khi biết LSTM làm được gì và vướng ở đâu, câu hỏi thực tế nhất là: khi nào thì nên chọn nó?

Cột bên trái là những trường hợp hợp với LSTM. Thứ nhất, dữ liệu phải có thứ tự, nghĩa là đảo thứ tự thì ý nghĩa thay đổi, như một câu văn hay chuỗi giá theo ngày. Thứ hai, quan hệ dài hạn phải quan trọng: thông tin ở xa thật sự ảnh hưởng tới kết quả. Đây đúng là chỗ LSTM thắng rõ trong thí nghiệm 2 và 3 của bạn Hiếu. Thứ ba, dữ liệu không quá lớn: với lượng dữ liệu vừa phải, LSTM thường học ổn, trong khi Transformer thường cần nhiều dữ liệu hơn mới phát huy. Thứ tư, khi cần xử lý tuần tự theo thời gian thực, ví dụ dữ liệu cảm biến hay giọng nói đổ về liên tục: mỗi khi có một bước mới, LSTM chỉ cần cập nhật bộ nhớ của nó một lần, không phải đọc lại cả chuỗi.

Cột bên phải là những trường hợp không nhất thiết phải dùng LSTM. Chuỗi ngắn, chỉ vài bước gần nhất có ích: như thí nghiệm 1 với 8 đơn hàng, LSTM chỉ hơn RNN rất ít mà tốn gấp 4 lần trọng số. Dữ liệu không có thứ tự, ví dụ một bảng thông tin khách hàng: khi đó dùng MLP hay cây quyết định là hợp hơn. Và chuỗi rất dài, cần huấn luyện song song trên card đồ họa: đó là đất của Transformer.

[click] Điều quan trọng nhất của slide này: đừng chọn LSTM chỉ vì dữ liệu có yếu tố thời gian. Hãy bắt đầu bằng một mô hình cơ sở thật đơn giản, tiếng Anh gọi là baseline, rồi mới thử RNN, LSTM, và so sánh kết quả trên cùng một bộ dữ liệu kiểm tra. Chỉ chọn LSTM khi nó thật sự tốt hơn, đủ để bù cho việc nặng hơn và chậm hơn.

### Slide 30 · Quy trình xây dựng một mô hình LSTM thực tế  
*Người 4 · ~2.5 phút*

Slide này gom lại toàn bộ các bước để làm một mô hình LSTM thật, từ dữ liệu thô tới lúc đem đi dự báo. Đi theo mũi tên từ trái sang phải: có dữ liệu thô, đọc vào và làm sạch, tức là xử lý giá trị bị thiếu, bị trùng hoặc sai định dạng. Sau đó chia dữ liệu thành ba phần: tập huấn luyện, tiếng Anh là train, tập kiểm định là validation, và tập kiểm tra là test. Tiếp theo là chuẩn hóa để các con số về cùng một thang đo, rồi cắt dữ liệu thành các cửa sổ thời gian, ví dụ lấy 60 ngày để đoán ngày kế tiếp. Có cửa sổ rồi mới tới bước huấn luyện LSTM, đánh giá, và cuối cùng là triển khai để dự báo thật.

Phần huấn luyện thì các bạn đã nghe nhiều rồi. Thực tế, lỗi hay nằm ở những bước xung quanh. Mình nhấn vào bốn chỗ.

[click] Bước 3: chia dữ liệu theo thời gian, không xáo trộn ngẫu nhiên. Quá khứ dùng để học, giai đoạn sau dùng để kiểm định, giai đoạn gần nhất dùng để kiểm tra. Nếu xáo trộn, mô hình sẽ được học trên những ngày nằm sau ngày nó phải đoán, tức là được nhìn trước tương lai, và kết quả sẽ đẹp một cách ảo. Lỗi này gọi là rò rỉ dữ liệu. Thí nghiệm cổ phiếu của nhóm cũng làm vậy: học trên 1995 đến 2016, kiểm tra trên 2020 đến 2025.

[click] Bước 4: chỉ học tham số chuẩn hóa từ tập train. Trung bình và độ lệch chuẩn phải tính trên tập train, rồi dùng đúng các con số đó cho validation và test. Nếu tính trên toàn bộ dữ liệu thì thông tin của tương lai đã lọt vào lúc học, lại là rò rỉ dữ liệu.

[click] Bước 5: độ dài cửa sổ là một lựa chọn, không có con số đúng sẵn. Hãy thử vài độ dài, ví dụ 20, 60, 120 ngày như thí nghiệm 2, và chọn dựa trên tập validation. Tập test chỉ dùng một lần ở cuối, nếu không thì mình đang vô tình chọn mô hình theo đáp án.

[click] Bước 7: luôn so sánh với một baseline đơn giản. Ví dụ đoán ngày mai giống hôm nay, lấy trung bình vài ngày gần nhất, hay với bài phân loại thì luôn đoán loại phổ biến nhất. Ở thí nghiệm 3, đường "đoán mò" chính là một baseline như vậy. LSTM chỉ đáng dùng khi nó vượt baseline một cách rõ ràng.

### Slide 31 · Tổng kết  
*Người 4 · ~1.5 phút*

Tóm lại bài hôm nay bằng ba ý.

Một: LSTM thêm vào RNN một bộ nhớ dài hạn, gọi là trạng thái ô nhớ. Nó chạy thẳng qua các bước như một băng chuyền, chỉ bị xóa bớt và cộng thêm.

Hai: ba cổng quên, ghi và đọc, mỗi cổng là một cái van mở từ 0 đến 1, quyết định xóa bao nhiêu, ghi bao nhiêu, đọc ra bao nhiêu.

Ba: nhờ phép cộng trong cách cập nhật bộ nhớ, tín hiệu lỗi có một con đường thẳng để đi ngược về quá khứ, nên LSTM học được những gì ở xa. Ba thí nghiệm của nhóm cho thấy chuỗi càng dài thì LSTM càng hơn RNN rõ.

---

## Q&A (Cả nhóm)

### Slide 32 · (không tiêu đề)  
*Cả nhóm · ~2 phút*

Phần trình bày của nhóm mình đến đây là kết thúc. Cảm ơn thầy và các bạn đã lắng nghe. Nhóm mình xin nhận câu hỏi.

*(Gợi ý chuẩn bị trước một số câu hỏi có thể gặp:)*
- *Vì sao các cổng không dùng ReLU?* Vì cổng là một hệ số nhân, cần nằm trong khoảng 0 đến 1 như độ mở của cái van. ReLU không có giới hạn trên, có thể ra số rất lớn.
- *LSTM có hết hẳn hiện tượng tín hiệu lỗi biến mất không?* Không hẳn. Nó tạo ra một con đường mà mạng có thể học để giữ tín hiệu; nếu cổng quên nhỏ thì tín hiệu vẫn yếu dần.
- *Vì sao ở thí nghiệm đơn hàng, LSTM chỉ hơn RNN rất ít?* Chuỗi chỉ có 8 đơn, thông tin quan trọng nằm ở đơn đang đặt và vài đơn gần nhất, không có gì ở xa cần nhớ. Trong khi đó LSTM có gấp 4 lần trọng số và chạy chậm gấp đôi. Nên luôn thử RNN trước khi chọn LSTM.
- *Với cổ phiếu, sao không cho xem nhiều ngày hơn để LSTM thắng đậm hơn?* Nhóm đã thử 20, 60 và 120 ngày: 20 ngày tốt nhất cho cả hai mô hình, vì thông tin quan trọng nằm trong khoảng một tháng gần đây. LSTM tốt hơn RNN ở cả ba trường hợp, còn RNN với 120 ngày cho kết quả y hệt 60 ngày.

---

**Tổng thời lượng dự kiến:** ~50.5 phút
