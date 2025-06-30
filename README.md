# LeVanQuocViet_2174802010907_TH0101

* Sinh viên thực hiện: Lê Văn Quốc Việt - 2174802010907
* Môn học: Nhập môn Xử lý ảnh số
* Giảng viên: Nguyễn Thái Anh

# Nhập Môn Xử Lý Ảnh Số - Lab 2  
**Biến Đổi Cường Độ Ảnh Grayscale & Phân Tích Bit-Plane**

- **Sinh viên thực hiện:** Lê Văn Quốc Việt - 2174802010907  
- **Lớp:** TH0101  
- **Môn học:** Nhập môn Xử lý ảnh số  
- **Giảng viên hướng dẫn:** Nguyễn Thái Anh

---

## Mục Tiêu Bài Lab

Bài lab này tập trung vào các phép **biến đổi cường độ ảnh xám**, là nền tảng quan trọng trong lĩnh vực xử lý ảnh số. Các phép biến đổi giúp:
- Tăng cường độ tương phản ảnh.
- Làm rõ các chi tiết mờ hoặc khó quan sát.
- Hỗ trợ các bước xử lý ảnh nâng cao như trích xuất đặc trưng, phân tích đối tượng.

---

## Công Nghệ Sử Dụng

| Công cụ        | Vai trò chính                                          |
|----------------|--------------------------------------------------------|
| **Python**     | Ngôn ngữ lập trình chính                               |
| **Pillow**     | Đọc, ghi và xử lý ảnh cơ bản                           |
| **NumPy**      | Xử lý ảnh dưới dạng mảng số học                        |
| **ImageIO**    | Hỗ trợ đọc nhiều định dạng ảnh hiện đại                |
| **Matplotlib** | Hiển thị ảnh trực quan, vẽ biểu đồ                     |

---

## Các Phép Biến Đổi Thực Hiện

### 1. Ảnh âm bản (Negative Transformation)

- **Mục đích:** Đảo ngược độ sáng để làm rõ chi tiết trong vùng tối, thường dùng trong ảnh X-quang.
- **Công thức:**  
  \[
  s = L - 1 - r
  \]
- **Code:**
  ```python
  negative_img = 255 - img_np
  ```

---

### 2. Biến đổi Logarit (Log Transformation)

- **Mục đích:** Làm sáng vùng tối, giảm độ chói vùng sáng.
- **Công thức:**  
  \[
  s = c \cdot \log(1 + r), \quad c = \frac{255}{\log(1 + \max(r))}
  \]
- **Code:**
  ```python
  c = 255 / np.log(1 + np.max(img_np))
  log_img = c * np.log(1 + img_np)
  log_img = np.array(log_img, dtype=np.uint8)
  ```

---

### 3. Biến đổi hàm mũ (Gamma Transformation)

- **Mục đích:** Điều chỉnh độ sáng/tối bằng tham số gamma.
- **Công thức:**  
  \[
  s = c \cdot r^\gamma
  \]
- **Lưu ý:**  
  - \( \gamma < 1 \): Làm ảnh sáng hơn.  
  - \( \gamma > 1 \): Làm ảnh tối đi.
- **Code:**
  ```python
  gamma = 0.5  # hoặc 2.0
  img_norm = img_np / 255.0
  gamma_img = np.power(img_norm, gamma)
  gamma_img = np.uint8(gamma_img * 255)
  ```

---

### 4. Phân tích mặt phẳng bit (Bit-Plane Slicing)

- **Mục đích:** Phân tích vai trò của từng bit trong độ sáng pixel.
- **Cách làm:**
  - Mỗi pixel ảnh 8-bit có 8 bit (từ bit 0 đến bit 7).
  - Mỗi bit tạo ra một ảnh nhị phân riêng biệt.
- **Code:**
  ```python
  bit_plane = 3  # chọn bit từ 0 đến 7
  bp_img = (img_np >> bit_plane) & 1
  bp_img = bp_img * 255
  ```
- **Ý nghĩa:**
  - Bit 6, 7: Chứa nhiều thông tin chính của ảnh.
  - Bit 0, 1: Chủ yếu là nhiễu hoặc chi tiết nhỏ.

