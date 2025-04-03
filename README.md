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


##  Demo

-  Giả sử rằng ta có 1 file `.exe` là 1 file trojan, ta có thể tính được CRC của file trojan đó và ta tạo 1 custom signature từ đó.
-  Tạo file `.exe` và đẩy 1 lượng data để file đó có dung lượng khoảng 2000 byte, sau đó nén lại và tính CRC của file nén, sau đó nén thêm 1 vài lớp nữa.
-  [[[file-trojan].zip-nén lần 1].zip-nén lần 2].zip-nén lần 3, dùng `zip` để nén `zip des-file.zip src-file`
-  CRC ở đây ta tính ở file nén lần đầu. File sau khi nén 1 lần thường sẽ không thay đổi nhiều dung lượng khi nén ở các lần tiếp.
-  Tính CRC bằng lệnh `unzip -v file-nen-lan-1.zip` ta sẽ biết được `CRC-32` của file `.exe`
-  Do ở đây chỉ demo cách mà signature hoạt động nên chỉ dùng 1 file trojan làm đích.

### Demo 1:

-  Tạo file malware giả để test
      
      ![Screenshot 2025-04-01 223533](https://github.com/user-attachments/assets/ab10c964-c5fa-41f4-b66d-e8c1dfd6bba8)
      -  Nén lại
      
      ![Screenshot 2025-04-01 223604](https://github.com/user-attachments/assets/58f41619-4c9e-41be-a908-6fdd2303a7a9)
      -  Tính CRC của file malware đich để làm dữ liệu cho signature
      
      ![Screenshot 2025-04-01 223629](https://github.com/user-attachments/assets/265f718a-28b6-4e2c-b358-f4fb7d72b6ad)
      
      ![Screenshot 2025-04-01 223712](https://github.com/user-attachments/assets/a5b259d5-8f38-46ce-a1ad-ab190334bd63)
      
      -  Tạo file signature rồi scan thử
      
      ![Screenshot 2025-04-01 224017](https://github.com/user-attachments/assets/13b47c21-417e-4225-8cbc-32a6f75b81ee)

### Demo 2:

[sample](https://virusshare.com/file?a1b6107c82a13a881e45ec94e2011f5b2220a2670e110a0319f673075e1aa1f8) [here](https://www.virustotal.com/gui/file/a1b6107c82a13a881e45ec94e2011f5b2220a2670e110a0319f673075e1aa1f8/detection)

-  Cấu trúc file như sau file a được nén vào file b.zip, file b.zip tiếp tục được nén vào file c.zip, giả định rằng ta biết 1 con virus có định dạng file nén tương tự với file b.zip. 
    -  1. Tạo file nén
       ![Screenshot 2025-04-03 113259](https://github.com/user-attachments/assets/cd1ba522-6a8b-46ca-b868-1bd4fdee3fa3)
    -  2. Tạo file signature, sau đó chuyển vào `/var/lib/clamav`
       ![Screenshot 2025-04-03 113324](https://github.com/user-attachments/assets/c3f27b28-45f6-4c1d-8c75-654be7156c9d)


#
-  Ta có thế thấy rằng Clamav so khớp được dù file malware đích đã được nén mà không cần giải nén file.
-  Tuy nhiên việc loại signature này chỉ đúng và tối ưu khi được kết hợp cùng các signature bởi một vài lý do sau đây:
    -  Hoàn toàn phụ thuộc vào metadata, tin tặc có thể thay đổi metadata của file để qua mặt AV dẫn tới phát hiện nhầm (false positive) hoặc bỏ sót (false negative). 
    -  Dễ gây false positive nếu regex không cụ thể.
-  ==> Tóm lại, signature này dùng để detect cơ bản các tệp nén nghi ngờ dựa trên mẫu virus trước đó, nhằm mục đích lọc nhanh và đánh dấu để tiến hành phân tích kỹ hơn. Nó không phải công cụ toàn diện, mà là một lớp phát hiện ban đầu trong chiến lược quét virus của ClamAV.


   
