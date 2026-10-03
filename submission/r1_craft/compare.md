# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_270517.jpg
## adasind_271039.jpg
- L8 center IGNORE_SCOPE
- L7 center SPURIOUS
- L9 center SPURIOUS
- R10 center MISSING
## adasind_295948.jpg
- L4 mid IGNORE_SCOPE
- L5 mid IGNORE_SCOPE
- L6 mid IGNORE_SCOPE
- L3+R1 center WRONG_CLASS
- L7 center SPURIOUS

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 12 | 10 | 2 | 4 |
| mid | 5 | 5 | 0 | 0 |
| edge | 3 | 3 | 0 | 0 |
