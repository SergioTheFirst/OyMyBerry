# OyMyBerry 

**Сладкие моменты, созданные дома**

Некоммерческий веб-проект о домашних десертах, приготовленных с любовью. Светлый премиальный дизайн, адаптивная вёрстка, централизованное управление контентом через `config.json`.

> **Домен:** [oymyberry.ru](https://oymyberry.ru)  
> **Хостинг:** GitHub Pages (статика)  
> **Админ-панель:** Локально через `server.py` (автономная, не в репозитории)

---

## Содержание

- [Ключевые особенности](#ключевые-особенности)
- [Технический стек](#технический-стек)
- [Быстрый старт](#быстрый-старт)
- [Архитектура](#архитектура)
- [Управление контентом](#управление-контентом)
- [Деплой](#деплой)
- [Структура проекта](#структура-проекта)
- [Авторские права](#авторские-права)

---

## Ключевые особенности

- **Некоммерческий фокус** — нет упоминаний заказов, продаж или цен нигде на сайте
- **Светлая премиальная тема** — карамельно-кремовая палитра с акцентами золотого
- **Тексты из `config.json`** — все заголовки, контакты, SEO, категории и тексты управляются из одного JSON-файла
- **Адаптивная галерея** — ленивая загрузка, фильтрация по категориям, hover-эффекты
- **Анимации при скролле** — IntersectionObserver, `animate-on-scroll`
- **Админ-панель (локальная)** — добавление/удаление/редактирование работ, обновление текстов, публикация на GitHub Pages
- **SEO-готовность** — Open Graph, meta description, keywords, robots.txt, CNAME
- **Шрифты Google Fonts** — Cormorant Garamond (заголовки, логотип) + DM Sans (текст, навигация)

---

## Технический стек

| Слой | Технология |
|------|------------|
| Frontend | HTML5 + CSS3 + vanilla JavaScript (ES6) |
| CSS | Custom properties (variables), flexbox, grid, media queries |
| JS | Vanilla ES6, IntersectionObserver, Fetch API, localStorage |
| Backend (dev only) | Python 3 `http.server` с кастомным POST-обработчиком |
| Хостинг | GitHub Pages (статический сайт) |
| Домен | oymyberry.ru (CNAME-файл для GitHub Pages) |
| Шрифты | [Cormorant Garamond](https://fonts.google.com/specimen/Cormorant+Garamond) + [DM Sans](https://fonts.google.com/specimen/DM+Sans) |

---

## Быстрый старт

### 1. Клонирование

```bash
git clone https://github.com/SergioTheFirst/OyMyBerry.git
cd OyMyBerry
```

### 2. Локальный запуск

**Windows (рекомендуется):**
```cmd
server.bat
```

**Любая ОС (Python 3):**
```bash
python server.py
```

Сервер запустится на `http://localhost:8000`. Остановить — `Ctrl+C`.

### 3. Админ-панель (только локально)

Откройте `http://localhost:8000/admin.html` после запуска сервера. Доступна функциональность:

- Добавление новых работ (фото + hover-фото + описание + категория)
- Удаление/редактирование существующих работ
- Редактирование `config.json` — все тексты сайта
- Публикация на GitHub Pages через `gh` CLI (или ручной push)

> **Важно:** `admin.html` и `server.py` исключены из `.gitignore` и не попадают в production (GitHub Pages).

---

## Архитектура

### Принцип `data-config`

Все динамические тексты в HTML помечаются атрибутом `data-config`, а значение берётся из `config.json` по пути через точку:

```html
<!-- HTML -->
<h2 data-config="about.title">О проекте</h2>
<p data-config="about.text">Каждый десерт...</p>
```

```json
// config.json
{
  "about": {
    "title": "О проекте",
    "text": "Каждый десерт, который здесь появляется..."
  }
}
```

Инициализация происходит в `js/main.js` — `initConfig()` загружает `config.json` и заменяет `textContent`/`innerHTML` всех элементов с `data-config`.

### Жизненный цикл страницы

```
Загрузка HTML
    ↓
CSS + Google Fonts (preload)
    ↓
main.js инициализирует:
  - initConfig()        → загрузка текстов из config.json
  - initHeaderScroll()  → скрытие/показ хедера при скролле
  - initMobileMenu()    → бургер-меню
  - initScrollTop()     → кнопка "наверх"
  - initAnimations()    → IntersectionObserver для .animate-on-scroll
  - initWorks()         → загрузка галереи из works/works.json
  - initFilters()       → фильтрация по категориям
  - initLightbox()      → клик по фото → полноэкранный просмотр
```

### Структура конфигурации

```json
{
  "site": { "name", "tagline", "subtitle", "hero_contact", "lang", "url" },
  "about": { "title", "text", "photo", "quote" },
  "contact": { "phone", "instagram", "email", "telegram", "whatsapp", "vk", ... },
  "nav": { "home", "gallery", "about", "contact" },
  "footer": { "copyright", "made_with" },
  "categories": { "all", "d1"..."d6" },
  "analytics": { "yandex_metrika_id", "google_analytics_id", "cloudflare_token" },
  "github": { "owner", "repo", "branch" },
  "seo": { "title", "description", "keywords" }
}
```

---

## Управление контентом

### Тексты и SEO

Всё в `config.json`. Отредактируйте файл напрямую или через админ-панель (`admin.html`). Изменения применяются сразу после перезагрузки страницы.

### Галерея работ

Работы хранятся в `works/works.json`:

```json
{
  "works": [
    {
      "id": "20240115_143022",
      "filename": "20240115_143022.jpg",
      "filename_hover": "20240115_143022_hover.jpg",
      "title": "Шоколадный торт",
      "description": "Домашний трюфельный бисквит...",
      "category": "d1",
      "date": "2024-01-15"
    }
  ]
}
```

Фотографии — в `works/images/`. Формат: `YYYYMMDD_HHMMSS.jpg`.

### Добавление работ через админ-панель

1. Запустите `server.py`
2. Откройте `http://localhost:8000/admin.html`
3. Заполните форму (фото + hover-фото + название + описание + категория)
4. Нажмите "Добавить" — файл сохранится в `works/images/`, JSON обновится автоматически

### Категории

Категории заданы в `config.json` → `categories`. Ключи: `d1`–`d5` для 5 групп десертов + `d6` ("Другое"). Переименуйте в `config.json` — фильтрация обновится автоматически.

---

## Деплой

### GitHub Pages (основной)

1. Убедитесь, что `CNAME` содержит `oymyberry.ru`
2. Push на GitHub:
   ```bash
   git push origin main
   ```
3. В настройках репозитория GitHub → Pages → Source: Deploy from a branch → `main` / `/(root)`
4. DNS A-записи домена `oymyberry.ru` → IP GitHub Pages (185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153)

### Ручной деплой из админ-панели

Админ-панель генерирует bash-скрипт для `gh` CLI:
```bash
cd OyMyBerry
gh auth login
git add .
git commit -m "Update content"
git push origin main
```

---

## Структура проекта

```
OyMyBerry/
├── index.html              # Главная (hero + галерея + о проекте + контакты)
├── gallery.html            # Галерея со всеми работами
├── about.html              # Страница "О проекте"
├── contact.html            # Страница контактов + форма обратной связи
├── config.json             # ⭐ Все тексты, SEO, контакты, категории
├── css/
│   └── style.css           # Дизайн-система (переменные, компоненты, адаптив)
├── js/
│   ├── main.js             # Ядро: config-loader, анимации, галерея, lightbox
│   └── admin-edit.js       # Редактор config.json (inline JSON editor)
├── works/
│   ├── works.json          # Данные галереи (автогенерируется)
│   └── images/             # Фото работ (автозагружаются)
├── uploads/                # Временные загрузки (dev)
├── favicon.ico             # Иконка сайта
├── favicon.svg             # SVG-иконка
├── back.png                # Фоновое изображение hero-секции
├── CNAME                   # Домен для GitHub Pages (oymyberry.ru)
├── robots.txt              # SEO: разрешить всё
├── .gitignore              # Исключения: *.py, *.bat, admin.html, secrets.json
├── server.py               # 🔒 Локальный dev-сервер (не в production)
├── server.bat              # 🔒 Запуск сервера на Windows (не в production)
└── README.md               # Этот файл
```

---

## Адаптивные breakpoint'ы

| Breakpoint | Ширина | Ключевые изменения |
|------------|--------|--------------------|
| Desktop XL | ≥1440px | 6-колоночная галерея, hero 6vw padding |
| Desktop | ≥1024px | 4-колонки, hover-эффекты вкл. |
| Tablet | ≤1023px | 2-колонки, бургер-меню, hero font-size ↓ |
| Mobile | ≤767px | 1-колонка, hero align-items: flex-end, hamburger nav |
| Mobile S | ≤480px | Минимальные отступы, компактные шрифты |

---

## Зависимости

- **Production:** нет внешних зависимостей (vanilla HTML/CSS/JS)
- **Dev-сервер:** Python 3.7+ (только `http.server` + `cgi` из стандартной библиотеки)
- **Деплой:** Git, GitHub CLI (`gh`) — опционально

---

## Авторские права

© OyMyBerry. Все права защищены.  
Дизайн и разработка — личный некоммерческий проект.  
Фотографии десертов — оригинальные, сделанные автором проекта.

> **Важно:** Этот проект не является коммерческим. Сайт не содержит функций оформления заказов, оплаты или продаж. Все материалы публикуются в информационных и вдохновляющих целях.

---

*Сделано с душой*
