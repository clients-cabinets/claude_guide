# Шаблон задачи для агента

Используй этот шаблон при постановке задач. Чем точнее задача — тем меньше итераций.

---

## Шаблон

```
Задача: [что нужно сделать — одна строка]

Компонент: src/[sections|pages|components]/[Name].tsx
Figma: https://www.figma.com/file/XXX/Project?node-id=[id]

Требования:
- [конкретное требование]
- Все тексты через i18next, добавь ключи в [namespace].*
- Иконки из lucide-react

Правила:
- Сверься с Figma для отступов и цветов (только структура, не изображения)
- Стандарты кода → .claude/docs/coding-standards.md
- [другие docs если нужны]

Ожидаемый результат:
- Файл [Name].tsx создан/обновлён
- Ключи добавлены в en.json и ru.json
- Компонент импортирован в [родительский файл]
```

---

## Пример — новая секция

```
Задача: реализовать секцию с карточками тарифов

Компонент: src/sections/PricingSection.tsx
Figma: https://www.figma.com/file/AbCdEf/Project?node-id=123%3A456

Требования:
- 3 карточки в ряд, одинаковая высота (flex flex-col justify-between)
- Кнопка CTA в каждой карточке — компонент <CtaButton variant="light">
- Все тексты через i18next, добавь ключи в pricing.*
- Иконки не нужны в этой секции

Правила:
- Сверься с Figma для точных отступов и цветов (только структура, не изображения)
- Стандарты кода → .claude/docs/coding-standards.md

Ожидаемый результат:
- src/sections/PricingSection.tsx создан
- Ключи добавлены в en.json и ru.json
- Компонент импортирован в src/pages/HomePage.tsx
```

---

## Пример — исправление компонента

```
Задача: исправить Navbar — не соответствует дизайну

Компонент: src/components/Navbar.tsx
Figma: https://www.figma.com/file/AbCdEf/Project?node-id=10%3A5

Расхождения:
- Navbar должен быть прозрачным поверх Hero (position: absolute)
- Логотип отображается некорректно
- Ссылки должны быть белыми

Правила:
- НЕ трогать логику переключения языка
- Только вёрстка и стили
- Сверься с Figma для точных значений

Ожидаемый результат:
- Navbar.tsx обновлён
- Визуально соответствует Figma
```

---

## Пример — новая страница

```
Задача: создать страницу [PageName]

Страница: src/pages/[Name]Page.tsx
Figma: https://www.figma.com/file/AbCdEf/Project?node-id=200%3A1

Требования:
- Добавить маршрут /[route] в src/router/index.tsx
- Изучить секции в Figma и реализовать по очереди
- Все тексты через i18next в namespace "[pageName]"
- Layout с transparentNav={true} если есть hero-секция

Правила:
- Figma MCP — только структура/цвета, не изображения → .claude/docs/figma-mcp.md
- Стандарты кода → .claude/docs/coding-standards.md

Ожидаемый результат:
- [Name]Page.tsx создан
- Маршрут добавлен в router
- Ключи переводов добавлены
- Ссылка в Navbar ведёт на новую страницу
```
