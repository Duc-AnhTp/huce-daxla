# 🎯 Báo cáo môn học: Xử lý ảnh số  
## Đề tài: Khôi phục ảnh bị nhiễu và mờ bằng các bộ lọc Inverse, Wiener, và Median  

**Trường Đại học Xây dựng Hà Nội – Khoa Công nghệ Thông tin**  
**Nhóm sinh viên thực hiện:**  
| Họ và tên | Lớp | MSSV |
|------------|------|------|
| Nguyễn Thu Hương | 68CS2 | 0209168 |
| Trần Phùng Đức Anh | 68CS2 | 0204168 |
| Trịnh Tuấn Anh | 68CS2 | 0204368 |

**Giảng viên hướng dẫn:** Đào Việt Cường  
**Thời gian:** Hà Nội, tháng 10 năm 2025  

---

## I. Cơ sở lý thuyết

### 1. Mô hình suy giảm ảnh
Ảnh quan sát \( g(x,y) \) được mô tả bằng mô hình:
$$
g(x,y) = h(x,y) * f(x,y) + \eta(x,y)
$$
Trong đó:
- \( f(x,y) \): ảnh gốc  
- \( h(x,y) \): hàm lan truyền điểm (PSF – Point Spread Function)  
- \( \eta(x,y) \): nhiễu ngẫu nhiên (Gaussian, Salt & Pepper, Poisson, ...)  
- \( * \): phép chập (convolution)  

**Mục tiêu:** ước lượng lại ảnh gốc \( f(x,y) \) từ ảnh suy giảm \( g(x,y) \).

### 2. Mô hình trong miền tần số
Dùng biến đổi Fourier 2 chiều:
$$
G(u,v) = H(u,v)F(u,v) + N(u,v)
$$
- \( G(u,v) \): phổ ảnh quan sát  
- \( H(u,v) \): hàm truyền hệ thống  
- \( F(u,v) \): phổ ảnh gốc  
- \( N(u,v) \): phổ nhiễu  

---

## II. Các bộ lọc khôi phục ảnh

### 1. Lọc nghịch đảo (Inverse Filter)
Công thức cơ bản:
$$
\hat{F}(u,v) = \frac{G(u,v)}{H(u,v)} = F(u,v) + \frac{N(u,v)}{H(u,v)}
$$
Khi \( H(u,v) \) nhỏ → nhiễu bị khuếch đại.  
Giải pháp: thêm hệ số điều chỉnh (Regularized Inverse Filter)
\[
\hat{F}(u,v) = \frac{H^*(u,v)}{|H(u,v)|^2 + \epsilon} G(u,v)
\]
→ ổn định hơn, tránh chia cho 0.

**Ứng dụng:** ảnh mờ nhẹ, ít nhiễu.

---

### 2. Lọc Wiener (Wiener Filter)
Tối ưu theo tiêu chí sai số bình phương trung bình (MSE):
\[
\hat{F}(u,v) = \frac{H^*(u,v)}{|H(u,v)|^2 + \frac{S_N(u,v)}{S_F(u,v)}} G(u,v)
\]

- \( S_N(u,v) \): phổ công suất nhiễu  
- \( S_F(u,v) \): phổ công suất tín hiệu  

Khi \( S_N / S_F \to 0 \) → lọc Wiener trở thành lọc nghịch đảo.

**Ưu điểm:** giảm đồng thời mờ và nhiễu, kết quả ổn định.  
**Ứng dụng:** ảnh vệ tinh, ảnh y khoa, ảnh đêm.

---

### 3. Lọc trung vị (Median Filter)
Là bộ lọc phi tuyến trong miền không gian:
\[
\hat{f}(x,y) = \text{median}\{f(s,t)\}, \quad (s,t)\in \text{cửa sổ}
\]

**Ưu điểm:** khử nhiễu muối tiêu hiệu quả, giữ biên rõ.  
**Ứng dụng:** ảnh cũ, ảnh scan, camera nhiễu.

---

## III. Không gian màu và khôi phục ảnh màu
Ảnh màu thường dùng các không gian:
- **RGB:** hiển thị trên màn hình.  
- **HSV/HSI/YCbCr:** tách độ sáng (Intensity/Luminance) và màu (Chrominance).

**Cách xử lý:**  
Chỉ khôi phục trên kênh độ sáng → tránh biến đổi màu sai lệch:


---

## IV. Thang đo đánh giá chất lượng

### 1. Thang đo định lượng
- **MSE (Mean Square Error):**
  \[
  MSE = \frac{1}{MN} \sum_{x,y} [f(x,y) - \hat{f}(x,y)]^2
  \]
- **PSNR (Peak Signal-to-Noise Ratio):**
  \[
  PSNR = 10 \log_{10}\left(\frac{L^2}{MSE}\right)
  \]
  Với \( L = 255 \) (ảnh 8-bit).  
  → PSNR càng cao → ảnh càng tốt.

### 2. Thang đo định tính
- Biên ảnh rõ hơn  
- Nhiễu giảm tự nhiên  
- Cấu trúc hình học bảo toàn  
- Đánh giá qua Sobel hoặc Canny

### 3. Bảng tiêu chí tổng hợp

| Tiêu chí | Thang đo | Giải thích |
|-----------|-----------|-------------|
| Độ chính xác khôi phục | PSNR (dB) | >30: tốt; 25–30: TB; <25: kém |
| Sai số trung bình | MSE | Càng nhỏ càng tốt |
| Độ sắc nét biên | Gradient/Canny | Biên rõ → tốt |
| Độ tự nhiên hình ảnh | MOS (1–5) | 1: xấu – 5: tốt |
| Tốc độ xử lý | Thời gian tính toán | Đo hiệu năng thuật toán |

---

## V. Dự đoán kết quả

| Bộ lọc | Kết quả kỳ vọng | Ghi chú |
|--------|------------------|---------|
| **Inverse Filter** | Khôi phục mờ nhẹ tốt, nhưng dễ nhiễu | Phụ thuộc vào \( H(u,v) \) |
| **Wiener Filter** | Ổn định, PSNR cao, giảm mờ và nhiễu | Tối ưu MSE |
| **Median Filter** | Hiệu quả với nhiễu muối tiêu | Giữ biên tốt |

**Dự kiến:**  
- PSNR: 28–35 dB  
- Giảm nhiễu rõ rệt  
- Biên ảnh sắc nét hơn  
- Wiener Filter cho kết quả tối ưu nhất  

---

## VI. Kết luận sơ bộ

**Đề tài:** *Khôi phục ảnh bị nhiễu và mờ bằng các bộ lọc Inverse, Wiener, Median*  
Là sự tổng hợp của các chương:

| Chương | Nội dung liên quan |
|--------|--------------------|
| **Chương 2** | Mô hình hóa và biến đổi Fourier |
| **Chương 3** | Các bộ lọc khôi phục ảnh |
| **Chương 4** | Không gian màu và in ảnh |
| **Chương 5** | Đánh giá PSNR, phát hiện biên |

**Kết luận sơ bộ:**
- **Wiener Filter:** hiệu quả nhất, ổn định.  
- **Median Filter:** phù hợp ảnh nhiễu xung.  
- **Inverse Filter:** tốt khi PSF biết chính xác và nhiễu nhỏ.  

---

> 📘 *Tài liệu tham khảo:*
> - Slide 2–5: Xử lý ảnh số – Bộ môn XLA, Khoa CNTT, ĐHXD.  
> - Gonzalez & Woods, *Digital Image Processing*, 4th Edition.  
> - MATLAB Image Processing Toolbox Documentation.

---

