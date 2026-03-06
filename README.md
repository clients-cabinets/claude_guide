# Разработка многостраничного сайта с Claude Code + Figma MCP

> Стек: React + Vite + TypeScript + Tailwind CSS + React Router + i18next  
> Инструмент: Claude Code с подключённым Figma MCP

---

## Содержание

1. [Структура .claude/](#1-структура-claude)
2. [Подключение Figma MCP](#2-подключение-figma-mcp)
3. [Как давать задачи агенту](#3-как-давать-задачи-агенту)
4. [Порядок разработки](#4-порядок-разработки)
5. [Тесты (Vitest)](#5-тесты-vitest)
6. [Известные проблемы](#6-известные-проблемы)

---

## 1. Структура `.claude/`

Папка `.claude/` в корне проекта — контекст агента. Без неё он работает вслепую и не знает о соглашениях проекта.

```
.claude/
  CLAUDE.md                  # главный файл — стек, правила, ссылки на docs
  commands/
    new-page.md              # slash-команда /new-page
    fix-component.md         # slash-команда /fix-component
  docs/
    coding-standards.md      # стандарты кода с примерами
    component-patterns.md    # переиспользуемые компоненты проекта
    figma-mcp.md             # правила работы с Figma MCP
    i18n.md                  # правила мультиязычности
    error-handling.md        # обработка ошибок
    decisions.md             # лог архитектурных решений
    task-template.md         # шаблон задачи для команды
```

### `CLAUDE.md` — главный файл

Агент читает его в начале каждой сессии. Должен быть коротким — только стек, правила и ссылки на docs.

```markdown
# [Название проекта]

## Stack
- React 18 + Vite + TypeScript
- Tailwind CSS + clsx + tailwind-merge
- React Router v6
- i18next (перечисли языки)
- lucide-react (иконки)

## Цвета
- Акцент: #000000
- Фон: #ffffff
- Текст: #1a1a1a

## Страницы
- / — Home
- /[route] — [Название]

## Правила
1. Все тексты ТОЛЬКО через i18next t('key') — никакого хардкода
2. Иконки ТОЛЬКО из lucide-react
3. Figma MCP: только структура/цвета/отступы — НЕ изображения
4. Условные классы через cn() из src/utils/cn.ts
5. Кнопка CTA — компонент <CtaButton>, не создавать вручную

## Docs
- Стандарты кода → .claude/docs/coding-standards.md
- Паттерны компонентов → .claude/docs/component-patterns.md
- Figma MCP → .claude/docs/figma-mcp.md
- i18n → .claude/docs/i18n.md
- Ошибки → .claude/docs/error-handling.md
- Решения → .claude/docs/decisions.md
```

### `commands/new-page.md` — slash-команда

Вызывается в Claude Code: `/new-page TradingAssets`

```markdown
Создай новую страницу: $ARGUMENTS

1. Файл в src/pages/[Name]Page.tsx
2. Добавь маршрут в src/router/index.tsx
3. Добавь ссылку в Navbar
4. Создай базовые ключи в en.json и ru.json
5. Сверься с Figma для структуры секций
   — только структура/цвета, не изображения → .claude/docs/figma-mcp.md
```

### `commands/fix-component.md` — slash-команда

Вызывается: `/fix-component Navbar`

```markdown
Исправь компонент: $ARGUMENTS

1. Открой файл компонента
2. Сверься с Figma — только структура/цвета/отступы
3. Исправь визуальные расхождения
4. Проверь что все тексты через i18next
5. Не трогай логику — только вёрстку
```

---

## 2. Подключение Figma MCP

### Установка (один раз на машину)

**Вариант 1 — Remote сервер (рекомендуется):**
```bash
claude mcp add --transport http figma https://mcp.figma.com/mcp
```
Затем в Claude Code: `/mcp` → выбери `figma` → OAuth в браузере → `Authentication successful`.

**Вариант 2 — Figma Desktop App (локальный сервер):**
```bash
# 1. Открой Figma Desktop → Preferences → включи "Dev Mode MCP Server"
# 2. Сервер запустится на http://127.0.0.1:3845/sse
claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse
```

Проверить подключение: `/mcp` в Claude Code — статус должен быть `connected`.

### Как передавать ссылки агенту

Всегда передавай ссылку с `node-id` — иначе агент читает всё дерево страницы и путается.

**Как получить:**
1. Открой Figma
2. Выдели нужный фрейм или компонент
3. Скопируй URL из адресной строки — он уже содержит `node-id`

```
# С node-id — правильно
https://www.figma.com/file/AbCdEf123/Project?node-id=123%3A456

# Без node-id — неправильно (агент читает всю страницу)
https://www.figma.com/file/AbCdEf123/Project
```

### Что MCP умеет и не умеет

```
✅ Цвета, отступы, размеры шрифтов, border-radius, shadows
✅ Структуру layout (flex, grid, direction)
✅ Размеры элементов, названия слоёв и компонентов

❌ Изображения, фото, иконки в PNG/JPG/SVG (API Error 400)
```

Подробнее → `.claude/docs/figma-mcp.md`

---

## 3. Как давать задачи агенту

Используй шаблон из `.claude/docs/task-template.md`. Чем точнее задача — тем меньше итераций.

```
Задача: [одна строка — что нужно сделать]

Компонент: src/[sections|pages|components]/[Name].tsx
Figma: https://www.figma.com/file/XXX/Project?node-id=123%3A456

Требования:
- [конкретное требование]
- Все тексты через i18next, добавь ключи в [namespace].*
- Иконки из lucide-react

Правила:
- Сверься с Figma для отступов и цветов (только структура, не изображения)
- Стандарты → .claude/docs/coding-standards.md

Ожидаемый результат:
- Файл создан/обновлён
- Ключи добавлены в en.json и ru.json
- Компонент импортирован в [родительский файл]
```

---

## 4. Порядок разработки

Придерживайся этого порядка — каждый шаг зависит от предыдущего.

**Шаг 1 — Архитектура**
Создать структуру папок, настроить React Router, i18next, пустые компоненты-заглушки.
Сверься с Figma чтобы учесть все страницы до начала работы.

**Шаг 2 — Navbar + Footer**
Переиспользуются на всех страницах. Делать первыми, до любых страниц.

**Шаг 3 — Главная страница**
Секция за секцией сверху вниз. После каждой секции — скриншот и сравнение с Figma.

**Шаг 4 — Остальные страницы**
По одной странице через `/new-page [Name]`.

**Шаг 5 — Полировка**
Адаптив, hover-эффекты, финальная сверка с Figma по всем страницам.

> **Правило:** после каждого шага делай скриншот и сравнивай с Figma вручную.  
> Агент не всегда замечает визуальные расхождения без подсказки.

---

## 5. Тесты (Vitest)

**Писать тесты:**
- Утилиты и хелперы — `formatPrice.test.ts`, `cn.test.ts`
- Хуки с логикой — `useLanguage.test.ts`
- Критичные UI-компоненты — `CtaButton.test.tsx`

**Не писать тесты:**
- Статичные секции без логики (Hero, Footer)
- Компоненты только с вёрсткой

Тесты располагай рядом с файлом:
```
src/utils/formatPrice.ts
src/utils/formatPrice.test.ts
src/hooks/useLanguage.ts
src/hooks/useLanguage.test.ts
```

---

## 6. Известные проблемы

### API Error 400 — Figma MCP не читает изображения

Если агент падает в петлю одинаковых ошибок — он пытается прочитать изображение через MCP.

Отправь:
```
СТОП. Не используй image-related инструменты Figma MCP.
Иконки → lucide-react, фото → picsum.photos
Продолжай без чтения изображений.
```

Подробнее → `.claude/docs/error-handling.md`

### Сессия агента умерла посреди работы

При открытии новой сессии отправь:
```
Продолжаем проект. Предыдущая сессия упала.
Уже сделано: [опиши кратко или покажи структуру папок]

ПРАВИЛА СЕССИИ:
- Figma MCP только для структуры/цветов/отступов
- Никогда не читать изображения через MCP
- Иконки → lucide-react, фото → picsum.photos

Продолжай с: [конкретный компонент или задача]
Figma: [ссылка с node-id]
```
