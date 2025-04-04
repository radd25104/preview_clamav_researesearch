#  Archive Passwords
> **Passwords for archive files [experimental]**

Ta biết rằng Clamav cho phép ta quét qua các file đã được nén dưới dạng .zip, .rar, v.v.

Và với tính năng này Clamav có thể giúp ta cho phép thử các mật khẩu để mở các tệp `.zip` được mã hóa (sử dụng mã hóa PKWARE truyền thống) khi nghi ngờ rằng bên trong có thể chứa phần mềm độc hại (malware). Tính năng này có một số điểm như sau:
  -  Đối với các file `.zip` được bảo vệ bằng mật khẩu do đó Clamav không thể quét như các file khác, Clamav sẽ dùng signature từ file `.pwdb` để mở khóa.
  -  Khá giống việc ta thực hiện tấn công vét cạn dùng từ điển (dictionary brute force attack) để mở khóa giúp ta không cần nhập password thủ công, quan trọng là Clamav sẽ giữ nguyên trạng thái của file nén gốc, không giải nén file.


-   Hạn chế: 
    -  Tuy nhiên tính năng này chỉ là bước đệm để phân tích sâu hơn khi kết hợp với các loại signature khác.
    -  Chỉ hỗ trợ .zip với PKWARE, không phải các định dạng nén hiện đại hơn (như AES hay .7z).
    -  Tính thử nghiệm: Đây là tính năng experimental từ ClamAV 0.99, nên không được tích hợp sâu hay sử dụng rộng rãi trong các cơ sở dữ liệu chính thức.
 

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

  -  Trước tiên ta tạo file để làm mẫu thử, nén file lại với mật khẩu là `infected`. Nội dung file sẽ chứa 1 string đặc biệt để giả làm file PE như dưới hình.

      ![Screenshot 2025-04-05 000547](https://github.com/user-attachments/assets/4c30b66b-2e6e-440d-94d8-9a344e09f1fe)
      ![Screenshot 2025-04-05 000212](https://github.com/user-attachments/assets/fb048e2e-c723-468d-b5cc-1466cdbcc593)

  -  Tạo file signature `.pwdb` và 1 file `.yara` để kết hợp detect file. Chuyển 2 file sang database signature.
      ![Screenshot 2025-04-05 000139](https://github.com/user-attachments/assets/8a0add3a-37c6-4c3d-a05a-60ab352d3cd8)
     
  -  Quét file và lưu lại log để xem clamav đã làm gì với file `.zip` có password kia.

      ![Screenshot 2025-04-05 001308](https://github.com/user-attachments/assets/f197a25c-5306-4533-9c05-c6f39022b0e8)

      ![Screenshot 2025-04-05 000347](https://github.com/user-attachments/assets/14caeea7-8774-427f-a157-53a822f1cb76)

      Ta có thể thấy rằng clamav load signature rồi dùng password để thử mở file rồi quét mà không làm file bị giải nén.

      ![Screenshot 2025-04-04 234823](https://github.com/user-attachments/assets/f2399f2a-7f4d-4bc0-8ce3-cfb0fa3959fc)



