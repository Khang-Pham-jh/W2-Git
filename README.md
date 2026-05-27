# PHẦN 2: BÀI TẬP (EXERCISES)

**Bối cảnh:** Nhánh `master` (chứa các tính năng đang test) và nhánh `production` (chứa code chạy thực tế).

### Câu 1: Khi tạo tính năng mới, nên dựa trên nhánh nào và tại sao?

**Câu trả lời:** Nên dựa trên nhánh `master` (nếu quy trình của bạn quy định master là nơi chứa mã nguồn mới nhất đã qua kiểm thử cơ bản) hoặc `production` (nếu bạn muốn đảm bảo tính năng mới bắt đầu từ trạng thái ổn định nhất của sản phẩm). Trong hầu hết các quy trình hiện đại, ta tạo nhánh từ nhánh chính (main/master).

**Lệnh Git:**
```bash
git checkout master
git pull origin master
git checkout -b feature/new-feature-name
```

---

### Câu 2: Nếu nhánh tính năng có lỗi và chưa được gộp vào production, bạn sẽ làm gì?

**Câu trả lời:** Bạn nên thực hiện sửa lỗi ngay trên nhánh tính năng đó. Nếu lỗi nằm ở commit cuối cùng và bạn chưa đẩy code lên server, bạn có thể dùng `--amend` để sửa đổi. Nếu đã đẩy lên hoặc lỗi nằm ở các commit cũ hơn, hãy tạo một commit sửa lỗi mới trên chính nhánh đó.

**Lệnh Git:**
```bash
git checkout master
git pull origin master
git checkout feature/login 
git add .
git commit -m "fix: resolve bug in feature description"

# Hoặc nếu sửa lỗi cho commit vừa mới tạo xong (chưa push):
git commit --amend --no-edit
```

---

### Câu 3: Cách loại bỏ một tính năng (gồm nhiều commit) đã lỡ merge vào production?

**Tình huống:** Đã merge `feature/delete-user` (IDs: `0492978`, `fc9348c`, `k101100`) vào `production`, sau đó có thêm commit `a1fsas8` đè lên trên.

**Câu trả lời:** Vì đã có commit mới (`a1fsas8`) nằm trên đỉnh, bạn **không nên dùng reset** vì sẽ làm mất commit `a1fsas8`. Cách an toàn nhất là dùng **`git revert`** để tạo các commit đảo ngược các thay đổi đã merge nhằm giữ an toàn cho lịch sử dự án.

**Lệnh Git:** 
Bạn có thể revert từng commit theo thứ tự ngược lại, hoặc revert chính commit merge (nếu bạn biết ID của commit merge đó).

```bash
# Revert từng commit của tính năng lỗi
git revert k101100
git revert fc9348c
git revert 0492978

# Hoặc nếu là revert một Merge Commit (giả sử ID merge là m12345):
# git revert -m 1 m12345
```
