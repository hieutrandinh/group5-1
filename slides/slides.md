---
theme: default
title: LSTM — Long Short-Term Memory
info: Thuyết trình LSTM, 45 phút, 4 người
colorSchema: light
highlighter: shiki
lineNumbers: false
mdc: true
transition: slide-left
fonts:
  sans: Be Vietnam Pro
  mono: JetBrains Mono
layout: cover
class: text-center
clicks: 2
---

# LSTM
## Long Short-Term Memory
<div class="text-lg opacity-70">mạng có "trí nhớ ngắn hạn nhưng giữ được lâu"</div>

<div class="mt-10 text-xl hook">
<span>"Tôi lớn lên ở </span><span class="key" :class="{ fade: $clicks >= 2 }">Pháp</span><span>, </span>
<span v-click="1" class="mid">… học tiểu học, chuyển nhà ba lần, chơi bóng đá, mê truyện tranh, làm quen bạn mới …</span>
<span>, nên tôi nói thành thạo tiếng </span><span class="blank">___</span>"
</div>

<div class="abs-b mb-8 w-full text-sm opacity-70">
<div class="font-bold">Nhóm 5</div>
<div>Nguyễn Phú Đại · Trần Vũ Tiến Minh · Trần Đình Hiếu · Phạm Thanh An</div>
</div>

