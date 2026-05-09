# Gagauzka Tours — правила для Claude

## Проект
Сайт туристического агентства **Gagauzka Tours** (Молдова). Гид — Маша (Maria Jelezoglo).
Основной файл: `index.html`. Изображения: `assets/images/`.
Контакт: `work.jelezoglomaria@gmail.com` | Instagram: `@gagauzkatours`

---

## Изображения туров (карточки `.tc-img`)

CSS класса по умолчанию: `object-fit: cover` — изображение заполняет карточку с обрезкой.

### Правило выбора object-fit

**Перед добавлением любого фото — прочитай его через Read и оцени ориентацию:**

| Ситуация | Решение |
|----------|---------|
| Фото **горизонтальное** (пейзаж, широкая сцена) | `object-fit:cover` + подобрать `object-position` |
| Фото **вертикальное** (портрет, человек в полный рост) | `object-fit:contain` + blurred background |

**Вертикальное фото с `object-fit:cover` ВСЕГДА обрежет голову/верх — не использовать.**

Для `object-fit:contain` использовать технику **blurred background**:
- Добавить `<div>` с тем же фото как фоном + `filter:blur(18px)` ПЕРЕД `<img>` в DOM
- У `<div>` и `<img>` **НЕ ставить z-index** — порядок слоёв определяется порядком в DOM
- Порядок в DOM (снизу вверх): blurred-div → img.tc-img → .tc-veil → .tc-body
- Если добавить z-index на img — он вылезет поверх veil и body, текст уйдёт под фото

```html
<div style="position:absolute;inset:0;background:url('путь') center/cover;filter:blur(18px) brightness(0.55);transform:scale(1.1)"></div>
<img class="tc-img" src="путь" style="object-fit:contain; object-position:center center; background:transparent">
```

### Текущие карточки туров
| Тур | Файл | Стиль | Примечание |
|-----|------|-------|------------|
| Gagauzia Discovery | `photo_2026-05-09_21-44-36.jpg` | `contain` + blurred bg | Вертикальное фото семьи в национальных костюмах |
| Soviet Nostalgia Tour | `tour-pmr.jpg` | `cover`, `center 40%` | Горизонтальное фото парка статуй |
| Guinness World Record | `tour-pmr.jpg` | `cover`, `center 40%` | Горизонтальное фото (та же локация) |
| Odessa Seafood Tour | `tour-odessa2.jpg` | `cover`, `center 30%` | Горизонтальное аэрофото заката |
| Chișinău City Tour | `gallery-night-chisinau.jpg` | `contain` + blurred bg | Вертикальное ночное фото |
| Moldova Tour | `hero.jpg` | `contain` + blurred bg | Вертикальное фото пейзаж с рекой |

---

## Работа с iPhone фото (HEIC формат)

Браузеры **не поддерживают HEIC** — нужно конвертировать в JPG перед использованием на сайте.

### Автоматическая конвертация через Python

**Зависимость установлена:** `pillow-heif` (установлен 29.04.2026)

```python
import pillow_heif
from PIL import Image
import os

pillow_heif.register_heif_opener()

src = r'D:\Code\Gagauzka Tours\Gagauzka Tours\Gagauzia'   # папка с HEIC
dst = r'D:\Code\Gagauzka Tours\Gagauzka Tours\assets\images'  # куда сохранять

# Конвертировать один файл:
img = Image.open(os.path.join(src, 'IMG_0075.HEIC'))
img.convert('RGB').save(os.path.join(dst, 'my-photo.jpg'), 'JPEG', quality=88)

# Конвертировать все HEIC в папке:
for f in os.listdir(src):
    if f.upper().endswith('.HEIC'):
        img = Image.open(os.path.join(src, f))
        out = os.path.join(dst, f.replace('.HEIC', '.jpg').replace('.heic', '.jpg'))
        img.convert('RGB').save(out, 'JPEG', quality=88)
        print(f'Saved: {os.path.basename(out)}')
```

**Через PowerShell (запустить в терминале):**
```powershell
python -c "
import pillow_heif; from PIL import Image; import os
pillow_heif.register_heif_opener()
src = r'ПАПКА_С_HEIC'
dst = r'D:\Code\Gagauzka Tours\Gagauzka Tours\assets\images'
for f in os.listdir(src):
    if f.upper().endswith('.HEIC'):
        img = Image.open(os.path.join(src, f))
        out = os.path.join(dst, f[:-5] + '.jpg')
        img.convert('RGB').save(out, 'JPEG', quality=88)
        print('Saved:', out)
"
```

### Папки с iPhone фото
| Папка | Содержимое |
|-------|-----------|
| `Gagauzia/` | Фото из Гагаузии — застолья, костюмы, виноградники |
| `PMR/` | Фото из Приднестровья |
| `Moldova/` | Фото из Молдовы |
| `Tourists/` | Фото туристов |
| `Maria/` | Фото гида Маши |

---

## Алгоритм добавления фото в карточку

1. Прочитай изображение через Read чтобы увидеть содержимое
2. Если файл HEIC — сначала сконвертируй в JPG (см. выше)
3. Определи ориентацию: горизонтальное или вертикальное
4. **Вертикальное** → `object-fit:contain` + blurred background
5. **Горизонтальное** → `object-fit:cover` + подобрать `object-position`:
   - Объект вверху → низкий % (0–15%)
   - Объект в центре → 30–50%
   - Объект внизу → высокий % (70–100%)

---

## Цветовая палитра (ребрендинг Gagauzka Tours)

```css
:root {
  --lime:   #D4FF00;   /* Лайм-жёлтый из логотипа */
  --purple: #7733CC;   /* Фиолетовый из логотипа */
  --dark:   #1A1915;
  --bg:     #F5F4EE;
  --bg2:    #EDECE5;
  --white:  #FFFFFF;
  --muted:  #7C7C6E;
  --border: #E0DFDA;
}
```

Логотип: `assets/images/logo_MAIN.png` (фиолетовые контуры + жёлтый платок)

---

## Структура секций сайта

```
#hero → #stats → #tours → #builder → #gallery → #includes → #guides → #reviews → #booking → footer
```

| Секция | Фон | Описание |
|--------|-----|----------|
| `#hero` | Фото `IMG_5032.JPG` | Полноэкранный hero |
| `#stats` | white | 4 цифры: 6 туров, ≤7 человек, 3ч ответ, ★5 |
| `#tours` | `--bg` | 6 карточек туров, 2 колонки × 3 ряда |
| `#builder` | `#18102E` (тёмно-фиолетовый) | Конструктор тура |
| `#gallery` | white | Горизонтальная прокрутка фото |
| `#includes` | `--bg2` | 6 карточек включённого |
| `#guides` | `--bg` | Маша (65%) + Кристи (35%) |
| `#reviews` | white | 9 карточек отзывов, 32 отзыва 5★ |
| `#booking` | `--bg2` | Форма + контакты |
| `footer` | `--dark` | Логотип, туры, контакты |
