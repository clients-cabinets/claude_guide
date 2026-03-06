# Coding Standards

## React / TypeScript

- Всегда стрелочные функции: `const Component = () => {}`
- Props через TypeScript interface
- Хуки для переиспользуемой логики (`useLanguage`, `useScrollPosition`)
- Никакого prop drilling глубже 2 уровней
- API-запросы только через `src/api/` — никогда fetch в компонентах
- Все тексты через `t()` из i18next — никогда хардкод строк
- Только Tailwind — никаких `<style>` блоков
- `data-testid` на всех интерактивных элементах

### Структура компонента

```tsx
// src/sections/ExampleSection.tsx
import { useTranslation } from 'react-i18next'
import { ArrowRight } from 'lucide-react'
import { cn } from '@/utils/cn'
import { CtaButton } from '@/components/CtaButton'
import type { ExampleItem } from '@/types'

interface Props {
  items: ExampleItem[]
  className?: string
}

const ExampleSection = ({ items, className }: Props) => {
  const { t } = useTranslation()

  return (
    <section className={cn('py-20 bg-white', className)}>
      <div className="max-w-6xl mx-auto px-6">
        <h2 className="text-3xl font-bold">
          {t('example.title')}
        </h2>
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mt-10">
          {items.map((item) => (
            <ItemCard key={item.id} item={item} />
          ))}
        </div>
      </div>
    </section>
  )
}

export default ExampleSection
```

### Хук с логикой

```tsx
// src/hooks/useLanguage.ts
import { useTranslation } from 'react-i18next'

const SUPPORTED_LANGUAGES = ['en', 'ru'] as const
type Language = typeof SUPPORTED_LANGUAGES[number]

export const useLanguage = () => {
  const { i18n } = useTranslation()

  const changeLanguage = (lang: Language) => {
    i18n.changeLanguage(lang)
    localStorage.setItem('lang', lang)
  }

  return {
    currentLanguage: i18n.language as Language,
    changeLanguage,
    languages: SUPPORTED_LANGUAGES,
  }
}
```

### Утилита cn() — обязательна в проекте

```tsx
// src/utils/cn.ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export const cn = (...inputs: ClassValue[]) => twMerge(clsx(inputs))
```

---

## Tailwind CSS

- Кастомные цвета через `[]`: `bg-[#e8401c]`, `text-[#1a1a1a]`
- Адаптив mobile-first: `grid-cols-1 md:grid-cols-2 lg:grid-cols-3`
- Секции: `py-20 px-6`
- Никаких инлайн-стилей — только Tailwind
- Группировать классы: layout → spacing → typography → colors → effects

```tsx
// ✅ Правильно
<div className={cn(
  'flex items-center justify-between',  // layout
  'px-6 py-4',                          // spacing
  'text-sm font-medium',                // typography
  'text-white bg-[#1a1a1a]',           // colors
  'rounded-lg shadow-md',               // effects
)}>

// ❌ Неправильно
<div style={{ display: 'flex', backgroundColor: '#1a1a1a' }}>
```

---

## Именование

```
PascalCase  → компоненты:  HeroSection.tsx, CtaButton.tsx
camelCase   → хуки:        useLanguage.ts, useScrollPosition.ts
camelCase   → утилиты:     cn.ts, formatPrice.ts
camelCase   → api:         itemsApi.ts
```

---

## Структура проекта

```
src/
  api/              # API клиенты
  components/       # переиспользуемые UI компоненты
    CtaButton.tsx
    Card.tsx
    SectionTitle.tsx
    Navbar.tsx
    Footer.tsx
    Layout.tsx
  hooks/            # кастомные хуки
    useLanguage.ts
  locales/
    en.json
    ru.json
  pages/            # страницы (роуты)
  router/
    index.tsx
  sections/         # секции страниц
  types/
    index.ts
  utils/
    cn.ts
    formatPrice.ts
```
