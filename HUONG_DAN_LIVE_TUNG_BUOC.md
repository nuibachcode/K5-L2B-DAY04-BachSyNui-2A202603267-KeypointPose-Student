# Hướng dẫn live từng bước — Terminal + CVAT (ảnh toàn màn hình)

> Lab Ngày 4 · KeypointPose · ảnh chụp **toàn màn hình** trên máy thật.  
> Mỗi bước: **(A) lệnh Terminal** → **(B) thao tác CVAT** kèm ảnh.

Ba luật: trái/phải theo người trong ảnh · không đoán bừa (`v=0/1/2`) · chỉ gán người thật ≥ 60 px.

---

## Bước 0 — Mở PowerShell và vào đúng thư mục repo

1. `Win + R` → gõ `powershell` → Enter.  
2. Gõ lệnh dưới (sửa đường dẫn nếu repo của bạn khác chỗ):

```powershell
cd C:\Users\phamv\Downloads\K5-L2-KeypointPose
dir
python --version
```

Phải thấy `lab.py`, thư mục `docs`, `src`, và Python ≥ 3.8:

![Terminal: cd + dir + python --version](images/guide_live/T01_cd_dir_python.png)

---

## Bước 1 — `python lab.py check` (bắt buộc đầu buổi)

```powershell
python lab.py check
```

Chờ đến dòng xanh **`CVAT sẵn sàng. Mở trình duyệt: http://localhost:8080`**:

![Terminal: lab.py check OK toàn màn hình](images/guide_live/T02_lab_py_check_ok.png)

| Nếu thấy | Làm gì |
|---|---|
| Docker lỗi | Mở Docker Desktop, đợi Engine running, chạy lại `check` |
| Connecting… trên web | Chạy lại `python lab.py check`, rồi F5 |
| Máy chậm / RAM 8GB | `python lab.py lite` rồi `check` lại |

---

## Bước 2 — `python lab.py setup` (một lần / mỗi MSSV)

```powershell
python lab.py setup --mssv 20241234 --ten NguyenVanAn
```

- Đổi `20241234` = **MSSV của bạn**, `NguyenVanAn` = **họ tên không dấu**.  
- Terminal hỏi **tên đăng nhập + mật khẩu CVAT** (tài khoản Ngày 2).  
- Xong sẽ có `src\exports\assignment.json` và **2 link job**.

Ảnh terminal sau setup (file `assignment.json` + link job):

![Terminal: setup → assignment.json + link job](images/guide_live/T03_setup_assignment.png)

Trong JSON cần thấy:

- `"mssv"`, `"ten"`, `"persons"` (12 ảnh), `"faces"` (8 ảnh)  
- `"tasks.persons.job_id"`, `"tasks.faces.job_id"`

---

## Bước 3 — Mở CVAT → Tasks / Jobs

