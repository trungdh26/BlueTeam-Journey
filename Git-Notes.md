# Git & GitHub - Ghi chú nhanh

## 1. Các lệnh cơ bản
| Lệnh | Mô tả |
|------|-------|
| `git clone [url]` | Sao chép repository từ GitHub về máy. |
| `git add .` | Thêm tất cả thay đổi vào vùng staging. |
| `git commit -m "message"` | Lưu lại thay đổi với một thông điệp. |
| `git push origin main` | Đẩy thay đổi lên GitHub. |
| `git pull origin main` | Kéo thay đổi mới nhất từ GitHub về máy. |
| `git status` | Kiểm tra trạng thái các file đã thay đổi. |
| `git log` | Xem lịch sử commit. |

## 2. Quản lý nhánh (Branch)
| Lệnh | Mô tả |
|------|-------|
| `git branch` | Xem danh sách nhánh. |
| `git branch [tên]` | Tạo nhánh mới. |
| `git checkout [tên]` | Chuyển sang nhánh khác. |
| `git merge [tên]` | Hợp nhất nhánh hiện tại với nhánh khác. |
| `git branch -d [tên]` | Xóa một nhánh (đã hợp nhất). |

## 3. Quy trình làm việc cơ bản (Workflow)
1. `git clone [url]` → Lấy repo về máy.
2. `git checkout -b [tên-nhánh]` → Tạo và chuyển sang nhánh mới.
3. Sửa file → `git add .` → `git commit -m "message"`.
4. `git push origin [tên-nhánh]` → Đẩy nhánh lên GitHub.
5. Tạo Pull Request trên GitHub để merge vào nhánh chính.
6. `git checkout main` → `git pull origin main` → Cập nhật nhánh chính.

## 4. Một số lưu ý khi dùng Git
- **Commit message**: Viết ngắn gọn, rõ ràng, bằng tiếng Anh (nếu có thể).
- **`.gitignore`**: Dùng để bỏ qua các file không cần thiết (ví dụ: file log, thư mục cache).
- **Pull Request**: Nên có mô tả rõ ràng về những gì đã thay đổi.

## 5. Tài liệu tham khảo
- [Git Documentation](https://git-scm.com/doc)
- [GitHub Guides](https://guides.github.com/)