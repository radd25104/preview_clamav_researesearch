# Bytecode Signature

- Bytecode Signatures trong ClamAV là phương pháp nâng cao để phát hiện mã độc bằng cách sử dụng code C, được biên dịch thành ngôn ngữ trung gian gọi là bytecode và mã hóa dưới dạng `ASCII` trong các tệp `.cbc`. Các tệp này sau đó được phân phối trong cơ sở dữ liệu như `bytecode.cvd` hoặc `bytecode.cld`. 

- Khi ClamAV tải cơ sở dữ liệu, nó diễn giải và thực thi bytecode thông qua một tập hợp API, cho phép truy cập dữ liệu mẫu (nội dung tệp) và siêu dữ liệu (metadata) đã có.

- Điểm mạnh của bytecode là khả năng linh hoạt: một hàm duy nhất có thể xử lý một loại tệp cụ thể và kích hoạt nhiều cảnh báo với các tên chữ ký khác nhau dựa trên các đặc điểm độc hại khác nhau, giúp giảm số lượng chữ ký cần thiết và tăng hiệu quả so với chữ ký tĩnh truyền thống.

- Dưới đây là format của 1 file bytecode signature, xem thêm [ở](https://github.com/Cisco-Talos/clamav-bytecode-compiler) [đây](https://drive.google.com/drive/u/0/folders/1TNiZES9XHsNsfMBj0YraxtHbSDuz5_ez) 

    <img src="https://github.com/user-attachments/assets/df6fb4d8-7cb0-4650-a37c-a7d4b6948e86" width=500>
    <img src="https://github.com/user-attachments/assets/9d35e3f3-670f-4d23-982d-1e5516620df4" width=500>

- Dưới đây là format của 1 file bytecode signature đã được mã hóa

  ![image](https://github.com/user-attachments/assets/d87f58e8-d25f-4b25-9c35-edacd65e4037)

