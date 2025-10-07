# Prime-uit-dsc-2025

## 📌 Tóm tắt dự án

Dự án này xây dựng hệ thống phân loại response dựa trên **Student–Teacher Framework**:

- **Student**: gồm nhiều mô hình nhị phân (binary classification) để phân loại response theo các tiêu chí nhỏ (ví dụ: yes/no, known/unknown, support/contradictory).
- **Teacher**: là mô hình 3 nhãn tổng quát (no, intrinsic, extrinsic).

Cơ chế hoạt động:

- Nếu các mô hình Student đồng thuận với nhau → sử dụng kết quả của Student.
- Nếu các mô hình Student không đồng thuận → sử dụng kết quả của Teacher.

Cách tiếp cận này giúp tăng độ chính xác bằng cách tận dụng ưu điểm của cả Student (chi tiết, chuyên biệt) và Teacher (tổng quát, ổn định).

---

## 🧩 Ánh xạ nhãn Student → Teacher

Mỗi Student phụ trách một khía cạnh nhỏ, sau đó ánh xạ về nhãn cuối cùng của **Teacher** (3 nhãn: **no, intrinsic, extrinsic**):

- **Student 1 (yes/no):**  
  - `yes` → **extrinsic** hoặc **intrinsic**  
  - `no` → **no**

- **Student 2 (known/unknown):**  
  - `known` → **no** hoặc **intrinsic**  
  - `unknown` → **extrinsic**

- **Student 3 (support/contradictory):**  
  - `support` → **no** hoặc **extrinsic**  
  - `contradictory` → **intrinsic**

---

### 🔑 Quy tắc đồng thuận
Hệ thống kiểm tra kết quả của cả 3 Student. Nếu đồng thuận theo các tổ hợp sau thì lấy kết quả trực tiếp từ Student:

1. **Dự đoán "no"**  
   - Student 1 → `no`  
   - Student 2 → `known`  
   - Student 3 → `support`  
   ⇒ Kết quả cuối: **no**

2. **Dự đoán "extrinsic"**  
   - Student 1 → `yes`  
   - Student 2 → `unknown`  
   - Student 3 → `support`  
   ⇒ Kết quả cuối: **extrinsic**

3. **Dự đoán "intrinsic"**  
   - Student 1 → `yes`  
   - Student 2 → `known`  
   - Student 3 → `contradictory`  
   ⇒ Kết quả cuối: **intrinsic**

---

### 📌 Khi không đồng thuận
Nếu kết quả từ 3 Student **không rơi vào một trong 3 trường hợp trên**, hệ thống sẽ **lấy nhãn dự đoán từ Teacher model** làm kết quả cuối cùng.

---

## 📂 Cấu trúc Project

Project được tổ chức thành 2 phần chính:

### 1. Folder `train/`

Chứa các notebook phục vụ việc huấn luyện mô hình Student và Teacher:

- `full_label_train.ipynb` → Huấn luyện mô hình **Teacher** (3 nhãn: no, intrinsic, extrinsic).
- `yes_no_train.ipynb` → Huấn luyện mô hình **Student 1** (phân loại yes / no).
- `known_unknown_train.ipynb` → Huấn luyện mô hình **Student 2** (phân loại known / unknown).
- `supported_contradictory_train.ipynb` → Huấn luyện mô hình **Student 3** (phân loại support / contradictory).

### 2. File `inference.ipynb`

Notebook chính để chạy **pipeline Student–Teacher**:

- Load các mô hình đã train.
- Thực hiện inference theo cơ chế Student–Teacher.
- So sánh kết quả chạy lại với file `SUBMITTED_FILE_PATH` (file đã submit trong cuộc thi) để kiểm tra reproducibility.
- Xuất ra file kết quả cuối cùng ở định dạng `.csv`.

---

👉 Với cấu trúc này, bạn chỉ cần train lại mô hình trong folder `train/` (nếu muốn), hoặc sử dụng trực tiếp mô hình đã tải về, sau đó chạy `inference.ipynb` để có kết quả.

## 📂 Cấu hình đường dẫn & các biến cần thiết

