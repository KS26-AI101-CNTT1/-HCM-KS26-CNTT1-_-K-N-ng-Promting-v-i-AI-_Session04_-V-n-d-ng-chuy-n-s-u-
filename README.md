Đề bài / Yêu cầu

Thiết kế giải pháp Prompting ứng dụng thực tế cho Kỹ thuật Prompting Nâng cao (Logic & Chuỗi)

1. Mục tiêu bài tập 

Ứng dụng kiến thức Kỹ thuật Prompting Nâng cao (Logic & Chuỗi) để thiết kế một giải pháp tự động hóa công việc thực tế, rèn luyện tư duy lập luận trước khi code.

2. Bối cảnh & vấn đề thực tế

Cửa hàng cần tự động hóa quy trình xử lý thông tin liên quan đến Chain-of-Thought, Prompt Chaining, xử lý đứt gãy.

3. Yêu cầu nghiệp vụ chi tiết

Sinh viên phải xác định rõ các thông tin cần đưa vào và cấu trúc kết quả mong muốn.

4. Ràng buộc & bẫy dữ liệu lường trước

AI dễ bỏ sót các trường hợp dữ liệu bị trống hoặc bị sai lệch. Thiết kế prompt phải có các câu lệnh điều kiện xử lý lỗi.

5. Yêu cầu đầu ra chi tiết

Bước 1: Thiết kế giải pháp (I/O)

Xác định rõ Input của bài toán gồm những gì, và cấu trúc Output mong muốn.

1. INPUT (Dữ liệu đầu vào)

Tin nhắn thô của khách hàng: Đoạn văn bản tự do (có thể thiếu thông tin hoặc sai định dạng).

Quy chuẩn nghiệp vụ: Mã đơn hàng (DH + 4 số) và Số điện thoại (10 số, bắt đầu bằng 0).

2. OUTPUT MONG MUỐN (Kết quả đầu ra 2 chặng)

Output Chặng 1 (JSON & Lập luận CoT):

Thẻ <thinking>: Ghi lại các bước AI tự kiểm tra dữ liệu thiếu/sai.

Chuỗi JSON chuẩn:

json

{
"yeu_cau": "Đổi hàng | Trả hàng | Bảo hành",

  "ma_don": "string | null",
  
  "sdt": "string | null",
  
  "loi_sp": "string",
  
  "trang_thai": "HOP_LE | LOI",
  
  "chi_tiet_loi": ["liệt kê trường thiếu hoặc sai"]
}


Output Chặng 2 (Hành động điều hướng):

Nếu trang_thai == "HOP_LE": Xuất Phiếu yêu cầu nội bộ gửi bộ phận kho.

Nếu trang_thai == "LOI": Xuất Tin nhắn phản hồi xin khách bổ sung/sửa lại đúng phần ghi trong "chi_tiet_loi".

Bước 2: Viết prompt và chạy thử

Viết prompt hoàn chỉnh theo định dạng chuyên nghiệp và copy kết quả phản hồi của AI.

1. CHUỖI PROMPT

Prompt Chặng 1 (Trích xuất & CoT):

Vai trò: Nhân viên xử lý dữ liệu cửa hàng.

Suy luận từng bước trong thẻ `<thinking>`:

1. Phân loại yêu cầu (Đổi / Trả / Bảo hành).

2. Tìm Mã đơn (`DH` + 4 số) và SĐT (10 số, bắt đầu bằng `0`).

3. Bẫy lỗi: Không tự bịa dữ liệu. Nếu thiếu/sai chuẩn -> gán `null` và đặt `trang_thai = "LOI"`.

Output JSON:

{"yeu_cau": "", "ma_don": null, "sdt": null, "loi_sp": "", "trang_thai": "HOP_LE|LOI", "ly_do_loi": ""}

Tin nhắn: "{{INPUT}}"

Prompt Chặng 2 (Điều hướng xử lý đứt gãy):

Dựa vào JSON từ Chặng 1 ({{JSON_CHANG_1}}):

- Nếu `trang_thai == "HOP_LE"`: Xuất Phiếu yêu cầu gửi kho.

- Nếu `trang_thai == "LOI"`: Soạn tin nhắn xin lỗi khách và yêu cầu bổ sung đúng phần ghi ở `"ly_do_loi"`.Hãy thận trọng khi sử dụng mã.

2. KẾT QUẢ CHẠY THỬ (PHẢN HỒI CỦA AI)

Input bẫy lỗi: "Shop ơi áo bị hỏng khóa, đổi giúp mình. SĐT 09123456."

Output Chặng 1:

xml

<thinking>

  1. Yêu cầu: Đổi hàng.

2. Mã đơn: Thiếu -> null. SĐT "09123456" chỉ có 8 số (sai chuẩn) -> null.

3. Kết luận: trang_thai = "LOI".

</thinking>

json

{

  "yeu_cau": "Đổi hàng",
  
  "ma_don": null,
  
  "sdt": null,
  
  "loi_sp": "Áo bị hỏng khóa",
  
  "trang_thai": "LOI",
  
  "ly_do_loi": "Thiếu mã đơn hàng và SĐT chưa đủ 10 số"

}
Output Chặng 2:

"Chào bạn, shop xin lỗi về sự cố hỏng khóa áo. Để hỗ trợ đổi hàng, bạn vui lòng cung cấp giúp shop Mã đơn hàng (VD: DH1024) và SĐT đủ 10 số nhé!"