---

## Cấu Trúc Dự Án

```
📦 grayscale-transformations-lab
├── main.ipynb        # Notebook chính chứa toàn bộ code và kết quả
├── image.png         # Ảnh gốc được dùng làm input
├── README.md         # Tài liệu mô tả chi tiết
```

---

## 📌 Hướng Dẫn Sử Dụng

1. Cài đặt các thư viện cần thiết:
   ```bash
   pip install numpy pillow imageio matplotlib
   ```

2. Mở file `main.ipynb` bằng Jupyter Notebook hoặc Google Colab.

3. Thay đổi ảnh đầu vào (`image.png`) nếu cần thử với ảnh khác.

---

## Tài Liệu Tham Khảo

- Gonzalez & Woods, *Digital Image Processing*
- [Pillow Docs](https://pillow.readthedocs.io/)
- [NumPy Docs](https://numpy.org/doc/)

---

# Nhập Môn Xử Lý Ảnh Số - Lab 3  
**Bộ Lọc Không Gian (Spatial Filtering)**

## Mục Tiêu Bài Lab

Lab 3 tập trung vào các kỹ thuật **lọc không gian** trong xử lý ảnh xám nhằm:
- Làm mịn (blur) ảnh để giảm nhiễu.
- Làm sắc nét (sharpen) ảnh để nhấn mạnh chi tiết và biên.
- Hiểu và áp dụng các mặt nạ lọc như trung bình, Gaussian, Laplacian và Sobel.
## Các Phép Lọc Thực Hiện

### 1. Lọc Trung Bình (Mean Filter)

- Làm mịn ảnh bằng cách tính trung bình giá trị pixel trong vùng lân cận.
- Giảm nhiễu, nhưng làm mờ biên ảnh.

```python
kernel = np.ones((3, 3), np.float32) / 9
blurred = cv2.filter2D(img, -1, kernel)
```

---

### 2. Lọc Gaussian

- Sử dụng hàm phân phối chuẩn để tính trọng số cho các pixel lân cận.
- Giữ được biên ảnh tốt hơn lọc trung bình.

```python
gauss = cv2.GaussianBlur(img, (5, 5), 1)
```

---

### 3. Lọc Trung Vị (Median Filter)

- Thay mỗi pixel bằng giá trị trung vị của vùng lân cận.
- Rất hiệu quả trong việc loại bỏ nhiễu muối tiêu (salt-and-pepper).

```python
median = cv2.medianBlur(img, 3)
```

---

### 4. Lọc Laplacian (Làm Sắc Nét)

- Dựa trên đạo hàm bậc hai để làm nổi bật các vùng thay đổi cường độ mạnh.

```python
lap = cv2.Laplacian(img, cv2.CV_64F)
lap = cv2.convertScaleAbs(lap)
```

---

### 5. Lọc Sobel (Phát Hiện Biên)

- Phát hiện biên theo hướng ngang (x) và dọc (y).

```python
sobelx = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
sobely = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)
sobel = cv2.magnitude(sobelx, sobely)
sobel = cv2.convertScaleAbs(sobel)
```

---

## Cấu Trúc Dự Án

```
📦 lab3-spatial-filtering
├── lythuyet.ipynb       # Nội dung lý thuyết và mô tả các bộ lọc
├── baitapTH.ipynb       # Bài tập thực hành và minh hoạ kết quả
├── input.png            # Ảnh đầu vào (có thể thay đổi tuỳ ý)
├── README.md            # Tài liệu mô tả chi tiết bài lab
```


## Ghi Chú

- Lọc trung bình & Gaussian làm ảnh mờ, thích hợp xử lý nhiễu nhẹ.
- Lọc Laplacian & Sobel làm sắc nét và phát hiện biên, nhưng dễ bị nhiễu ảnh hưởng.
- Kết hợp các kỹ thuật lọc có thể tạo ra pipeline xử lý ảnh hiệu quả hơn.

---

## Tài Liệu Tham Khảo

- Gonzalez & Woods, *Digital Image Processing*
- [OpenCV Docs](https://docs.opencv.org/)
- [NumPy Docs](https://numpy.org/doc/)

