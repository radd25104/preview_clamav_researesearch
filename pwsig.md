#  Archive Passwords
> **Passwords for archive files [experimental]**

Ta biết rằng Clamav cho phép ta quét qua các file đã được nén dưới dạng .zip, .rar, v.v.

Và với tính năng này Clamav có thể giúp ta cho phép thử các mật khẩu để mở các tệp `.zip` được mã hóa (sử dụng mã hóa PKWARE truyền thống) khi nghi ngờ rằng bên trong có thể chứa phần mềm độc hại (malware). Tính năng này có một số ưu điểm như sau:
  -  Đối với các file `.zip` được bảo vệ bằng mật khẩu do đó Clamav không thể quét như các file khác, Clamav sẽ dùng signature từ file `.pwdb` để mở khóa.
  -  Khá giống việc ta thực hiện tấn công vét cạn dùng từ điển (dictionary brute force attack) để mở khóa giúp ta không cần nhập password thủ công, quan trọng là Clamav sẽ giữ nguyên trạng thái của file nén gốc, không giải nén file.

**The signature format:**
```
    SignatureName;TargetDescriptionBlock;PWStorageType;Password
```

Định dạng signature gồm 4 trường, ngăn cách bởi dấu `;`
  -  SignatureName: Tên hiển thị khi mật khẩu thành công (dùng trong debug).
  -  TargetDescriptionBlock: Thông tin về engine và tệp đích, dạng cặp Arg:Val phân tách bằng dấu phẩy.
      -  Engine:X-Y: Mức độ chức năng engine yêu cầu (xem tài liệu FLEVEL).
      -  Container:CL_TYPE_*: Loại tệp lưu trữ áp dụng.
  -  PWStorageType: Cách mật khẩu được lưu trữ:
      -  0 = văn bản rõ (cleartext).
      -  1 = mã hóa hex.
  -  Password: Giá trị mật khẩu thực tế để thử.
  -  Các signature mật khẩu được lưu trong các tệp có đuôi `.pwdb`.


## Demo
