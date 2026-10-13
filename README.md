# مصحف التجويد بخرائط إحداثية للتفاعل مع الآيات
# Tajweed Mushaf with Verse Coordinate Maps for Interactive Reading

<div dir="rtl">

## نظرة عامة

مصحف التجويد كاملاً في **٦٠٤ صفحات**، ومعه **خريطة إحداثية دقيقة لكل آية** (٦٬٢٣٦ آية)، تتيح لتطبيقك أن يجعل الآيات على الصفحة المصوّرة قابلة للتفاعل: الضغط على آية لتظليلها، وفتح تفسيرها، ومشاركتها، وتتبّع الآية أثناء التلاوة، وغير ذلك.

تم تحسين صور الصفحات للعرض على شاشات الهواتف والأجهزة اللوحية بدقة عالية، وبحجم مثالي بصيغة **WebP** (٧٤٠×١١٥٠ بكسل).

### محتويات المستودع

| المجلد | المحتوى |
|---|---|
| `Quran Pages Images/` | صور الصفحات `p_1.webp` … `p_604.webp` |
| `Overlays/` | صور المعاينة: الصفحة مع حدود كل آية مرسومة عليها، لمراجعة الخرائط بصرياً |
| `/versePolygons.min.json` | خرائط الآيات لجميع الصفحات في ملف واحد مضغوط (٣٩١ كيلوبايت، نحو ١٠٥ كيلوبايت بعد ضغط gzip) |

### صيغة الخريطة

```json
{"w":740,"h":1150,"p":{"3":{"13":[[684,62,58,62,58,126,512,126,512,194,684,194]]}}}
```

- `w` و`h`: أبعاد الصورة المرجعية التي قيست عليها الإحداثيات (٧٤٠×١١٥٠).
- `p`: رقم الصفحة ← رقم الآية ← قائمة المضلّعات.
- كل مضلّع قائمة مسطّحة `[x1, y1, x2, y2, …]` بإحداثيات صحيحة بالبكسل.
- الآية التي تلتف من نهاية سطر إلى بداية السطر التالي لها **مضلّعان منفصلان** أو أكثر.
- رقم الآية هو **ترتيبها التسلسلي في المصحف من ١ إلى ٦٢٣٦** (الفاتحة ١ ← ١، البقرة ١ ← ٨، آخر آية في الناس ← ٦٢٣٦). لا توجد عناصر لرؤوس السور في الخريطة، فهي لا تعتمد على أي نموذج بيانات بعينه.
- لا توجد آية تعبر من صفحة إلى أخرى؛ كل آية تقع كاملة في صفحتها.

### الاستخدام: مستقل عن حجم الشاشة

الإحداثيات مخزّنة في فضاء الصورة المرجعية، وليست في بكسلات الشاشة، لذلك تعمل بالطريقة نفسها على الهواتف الصغيرة والأجهزة اللوحية الكبيرة:

1. ضع الصورة وطبقة التفاعل في صندوق واحد نسبته `740 / 1150`.
2. **الرسم:** `x_شاشة = x × (عرض_الصندوق / 740)` وكذلك للمحور الرأسي.
3. **اختبار اللمس:** حوّل نقطة اللمس إلى فضاء الصورة بالقسمة على أبعاد الصندوق والضرب في `740 × 1150`، ثم طبّق اختبار «النقطة داخل المضلّع» (ray casting) على مضلّعات كل آية.


إن وجدت آية بحدود غير دقيقة فافتح Issue مع رقم الصفحة والآية.
</div>

## Overview

The complete Tajweed Mushaf in **604 pages**, paired with an accurate **coordinate map for every verse** (6,236 verses), so an app can make the verses on the page image interactive: press to highlight a verse, open its tafseer, share it, follow it during recitation, and more.

The page images are optimised for phone and tablet screens: high resolution and an ideal file size, in **WebP** at 740×1150 px.

### What's inside

| Folder | Contents |
|---|---|
| `Quran Pages Images/` | Page images `p_1.webp` … `p_604.webp` |
| `Overlays/` | Preview images: each page with its verse outlines drawn on top, for visual review of the maps |
| `/versePolygons.min.json` | The verse maps for all pages in one minified file (391 KB, about 105 KB gzipped) |

### Map format

```json
{"w":740,"h":1150,"p":{"3":{"13":[[684,62,58,62,58,126,512,126,512,194,684,194]]}}}
```

- `w`, `h`: size of the reference image the coordinates were measured on (740×1150).
- `p`: page number → verse number → list of polygons.
- Each polygon is a flat list `[x1, y1, x2, y2, …]` of integer pixel coordinates.
- A verse that wraps from the end of one line to the start of the next has **two or more disjoint polygons**.
- The verse number is its **sequential position in the Quran, 1 to 6236** (Al-Fatihah 1:1 → 1, Al-Baqarah 2:1 → 8, the last verse of An-Nas → 6236). Surah headers are not part of the map, so it does not depend on any particular data model.
- No verse crosses a page boundary; every verse sits entirely on its own page.

### Usage: independent of screen size

Coordinates live in the reference image's space, not in screen pixels, so they behave identically on small phones and large tablets:

1. Put the image and the interaction layer in one box with aspect ratio `740 / 1150`.
2. **Drawing:** `screen_x = x × (box_width / 740)`, and likewise for the vertical axis.
3. **Hit-testing:** convert the touch point to reference space by dividing by the box size and multiplying by `740 × 1150`, then run a point-in-polygon test (ray casting) against each verse's polygons.

```kotlin
// Kotlin / Jetpack Compose: touch → reference space
val sx = touch.x * 740f / boxWidthPx
val sy = touch.y * 1150f / boxHeightPx
val verse = regions.firstOrNull { it.contains(sx, sy) }
```


If you find a verse with inaccurate bounds, please open an issue with the page and verse number.