Trình duyệt: [http://localhost:8080](http://localhost:8080) → đăng nhập → tab **Tasks**.

Bạn phải thấy `persons-<Tên>` và `faces-<Tên>`:

![CVAT Tasks — toàn trang](images/guide_live/B01_tasks_fullpage.png)

Hoặc tab **Jobs** (12 frames = persons, 8 frames = faces):

![CVAT Jobs — toàn trang](images/guide_live/B08_jobs_list.png)

**Cách nhanh:** bấm đúng link `setup` in ra (thanh địa chỉ phải có `/jobs/...`).

---

## Bước 4 — CP0: kiểm label trong project

**Projects → `d04-keypoint`**. Phải có đúng 2 label Skeleton: `person`, `face`.

![Project d04-keypoint: person + face](images/guide_live/B10_project_labels.png)

Đếm 17 điểm `person` / 13 điểm `face` theo `docs/GUIDELINE.md`. Sai tên → **báo coach**, không tự sửa.

---

## Bước 5 — Mở job persons (màn hình gán)

Bấm Open / link job persons. Giao diện gồm: Menu · Save · thanh công cụ trái · canvas ảnh · Objects bên phải.

![Job persons — toàn trang](images/guide_live/B02_job_persons_fullpage.png)

---

## Bước 6 — Vẽ skeleton `person` (chi tiết)

### 6.1 Chọn công cụ

Thanh trái → **Draw new skeleton** → Label **`person`** → bấm **`Shape`** (không bấm Track):

![Popup Draw new skeleton: person + Shape](images/guide_live/B03_draw_skeleton_popup.png)

### 6.2 Kéo khung bao người → chỉnh điểm

Kéo khung quanh người → CVAT đặt 17 điểm.  
**Nói hướng người** → kéo điểm vào tâm khớp (đầu → vai → tay → hông → chân). Phóng to mắt/mũi/tai.

![Skeleton đã vẽ trên người](images/guide_live/B04_skeleton_drawn.png)

### 6.3 Đổi cờ từng điểm + xem PARTS

Rê chuột **đúng 1 điểm**:

| Phím | Ý nghĩa | `v` |
|---|---|---:|
| `Q` | occluded (bị che, đoán chắc) | 1 |
| `O` | outside (ngoài ảnh / không đoán được) | 0 |

**Không dùng `H` (hidden).** Sau mỗi người: `Ctrl+S`.

Objects → skeleton → **PARTS** (đủ 17 tên: `nose`, `left_eye`, …):

![PARTS — danh sách điểm](images/guide_live/B05_parts_list.png)

---

## Bước 7 — Job faces (13 điểm khuôn mặt)

Mở job `faces-<ten>` (8 ảnh). Skeleton → label **`face`** → **Shape** → kéo khung mặt → phóng to mí mắt.

![Job faces — toàn trang](images/guide_live/B09_job_faces.png)

Nhắc: mặt quay camera → `left_eye_*` nằm bên **phải ảnh**. Kính râm tối → 4 điểm mắt `occluded`.

---

## Bước 8 — Xuất file

### 8.1 Cách tay trên CVAT (để hiểu)

Trong job: **Menu → Export job dataset** (cũng có **Finish the job**):

![Menu Export / Finish](images/guide_live/B06_menu_export.png)

Chọn:

1. Format = **COCO Keypoints 1.0**  
2. **Save images = OFF**

![Export: COCO Keypoints 1.0, Save images tắt](images/guide_live/B07_export_coco.png)

### 8.2 Cách nhanh trên Terminal (khuyên dùng)

```powershell
dir src\exports
python lab.py export
```

![Terminal: dir exports + lab.py export / check_submission](images/guide_live/T04_export_dir.png)

`export` tạo:

- `src/exports/<HoVaTen>_persons.zip`  
- `src/exports/<HoVaTen>_faces.zip`  

rồi tự chạy kiểm. Cần thấy **`Kết quả: ĐẠT`** (không có `[LỖI]`).

Cuối cùng trong mỗi job: **Menu → Finish the job**.

---

## Bảng lệnh cả buổi (copy nhanh)

```powershell
cd C:\Users\...\K5-L2-KeypointPose
python lab.py check
python lab.py setup --mssv <MSSV> --ten <HoVaTen>
python lab.py export
python lab.py lite          # máy yếu
python lab.py stop          # tắt CVAT sau buổi
```

| Phím trên CVAT | Việc |
|---|---|
| `N` | bắt đầu / kết thúc vẽ |
| `F` / `D` | ảnh sau / trước |
| `Q` / `O` | occluded / outside (rê vào điểm) |
| `L` | khóa skeleton đã xong |
| `Ctrl+S` | lưu |
| `Esc` | hủy vẽ dở |
| `F1` | bảng phím đầy đủ |

---

## Checklist nộp bài

- [ ] Terminal: `check` → sẵn sàng; `setup` → có `assignment.json` đúng MSSV  
- [ ] CVAT: 12 ảnh người + 8 ảnh mặt đã gán  
- [ ] Không dùng `hidden`; điểm ngoài ảnh = `outside`  
- [ ] Terminal: `python lab.py export` → `Kết quả: ĐẠT`  
- [ ] Hai job **completed**  
- [ ] Báo cáo `report/individual/MSSV_HoTen.md` + 4 ảnh  
- [ ] Repo: `K5-L2-DAY04-HoVaTen-MSSV-KeypointPose`

Đọc thêm: [`GUIDELINE.md`](GUIDELINE.md) · [`CVAT_CHEATSHEET.md`](CVAT_CHEATSHEET.md) · [`../CHECKPOINTS.md`](../CHECKPOINTS.md) · [`../SUBMISSION.md`](../SUBMISSION.md).