<style>
.hook .key { color: #2563eb; font-weight: 800; transition: opacity 2.5s; }
.hook .key.fade { opacity: .12; }
.hook .mid { opacity: .6; font-style: italic; }
.hook .blank { color: #dc2626; font-weight: 800; }
</style>

<!--
**[Người 1 · ~1 phút]**

Xin chào thầy và các bạn. Hôm nay nhóm mình trình bày về LSTM, viết tắt của Long Short-Term Memory. Dịch sát nghĩa thì hơi khó nghe, nhưng ý của cái tên là: một mạng nơ-ron có trí nhớ ngắn hạn, nhưng giữ được lâu.

Mở đầu bằng một trò chơi nhỏ. Các bạn đọc câu này: "Tôi lớn lên ở Pháp, nên tôi nói thành thạo tiếng gì?" Ai cũng trả lời ngay: tiếng Pháp.

[click] Bây giờ mình chèn vào giữa câu một đoạn dài không liên quan: học tiểu học, chuyển nhà, chơi bóng đá… Các bạn vẫn trả lời được, vì các bạn nhớ chữ "Pháp" ở đầu câu.

[click] Nhưng với một mạng nơ-ron hồi quy cơ bản, chữ "Pháp" mờ dần sau mỗi từ, giống như trên màn hình. Đến cuối câu thì mạng gần như đã quên nó. Bài hôm nay trả lời ba câu hỏi: vì sao mạng hồi quy cơ bản hay quên, LSTM nhớ lâu bằng cách nào, và trong thực tế LSTM tốt hơn bao nhiêu.
-->

---
layout: center
---

# LSTM nằm ở đâu trong các loại mạng nơ-ron?

<svg viewBox="0 0 760 330" class="w-full fam" style="max-height: 360px">
  <defs><marker id="a2" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#2563eb"/></marker></defs>
  <g v-click="1">
    <rect x="10" y="10" width="225" height="110" rx="10" fill="#f1f5f9" stroke="#475569" stroke-width="2"/>
    <text x="122" y="34" class="t1">MLP</text>
    <text x="122" y="51" class="t3">Multi-Layer Perceptron</text>
    <text x="122" y="65" class="t3">(mạng nơ-ron nhiều lớp)</text>
    <text x="122" y="88" class="t2">Dữ liệu: một vector cố định</text>
    <text x="122" y="108" class="t2">ví dụ: một dòng trong bảng</text>
  </g>
  <g v-click="2">
    <rect x="250" y="10" width="225" height="110" rx="10" fill="#fef3c7" stroke="#d97706" stroke-width="2"/>
    <text x="362" y="34" class="t1">CNN</text>
    <text x="362" y="51" class="t3">Convolutional Neural Network</text>
    <text x="362" y="65" class="t3">(mạng nơ-ron tích chập)</text>
    <text x="362" y="88" class="t2">Dữ liệu: ảnh</text>
    <text x="362" y="108" class="t2">một bộ lọc quét khắp ảnh</text>
  </g>
  <g v-click="3">
    <rect x="490" y="10" width="260" height="250" rx="12" fill="#fff7ed" stroke="#ea580c" stroke-width="2"/>
    <text x="620" y="34" class="t1">Họ RNN</text>
    <text x="620" y="51" class="t3">Recurrent Neural Network</text>
    <text x="620" y="65" class="t3">(mạng nơ-ron hồi quy)</text>
    <text x="620" y="86" class="t2">Dữ liệu: chuỗi (câu, chuỗi giá…)</text>
    <text x="620" y="104" class="t2">đọc lần lượt từng phần tử, có bộ nhớ</text>
    <rect x="510" y="114" width="220" height="50" rx="8" fill="#ffedd5" stroke="#ea580c" stroke-width="1.5"/>
    <text x="620" y="135" class="t4">RNN cơ bản (1990)</text>
    <text x="620" y="154" class="t3">bộ nhớ bị viết lại mỗi bước</text>
    <text x="250" y="150" class="t2" fill="#475569">Ba họ mạng song song, mỗi họ hợp với một kiểu dữ liệu</text>
  </g>
  <g v-click="4">
    <rect x="510" y="174" width="220" height="70" rx="10" fill="#dbeafe" stroke="#2563eb" stroke-width="3"/>
    <text x="620" y="200" class="t1" fill="#1d4ed8">LSTM (1997)</text>
    <text x="620" y="220" class="t3">thêm một bộ nhớ dài hạn</text>
    <text x="620" y="236" class="t3">và 3 "cổng" điều khiển nó</text>
    <rect x="20" y="192" width="445" height="112" rx="10" fill="#eff6ff" stroke="#2563eb" stroke-dasharray="5 4"/>
    <text x="34" y="216" class="t4 l" fill="#1d4ed8">LSTM là một thành viên của họ RNN:</text>
    <text x="34" y="240" class="t2 l">• vẫn đọc chuỗi từng bước, dùng lại cùng trọng số ở mọi bước</text>
    <text x="34" y="262" class="t2 l">• vẫn huấn luyện như RNN</text>
    <text x="34" y="284" class="t2 l">• chỉ khác ở cách cập nhật bộ nhớ bên trong mỗi bước</text>
    <line x1="465" y1="230" x2="506" y2="212" stroke="#2563eb" stroke-width="2" marker-end="url(#a2)"/>
  </g>
</svg>

<style>
.fam text { text-anchor: middle; font-size: 13px; }
.fam .t1 { font-size: 20px; font-weight: 700; }
.fam .t3 { font-size: 11.5px; fill: #475569; }
.fam .t4 { font-size: 14px; font-weight: 700; }
.fam text.l { text-anchor: start; }
</style>

<!--
**[Người 1 · ~1.5 phút]**

Trước khi vào LSTM, mình đặt nó vào bức tranh chung các loại mạng nơ-ron mà lớp đã học.

[click] Đầu tiên là MLP, viết tắt của Multi-Layer Perceptron, tức mạng nơ-ron nhiều lớp thông thường. Đầu vào là một vector có kích thước cố định, ví dụ một dòng dữ liệu trong bảng.

[click] Thứ hai là CNN, mạng nơ-ron tích chập, các bạn đã làm ở bài tập trước. CNN dành cho ảnh: cùng một bộ lọc được quét qua mọi vị trí trên ảnh.

[click] Thứ ba là họ RNN, mạng nơ-ron hồi quy, dành cho dữ liệu dạng chuỗi như một câu văn hay chuỗi giá cổ phiếu. RNN đọc lần lượt từng phần tử và mang theo một "bộ nhớ" từ bước này sang bước sau. Ba họ này song song với nhau, mỗi họ hợp với một kiểu dữ liệu, không họ nào sinh ra từ họ nào.

[click] LSTM không phải một họ mới. Nó là một thành viên trong họ RNN: vẫn đọc chuỗi từng bước, vẫn dùng lại cùng một bộ trọng số ở mọi bước, vẫn huấn luyện giống RNN. Điểm khác duy nhất nằm bên trong mỗi bước: LSTM có thêm một bộ nhớ dài hạn và ba "cổng" để quyết định nhớ gì, quên gì.
-->

---

# So sánh MLP · CNN · RNN · LSTM

<div class="cmp">

| | MLP | CNN | RNN | <span class="text-blue-600">LSTM</span> |
|---|---|---|---|---|
| **Dữ liệu** | một vector cố định | ảnh | chuỗi | chuỗi |
| **Dùng lại trọng số** | không | ở mọi vị trí trên ảnh | ở mọi bước của chuỗi | ở mọi bước của chuỗi |
| **Bộ nhớ** | không | không | một bộ nhớ, bị viết lại mỗi bước | thêm **bộ nhớ dài hạn** |
| **Nhớ được bao xa** | — | — | thực tế khoảng 10 bước | hơn 1000 bước (thí nghiệm năm 1997) |
| **Số trọng số¹** | — | — | 29,312 | 117,248 (gấp 4) |
| **Dùng cho** | dữ liệu bảng | phân loại ảnh | chuỗi ngắn | dịch máy, giọng nói, chuỗi thời gian |

</div>

<div class="text-xs opacity-70 mt-1">¹ Một lớp, mỗi bước đầu vào có 100 số, bộ nhớ có 128 số.</div>

<div v-click class="mt-4 text-center text-lg">
LSTM giữ nguyên cách làm việc của RNN, chỉ sửa điểm yếu lớn nhất: <b class="text-red-600">hay quên những gì ở xa</b>.
</div>

<style>
.cmp table { font-size: .8rem; line-height: 1.3; }
.cmp th, .cmp td { padding: .35rem .5rem !important; }
.cmp td:first-child { white-space: nowrap; }
</style>

<!--
**[Người 1 · ~1 phút]**

Bảng này tóm tắt bốn loại mạng. Mình chỉ lưu ý ba dòng.

Dòng bộ nhớ: MLP và CNN không có bộ nhớ, mỗi đầu vào được xử lý riêng. RNN có một bộ nhớ, nhưng bộ nhớ này bị viết lại ở mỗi bước. LSTM có thêm một bộ nhớ dài hạn, và đây là nhân vật chính của cả bài.

Dòng nhớ được bao xa: trong thực tế, RNN cơ bản chỉ học được mối liên hệ trong khoảng 10 bước. Bài báo gốc năm 1997 cho thấy LSTM học được mối liên hệ cách nhau hơn 1000 bước.

Dòng số trọng số: với cùng kích thước, LSTM có gấp 4 lần số trọng số của RNN, vì nó có ba cổng cộng một phần tạo nội dung mới, mỗi phần cần một bộ trọng số riêng.

[click] Tóm lại: LSTM giữ nguyên cách làm việc của RNN, và sửa điểm yếu lớn nhất của RNN là hay quên những gì ở xa.
-->

---

# Ôn lại RNN: đọc từng từ, cập nhật một "bản tóm tắt"

<div class="grid grid-cols-[1fr_1.2fr] gap-4 items-center">
<div class="text-center text-xl">

$h_t = \tanh(W_h\, h_{t-1} + W_x\, x_t + b)$

</div>
<div class="legend">

- $h_t$: **bản tóm tắt** những gì đã đọc đến từ thứ $t$ (gọi là trạng thái ẩn, *hidden state*)
- $x_t$: **từ đang đọc** ở bước $t$ (đã đổi thành vector số)
- $W_h$, $W_x$, $b$: **trọng số** mạng học được, dùng lại ở mọi bước
- $\tanh$: hàm ép giá trị về khoảng từ −1 đến 1

</div>
</div>

<svg viewBox="0 0 760 200" class="w-full" style="max-height: 175px">
  <defs>
    <marker id="a4" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#475569"/></marker>
    <marker id="a4o" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#ea580c"/></marker>
  </defs>
  <g v-click="1">
    <rect x="60" y="60" width="110" height="60" rx="10" fill="#ffedd5" stroke="#ea580c" stroke-width="2"/>
    <text x="115" y="96" text-anchor="middle" style="font-size:16px">RNN</text>
    <line x1="115" y1="170" x2="115" y2="122" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="115" y="192" text-anchor="middle" style="font-size:16px;font-weight:700">"Tôi"</text>
    <line x1="115" y1="60" x2="115" y2="20" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="125" y="20" style="font-size:12px">tóm tắt 1</text>
    <line x1="170" y1="90" x2="238" y2="90" stroke="#ea580c" stroke-width="2.5" marker-end="url(#a4o)"/>
  </g>
  <g v-click="2">
    <rect x="240" y="60" width="110" height="60" rx="10" fill="#ffedd5" stroke="#ea580c" stroke-width="2"/>
    <text x="295" y="96" text-anchor="middle" style="font-size:16px">RNN</text>
    <line x1="295" y1="170" x2="295" y2="122" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="295" y="192" text-anchor="middle" style="font-size:16px;font-weight:700">"lớn"</text>
    <line x1="295" y1="60" x2="295" y2="20" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="305" y="20" style="font-size:12px">tóm tắt 2</text>
    <line x1="350" y1="90" x2="418" y2="90" stroke="#ea580c" stroke-width="2.5" marker-end="url(#a4o)"/>
  </g>
  <g v-click="3">
    <rect x="420" y="60" width="110" height="60" rx="10" fill="#ffedd5" stroke="#ea580c" stroke-width="2"/>
    <text x="475" y="96" text-anchor="middle" style="font-size:16px">RNN</text>
    <line x1="475" y1="170" x2="475" y2="122" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="475" y="192" text-anchor="middle" style="font-size:16px;font-weight:700">"lên"</text>
    <line x1="475" y1="60" x2="475" y2="20" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="485" y="20" style="font-size:12px">tóm tắt 3</text>
    <line x1="530" y1="90" x2="598" y2="90" stroke="#ea580c" stroke-width="2.5" marker-end="url(#a4o)"/>
    <text x="548" y="82" fill="#ea580c" style="font-size:14px">…</text>
  </g>
  <g v-click="4">
    <rect x="600" y="60" width="110" height="60" rx="10" fill="#ffedd5" stroke="#ea580c" stroke-width="2"/>
    <text x="655" y="96" text-anchor="middle" style="font-size:16px">RNN</text>
    <line x1="655" y1="170" x2="655" y2="122" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="655" y="192" text-anchor="middle" style="font-size:16px;font-weight:700">"tiếng"</text>
    <line x1="655" y1="60" x2="655" y2="20" stroke="#475569" stroke-width="2" marker-end="url(#a4)"/>
    <text x="665" y="20" style="font-size:12px">→ đoán "Pháp"</text>
  </g>
</svg>

<div v-click="5" class="text-center">

Cùng **một** bộ trọng số dùng ở **mọi** bước · Khi học, sai số ở cuối câu được truyền ngược lại từng bước về đầu câu: **BPTT** (*Backpropagation Through Time* – lan truyền ngược theo thời gian)

</div>

<!--
**[Người 1 · ~1.5 phút]**

Ôn lại nhanh RNN mà các bạn đã làm. Ý tưởng rất đơn giản: RNN đọc câu từng từ một, và luôn giữ một "bản tóm tắt" những gì đã đọc. Bản tóm tắt này hay còn gọi là trạng thái ẩn, tên tiếng Anh là hidden state.

Ở mỗi bước, bản tóm tắt mới được tính từ hai thứ: bản tóm tắt cũ, và từ đang đọc. Cả hai được nhân với trọng số, cộng lại, rồi đưa qua hàm tanh để ép về khoảng từ trừ 1 đến 1. Công thức trên slide chỉ nói đúng điều đó.

[click] Đọc chữ "Tôi", ra bản tóm tắt thứ nhất. [click] Đọc chữ "lớn", bản tóm tắt được viết lại. [click] Cứ thế tiếp tục, [click] cho tới chữ "tiếng", lúc đó mạng phải dựa vào bản tóm tắt để đoán chữ tiếp theo là "Pháp".

Các bạn để ý mũi tên màu cam: thông tin từ đầu câu muốn tới cuối câu thì phải đi qua tất cả các ô, và ở mỗi ô nó lại bị trộn với từ mới rồi viết lại.

[click] Một điều nữa: mọi ô dùng chung một bộ trọng số. Khi huấn luyện, sai số ở cuối câu được truyền ngược lại từng bước về đầu câu để sửa trọng số. Cách làm này gọi là BPTT, lan truyền ngược theo thời gian. Chính quãng đường đi ngược dài này gây ra vấn đề ở slide sau.
-->

---

# Vấn đề: RNN "quên" khi học

<div class="grid grid-cols-[1.25fr_1fr] gap-6 items-center">
<div class="text-sm">

Mạng đoán sai ở chỗ trống → **tín hiệu lỗi** (*gradient*: cho biết cần sửa trọng số theo hướng nào) phải đi ngược từ cuối câu về tận chữ "Pháp".

Mỗi lần lùi một bước, tín hiệu bị **nhân với một hệ số** (do trọng số và hàm tanh quyết định):

</div>
<div class="text-center">

<div class="text-sm">Tín hiệu còn lại sau T bước:</div>

$\gamma \times \gamma \times \dots \times \gamma = \gamma^{T}$

<div class="text-xs opacity-70">γ: hệ số mỗi bước · T: số bước phải đi ngược</div>

</div>
</div>

<div class="grid grid-cols-2 gap-4 text-center text-sm my-1">
<div class="text-orange-600">Hệ số &lt; 1 → tín hiệu nhỏ dần về 0: <b>gradient biến mất</b> → mạng không học được "Pháp" ↔ "tiếng"</div>
<div class="text-red-600">Hệ số &gt; 1 → tín hiệu phình to: <b>gradient bùng nổ</b> → huấn luyện không ổn định</div>
</div>

<GradientDecay />

<div class="text-xs opacity-60 text-right">Biểu đồ vẽ từ công thức với hệ số tự chọn, chưa phải mạng thật. Số đo trên mạng thật: slide 20.</div>

<!--
**[Người 1 · ~2 phút]**

Đây là vấn đề chính của RNN. Khi huấn luyện, nếu mạng đoán sai ở chỗ trống, ta cần một tín hiệu lỗi đi ngược lại để sửa trọng số. Tín hiệu này có tên là gradient, các bạn hiểu đơn giản là nó cho biết cần chỉnh trọng số theo hướng nào và chỉnh bao nhiêu.

Muốn mạng học được rằng chữ "Pháp" quyết định chữ cuối câu, tín hiệu lỗi phải đi ngược về tận chữ "Pháp". Vấn đề là mỗi lần lùi lại một bước, tín hiệu bị nhân với một hệ số, do trọng số và hàm tanh quyết định. Đi ngược 50 bước thì bị nhân 50 lần.

Nếu hệ số nhỏ hơn 1, tín hiệu teo dần về 0. Ta gọi là gradient biến mất. Nếu hệ số lớn hơn 1, tín hiệu phình to, gọi là gradient bùng nổ. Giống như trò chơi truyền tin qua 50 người: chỉ cần mỗi người nói nhỏ đi một chút, đến cuối hàng thì không còn nghe thấy gì.

**(Demo)** Mình thử trên biểu đồ. Thanh trượt trên cùng là số bước cần nhớ lại, tức khoảng cách từ chữ "Pháp" tới chỗ trống, đang là 50. Thanh thứ hai là hệ số mỗi bước, đang là 0.9, tức mỗi bước tín hiệu chỉ giữ lại 90%. Nghe thì có vẻ nhiều, nhưng sau 50 bước, tín hiệu chỉ còn khoảng nửa phần trăm. Kéo số bước lên 100 thì gần như bằng 0. Còn nếu kéo hệ số lên 1.1, đường cong vọt lên trên: đó là bùng nổ.

Ta không thể giữ hệ số đúng bằng 1, vì trọng số thay đổi liên tục trong quá trình học. Đó là lý do RNN không nối được chữ "Pháp" với chữ "tiếng".

Lưu ý: biểu đồ này vẽ thẳng từ công thức, hệ số mỗi bước do mình tự chọn, chưa phải một mạng nơ-ron cụ thể. Ở slide 20, bạn Hiếu sẽ đo trên một mạng thật đã huấn luyện, có số lớp và số nơ-ron rõ ràng.
-->

---

# Ai phát hiện ra vấn đề và sửa nó?

<div class="grid grid-cols-2 gap-6 mt-2">
<div v-click="1" class="card">

### 1991 · Sepp Hochreiter
Sinh viên **Đại học Kỹ thuật München** (Đức), làm luận văn tốt nghiệp với thầy **Jürgen Schmidhuber**

→ Người **đầu tiên chứng minh bằng toán** vì sao RNN "quên": chính là phép nhân lặp lại ở slide trước

<span class="text-sm opacity-70">(Năm 1994, Bengio và cộng sự cũng độc lập tìm ra điều này)</span>

</div>
<div v-click="2" class="card">

### Ý tưởng sửa
Tín hiệu yếu dần vì bị **nhân với số khác 1** nhiều lần.

→ Vậy làm một **ô nhớ** chỉ việc giữ nguyên giá trị cũ rồi **cộng thêm** cái mới:

<div class="text-center my-1">bộ nhớ mới = <b>1 ×</b> bộ nhớ cũ <b>+</b> thông tin mới</div>

→ Hệ số luôn bằng 1, tín hiệu đi qua bao nhiêu bước cũng không đổi. Ông gọi là **CEC** (*Constant Error Carousel* – "băng chuyền giữ nguyên lỗi")

</div>
</div>

<div v-click="3" class="card mt-3">

**Vấn đề mới:** từ nào cũng được cộng vào ô nhớ → ô nhớ bị lẫn lộn. Cách giải: thêm các **cổng**, hoạt động như **cái van** (mở từ 0 đến 1), quyết định *lúc nào được ghi* và *lúc nào được đọc*.
**1997:** Hochreiter và Schmidhuber công bố bài báo *"Long Short-Term Memory"* trên tạp chí *Neural Computation*.

</div>

<style>
.card { background: #f8fafc; border: 1px solid #cbd5e1; border-radius: 12px; padding: .6rem 1rem; font-size: .85rem; }
.card h3 { margin-top: 0 !important; color: #1d4ed8; }
</style>

<!--
**[Người 1 · ~2 phút]**

Vậy ai đã nhìn ra vấn đề này và sửa nó?

[click] Năm 1991, Sepp Hochreiter, khi đó là sinh viên Đại học Kỹ thuật München ở Đức, làm luận văn tốt nghiệp với thầy Jürgen Schmidhuber. Trong luận văn này, ông là người đầu tiên chứng minh bằng toán vì sao RNN hay quên, chính là phép nhân lặp đi lặp lại mà mình vừa demo. Ba năm sau, nhóm của Bengio cũng độc lập tìm ra kết quả tương tự.

[click] Cách sửa của ông rất tự nhiên. Tín hiệu yếu dần vì bị nhân với một số khác 1 nhiều lần. Vậy thì làm một ô nhớ mà ở mỗi bước, nó chỉ giữ nguyên giá trị cũ rồi cộng thêm thông tin mới. Hệ số khi đó luôn đúng bằng 1, nên tín hiệu đi qua bao nhiêu bước cũng không bị yếu đi. Ông đặt tên cho ô nhớ này là CEC, viết tắt của Constant Error Carousel, tạm hiểu là "băng chuyền giữ nguyên lỗi".

[click] Nhưng làm vậy lại sinh ra vấn đề mới: từ nào cũng được cộng vào ô nhớ, kể cả những từ không quan trọng, nên ô nhớ bị lẫn lộn. Giải pháp là thêm các cổng. Các bạn hình dung cổng như một cái van, mở từ 0 đến 1, quyết định lúc nào được ghi vào ô nhớ và lúc nào được đọc ra. Năm 1997, Hochreiter và Schmidhuber công bố bài báo "Long Short-Term Memory", và bài báo này trở thành một trong những bài được trích dẫn nhiều nhất của ngành học sâu.
-->

---

# Các mốc phát triển của LSTM

<div class="timeline">
  <div v-click class="ev"><b>1997</b><span>Bản đầu tiên: có cổng ghi và cổng đọc, nhưng <span class="text-red-600">chưa có cổng quên</span> → ô nhớ chỉ cộng thêm, không xóa được</span></div>
  <div v-click class="ev"><b>1999–2000</b><span>Felix Gers và cộng sự thêm <b class="text-red-600">cổng quên</b>: mạng tự học khi nào xóa bớt ô nhớ. LSTM dùng ngày nay là bản này</span></div>
  <div v-click class="ev"><b>2005</b><span>Huấn luyện LSTM theo cách đầy đủ hơn; ra đời <b>LSTM hai chiều</b> (đọc câu cả xuôi lẫn ngược)</span></div>
  <div v-click class="ev"><b>2009</b><span>LSTM thắng các cuộc thi nhận dạng chữ viết tay</span></div>
  <div v-click class="ev"><b>2014</b><span><b>Dịch máy</b> bằng hai LSTM: một LSTM đọc câu nguồn, một LSTM viết câu đích (gọi là <i>seq2seq</i> – sequence to sequence, chuỗi sang chuỗi)</span></div>
  <div v-click class="ev"><b>2015–2016</b><span>Nhận dạng giọng nói của Google và Google Dịch chuyển sang dùng LSTM</span></div>
  <div v-click class="ev"><b>2017</b><span><b>Transformer</b> ra đời (nền tảng của ChatGPT) và dần thay thế LSTM trong xử lý ngôn ngữ</span></div>
</div>

<div v-click class="mt-4 text-center text-sm">
Ý nghĩa tên gọi: <i>short-term memory</i> (trí nhớ ngắn hạn) là những gì mạng đang giữ trong lúc đọc; LSTM giữ được trí nhớ ngắn hạn đó <b>lâu</b> (<i>long</i>) qua nhiều bước
</div>

<style>
.timeline { border-left: 3px solid #2563eb; margin-left: 1rem; padding-left: 1rem; }
.ev { display: grid; grid-template-columns: 7rem 1fr; gap: .5rem; padding: .25rem 0; font-size: .88rem; position: relative; }
.ev::before { content: ''; position: absolute; left: -1.45rem; top: .6rem; width: .7rem; height: .7rem; border-radius: 50%; background: #2563eb; }
.ev b:first-child { color: #1d4ed8; }
</style>

<!--
**[Người 1 · ~1 phút]**

Điểm qua nhanh hành trình của LSTM.

[click] Bản đầu tiên năm 1997 có cổng ghi và cổng đọc, nhưng chưa có cổng quên. Nghĩa là ô nhớ chỉ được cộng thêm, không có cách nào xóa bớt.

[click] Năm 2000, Felix Gers và cộng sự thêm cổng quên, để mạng tự học khi nào cần xóa bớt nội dung cũ. LSTM mà các bạn dùng trong PyTorch ngày nay chính là phiên bản có cổng quên này.

[click] Năm 2005, LSTM được huấn luyện theo cách đầy đủ hơn, và có thêm LSTM hai chiều, đọc câu cả xuôi lẫn ngược. [click] Năm 2009, LSTM thắng các cuộc thi nhận dạng chữ viết tay. [click] Năm 2014, người ta dùng hai LSTM để dịch máy: một LSTM đọc câu tiếng Anh, một LSTM viết ra câu tiếng Pháp. [click] Khoảng 2015, 2016, nhận dạng giọng nói của Google và Google Dịch đều chuyển sang LSTM. [click] Đến 2017, Transformer, nền tảng của ChatGPT, ra đời và dần thay thế LSTM trong xử lý ngôn ngữ.

[click] Còn cái tên: trí nhớ ngắn hạn là những gì mạng đang giữ trong lúc đọc câu. LSTM là trí nhớ ngắn hạn nhưng giữ được lâu.
-->

---
layout: center
class: text-center
---

# Bên trong một ô LSTM

<div class="text-xl opacity-80 mt-4">Một <b>ô nhớ</b> giữ thông tin lâu dài + <b>3 cổng</b>: quên · ghi · đọc</div>

<!--
**[Người 1 · ~0.5 phút]**

Tóm lại phần của mình: RNN hay quên vì tín hiệu lỗi bị nhân đi nhân lại qua rất nhiều bước. Hochreiter sửa bằng một ô nhớ chỉ giữ nguyên rồi cộng thêm, cùng với các cổng để điều khiển nó. Tiếp theo, mời bạn Minh mở ô LSTM ra xem bên trong có gì.
-->

---


# Hai loại bộ nhớ: trạng thái ẩn và trạng thái ô nhớ

<div class="grid grid-cols-2 gap-6 items-center">
<div class="text-center">

**RNN: chỉ có một bộ nhớ**

<svg viewBox="0 0 260 180" style="width: 85%; margin: auto">
  <defs><marker id="a9" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="#475569"/></marker></defs>
  <rect x="70" y="40" width="120" height="90" rx="14" fill="#ffedd5" stroke="#ea580c" stroke-width="2"/>
  <text x="130" y="92" text-anchor="middle" style="font-size:20px">tanh</text>
  <line x1="5" y1="85" x2="68" y2="85" stroke="#475569" stroke-width="2.5" marker-end="url(#a9)"/>
  <line x1="190" y1="85" x2="255" y2="85" stroke="#475569" stroke-width="2.5" marker-end="url(#a9)"/>
  <line x1="130" y1="178" x2="130" y2="132" stroke="#475569" stroke-width="2.5" marker-end="url(#a9)"/>
  <text x="2" y="72" style="font-size:12px">tóm tắt cũ</text><text x="258" y="72" text-anchor="end" style="font-size:12px">tóm tắt mới</text><text x="140" y="172" style="font-size:12px">từ hiện tại</text>
</svg>

</div>
<div class="text-center">

**LSTM: thêm <span class="text-blue-600">một bộ nhớ dài hạn</span>**

<LstmCell active="state" />

</div>
</div>

<div class="grid grid-cols-2 gap-4 mt-2 text-sm">
<div v-click class="p-2 rounded bg-blue-50">

<b class="text-blue-600">Trạng thái ô nhớ</b> (*cell state*): đường thẳng phía trên, chạy xuyên qua như **băng chuyền**. Chỉ bị *xóa bớt* và *cộng thêm*, không bị viết lại → **bộ nhớ dài hạn** (chính là CEC)

</div>
<div v-click class="p-2 rounded bg-slate-100">

<b>Trạng thái ẩn</b> (*hidden state*): **đầu ra** của mỗi bước, đọc ra từ ô nhớ → **bộ nhớ ngắn hạn**, giống bản tóm tắt của RNN

</div>
</div>

<!--
**[Người 2 · ~1 phút]**

Cảm ơn Đại. Mình bắt đầu bằng bức tranh tổng thể.

Bên trái là RNN: chỉ có một bộ nhớ, chính là bản tóm tắt. Ở mỗi bước, bản tóm tắt này bị trộn với từ mới, đi qua hàm tanh, và được viết lại hoàn toàn.

Bên phải là LSTM. Các bạn để ý đường màu xanh nằm ngang phía trên. Đó là bộ nhớ dài hạn, hay còn gọi là trạng thái ô nhớ, tên tiếng Anh là cell state.

[click] Đường này chạy thẳng qua ô giống như một băng chuyền. Trên đường đi chỉ có hai thao tác: xóa bớt một phần, và cộng thêm thông tin mới. Không có gì viết lại toàn bộ. Đây chính là ô nhớ giữ nguyên của Hochreiter mà bạn Đại vừa kể.

[click] Ngoài ra LSTM vẫn có trạng thái ẩn, là đầu ra ở mỗi bước, giống bản tóm tắt của RNN. Nó được đọc ra từ ô nhớ, và đóng vai trò bộ nhớ ngắn hạn.

Ba cổng tiếp theo điều khiển ba việc trên băng chuyền: xóa, ghi, và đọc. Mình đi lần lượt từng cổng.
-->

---

# Cổng 1 · Cổng quên (*forget gate*): giữ lại bao nhiêu bộ nhớ cũ?

<div class="grid grid-cols-[1.3fr_1fr] gap-6 items-center">
<LstmCell active="f" />
<div>

<div class="p-2 rounded bg-red-50 text-sm mb-2">

Nhìn **từ hiện tại** và **đầu ra bước trước**, cổng quên cho ra một số **từ 0 đến 1** cho mỗi ô nhớ:

- **0** → xóa sạch ô nhớ đó
- **1** → giữ nguyên ô nhớ đó
- 0.7 → giữ lại 70%

</div>

<div class="text-sm">

<v-clicks>

- Số từ 0 đến 1 có được nhờ hàm **sigmoid**, giống **độ mở của một cái van**
- Ví dụ: đọc sang câu nói về một người khác → cổng quên **gần 0** để xóa thông tin "Pháp" cũ
- Bản 1997 **chưa có** cổng này (tương đương luôn bằng 1: không bao giờ xóa)

</v-clicks>

</div>

<div class="text-xs opacity-60 mt-2">

Công thức: $f_t = \sigma\big(W_f\,[h_{t-1}, x_t] + b_f\big)$ — σ là sigmoid

</div>

</div>
</div>

<!--
**[Người 2 · ~1.5 phút]**

Cổng đầu tiên là cổng quên, tiếng Anh là forget gate, tô màu đỏ trên sơ đồ.

Cổng quên nhìn vào hai thứ: từ đang đọc, và đầu ra của bước trước. Từ đó nó quyết định giữ lại bao nhiêu phần bộ nhớ cũ. Kết quả là một con số từ 0 đến 1 cho mỗi ô nhớ. Bằng 0 nghĩa là xóa sạch, bằng 1 nghĩa là giữ nguyên, còn 0.7 nghĩa là giữ lại 70 phần trăm.

[click] Để có con số từ 0 đến 1, cổng dùng hàm sigmoid. Các bạn cứ hình dung nó như độ mở của một cái van: 0 là đóng hẳn, 1 là mở hết.

[click] Ví dụ, khi đoạn văn chuyển sang kể về một người khác, sinh ra ở Nhật chẳng hạn, thì thông tin "Pháp" không còn đúng nữa. Lúc đó mạng học cách đóng cổng quên về gần 0 để xóa nó đi.

[click] Như bạn Đại đã kể, bản năm 1997 chưa có cổng này. Khi đó bộ nhớ chỉ được cộng thêm mà không bao giờ bị xóa, giống một cái tủ chỉ cho thêm đồ vào mà không cho lấy ra.
-->

---

# Cổng 2 · Cổng ghi (*input gate*): ghi gì, ghi bao nhiêu?

<div class="grid grid-cols-[1.3fr_1fr] gap-6 items-center">
<LstmCell active="i,c" />
<div>

<div class="p-2 rounded bg-green-50 text-sm mb-2">

Hai phần làm việc cùng nhau:

- **Nội dung mới**: thông tin *định ghi* vào ô nhớ (từ −1 đến 1, dùng hàm tanh)
- **Cổng ghi**: *ghi bao nhiêu phần* nội dung đó (từ 0 đến 1, dùng sigmoid)

</div>

<div class="text-sm">

<v-clicks>

- Phần thật sự được ghi = **cổng ghi × nội dung mới**
- Tại chữ **"Pháp"**: cổng ghi **mở** (≈ 1) → ghi "Pháp" vào ô nhớ
- Tại "chơi bóng đá", "mê truyện tranh": cổng ghi **đóng** (≈ 0) → không ghi đè lên "Pháp"

</v-clicks>

</div>

<div class="text-xs opacity-60 mt-2">

Công thức: $i_t = \sigma(W_i[h_{t-1}, x_t] + b_i)$ · $\tilde c_t = \tanh(W_c[h_{t-1}, x_t] + b_c)$

</div>

</div>
</div>

<!--
**[Người 2 · ~1.5 phút]**

Cổng thứ hai là cổng ghi, tiếng Anh là input gate, màu xanh lá. Phần này có hai thành phần làm việc cùng nhau.

Thành phần thứ nhất tạo ra nội dung mới, tức thông tin định ghi vào ô nhớ. Nó dùng hàm tanh, nên giá trị nằm trong khoảng từ trừ 1 đến 1, có thể làm ô nhớ tăng lên hoặc giảm xuống.

Thành phần thứ hai là cổng ghi, lại là một cái van từ 0 đến 1, quyết định ghi bao nhiêu phần nội dung đó.

[click] Phần thật sự được ghi vào ô nhớ là cổng ghi nhân với nội dung mới.

[click] Vì sao phải tách làm hai? Vì không phải từ nào cũng đáng nhớ. Tại chữ "Pháp", cổng ghi mở gần hết để ghi thông tin quan trọng này vào. [click] Còn tại những từ như "chơi bóng đá", "mê truyện tranh", cổng ghi gần như đóng, để không ghi đè lên chữ "Pháp" đã lưu. Đây chính là vấn đề lẫn lộn mà Hochreiter giải quyết bằng cổng.
-->

---

# Cập nhật bộ nhớ: xóa bớt rồi cộng thêm

<div class="grid grid-cols-[1.3fr_1fr] gap-6 items-center">
<LstmCell active="state,f,i,c" />
<div>

<div class="p-3 rounded bg-blue-50 text-center">

**bộ nhớ mới** = <span class="text-red-600">**cổng quên** × bộ nhớ cũ</span> **+** <span class="text-green-600">**cổng ghi** × nội dung mới</span>

<div class="text-sm mt-1 opacity-70">

$c_t = f_t \odot c_{t-1} + i_t \odot \tilde c_t$

</div>

</div>

<div class="text-sm mt-3">

<v-clicks>

- **RNN:** mỗi bước, toàn bộ bản tóm tắt bị **viết lại** (nhân trọng số rồi qua tanh)
- **LSTM:** bộ nhớ cũ chỉ bị **xóa bớt** theo cổng quên, rồi **cộng thêm** phần mới
- Chính **phép cộng** này giúp LSTM học được những gì ở xa (phần 3 sẽ giải thích)

</v-clicks>

</div>
</div>
</div>

<!--
**[Người 2 · ~1 phút]**

Bây giờ ghép hai cổng lại. Đây là công thức quan trọng nhất của LSTM, và mình giải thích nó bằng một câu: bộ nhớ mới sẽ bằng tổng của cổng quên nhân bộ nhớ cũ và cổng ghi nhân nội dung mới. Nói cách khác: xóa bớt một phần cái cũ, rồi cộng thêm một phần cái mới.

[click] So với RNN: ở RNN, mỗi bước bản tóm tắt bị viết lại hoàn toàn, nhân với trọng số rồi đi qua tanh.

[click] Còn ở LSTM, bộ nhớ cũ không bị viết lại. Nó chỉ bị xóa bớt theo cổng quên, rồi được cộng thêm phần mới. Nếu cổng quên mở hết và cổng ghi đóng, bộ nhớ đi qua bước đó y nguyên.

[click] Lát nữa bạn Hiếu sẽ cho thấy chính phép cộng đơn giản này là lý do LSTM học được những mối liên hệ ở xa.
-->

---

# Cổng 3 · Cổng đọc (*output gate*): đọc ra bao nhiêu?

<div class="grid grid-cols-[1.3fr_1fr] gap-6 items-center">
<LstmCell active="o" />
<div>

<div class="p-2 rounded bg-purple-50 text-sm mb-2">

**đầu ra** = **cổng đọc** × tanh(**bộ nhớ**)

- tanh: ép giá trị bộ nhớ về khoảng −1 đến 1
- cổng đọc (0 đến 1): ở bước này **cho đọc ra bao nhiêu**

</div>

<div class="text-sm">

<v-clicks>

- Bộ nhớ có thể **cất** chữ "Pháp" suốt 30 bước mà **không làm nhiễu** đầu ra ở các bước đó
- Đến chữ **"tiếng"**: cổng đọc **mở** → lấy "Pháp" ra để đoán từ tiếp theo

</v-clicks>

</div>

<div class="text-xs opacity-60 mt-2">

Công thức: $o_t = \sigma(W_o[h_{t-1}, x_t] + b_o)$ · $h_t = o_t \odot \tanh(c_t)$

</div>

</div>
</div>

<!--
**[Người 2 · ~1 phút]**

Cổng cuối cùng là cổng đọc, tiếng Anh là output gate, màu tím.

Những gì được ghi vào ô nhớ chỉ nằm bên trong LSTM; bên ngoài không nhìn thấy trực tiếp. Thứ bên ngoài nhận được ở mỗi bước, và dùng để đoán từ tiếp theo, là đầu ra. Cổng đọc quyết định lấy bao nhiêu từ bộ nhớ ra làm đầu ra. Giống như một cuốn sổ ghi chú: mình ghi nhiều thứ vào sổ, nhưng lúc nói chuyện chỉ đọc ra phần đang cần.

Cụ thể có hai bước: đưa bộ nhớ qua hàm tanh để ép về khoảng từ trừ 1 đến 1, rồi nhân với cổng đọc. Cổng đọc cũng là một cái van từ 0 đến 1: bằng 0 là không cho gì ra, bằng 1 là cho ra hết.

[click] Nhờ vậy, mạng có thể cất chữ "Pháp" trong ô nhớ suốt 30 bước mà không làm nhiễu đầu ra ở các bước giữa: cổng đọc đóng thì thông tin vẫn nằm yên trong ô nhớ.

[click] Đến chữ "tiếng", khi cần đoán tên ngôn ngữ, cổng đọc mới mở ra để lấy thông tin "Pháp" ra dùng.
-->

---
clicks: 4
---

# Tổng hợp: một ô LSTM làm 4 việc

<div class="grid grid-cols-[1.15fr_1fr] gap-6 items-center">
<LstmCell :active="['all', 'f', 'i,c', 'state,f,i,c', 'o'][$clicks] || 'all'" />
<div class="eqs">

<div :class="{ hl: $clicks === 1 }">

**1 · Quên:** <span class="text-red-600">cổng quên</span> quyết định giữ bao nhiêu bộ nhớ cũ

$f_t = \sigma(W_f[h_{t-1}, x_t] + b_f)$

</div>
<div :class="{ hl: $clicks === 2 }">

**2 · Chuẩn bị ghi:** tạo <span class="text-green-600">nội dung mới</span> và <span class="text-green-600">cổng ghi</span>

$i_t = \sigma(W_i[h_{t-1}, x_t] + b_i)$ · $\tilde c_t = \tanh(W_c[h_{t-1}, x_t] + b_c)$

</div>
<div :class="{ hl: $clicks === 3 }">

**3 · Cập nhật:** <span class="text-blue-600">bộ nhớ</span> = xóa bớt + cộng thêm

$c_t = f_t \odot c_{t-1} + i_t \odot \tilde c_t$

</div>
<div :class="{ hl: $clicks === 4 }">

**4 · Đọc:** <span class="text-purple-600">cổng đọc</span> lấy đầu ra từ bộ nhớ

$o_t = \sigma(W_o[h_{t-1}, x_t] + b_o)$ · $h_t = o_t \odot \tanh(c_t)$

</div>
<div class="text-xs opacity-70 mt-2 px-2">

Ký hiệu: $x$ đầu vào · $h$ đầu ra (trạng thái ẩn) · $c$ bộ nhớ (trạng thái ô nhớ) · $W$, $b$ trọng số · σ sigmoid · $\odot$ nhân từng phần tử. Có 4 bộ trọng số $W_f, W_i, W_c, W_o$ → LSTM có **gấp 4 lần** trọng số so với RNN

</div>
</div>
</div>

<style>
.eqs > div { padding: .15rem .6rem; border-radius: 8px; transition: background .3s; font-size: .85rem; }
.eqs p { margin: .1rem 0; }
.eqs p + p { font-size: .78rem; opacity: .75; }
.eqs .hl { background: #fef9c3; }
</style>

<!--
**[Người 2 · ~1 phút]**

Đây là toàn bộ LSTM trên một slide. Một ô LSTM làm đúng bốn việc, mình bấm lần lượt để các bạn đối chiếu với sơ đồ.

[click] Việc một: quên. Cổng quên quyết định giữ lại bao nhiêu bộ nhớ cũ.

[click] Việc hai: chuẩn bị ghi. Tạo nội dung mới, và cổng ghi quyết định ghi bao nhiêu.

[click] Việc ba: cập nhật bộ nhớ. Giữ lại phần cũ sau khi đã xóa bớt, rồi cộng thêm phần mới vừa ghi vào.

[click] Việc bốn: đọc. Cổng đọc quyết định lấy ra bao nhiêu từ bộ nhớ để làm đầu ra.

Công thức nằm dưới mỗi dòng cho bạn nào muốn đối chiếu với tài liệu. Các bạn để ý ba cổng và phần nội dung mới đều có cùng một dạng, mỗi phần có một bộ trọng số riêng. Bốn bộ trọng số, nên LSTM có gấp 4 lần trọng số so với RNN cùng kích thước, đúng như bảng so sánh lúc đầu.
-->

---

# Viết thành code: ô LSTM = ghép các hàm sơ cấp

<div class="text-sm mb-2">Chỉ dùng NumPy, không dùng PyTorch hay Keras. Cả ô LSTM được ghép từ <b>5 hàm sơ cấp</b> (<i>elementary functions</i>):</div>

<div class="grid grid-cols-[1fr_1.45fr] gap-4 code-sm">
<div>

```python {all|3|4-5|6|7}
import numpy as np

def affine(x, W, b): return x @ W + b  # nhân trọng số, cộng độ lệch
def sigmoid(z): return 1 / (1 + np.exp(-z))  # độ mở van, 0..1
def tanh(z):    return np.tanh(z)            # nén về -1..1
def mul(a, b):  return a * b  # nhân từng phần tử
def add(a, b):  return a + b  # cộng từng phần tử
```

</div>
<div>

```python {all|3|4-7|8|9|11-15}
def lstm_cell(x, h_prev, c_prev, W, b):
    H = h_prev.shape[1]                 # số nơ-ron ẩn
    z = affine(np.concatenate([x, h_prev], 1), W, b)  # 4 phần cùng lúc
    i = sigmoid(z[:, 0*H:1*H])          # cổng ghi
    f = sigmoid(z[:, 1*H:2*H])          # cổng quên
    g = tanh(z[:, 2*H:3*H])             # nội dung mới
    o = sigmoid(z[:, 3*H:4*H])          # cổng đọc
    c = add(mul(f, c_prev), mul(i, g))  # xóa bớt rồi cộng thêm
    h = mul(o, tanh(c))                 # đọc ra
    return h, c

def lstm(X, W, b, H):                   # X: (số mẫu, số bước, số đầu vào)
    h = c = np.zeros((len(X), H))
    for t in range(X.shape[1]):         # lặp theo thời gian,
        h, c = lstm_cell(X[:, t], h, c, W, b)  # dùng lại cùng W, b
    return h
```

</div>
</div>

<div class="flex items-center gap-4 mt-1 text-xs">
<div v-click class="p-1 px-2 rounded bg-slate-100 whitespace-nowrap">Gộp lại: <code>h = mul(o, tanh(add(mul(f, c_prev), mul(i, g))))</code></div>
<div class="opacity-70">Bản đầy đủ (lan truyền ngược, huấn luyện, kiểm tra với PyTorch): <code>experiment/lstm_numpy.ipynb</code></div>
</div>

<style>
.code-sm .slidev-code { font-size: .6rem !important; line-height: 1.4 !important; }
</style>

<!--
**[Người 2 · ~2 phút]**

Bốn việc vừa rồi viết thành code thì trông thế nào? Nhóm tự viết lại LSTM chỉ bằng thư viện NumPy, không dùng PyTorch hay Keras, để thấy rằng bên trong không có gì bí ẩn: cả ô LSTM chỉ là ghép năm hàm sơ cấp lại với nhau, hay còn gọi là elementary functions.

Bên trái là năm hàm đó. [click] Hàm thứ nhất: nhân đầu vào với trọng số rồi cộng độ lệch. [click] Hai hàm tiếp theo là sigmoid, cho ra số từ 0 đến 1 giống độ mở của van, và tanh, nén số về khoảng từ trừ 1 đến 1. [click] Hàm thứ tư nhân từng phần tử với nhau. [click] Hàm cuối cùng cộng từng phần tử.

Bên phải là một ô LSTM. [click] Dòng đầu tiên: ghép đầu vào hiện tại với đầu ra của bước trước, rồi nhân với trọng số. Bốn bộ trọng số của bốn phần được đặt cạnh nhau trong một ma trận lớn, nên chỉ cần một phép nhân. [click] Bốn dòng tiếp theo cắt kết quả thành bốn đoạn: ba cổng dùng sigmoid, nội dung mới dùng tanh. [click] Dòng cập nhật bộ nhớ: nhân cổng quên với bộ nhớ cũ, nhân cổng ghi với nội dung mới, rồi cộng lại. Đúng câu "xóa bớt rồi cộng thêm". [click] Dòng đọc ra: cổng đọc nhân với tanh của bộ nhớ.

[click] Hàm cuối cùng chạy ô LSTM lần lượt qua từng bước của chuỗi, và ở mọi bước đều dùng lại đúng một bộ trọng số.

[click] Nếu viết gộp lại, cả ô LSTM chỉ là một biểu thức lồng các hàm sơ cấp vào nhau, như dòng ở dưới cùng.

Vì mỗi hàm sơ cấp đều có đạo hàm đơn giản, nên khi học, ta chỉ cần đi ngược lại đúng các dòng này. Bạn Hiếu sẽ cho xem phần đi ngược ở phần sau.
-->

---

# Thử tay: theo dõi bộ nhớ của một ô LSTM

<LstmSimulator />

<div class="text-xs opacity-70 mt-1">Mô hình đồ chơi để nhìn các cổng: <b>1 lớp, 1 ô LSTM, 1 nơ-ron</b> (bộ nhớ là 1 con số), trọng số nhóm đặt bằng tay, không qua huấn luyện.</div>

<!--
**[Người 2 · ~3 phút] — slide demo, nên để chủ động thời gian**

Bây giờ mình chạy thử một ô LSTM thu nhỏ để các bạn nhìn thấy các cổng hoạt động. Nói rõ kích thước: đây là 1 lớp, chỉ có 1 nơ-ron, nên bộ nhớ chỉ là một con số. Nó chính là hàm ô LSTM vừa viết ở slide trước, chỉ thu nhỏ lại.

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
-->

---

# Ví dụ: các cổng mở / đóng thế nào trên câu mẫu?

<div class="mt-4">
<GateHeatmap />
</div>

<v-clicks>

- Tại **"Pháp"**: <span class="text-green-600">cổng ghi ≈ 1</span> → ghi "Pháp" vào bộ nhớ
- Ở các từ giữa: <span class="text-red-600">cổng quên ≈ 1</span> → giữ nguyên bộ nhớ; <span class="text-green-600">cổng ghi ≈ 0</span> → không ghi đè
- Tại **"tiếng"**: <span class="text-purple-600">cổng đọc ≈ 1</span> → đọc "Pháp" ra để đoán từ tiếp theo

</v-clicks>

<!--
**[Người 2 · ~1 phút]**

Quay lại câu ví dụ ở đầu bài. Giả sử LSTM đã học tốt, thì khi đọc câu này, ở mỗi từ ba cổng sẽ mở hay đóng như trong bảng. Màu càng đậm thì cổng càng mở. Các con số ở đây do nhóm tự đặt để minh họa ý tưởng, không phải đo từ một mô hình thật.

[click] Tại chữ "Pháp", cổng ghi mở để ghi thông tin vào bộ nhớ.

[click] Ở các từ ở giữa, cổng quên mở để giữ nguyên bộ nhớ, còn cổng ghi đóng để không ghi đè lên.

[click] Tại chữ "tiếng", cổng đọc mở để lấy chữ "Pháp" ra và đoán từ tiếp theo.

Đến đây là toàn bộ cách một ô LSTM hoạt động khi đọc câu. Câu hỏi còn lại là: vì sao phép cộng đơn giản này lại giúp LSTM học được những gì ở xa? Mời bạn Hiếu.
-->

---


# Vì sao LSTM học được những gì ở xa?

<div class="text-sm mb-3">Khi học, tín hiệu lỗi phải đi ngược từ chữ "tiếng" về chữ "Pháp". Hai mạng cho nó đi theo hai con đường khác nhau:</div>

<div class="grid grid-cols-2 gap-5">
<div v-click="1" class="p-3 rounded bg-orange-50">

### <span class="text-orange-600">RNN: đi qua bản tóm tắt</span>

Mỗi bước lùi, tín hiệu bị nhân với **trọng số** và **đạo hàm của tanh**, một hệ số khó giữ đúng bằng 1:

<div class="text-center my-2">0.9 × 0.9 × … × 0.9 (50 lần) ≈ <b class="text-orange-600">0.005</b></div>

→ đến chữ "Pháp" thì tín hiệu gần như **mất hết**

</div>
<div v-click="2" class="p-3 rounded bg-blue-50">

### <span class="text-blue-600">LSTM: đi dọc băng chuyền bộ nhớ</span>

Vì bộ nhớ mới = cổng quên × bộ nhớ cũ **+** …, mỗi bước lùi tín hiệu **chỉ nhân với cổng quên**, không có trọng số, không có tanh:

<div class="text-center my-2">0.99 × 0.99 × … × 0.99 (50 lần) ≈ <b class="text-blue-600">0.6</b></div>

→ mạng **tự học** giữ cổng quên gần 1 khi cần nhớ

</div>
</div>

<div v-click="3" class="mt-3 text-center text-sm p-2 rounded bg-slate-100">

LSTM vẫn có những "đường vòng" giống RNN (qua đầu ra của bước trước), các đường này vẫn có thể yếu dần.
Nhưng chỉ cần **còn một con đường thẳng** qua bộ nhớ là tín hiệu vẫn về tới nơi.

</div>

<!--
**[Người 3 · ~2.5 phút] — slide quan trọng nhất của phần này**

Cảm ơn Minh. Phần của mình trả lời câu hỏi: vì sao LSTM học được những gì ở xa, còn RNN thì không?

Khi huấn luyện, mạng đoán sai ở chữ cuối, và tín hiệu lỗi phải đi ngược về tận chữ "Pháp" để mạng biết chữ "Pháp" quan trọng. RNN và LSTM cho tín hiệu này đi theo hai con đường khác nhau.

[click] Ở RNN, tín hiệu chỉ có một đường là đi ngược qua các bản tóm tắt. Mỗi lần lùi một bước, nó bị nhân với trọng số và với đạo hàm của hàm tanh. Hệ số này rất khó giữ đúng bằng 1. Giả sử nó là 0.9, thì sau 50 bước chỉ còn khoảng 0.005, tức nửa phần trăm. Đến chữ "Pháp" thì tín hiệu gần như không còn gì.

[click] Ở LSTM, nhớ lại công thức bộ nhớ của bạn Minh: bộ nhớ mới bằng cổng quên nhân bộ nhớ cũ, cộng phần mới. Vì là phép cộng, khi đi ngược dọc theo băng chuyền bộ nhớ, tín hiệu chỉ bị nhân với cổng quên, không có trọng số, không có tanh. Mà cổng quên là thứ mạng tự học được. Khi cần nhớ, mạng học cách giữ cổng quên gần 1. Với cổng quên bằng 0.99, sau 50 bước tín hiệu vẫn còn khoảng 60 phần trăm.

[click] Nói cho công bằng: LSTM vẫn còn những đường vòng giống RNN, đi qua đầu ra của bước trước, và các đường này vẫn có thể yếu dần. Nhưng tín hiệu là tổng của tất cả các đường. Chỉ cần còn một con đường thẳng qua bộ nhớ là tín hiệu vẫn về tới chữ "Pháp". Đây chính là ý tưởng "giữ nguyên rồi cộng thêm" của Hochreiter năm 1991.
-->

---

# Trong code: con đường thẳng nằm ở đâu?

<div class="text-sm mb-2">Khi học, đi ngược lại đúng các dòng của slide 15. Mỗi hàm sơ cấp có một hàm đạo hàm (quy tắc dây chuyền cho đúng một phép toán):</div>

<div class="grid grid-cols-[1fr_1.35fr] gap-4 code-sm">
<div>

```python
def affine_back(d, x, W): return d @ W.T, x.T @ d, d.sum(0)
def sigmoid_back(d, s):   return d * s * (1 - s)
def tanh_back(d, t):      return d * (1 - t * t)
def mul_back(d, a, b):    return d * b, d * a
def add_back(d):          return d, d
```

<div v-click="2" class="mt-3">

<div class="text-xs text-orange-600 font-bold mb-1">RNN, mỗi bước lùi:</div>

```python
dz = tanh_back(dh, h)       # qua đạo hàm tanh
dx, dh_prev, dW, db = affine_back(dz, ...)  # qua trọng số W
```

<div class="text-xs mt-1">→ tín hiệu về bước trước <b>luôn</b> bị nhân với đạo hàm tanh và trọng số</div>

</div>
</div>
<div>

<div class="text-xs text-blue-600 font-bold mb-1">LSTM, mỗi bước lùi (đi ngược từ dưới lên):</div>

```python {all|5}
def lstm_cell_back(dh, dc, cache):
    do, dtc = mul_back(dh, o, tanh_c)           # h = o * tanh(c)
    dc = dc + tanh_back(dtc, tanh_c)
    dfc, dig = add_back(dc)                     # c = f*c_prev + i*g
    df, dc_prev = mul_back(dfc, f, c_prev)      # dc_prev = dfc * f
    di, dg = mul_back(dig, i, g)
    dz = concat(sigmoid_back(di, i), sigmoid_back(df, f),
                tanh_back(dg, g), sigmoid_back(do, o))
    dx, dh_prev, dW, db = affine_back(dz, ...)
    return dx, dh_prev, dc_prev, dW, db
```

<div v-click="1" class="text-xs mt-1 p-2 rounded bg-blue-50">Dòng 5: tín hiệu về bộ nhớ cũ = tín hiệu ở bộ nhớ mới × <b>cổng quên</b>. Không có trọng số, không có tanh. Đây là con đường thẳng của slide 18.</div>

</div>
</div>

<div v-click="3" class="text-xs mt-2 text-center">Kiểm tra: đạo hàm tự viết khớp phép tính số (sai số ~10⁻⁹) và khớp PyTorch (sai số ~10⁻¹⁶). Code trên đã rút gọn phần lưu dữ liệu trung gian.</div>

<style>
.code-sm .slidev-code { font-size: .6rem !important; line-height: 1.4 !important; }
</style>

<!--
**[Người 3 · ~2 phút]**

Ở slide 15, bạn Minh đã cho xem phần tính xuôi của LSTM viết bằng NumPy. Khi học, máy phải đi ngược lại để tính tín hiệu lỗi. Vì ô LSTM chỉ là ghép năm hàm sơ cấp, nên phần đi ngược cũng chỉ là năm hàm đạo hàm, như bên trái. Mỗi hàm nhận tín hiệu lỗi ở đầu ra và trả về tín hiệu lỗi ở đầu vào. Ví dụ đạo hàm của phép cộng là chia đều tín hiệu cho cả hai vế, đạo hàm của phép nhân là nhân chéo.

Bên phải là phần đi ngược của một ô LSTM. Các bạn để ý nó đi ngược đúng các dòng của phần tính xuôi, từ dòng đọc ra, lên dòng cập nhật bộ nhớ, lên các cổng, cuối cùng là phép nhân trọng số.

[click] Dòng quan trọng nhất là dòng thứ năm. Tín hiệu lỗi gửi về bộ nhớ của bước trước bằng tín hiệu ở bộ nhớ hiện tại nhân với cổng quên. Chỉ vậy thôi: không có trọng số, không có tanh. Đây chính là con đường thẳng mà mình vừa nói ở slide trước, giờ các bạn thấy nó nằm ở đâu trong code.

[click] So với RNN: mỗi bước lùi chỉ có hai dòng, nhưng tín hiệu về bước trước luôn phải đi qua đạo hàm của tanh rồi qua trọng số. Không có đường nào tránh được hai thứ đó.

[click] Để chắc code đúng, nhóm kiểm tra hai cách: so với đạo hàm tính bằng cách nhích từng trọng số một chút rồi xem sai số thay đổi bao nhiêu, và so với PyTorch. Cả hai đều khớp gần như tuyệt đối. Code trên slide đã rút gọn phần lưu dữ liệu trung gian, bản đầy đủ nằm trong notebook.
-->

---

# Đo trên mạng thật: tín hiệu lỗi về được bao xa?

<div class="text-xs leading-snug p-2 rounded bg-slate-100 mb-3">
<div><b>Mô hình</b> (viết bằng NumPy, code ở slide 15 và 19): mỗi bước nhận <b>2 số</b> → <b>1 lớp</b> RNN hoặc LSTM, <b>64 nơ-ron ẩn</b> → <b>1 nơ-ron</b> đầu ra. LSTM có 17 217 trọng số, RNN có 4 353.</div>
<div><b>Huấn luyện</b> 5000 lần cập nhật trên bài toán cộng, chuỗi <b>100 bước</b>: phải nhớ một số được đánh dấu ở nửa đầu chuỗi (chi tiết ở thí nghiệm 3). Đo trên 2000 mẫu kiểm tra.</div>
</div>

<SignalReach />

<!--
**[Người 3 · ~2 phút]**

Hai slide trước là lý thuyết và code. Bây giờ mình đo thật. Nói rõ mô hình: mỗi bước nhận vào 2 số, đi qua 1 lớp RNN hoặc 1 lớp LSTM có 64 nơ-ron ẩn, cuối cùng là 1 nơ-ron cho ra kết quả. LSTM có khoảng 17 nghìn trọng số, RNN khoảng 4 nghìn 4, vì LSTM có 4 bộ trọng số. Cả hai được huấn luyện bằng chính code NumPy vừa rồi, trên bài toán cộng với chuỗi 100 bước. Bài toán này mình sẽ nói kỹ ở thí nghiệm 3; ở đây chỉ cần biết mô hình phải nhớ một con số ở nửa đầu chuỗi cho tới cuối chuỗi.

Cách đo: ở bước cuối, gửi một tín hiệu lỗi ngược về, rồi xem ở từng bước trong quá khứ còn lại bao nhiêu phần trăm so với ở bước cuối. Trục dọc chia theo cấp số nhân, mỗi vạch nhỏ đi một trăm nghìn lần.

**(Demo)** Bấm "Lúc mới khởi tạo", chưa học gì. Kéo thanh về 50 bước: RNN gần như bằng 0, LSTM cũng đã rất nhỏ, khoảng năm phần trăm của một phần trăm. Lúc chưa học, LSTM cũng quên, vì cổng quên mới ở khoảng 0.73.

Bấm "Sau khi học". Đường xanh của LSTM gần như nằm ngang: từ khoảng 10 bước trở về trước, tín hiệu giữ ở khoảng 20 đến 25 phần trăm, kéo tới 99 bước vẫn còn. Mạng đã tự học cách giữ bộ nhớ: trong 64 nơ-ron, có một nơ-ron có cổng quên trung bình 0.96, cao hẳn các nơ-ron còn lại, nó làm nhiệm vụ mang con số được đánh dấu tới cuối chuỗi. Đường cam của RNN thì không đổi: lùi 50 bước chỉ còn khoảng một phần tỷ của một phần trăm.

Kết quả học khớp với điều đó: sai số của RNN đứng ở mức đoán mò, còn LSTM nhỏ hơn khoảng 160 lần. Đây cũng là câu trả lời cho slide 5: hiện tượng tín hiệu lỗi biến mất không chỉ là công thức, mà đo được trên mạng thật.
-->

---

# Về sau, ResNet cũng dùng lại ý tưởng này

<div class="grid grid-cols-2 gap-6 mt-4">
<div class="p-4 rounded bg-amber-50">

### ResNet (2015)
*Residual Network* – mạng nhận dạng ảnh có lối tắt, sâu hơn 100 lớp

<div class="text-center my-2">đầu ra = <b>đầu vào</b> + phần thay đổi</div>

<div class="text-sm opacity-70 text-center">

$x_{l+1} = x_l + F(x_l)$

</div>

Lối tắt nối **lớp này sang lớp sau** (theo chiều sâu)

</div>
<div class="p-4 rounded bg-blue-50">

### LSTM (1997)

<div class="text-center my-2">bộ nhớ mới = <b>cổng quên × bộ nhớ cũ</b> + phần mới</div>

<div class="text-sm opacity-70 text-center">

$c_t = f_t \odot c_{t-1} + i_t \odot \tilde c_t$

</div>

Lối tắt nối **bước này sang bước sau** (theo thời gian); khi cổng quên = 1 thì giống hệt ResNet

</div>
</div>

<div v-click class="mt-4 text-center">
Cùng một nguyên lý: <b>phép cộng</b> tạo một lối tắt để tín hiệu lỗi đi thẳng, không bị nhân qua trọng số.<br>
<span class="text-sm opacity-80">LSTM (1997) có ý tưởng này trước ResNet (2015) tận 18 năm.</span>
</div>

<!--
**[Người 3 · ~1.5 phút]**

Ý tưởng "giữ nguyên rồi cộng thêm" này hay đến mức về sau được dùng lại ở chỗ khác. Ví dụ nổi tiếng nhất là ResNet, viết tắt của Residual Network. Lớp mình chưa học mô hình này nên mình chỉ nói ý chính: ResNet là một mạng nhận dạng ảnh ra đời năm 2015. Trước đó, mạng càng nhiều lớp thì càng khó huấn luyện, cũng vì tín hiệu lỗi yếu dần khi đi ngược qua từng lớp, giống hệt vấn đề của RNN.

Bên trái là cách ResNet giải quyết. Mỗi khối tính đầu ra bằng đầu vào cộng với một phần thay đổi. Nhờ phép cộng đó, tín hiệu lỗi luôn có một lối tắt đi thẳng qua khối mà không bị nhân với trọng số. Nhờ vậy ResNet huấn luyện được mạng hơn 100 lớp.

Bên phải là LSTM: bộ nhớ mới bằng cổng quên nhân bộ nhớ cũ, cộng phần mới. Khi cổng quên bằng 1, nó giống hệt lối tắt của ResNet.

[click] Hai mô hình dùng cùng một nguyên lý: phép cộng tạo ra một lối tắt cho tín hiệu lỗi. ResNet làm theo chiều sâu, từ lớp này sang lớp sau. LSTM làm theo chiều thời gian, từ bước này sang bước sau. Và LSTM có ý tưởng này trước ResNet tận 18 năm.

Lý thuyết là vậy. Bây giờ mình kiểm tra trên dữ liệu thật.
-->

---

# Thí nghiệm 1: đơn hàng, chuỗi ngắn (8 bước)

<div class="small mb-1">

**Dữ liệu (từ bài A6):** 407 nghìn đơn hàng của một sàn thương mại điện tử ở Pakistan (2016–2018) · **Bài toán:** khi khách đặt đơn, đoán đơn sẽ được **giao xong / bị hủy / bị trả lại**, dựa trên **8 đơn gần nhất** của khách · học trên đơn đến hết 2017, kiểm tra trên đơn 04–08/2018

</div>

<div class="p-1 px-2 rounded bg-slate-100 small mb-2">

**Công bằng:** hai mô hình giống hệt nhau về kích thước và cách huấn luyện, **chỉ khác** RNN ↔ LSTM · mỗi mô hình huấn luyện **5 lần** với khởi tạo ngẫu nhiên khác nhau (*seed*), lấy trung bình

</div>

<div class="exp">

| Kết quả trên dữ liệu kiểm tra (trung bình 5 lần) | <span class="crnn">RNN</span> | <span class="clstm">LSTM</span> | LSTM tốt hơn |
|---|---|---|---|
| Tỉ lệ đoán đúng (*accuracy*) ↑ | 66.3% | **66.6%** | **5/5 lần** |
| ROC-AUC¹ ↑ | 0.794 | **0.797** | **5/5 lần** |
| Macro F1² ↑ | **0.511** | 0.508 | 1/5 lần |
| Tỉ lệ tìm ra đơn bị trả lại ↑ | **9.2%** | 8.1% | 1/5 lần |
| Số trọng số · thời gian mỗi vòng học | 10,307 · 4.3 giây | 40,931 · 8.8 giây | |

</div>

<div class="text-xs opacity-60 mt-1">¹ ROC-AUC (<i>Area Under the ROC Curve</i>): khả năng xếp đơn đúng loại lên trước, 0.5 = đoán mò, 1 = hoàn hảo · ² Macro F1: điểm trung bình của 3 loại đơn, coi loại hiếm (trả lại) quan trọng ngang loại phổ biến</div>

<div v-click class="mt-2 p-2 rounded bg-amber-50 small">

**LSTM tốt hơn, nhưng rất ít** (+0.3% tỉ lệ đoán đúng) và **không phải ở mọi chỉ số** → chuỗi chỉ có 8 đơn, thông tin quan trọng nằm ở đơn đang đặt và vài đơn gần nhất, **không có gì ở xa** để bộ nhớ dài hạn phát huy

</div>

<!--
**[Người 3 · ~1.5 phút]**

Nhóm so sánh LSTM với RNN trên ba thí nghiệm, xếp theo độ dài chuỗi tăng dần: 8 bước, 60 bước, rồi 100 bước. Ở mọi thí nghiệm, hai mô hình giống hệt nhau về kích thước và cách huấn luyện; điểm khác duy nhất là thay RNN bằng LSTM. Mỗi mô hình được huấn luyện 5 lần với khởi tạo ngẫu nhiên khác nhau, để chắc chắn kết quả không phải do may rủi.

Hai thí nghiệm đầu dùng lại đúng hai bộ dữ liệu của bài A6 đã làm. Mô hình RNN ở đây cũng chính là mô hình RNN của bài A6; nhóm chỉ thay RNN bằng LSTM rồi so sánh.

Thí nghiệm thứ nhất dùng bộ dữ liệu đơn hàng của bài A6: khoảng 400 nghìn đơn hàng của một sàn thương mại điện tử ở Pakistan. Khi khách đặt một đơn, mô hình đoán đơn đó sẽ được giao xong, bị hủy, hay bị trả lại, dựa trên 8 đơn gần nhất của khách đó.

Kết quả: LSTM đoán đúng nhiều hơn, và ROC-AUC cao hơn. ROC-AUC là chỉ số đo khả năng xếp đúng loại đơn lên trước, 0.5 là đoán mò, 1 là hoàn hảo. LSTM hơn ở cả 5 lần chạy. Nhưng mức chênh rất nhỏ: chỉ khoảng 0.3 phần trăm. Và ở hai chỉ số liên quan tới đơn bị trả lại, LSTM còn kém hơn một chút.

[click] Lý do là chuỗi ở đây rất ngắn, chỉ 8 đơn. Thông tin quan trọng nằm ở chính đơn đang đặt và vài đơn gần nhất. Không có gì ở xa cần nhớ, nên bộ nhớ dài hạn của LSTM không có đất dụng võ.

Vậy nếu chuỗi dài hơn thì sao? Đó là thí nghiệm thứ hai.
-->

---

# Thí nghiệm 2: cổ phiếu, chuỗi dài (60 bước)

<div class="small mb-2">

**Dữ liệu (từ bài A6):** giá theo ngày của Apple, Microsoft, HP · **Bài toán:** dự báo **ngày mai giá dao động mạnh hay nhẹ** (khoảng cách giữa giá cao nhất và thấp nhất trong ngày) từ **60 ngày giao dịch** trước đó · học trên 1995–2016, kiểm tra trên 2020–2025 · cách so sánh giống thí nghiệm 1

</div>

<div class="grid grid-cols-[1.05fr_1fr] gap-4 items-start">
<div class="exp">

| Kiểm tra (trung bình 5 lần) | <span class="crnn">RNN</span> | <span class="clstm">LSTM</span> | LSTM tốt hơn |
|---|---|---|---|
| MAE¹ ↓ | 0.727 | **0.709** | **5/5 lần** |
| RMSE² ↓ | 1.163 | **1.139** | **5/5 lần** |
| R²³ ↑ | 0.348 | **0.377** | **5/5 lần** |
| Đoán đúng ngày mai mạnh hơn hay nhẹ hơn ↑ | 69.7% | **70.8%** | **5/5 lần** |
| Số trọng số | 7,873 | 31,393 | |
| Thời gian mỗi vòng học | 2.0 giây | 2.9 giây | |

</div>
<div>

<SeedStrip task="stock" :metrics="['MAE', 'RMSE', 'R2 log', 'direction acc']" />

</div>
</div>

<div class="text-xs opacity-60 mt-1">¹ MAE (<i>Mean Absolute Error</i>): sai lệch trung bình, tính bằng điểm % · ² RMSE (<i>Root Mean Squared Error</i>): giống MAE nhưng phạt nặng các lần đoán sai nhiều · ³ R²: mô hình giải thích được bao nhiêu phần sự lên xuống, 1 = hoàn hảo</div>

<div v-click class="mt-2 p-2 rounded bg-blue-50 small">

**LSTM tốt hơn RNN ở mọi chỉ số, cả 5/5 lần:** sai số giảm 2.4%; lần chạy **tệ nhất** của LSTM vẫn tốt hơn lần **tốt nhất** của RNN. Kết quả của LSTM cũng **ổn định hơn** giữa các lần chạy

</div>

<!--
**[Người 3 · ~2 phút]**

Thí nghiệm thứ hai là bộ dữ liệu cổ phiếu của bài A6: giá cổ phiếu theo ngày của Apple, Microsoft và HP. Mô hình nhìn 60 ngày giao dịch gần nhất và dự báo ngày mai giá sẽ dao động mạnh hay nhẹ, tức khoảng cách giữa giá cao nhất và thấp nhất trong ngày. Nhóm chọn bài toán này vì độ dao động có tính "nhớ": giai đoạn sôi động thường kéo dài nhiều tuần. Mô hình học trên dữ liệu từ 1995 đến 2016, và được kiểm tra trên 6 năm từ 2020 đến 2025.

Bảng bên trái là trung bình 5 lần chạy. LSTM tốt hơn ở mọi chỉ số. Sai số trung bình, MAE, giảm từ 0.727 xuống 0.709. Chỉ số R², tức mô hình giải thích được bao nhiêu phần sự lên xuống, tăng từ 0.35 lên 0.38. Và LSTM đoán đúng ngày mai dao động mạnh hơn hay nhẹ hơn nhiều hơn khoảng 1 phần trăm.

**(Demo: bấm qua các chỉ số)** Hình bên phải vẽ riêng từng lần chạy, mỗi chấm là một lần. Năm chấm xanh của LSTM và năm chấm cam của RNN tách hẳn nhau, không chồng lên nhau.

[click] Nghĩa là: lần chạy tệ nhất của LSTM vẫn tốt hơn lần chạy tốt nhất của RNN. Kết quả của LSTM cũng ổn định hơn giữa các lần. Cái giá phải trả là LSTM có gấp 4 lần trọng số và chạy chậm hơn khoảng một lần rưỡi.

Vậy vì sao LSTM tốt hơn ở đây?
-->

---

# Vì sao? LSTM dùng được cả những ngày ở xa

<StockReach />

<div v-click class="mt-2 p-2 rounded bg-blue-50 small">

**RNN:** ngày đầu tiên trong 60 ngày chỉ còn ảnh hưởng **0.014%** so với hôm nay → 40 ngày cũ gần như bị bỏ qua; cho xem 120 ngày cũng ra kết quả **y hệt** 60 ngày (đúng hiện tượng tín hiệu lỗi biến mất ở slide 5). **LSTM:** ngày đầu tiên vẫn còn ảnh hưởng **1.1%**, gấp khoảng 75 lần; **25%** ảnh hưởng đến từ 40 ngày cũ. Thông tin quan trọng nhất vẫn nằm ở khoảng 20 ngày gần đây, nhưng LSTM tốt hơn RNN **dù cho xem bao nhiêu ngày**

</div>

<!--
**[Người 3 · ~1.5 phút]**

Để trả lời, nhóm đo xem mỗi ngày trong 60 ngày ảnh hưởng tới dự báo bao nhiêu: thay đổi dữ liệu của một ngày một chút, rồi xem dự báo thay đổi bao nhiêu.

**(Demo: tab 1)** Trục ngang là 60 ngày, ngày 60 là hôm nay. Trục dọc là mức ảnh hưởng so với hôm nay. Đường cam của RNN tụt rất nhanh: ngày đầu tiên chỉ còn ảnh hưởng khoảng một phần mười nghìn so với hôm nay. Đường xanh của LSTM tụt chậm hơn nhiều, ngày đầu tiên vẫn còn khoảng một phần trăm, gấp khoảng 75 lần RNN. Bảng bên phải: chỉ 3.7 phần trăm ảnh hưởng của RNN đến từ 40 ngày cũ, còn ở LSTM là 25 phần trăm.

**(Demo: tab 2)** Nhóm thử xóa dữ liệu của các ngày cũ rồi cho dự báo lại. Với RNN, giữ 20 ngày hay 60 ngày cũng như nhau, các ngày cũ không đóng góp gì. Với LSTM thì ngược lại: nếu chỉ giữ 5 hay 10 ngày, LSTM còn sai nhiều hơn RNN, vì nó đã quen dựa vào những ngày cũ, bỏ đi thì nó mất thông tin. Từ 20 ngày trở lên thì LSTM tốt hơn RNN.

**(Demo: tab 3)** Nhóm huấn luyện lại với 20, 60 và 120 ngày. RNN với 120 ngày cho kết quả y hệt 60 ngày: 60 ngày thêm vào hoàn toàn vô ích, vì tín hiệu lỗi không đi ngược xa được tới đó. Một điểm cần lưu ý: với dữ liệu này, cho xem 20 ngày là tốt nhất ở cả hai mô hình, vì thông tin quan trọng nhất nằm trong khoảng một tháng gần đây. Nhưng ở cả ba trường hợp, LSTM đều tốt hơn RNN.

[click] Tóm lại: RNN gặp đúng hiện tượng tín hiệu lỗi biến mất ở slide 5, còn LSTM nhờ con đường thẳng qua bộ nhớ nên vẫn nghe được tín hiệu từ những ngày xa.
-->

---

# Thí nghiệm 3: phải nhớ rất xa (bài toán cộng)

<div class="text-sm mb-2"><b>Bài toán cộng</b> (<i>Adding Problem</i>, có trong bài báo 1997): một chuỗi gồm nhiều số ngẫu nhiên từ 0 đến 1, trong đó <b>đúng 2 số được đánh dấu</b>, một số ở nửa đầu, một số ở nửa sau → mô hình phải trả lời <b>tổng của 2 số đó</b>. Chuỗi 100 số nghĩa là phải nhớ số đầu tiên qua ít nhất 50 bước.</div>

<CompareChart />

<div v-click class="mt-2 p-2 rounded bg-blue-50 small">

**Chuỗi 100 số:** RNN đứng yên ở mức "đoán mò" suốt quá trình học, vì tín hiệu lỗi không về tới số được đánh dấu đầu tiên. LSTM sau một thời gian thì "vỡ lẽ" và giảm sai số xuống gần 0.

**Tổng hợp 3 thí nghiệm:** chuỗi càng dài, LSTM càng hơn RNN rõ: 8 bước → hơn rất ít · 60 bước → hơn ở mọi chỉ số, cả 5/5 lần · 100 bước → RNN không học được, LSTM học được

</div>

<!--
**[Người 3 · ~1.5 phút]**

Thí nghiệm thứ ba đẩy độ dài lên mức cực đoan. Đây là bài toán cộng, một bài toán kinh điển có trong chính bài báo năm 1997. Mô hình đọc một chuỗi số ngẫu nhiên, trong đó có đúng hai số được đánh dấu: một số ở nửa đầu, một số ở nửa sau. Nhiệm vụ là trả lời tổng của hai số đó. Với chuỗi 100 số, mô hình phải nhớ số được đánh dấu đầu tiên qua ít nhất 50 bước.

Đường nét đứt là mức "đoán mò": luôn trả lời tổng bằng 1, là giá trị trung bình. Mô hình nào nằm trên đường này là chưa học được gì.

**(Bấm "Chuỗi 10 số")** Với chuỗi ngắn, cả hai mô hình đều học được, sai số giảm xuống dưới mức đoán mò. Giống thí nghiệm 1: khi không cần nhớ xa thì RNN cũng làm được.

**(Bấm "Chuỗi 100 số")** Với chuỗi dài, đường cam của RNN nằm yên ở mức đoán mò suốt quá trình học: RNN không học được gì. LSTM cũng loay hoay ở mức đó một thời gian, rồi đến khoảng giữa quá trình thì "vỡ lẽ", sai số tụt xuống gần 0. Sai số cuối cùng của LSTM thấp hơn RNN khoảng 50 lần.

[click] Tóm lại ba thí nghiệm: chuỗi càng dài thì LSTM càng hơn RNN rõ. 8 bước: hơn rất ít. 60 bước: hơn ở mọi chỉ số, cả 5 lần chạy. 100 bước: RNN không học được, LSTM học được. Đổi lại, LSTM có gấp 4 lần trọng số và chạy chậm hơn. Sang phần cuối, bạn An sẽ nói về cách dùng LSTM trong thực tế.
-->

---


# Hai mẹo khi tự cài đặt LSTM

<div class="grid grid-cols-2 gap-6 mt-2">
<div>

### 1 · Cho cổng quên "hơi mở" từ đầu

Lúc mới khởi tạo, cổng quên ≈ 0.5 → mỗi bước mất một nửa bộ nhớ. Đặt sẵn độ lệch (*bias*) của cổng quên = 1 → cổng quên ≈ 0.73 → mạng **có xu hướng nhớ** ngay từ đầu

```python
for name, p in lstm.named_parameters():
    if 'bias_ih' in name:
        h = p.size(0) // 4
        # PyTorch xếp các cổng theo thứ tự:
        # ghi, quên, nội dung mới, đọc
        p.data[h:2*h].fill_(1.0)  # phần của cổng quên
```

</div>
<div v-click>

### 2 · Vẫn cần "cắt" tín hiệu lỗi quá lớn

Con đường thẳng qua bộ nhớ chữa được việc tín hiệu **biến mất**, nhưng các "đường vòng" vẫn có thể làm tín hiệu **bùng nổ** → giới hạn độ lớn tín hiệu lỗi (*gradient clipping*)

```python
loss.backward()
torch.nn.utils.clip_grad_norm_(   # nếu tín hiệu lớn hơn 1
    model.parameters(), max_norm=1.0)  # thì thu nhỏ lại
optimizer.step()
```

</div>
</div>

<!--
**[Người 4 · ~1.5 phút]**

Cảm ơn Hiếu. Phần cuối nói về cách dùng LSTM trong thực tế, bắt đầu từ hai mẹo khi cài đặt, rút ra trực tiếp từ những gì các bạn vừa nghe.

Mẹo thứ nhất: cho cổng quên hơi mở ngay từ đầu. Lúc mới khởi tạo, trọng số còn nhỏ nên cổng quên thường nằm quanh 0.5, tức mỗi bước mạng mất một nửa bộ nhớ, và tín hiệu lỗi cũng yếu đi một nửa mỗi bước. Nếu đặt sẵn độ lệch, tức bias, của cổng quên bằng 1, thì cổng quên khởi đầu ở khoảng 0.73, mạng có xu hướng nhớ ngay từ đầu. Đây là khuyến nghị của một nhóm nghiên cứu ở Google năm 2015. Trong PyTorch, độ lệch của bốn phần được xếp liền nhau theo thứ tự: cổng ghi, cổng quên, nội dung mới, cổng đọc, nên phần của cổng quên là đoạn thứ hai.

[click] Mẹo thứ hai: vẫn phải giới hạn độ lớn của tín hiệu lỗi, gọi là gradient clipping. Con đường thẳng qua bộ nhớ chỉ chữa được việc tín hiệu biến mất. Các đường vòng giống RNN vẫn có thể làm tín hiệu phình to, tức bùng nổ. Hàm clip grad norm làm đúng một việc: nếu tín hiệu lớn hơn ngưỡng, ở đây là 1, thì thu nhỏ nó lại. Nhờ đó quá trình học không bị hỏng giữa chừng. Cả ba thí nghiệm của nhóm đều dùng cách này.
-->

---

# Xếp chồng và đọc hai chiều

<div class="grid grid-cols-2 gap-6">
<div>

### Xếp chồng (*stacked*): nhiều lớp LSTM
Đầu ra từng bước của lớp 1 là đầu vào của lớp 2 → lớp trên hiểu ý nghĩa "trừu tượng" hơn, giống CNN nhiều lớp

### Hai chiều (*bidirectional*)
Một LSTM đọc **từ trái sang phải**, một LSTM đọc **từ phải sang trái**, rồi ghép kết quả → mỗi từ được hiểu nhờ **cả phần trước lẫn phần sau** của câu

<div v-click class="text-red-600 text-sm mt-2">⚠️ Không dùng hai chiều khi phải dự đoán tương lai (<b>dự báo giá</b>, viết tiếp câu): lúc học mô hình được "nhìn trước" tương lai, nhưng lúc dùng thật thì không có → kết quả học được là ảo (rò rỉ dữ liệu)</div>

</div>
<div>

```python
lstm = nn.LSTM(input_size=d, hidden_size=h,
               num_layers=2,          # xếp chồng 2 lớp
               bidirectional=True,    # đọc 2 chiều
               batch_first=True,
               dropout=0.2)           # chống học vẹt

output, (h_n, c_n) = lstm(x)
# output: đầu ra từng bước của lớp trên cùng,
#         mỗi bước 2×h số (nối 2 chiều)
# h_n, c_n: đầu ra và bộ nhớ ở bước cuối
```

</div>
</div>

<!--
**[Người 4 · ~1.5 phút]**

Hai cách mở rộng LSTM hay dùng nhất.

Thứ nhất là xếp chồng nhiều lớp. Đầu ra ở từng bước của lớp LSTM thứ nhất trở thành đầu vào của lớp thứ hai. Giống CNN nhiều lớp, lớp càng cao thì hiểu ý nghĩa càng trừu tượng.

Thứ hai là đọc hai chiều. Dùng hai LSTM riêng: một cái đọc câu từ trái sang phải, một cái đọc từ phải sang trái, rồi ghép kết quả lại. Như vậy mỗi từ được hiểu nhờ cả phần trước lẫn phần sau của câu. Cách này hợp với các bài toán có sẵn cả câu, ví dụ phân loại một bình luận là khen hay chê.

[click] Nhưng lưu ý: không dùng hai chiều khi phải dự đoán tương lai, ví dụ dự báo giá cổ phiếu như thí nghiệm 2. Lúc học, mô hình sẽ được nhìn trước dữ liệu tương lai, nhưng lúc dùng thật thì không có. Kết quả trong lúc học sẽ đẹp một cách ảo. Lỗi này gọi là rò rỉ dữ liệu.

Bên phải là code PyTorch. Muốn có hai cách mở rộng này, chỉ cần đặt số lớp bằng 2 và bật chế độ hai chiều. Thêm một tham số nữa là dropout, giúp mô hình đỡ học vẹt: trong lúc học, mỗi lần mô hình bỏ qua ngẫu nhiên khoảng 20 phần trăm tín hiệu đi từ lớp dưới lên lớp trên, nên nó không thể chỉ dựa vào vài đường cố định.

Một chỗ hay gây lỗi khi viết code: đọc hai chiều thì ở mỗi bước có hai kết quả, một của chiều xuôi, một của chiều ngược, và PyTorch nối chúng lại thành một. Nên đầu ra dài gấp đôi: nếu mỗi chiều cho 64 số thì đầu ra có 128 số. Lớp phía sau phải được khai báo nhận 128 số chứ không phải 64, nếu không chương trình sẽ báo lỗi sai kích thước.
-->

---

# LSTM dùng để làm gì, và giới hạn của nó

<div class="grid grid-cols-2 gap-6">
<div>

### Ứng dụng
- **Cả chuỗi → một nhãn** (*many-to-one*): bình luận khen hay chê, đơn hàng có bị hủy không
- **Mỗi bước một nhãn** (*many-to-many*): nhận dạng giọng nói, gán từ loại
- **Chuỗi → chuỗi** (*seq2seq*): dịch máy
- **Chuỗi thời gian**: dự báo nhu cầu, cảm biến, tài chính
- **Sinh dữ liệu**: viết văn bản, sáng tác nhạc, chữ viết tay

</div>
<div>

### Giới hạn
- Phải đọc **lần lượt**: bước sau phải chờ bước trước → **không chạy song song** được trên GPU (*Graphics Processing Unit* – card đồ họa)
- Chuỗi rất dài (hàng nghìn từ): mọi thứ phải nhồi vào **một bộ nhớ có kích thước cố định**
- Gấp **4 lần** trọng số so với RNN

<div v-click class="mt-4 p-3 rounded bg-slate-100">

→ **Attention** (2014) → **Transformer** (2017): bỏ cách đọc lần lượt, mỗi từ **nhìn thẳng** vào mọi từ khác trong câu

<span class="text-sm">Dòng phát triển: RNN (1990) → LSTM (1997) → Transformer (2017)</span>

</div>
</div>
</div>

<!--
**[Người 4 · ~1.5 phút]**

LSTM được dùng ở đâu? Có thể chia theo dạng đầu vào và đầu ra. Cả chuỗi cho ra một nhãn, ví dụ đọc một bình luận rồi đoán là khen hay chê, hoặc như thí nghiệm 1 của nhóm: đọc lịch sử đơn hàng rồi đoán đơn có bị hủy không. Mỗi bước một nhãn, ví dụ nhận dạng giọng nói. Chuỗi sang chuỗi, ví dụ dịch máy: một LSTM đọc câu nguồn, một LSTM viết câu đích, Google Dịch từng dùng cách này. Ngoài ra LSTM được dùng rất nhiều cho chuỗi thời gian và để sinh dữ liệu như văn bản hay nhạc.

LSTM cũng có giới hạn. Lớn nhất là phải đọc lần lượt: muốn tính bước sau thì phải có kết quả bước trước, nên không tận dụng được sức chạy song song của card đồ họa. Với chuỗi rất dài, mọi thứ phải nhồi vào một bộ nhớ có kích thước cố định. Và nó có gấp 4 lần trọng số so với RNN.

[click] Đó là lý do Attention, rồi Transformer năm 2017 ra đời: bỏ cách đọc lần lượt, mỗi từ nhìn thẳng vào mọi từ khác trong câu. Đó là chủ đề cho một buổi khác. Dòng phát triển của các mô hình xử lý chuỗi là: RNN, rồi LSTM, rồi Transformer.
-->

---

# Tổng kết

<div class="grid grid-cols-3 gap-6 text-center mt-10">
<div class="p-5 rounded bg-blue-50">

<b>🧠 Bộ nhớ dài hạn</b> (trạng thái ô nhớ): chạy thẳng như băng chuyền, chỉ bị xóa bớt và cộng thêm

</div>
<div class="p-5 rounded bg-green-50">

<b>🚪 3 cổng</b>: quên · ghi · đọc, mỗi cổng là một "cái van" mở từ 0 đến 1

</div>
<div class="p-5 rounded bg-amber-50">

<b>➕ Phép cộng</b>: tạo con đường thẳng cho tín hiệu lỗi → học được những gì ở xa; chuỗi càng dài càng hơn RNN

</div>
</div>

<!--
**[Người 4 · ~1.5 phút]**

Tóm lại bài hôm nay bằng ba ý.

Một: LSTM thêm vào RNN một bộ nhớ dài hạn, gọi là trạng thái ô nhớ. Nó chạy thẳng qua các bước như một băng chuyền, chỉ bị xóa bớt và cộng thêm.

Hai: ba cổng quên, ghi và đọc, mỗi cổng là một cái van mở từ 0 đến 1, quyết định xóa bao nhiêu, ghi bao nhiêu, đọc ra bao nhiêu.

Ba: nhờ phép cộng trong cách cập nhật bộ nhớ, tín hiệu lỗi có một con đường thẳng để đi ngược về quá khứ, nên LSTM học được những gì ở xa. Ba thí nghiệm của nhóm cho thấy chuỗi càng dài thì LSTM càng hơn RNN rõ.

Để kiểm tra lại, mình có bốn câu hỏi trắc nghiệm nhanh.
-->

---

# Câu hỏi 1/4

<QuizSet :n="1" />

<!--
**[Người 4 · ~0.5 phút]**

Mời các bạn chọn đáp án, mình sẽ bấm vào đáp án được chọn: ô chuyển xanh là đúng, đỏ là sai; nếu sai thì mời bạn khác chọn lại. Khi chọn đúng, lời giải hiện ra bên dưới.

**(Demo)** Câu 1: ba cổng của LSTM là gì? Đáp án A: cổng quên, cổng ghi, cổng đọc. Các đáp án còn lại là thành phần của mô hình khác: tích chập và gộp thuộc CNN; truy vấn, khóa, giá trị thuộc Transformer.
-->

---

# Câu hỏi 2/4

<QuizSet :n="2" />

<!--
**[Người 4 · ~0.5 phút]**

**(Demo)** Câu 2: các cổng dùng hàm kích hoạt nào? Đáp án B, sigmoid, vì cổng cần một con số từ 0 đến 1, giống độ mở của cái van: 0 là đóng hẳn, 1 là mở hết.
-->

---

# Câu hỏi 3/4

<QuizSet :n="3" />

<!--
**[Người 4 · ~0.5 phút]**

**(Demo)** Câu 3: thành phần nào là bộ nhớ dài hạn của LSTM? Đáp án C, trạng thái ô nhớ. Nó chỉ bị xóa bớt và cộng thêm, không bị viết lại toàn bộ ở mỗi bước, nên giữ được thông tin lâu.
-->

---

# Câu hỏi 4/4

<QuizSet :n="4" />

<!--
**[Người 4 · ~0.5 phút]**

**(Demo)** Câu 4: LSTM sinh ra để khắc phục vấn đề nào của RNN? Đáp án A: tín hiệu lỗi yếu dần rồi biến mất, nên RNN không học được mối liên hệ giữa hai từ cách xa nhau.
-->

---
layout: center
class: text-center
---

# Cảm ơn thầy và các bạn đã lắng nghe!

<div class="mt-8 text-sm opacity-70">
Tài liệu: Hochreiter & Schmidhuber (1997) · Gers và cộng sự (2000) · Graves & Schmidhuber (2005) · Bengio và cộng sự (1994) · colah.github.io/posts/2015-08-Understanding-LSTMs
</div>

<!--
**[Cả nhóm · ~2 phút]**

Phần trình bày của nhóm mình đến đây là kết thúc. Cảm ơn thầy và các bạn đã lắng nghe. Nhóm mình xin nhận câu hỏi.

*(Gợi ý chuẩn bị trước một số câu hỏi có thể gặp:)*
- *Vì sao các cổng không dùng ReLU?* Vì cổng là một hệ số nhân, cần nằm trong khoảng 0 đến 1 như độ mở của cái van. ReLU không có giới hạn trên, có thể ra số rất lớn.
- *LSTM có hết hẳn hiện tượng tín hiệu lỗi biến mất không?* Không hẳn. Nó tạo ra một con đường mà mạng có thể học để giữ tín hiệu; nếu cổng quên nhỏ thì tín hiệu vẫn yếu dần.
- *Vì sao ở thí nghiệm đơn hàng, LSTM chỉ hơn RNN rất ít?* Chuỗi chỉ có 8 đơn, thông tin quan trọng nằm ở đơn đang đặt và vài đơn gần nhất, không có gì ở xa cần nhớ. Trong khi đó LSTM có gấp 4 lần trọng số và chạy chậm gấp đôi. Nên luôn thử RNN trước khi chọn LSTM.
- *Với cổ phiếu, sao không cho xem nhiều ngày hơn để LSTM thắng đậm hơn?* Nhóm đã thử 20, 60 và 120 ngày: 20 ngày tốt nhất cho cả hai mô hình, vì thông tin quan trọng nằm trong khoảng một tháng gần đây. LSTM tốt hơn RNN ở cả ba trường hợp, còn RNN với 120 ngày cho kết quả y hệt 60 ngày.
-->
