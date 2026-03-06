# Figma MCP — Правила работы

## Подключение (один раз на машину)

**Вариант 1 — Remote сервер (рекомендуется):**
```bash
claude mcp add --transport http figma https://mcp.figma.com/mcp
```
Затем в Claude Code: `/mcp` → выбери `figma` → OAuth → `Authentication successful`.

**Вариант 2 — Figma Desktop App:**
```bash
# Открой Figma Desktop → Preferences → включи "Dev Mode MCP Server"
# Сервер запустится на http://127.0.0.1:3845/sse
claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse
```

Проверить: `/mcp` в Claude Code — статус `connected`.

---

## Как передавать ссылки агенту

Всегда передавай ссылку с `node-id`. Без него агент читает всё дерево страницы — медленно и неточно.

**Как получить ссылку с node-id:**
1. Открой Figma
2. Выдели нужный фрейм или компонент
3. Скопируй URL из адресной строки — он уже содержит `node-id`

```
# Правильно — с node-id
https://www.figma.com/file/AbCdEf123/Project?node-id=123%3A456

# Неправильно — без node-id (агент читает всю страницу)
https://www.figma.com/file/AbCdEf123/Project
```

---

## Что можно читать через MCP

```
✅ Цвета (fill, stroke, background)
✅ Отступы (padding, margin, gap)
✅ Размеры шрифтов, font-weight, line-height
✅ Размеры элементов (width, height)
✅ Структуру layout (flex, grid, direction)
✅ Border radius, shadows
✅ Названия компонентов и слоёв
```

## Что нельзя читать — API Error 400

```
❌ Изображения (фото, растровая графика)
❌ Иконки в формате PNG / JPG
❌ Любые бинарные файлы
```

**Никогда не вызывать:** `get_image`, `read_image`, `export_image` и любые image-related инструменты.

---

## Замены для недоступных ресурсов

| Что в Figma | Чем заменять |
|---|---|
| Иконки | lucide-react |
| Фото-плейсхолдеры | `https://picsum.photos/[w]/[h]` |
| Логотип | inline SVG или CSS |
| Реальные фото | Экспортировать вручную → `src/assets/images/` |

---

## Агент застрял в петле ошибок

Если видишь повторяющийся `API Error 400` — агент пытается читать изображение. Отправь:

```
СТОП. Не используй image-related инструменты Figma MCP.
Иконки → lucide-react, фото → picsum.photos
Продолжай без чтения изображений.
```

---

## Dev Mode

Если есть Figma Dev или Full seat — включи **Dev Mode** перед работой.
Агент получит точные CSS-значения вместо приблизительных.
