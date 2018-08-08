# Change log

1. Fix lỗi StartimeCommand chạy liên tục trong file vn_ime.py
- Hiện tượng: gõ aaaa luôn trả về â 
- Lý do: hàm biến chuỗi nhập vào thành tiếng Việt StartimeCommand bị gọi liên tục, dẫn đến aa --> â + a --> aa --> â + a --> aa --> â --> â...
- Nguyên nhân: hàm StarttimeCommand khi gọi hàm runchange để thay chữ gốc bằng chữ tiếng Việt thì cũng làm thay đổi sublime view --> khởi sinh sự kiện on_modified_async --> lại chạy StarttimeCommand
- Giải pháp: bổ sung biến global LAST_WORD lưu word được xử lý gần nhất, nếu word không thay đổi (do StarttimeCommand bị gọi lại) thì dừng

2. Fix lỗi bỏ dấu sai trong file bogo/core.py
- Hiện tượng: âa trả về â chứ không phải aa
- Lý do: hàm process_key hoạt động trên cơ sở fallback_sequence phải chứa chuỗi gốc chưa đổi thành tiếng Việt (ví dụ với "aaa" thì hàm trả về đúng "aa" vì fallback_sequence khi đó là "aa"), nhưng nếu input đã chứa ký tự tiếng Việt (như "âa") thì hàm này xử lý lỗi, vì fallback_sequence khi đó là "â"
- Giải pháp: bổ sung hàm return_raw_string(fallback_sequence) để convert fallback_sequence có chứa ký tự tiếng Việt về dạng đúng ("â" thành "aa") và sử dụng trong function process_key

3. Fix lỗi không gõ được tiếng Anh
- Hiện tượng: gõ "thissss" trả về thí hoặc thís...
- Lý do: sau khi bổ sung "s" để bỏ dấu ở "this" thì các kí tự sau vẫn tiếp tục được xử lý như là kí tự dấu sắc (kiểu gõ telex) thay vì chỉ coi như kí tự s bổ sung
- Giải pháp: bổ sung tham số và biến toàn cục global UNDO. Nếu phát hiện thấy đã xảy ra hiện tượng bỏ dấu, thì kể từ đó các kí tự tiếp theo sẽ không được xử lý, mà ghép thẳng vào từ. UNDO sẽ reset khi gõ từ mới, phát hiện bằng cách sử dụng biến global LÁST_PÓS và so sánh với current pos để phát hiện khi người dùng tạo region mới 

4. Fix một số lỗi chưa xử lý được
- Hiện tượng: hàm process_key bỏ dấu không chuẩn ở một số từ, ví dụ: giờ thành "giowf", "things" thành "thisng", và các từ ở dạng hoa "Ás" thành "Ás"...
- Lý do: chưa tìm được, có lẽ nằm trong các function về biến đổi
- Giải pháp: xây dựng một từ điển sửa lỗi global REPLACE_DICT và thay trực tiếp các trường hợp đặc biệt thành từ xử lý đúng



