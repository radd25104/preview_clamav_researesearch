# Container metadata Signatures
- Từ phiên bản 0.96, Clamav cho phép tạo ra các loại chữ ký chung (generic signature) để phát hiện tệp lưu trữ trong các loại container khác nhau dựa trên các điều kiện cụ thể.
- **Định dạng Signature**

  ```
    VirusName:ContainerType:ContainerSize:FileNameREGEX:
    FileSizeInContainer:FileSizeReal:IsEncrypted:FilePos:
    Res1:Res2[:MinFL[:MaxFL]]
  ```

  **Ý nghĩa các trường:**
    -  `VirusName:` Tên virus hiển thị khi khớp chữ ký.
    -  `ContainerType:` Loại container (ví dụ: CL_TYPE_ZIP, CL_TYPE_RAR, CL_TYPE_ARJ, CL_TYPE_MSCAB, CL_TYPE_7Z, CL_TYPE_MAIL, v.v.), dùng * để khớp mọi loại.
    -  `ContainerSize:` Kích thước container (bytes), có thể là giá trị tuyệt đối hoặc khoảng (x-y).
    -  `FileNameREGEX:` Biểu thức chính quy mô tả tên tệp đích.
    -  `FileSizeInContainer:` Kích thước tệp trong container (thường là kích thước nén), có thể là giá trị tuyệt đối hoặc khoảng.
    -  `FileSizeReal:` Kích thước thực (thường là kích thước không nén), có thể là giá trị tuyệt đối hoặc khoảng.
    -  `IsEncrypted:` 1 (mã hóa), 0 (không mã hóa), hoặc * (bỏ qua).
    -  `FilePos:` Vị trí tệp trong container (tính từ 1), có thể là giá trị tuyệt đối hoặc khoảng.
    -  `Res1:` Với ZIP/RAR, trường này là tổng kiểm tra CRC (hex); các loại khác bỏ qua.
    -  `Res2:` Chưa sử dụng trong ClamAV 0.96.
-  Các chữ ký cho container được lưu trữ ở trong file `.cdb`.

    ** **Tổng CRC là phương pháp phát hiện lỗi dựa trên nội dung của dữ liệu, trong trường hợp này CRC giúp phân biệt các tệp có cùng tên, kích thước, hoặc vị trí nhưng nội dung khác nhau.**

   
