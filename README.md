# n8n Zalo + Facebook content bot

Bộ workflow **n8n** tự động hóa quy trình nội dung: AI viết bài (Gemini) hoặc nhập bài tự viết → duyệt và sửa ngay trong **nhóm Zalo** bằng lệnh → đăng **Fanpage Facebook** (album nhiều ảnh) và **bài viết Zalo OA**, có xem trước (đăng ẩn) và hẹn giờ.

## Workflow

| Workflow | Việc | Số node | File |
|---|---|---|---|
| Zalo OA - Group Content Bot (GMF) | Bot nhóm Zalo: nhận lệnh, ảnh, link video và chuyển sang workflow con; nhớ ảnh gửi 15 phút (Data Table) | 66 | [zalo-oa-group-content-bot-gmf.json](workflows/zalo-oa-group-content-bot-gmf.json) |
| AI viết bài theo tiêu đề trên Sheet (chờ duyệt) | Gemini viết bài từ tiêu đề trên Google Sheet → dòng Chờ duyệt → gửi nhóm | 41 | [ai-viet-bai-theo-tieu-de-tren-sheet-cho-duyet.json](workflows/ai-viet-bai-theo-tieu-de-tren-sheet-cho-duyet.json) |
| Duyệt bài từ Zalo | /duyet, /bo, /hengio, /choduyet | 8 | [duyet-bai-tu-zalo.json](workflows/duyet-bai-tu-zalo.json) |
| Sửa bài từ Zalo | /sua (AI), /dungbai, /anh, /lichsu, /xem, /hoantac – lưu lịch sử mọi bản | 64 | [sua-bai-tu-zalo.json](workflows/sua-bai-tu-zalo.json) |
| Xem trước & công khai | /xemtruoc đăng ẨN Fanpage + Zalo OA; /congkhai ngay hoặc hẹn giờ | 35 | [xem-truoc-cong-khai.json](workflows/xem-truoc-cong-khai.json) |
| Tự động đăng bài Fanpage GOS Academy | Đăng bài Chờ đăng lên Fanpage (album nhiều ảnh) theo giờ hẹn | 31 | [tu-dong-dang-bai-fanpage-gos-academy.json](workflows/tu-dong-dang-bai-fanpage-gos-academy.json) |
| Đăng chéo đa kênh | Đăng bài viết Zalo OA (AI xếp ảnh theo ý, đánh dấu [ảnh N]) + báo nhóm | 43 | [dang-cheo-da-kenh.json](workflows/dang-cheo-da-kenh.json) |
| Đặt bộ ảnh | /datanh – đặt cả bộ ảnh (vuông = ảnh chính, ngang = ảnh bìa) | 23 | [dat-bo-anh.json](workflows/dat-bo-anh.json) |
| Dùng ảnh tự thiết kế | /dunganh – dùng 1 ảnh tự làm cho ảnh chính / ảnh bìa | 26 | [dung-anh-tu-thiet-ke.json](workflows/dung-anh-tu-thiet-ke.json) |
| Thêm ảnh cho bài | /themanh, /xoaanh, /xepanh, /xemanh | 23 | [them-anh-cho-bai.json](workflows/them-anh-cho-bai.json) |
| Tạo ảnh bìa Zalo OA | /anhbia – AI dàn ảnh ngang 16:9 từ ảnh chính | 32 | [tao-anh-bia-zalo-oa.json](workflows/tao-anh-bia-zalo-oa.json) |
| Ghép ảnh xem trước (lưới) | Workflow con: ghép nhiều ảnh thành 1 ảnh lưới đánh số | 9 | [ghep-anh-xem-truoc-luoi.json](workflows/ghep-anh-xem-truoc-luoi.json) |
| Lọc ảnh trùng | Workflow con: so nội dung ảnh (dHash) để bỏ ảnh gửi lặp | 6 | [loc-anh-trung.json](workflows/loc-anh-trung.json) |
| Zalo OA - Token | Tự làm mới access token Zalo OA | 14 | [zalo-oa-token.json](workflows/zalo-oa-token.json) |
| Báo lỗi về Zalo | Error workflow: báo lỗi hệ thống vào nhóm | 5 | [bao-loi-ve-zalo.json](workflows/bao-loi-ve-zalo.json) |
| Zalo OA - Ads Image | /ads – ảnh + nội dung quảng cáo | 26 | [zalo-oa-ads-image.json](workflows/zalo-oa-ads-image.json) |
| Lấy số liệu Ads GOSTECH01 vào Sheet | Lấy số liệu Facebook Ads vào Google Sheet (đang tắt) | 16 | [lay-so-lieu-ads-gostech01-vao-sheet.json](workflows/lay-so-lieu-ads-gostech01-vao-sheet.json) |

## Lệnh trong nhóm Zalo

| Nhóm | Lệnh |
|---|---|
| Tạo bài | `/baimoi` + bài tự viết · `/tieude <tiêu đề>` · `/dstieude` · gửi link YouTube/TikTok |
| Ảnh | `/datanh` · `/xemanh` · `/xepanh` · `/themanh [fb\|zalo]` · `/xoaanh` · `/dunganh [fb\|zalo]` · `/anh` · `/anhbia` · dòng `[ảnh N]` trong bài |
| Sửa chữ | `/dungbai` · `/sua` · `/lichsu` · `/xem` · `/hoantac` |
| Duyệt & đăng | `/choduyet` · `/xemtruoc` · `/congkhai [giờ\|huy]` · `/duyet [giờ]` · `/hengio` · `/bo` |

## Dữ liệu

Google Sheet "lịch đăng": `ID | Tiêu đề | Nội dung | Link ảnh | Thời gian đăng | Trạng thái | Post ID | Link bài | Ghi chú | Ảnh bìa OA | Ảnh thêm | Ảnh Zalo`, thêm tab "Lịch sử sửa". Data Table n8n: token Zalo OA, ảnh chờ.

## Đã lược bỏ trước khi công khai

- Token, API key, secret (Facebook, Zalo, Google…) → `<REDACTED>`; ID credential n8n (chỉ giữ tên).
- ID Google Sheet, nhóm Zalo, Zalo OA, Trang Facebook, Data Table, ID người dùng Zalo, địa chỉ n8n, đường dẫn webhook → `<...>`.
- Dữ liệu chạy thử (pinData), staticData.

## Nhập vào n8n

1. **Import from File** từng file trong `workflows/`.
2. Chọn lại credential (Facebook Graph API, Google Sheets, Google Gemini) và thay các giá trị `<...>` bằng ID của bạn.
3. Ở các node *Execute Workflow*, chọn lại workflow con; bật (Publish) workflow con trước, bot GMF sau.
