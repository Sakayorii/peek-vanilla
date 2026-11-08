**Tiếng Việt** | [English](README.en.md)

# peek-vanilla

Bản port Vanilla JS của [Peek](https://github.com/doan-labs/peek) do Doan Labs làm, tạo avatar từ tên, không cần React, không cần bước build, không cần cơ sở dữ liệu.

Một chuỗi đưa vào, một khuôn mặt đi ra.

## Credit

Toàn bộ logic tạo avatar được port từ [@doan-labs/peek](https://github.com/doan-labs/peek) (giấy phép MIT, © 2026 Doan Labs). Repo này gỡ bỏ lớp bọc React và các type của TypeScript, chỉ giữ lại JavaScript thuần chạy ở mọi nơi.

Bản gốc: https://peek.doan-labs.com/

## Vì sao có bản port này?

Peek gốc là một component React. Điều đó ổn với các app React, nhưng nhiều trang web (như các trang Astro tĩnh) không dùng React, và việc thêm react, react-dom cùng một bước build JSX chỉ để render avatar là quá nặng cho một việc vốn là một hàm thuần túy: tên đưa vào, SVG đi ra. (Chính vòng lặp animation của bản gốc cũng đã bỏ qua React hoàn toàn, React không hề biết về từng frame.)

Bản port này:

- **Không phụ thuộc**: JavaScript thuần, không React, không bước build
- **Cùng kết quả**: SVG giống hệt bản gốc đến từng byte với cùng đầu vào
- **Cùng animation**: toàn bộ hệ thống animation (lò xo, chớp mắt, liếc mắt, thở, biên đạo cho từng biểu cảm) được port 1:1; mã animation của bản gốc vốn đã không chạm vào React
- **Dùng ngay**: `import { toSvg, mount } from 'peek-vanilla'` chạy trong mọi môi trường JS

## Tính năng

- Phụ kiện: mặc cho khuôn mặt bằng ba slot độc lập, kính mắt (glasses, sunglasses), đồ đội đầu (bow, cap, sprout) và đồ đeo cổ (tie), tất cả mặc định là none
- 4 kiểu mặt, 7 màu, 11 biểu cảm, hơn 20.000 tổ hợp
- Xác định: cùng một tên luôn cho cùng một avatar, mọi lúc
- Mỗi avatar SVG khoảng 2.5 KB
- Tùy biến được: ghi đè mặt, màu, mắt, lông mày, miệng, má, biểu cảm, hướng nhìn
- Không cơ sở dữ liệu, không gọi mạng, không phụ thuộc lúc chạy

## Cách dùng

```js
import { toSvg } from 'peek-vanilla';

// Cơ bản: tên đưa vào, SVG đi ra
const svg = toSvg('Sakayori');
document.getElementById('avatar').innerHTML = svg;

// Có tùy chọn
const svg2 = toSvg('Rem', {
  size: 128,
  expression: 'happy',
  square: false,  // cắt hình tròn
});

// Có phụ kiện: ba slot kết hợp tự do, mặc định đều là none
const svg3 = toSvg('Rem', {
  eyewear: 'glasses',
  headwear: 'sprout',
  neckwear: 'tie',
});
```

### Avatar sống (có animation, không cần React)

`mount()` là bản thay thế vanilla cho `<Peek animate />` của React, cùng một cây node, cùng hệ thống animation, cập nhật thẳng vào DOM:

```js
import { mount } from 'peek-vanilla';

const avatar = mount('Sakayori', '#avatar', {
  animate: true,
  expression: 'happy',
  gaze: 'pointer', // mắt đi theo con trỏ
});

// sau đó:
avatar.setExpression('surprised');
avatar.setGaze([0.5, -0.2]);
avatar.destroy(); // dừng vòng lặp, gỡ SVG
```

`mount(name, target, options)`: `target` là một element hoặc một CSS selector. Các tùy chọn giống `toSvg`, cộng thêm `animate` (mặc định `false`) và `className`. Trả về `{ el, live, setExpression, setGaze, destroy }`. Tôn trọng `prefers-reduced-motion` (render tư thế tĩnh thay vì chuyển động).

Muốn kiểm soát toàn bộ thì có sẵn `Live`: `new Live(svgElement, tree, who, opts, expression, gaze)`, dùng cùng `draw()` và `restPose()` từ cùng package này.

### Các tùy chọn

| Tùy chọn | Kiểu | Mặc định | Mô tả |
|--------|------|---------|-------------|
| `size` | number | 64 | Kích thước pixel (quyết định độ dày nét vẽ) |
| `expression` | string | 'normal' | happy, sad, angry, surprised, v.v. |
| `face` | string | (theo tên) | Ghi đè kiểu mặt |
| `color` | string | (theo tên) | Ghi đè màu |
| `eyes` | string | (theo tên) | Ghi đè mắt |
| `brows` | string | (theo tên) | Ghi đè lông mày |
| `mouth` | string | (theo tên) | Ghi đè miệng |
| `cheeks` | string | (theo tên) | Ghi đè má |
| `eyewear` | string | 'none' | glasses hoặc sunglasses |
| `headwear` | string | 'none' | bow, cap hoặc sprout |
| `neckwear` | string | 'none' | tie |
| `gaze` | [x, y] | (không có) | Hướng nhìn của mắt, từ -1 đến 1 |
| `square` | boolean | true | false = cắt hình tròn |
| `title` | string/false | tên | Tên dùng cho trợ năng |

## Bản này port từ upstream phiên bản nào?

Bản này port từ upstream @doan-labs/peek 1.1.0, bao gồm hệ thống phụ kiện. Số version trên npm của bản port là 1.2.0, vì npm 1.1.0 đã bị bản phát hành trước của chính bản port này dùng mất, và bản đó dựa trên upstream 1.0. Các khuôn mặt không có phụ kiện vẫn render giống hệt trước đây đến từng byte, và id SVG của khuôn mặt không có phụ kiện vẫn được tính đúng như bản phát hành trước.

## Giấy phép

MIT, giống bản gốc. Xem file LICENSE.