Trước khi chạy code, bạn cần tải các mô hình và dataset từ link sau:  
👉 [Download Models & Data](https://drive.google.com/drive/folders/1lz7xs34445r4R7mJQBLb-BdVUU6Ss7-B)

Sau khi tải về, hãy thay đổi các biến đường dẫn trong code của bạn:

- `FULL_LABEL_MODEL_PATH` → đường dẫn tới mô hình Teacher (3 nhãn).
- `KNOWN_UNKNOWN_MODEL_PATH` → đường dẫn tới mô hình Student phân loại known / unknown.
- `SUPPORTED_CONTRADICTORY_MODEL_PATH` → đường dẫn tới mô hình Student phân loại support / contradictory.
- `TRUTH_HALLUCINATION_MODEL_PATH` → đường dẫn tới mô hình Student phân loại no / yes.
- `PRIVATE_TEST_DATASET_PATH` → đường dẫn tới dataset test riêng.
- `SUBMITTED_FILE_PATH` → đường dẫn nơi lưu file kết quả sau khi chạy.

Ví dụ:

```python
FULL_LABEL_MODEL_PATH = "/path/to/teacher_model"
KNOWN_UNKNOWN_MODEL_PATH = "/path/to/student_known_unknown"
SUPPORTED_CONTRADICTORY_MODEL_PATH = "/path/to/student_support_contradictory"
TRUTH_HALLUCINATION_MODEL_PATH = "/path/to/student_truth_hallucination"
PRIVATE_TEST_DATASET_PATH = "/path/to/private_test_dataset.json"
SUBMITTED_FILE_PATH = "/path/to/submission.csv"
```

## 🚀 Cách chạy

Toàn bộ project được triển khai trong **Kaggle Notebook** environment sử dụng Accelerator GPU P100. Người dùng chỉ cần:

1. **Tải mô hình và dataset** từ link đã cung cấp ở phần trên, hoặc Add Dataset và Add Models trên Kaggle site.
2. **Chỉnh sửa các biến đường dẫn** trong notebook:

   - `FULL_LABEL_MODEL_PATH`
   - `KNOWN_UNKNOWN_MODEL_PATH`
   - `SUPPORTED_CONTRADICTORY_MODEL_PATH`
   - `TRUTH_HALLUCINATION_MODEL_PATH`
   - `PRIVATE_TEST_DATASET_PATH`
   - `SUBMITTED_FILE_PATH`

3. **Chạy toàn bộ notebook từ đầu đến cuối**.
   - Notebook sẽ tự động load mô hình Student và Teacher.
   - Thực hiện dự đoán theo cơ chế Student–Teacher.
   - Xuất kết quả cuối cùng ra file `.csv` tại `SUBMITTED_FILE_PATH`.

---

## ⚙️ Tham số huấn luyện

Dự án sử dụng `Trainer` của HuggingFace với một số cấu hình quan trọng:

- **Epochs & Batch size**
  - `num_train_epochs=1`: Mỗi lần train chỉ 1 epoch, sau đó resume tiếp → giúp kiểm soát tài nguyên.
  - `per_device_train_batch_size=1`, `gradient_accumulation_steps=16` → batch hiệu quả ~16, cân bằng giữa bộ nhớ và tốc độ.

- **Logging & Evaluation**
  - `logging_steps=20`: log vừa phải, tránh quá dày đặc.
  - `eval_strategy="epoch"`, `save_strategy="epoch"`: đánh giá và lưu mô hình sau mỗi epoch.
  - `load_best_model_at_end=True`: tự động chọn checkpoint tốt nhất theo metric `f1`.

- **Tối ưu hóa**
  - `learning_rate=3e-4`, `weight_decay=0.01`: tốc độ học vừa phải với regularization nhẹ.
  - `lr_scheduler_type="cosine"`, `warmup_ratio=0.03`: lịch giảm LR theo cosine, có warmup để ổn định training.
  - `optim="paged_adamw_8bit"`: tối ưu hóa bộ nhớ bằng AdamW 8-bit.
  - `fp16=True`: huấn luyện nửa chính xác (mixed precision) để tăng tốc và giảm VRAM.

- **Hiệu năng**
  - `dataloader_num_workers=2`, `dataloader_pin_memory=True`: cải thiện I/O trên GPU.
  - `group_by_length=True`: gom mẫu có độ dài gần nhau → giảm padding → training nhanh và ổn định hơn.

---

## 🔮 Tham số inference

Trong quá trình sinh output (`model.generate`):

- `max_new_tokens`: số token tối đa cần sinh.  
- `do_sample=False, temperature=0.0`: sinh **deterministic** (greedy search), đảm bảo tính nhất quán khi đánh giá.  
- `pad_token_id=tokenizer.eos_token_id`: dùng token EOS để pad, tránh lỗi padding.  
- `eos_token_id=[tokenizer.eos_token_id]`: dừng sinh khi gặp token kết thúc câu.

⚡ Thiết lập inference này đảm bảo kết quả **tái lập (reproducible)** và **ổn định** giữa các lần chạy, điều rất quan trọng trong bối cảnh kiểm tra tính consistency của hệ thống.

---

## 📊 Kết quả cuối cùng

- `SUBMITTED_FILE_PATH` là **file kết quả đã nộp trong cuộc thi**.
- Khi chạy lại notebook, hệ thống sẽ sinh ra một file kết quả mới để **so sánh với file submit** nhằm kiểm tra tính reproducibility.
- Kết quả so sánh cho thấy: chỉ có **1/2000 dòng bị thay đổi**.
  - Nguyên nhân có thể đến từ sự ngẫu nhiên hoặc đặc tính của LLM.
  - Tuy nhiên, việc chỉ thay đổi **1 dòng trên tổng 2000** chứng minh rằng hệ thống có **tính ổn định và consistency cao**.
