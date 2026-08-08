# Data Model: Khiếu nại & Thu hồi vi phạm

## Entity: Violation (mở rộng)

Collection `violations`. Trường **in đậm** là bổ sung mới.

| Trường | Kiểu | Bắt buộc | Ghi chú |
|--------|------|----------|---------|
| student | ObjectId → User | ✅ | Sinh viên bị ghi vi phạm |
| reason | string | ✅ | Lý do vi phạm |
| points | number (1–100) | ✅ | Số điểm bị trừ |
| markedBy | ObjectId → User | – | Admin đã ghi nhận |
| scoreAfter | number | – | Điểm còn lại tại thời điểm ghi |
| **status** | **enum VIOLATION_STATUS** | ✅ | Mặc định `ACTIVE` |
| **appealReason** | **string (≤500)** | – | Lý do sinh viên khiếu nại |
| **appealedAt** | **Date** | – | Thời điểm khiếu nại |
| **reviewNote** | **string (≤500)** | – | Ghi chú của admin khi duyệt |
| **reviewedBy** | **ObjectId → User** | – | Admin đã duyệt |
| **reviewedAt** | **Date** | – | Thời điểm duyệt/thu hồi |

## Enum: VIOLATION_STATUS

| Giá trị | Ý nghĩa |
|---------|---------|
| `ACTIVE` | Vi phạm đang hiệu lực, đã trừ điểm (mặc định) |
| `APPEAL_PENDING` | Sinh viên đã khiếu nại, chờ ban quản lý duyệt |
| `REVOKED` | Đã thu hồi (do duyệt chấp nhận hoặc admin thu hồi trực tiếp) → đã hoàn điểm |
| `APPEAL_REJECTED` | Khiếu nại bị từ chối, vi phạm vẫn hiệu lực, điểm giữ nguyên |

## Sơ đồ chuyển trạng thái

```
                 appeal (student)         review ACCEPT (admin)
   ACTIVE ───────────────────────▶ APPEAL_PENDING ───────────────▶ REVOKED  (hoàn điểm)
     │                                   │
     │                                   └── review REJECT (admin) ─▶ APPEAL_REJECTED (giữ điểm)
     │
     └────────── revoke trực tiếp (admin, từ ACTIVE / APPEAL_PENDING / APPEAL_REJECTED) ─▶ REVOKED (hoàn điểm)
```

## Ràng buộc & bất biến

- Khiếu nại chỉ hợp lệ khi `status = ACTIVE` và `student === người gọi`; `appealReason` khác rỗng, ≤500.
- Duyệt chỉ hợp lệ khi `status = APPEAL_PENDING`; `decision ∈ {ACCEPT, REJECT}`.
- Thu hồi trực tiếp hợp lệ khi `status ≠ REVOKED`.
- **Hoàn điểm chỉ xảy ra đúng một lần** khi vi phạm chuyển sang `REVOKED`: `behaviorScore = min(100, behaviorScore + points)`.
- `REJECT` không thay đổi `behaviorScore`.
- `reviewNote`, `appealReason` ≤ 500 ký tự.
