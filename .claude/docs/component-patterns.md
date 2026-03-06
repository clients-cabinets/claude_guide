# Component Patterns

## CtaButton — основная кнопка действия

Единственная кнопка CTA в проекте. Не создавай новые кнопки — всегда используй этот компонент.

```tsx
// src/components/CtaButton.tsx
import { ArrowRight } from 'lucide-react'
import { useTranslation } from 'react-i18next'
import { cn } from '@/utils/cn'

interface Props {
  label?: string
  onClick?: () => void
  className?: string
  variant?: 'dark' | 'light'
}

const CtaButton = ({ label, onClick, className, variant = 'dark' }: Props) => {
  const { t } = useTranslation()

  return (
    <button
      onClick={onClick}
      data-testid="cta-button"
      className={cn(
        'flex items-center gap-3 px-5 py-3',
        'border-l-4 border-[#e8401c]',       // ← замени на акцентный цвет проекта
        'text-sm font-medium uppercase tracking-wide',
        'transition-opacity hover:opacity-80',
        variant === 'dark' && 'bg-[#1a1a1a] text-white',
        variant === 'light' && 'bg-white text-[#1a1a1a]',
        className
      )}
    >
      {label ?? t('common.cta')}
      <ArrowRight size={16} />
    </button>
  )
}

export default CtaButton
```

---

## SectionTitle — заголовок секции

```tsx
// src/components/SectionTitle.tsx
import { cn } from '@/utils/cn'

interface Props {
  title: string
  subtitle?: string
  align?: 'left' | 'center'
  className?: string
}

const SectionTitle = ({ title, subtitle, align = 'left', className }: Props) => (
  <div className={cn(align === 'center' && 'text-center', className)}>
    <h2 className="text-3xl font-bold leading-tight">{title}</h2>
    {subtitle && (
      <p className="mt-4 text-gray-600 text-sm leading-relaxed max-w-xl">
        {subtitle}
      </p>
    )}
  </div>
)

export default SectionTitle
```

---

## Card — универсальная карточка

```tsx
// src/components/Card.tsx
import { cn } from '@/utils/cn'
import CtaButton from './CtaButton'

interface Props {
  title: string
  description: string
  meta?: string        // дополнительная строка (цена, дата и т.д.)
  showCta?: boolean
  className?: string
}

const Card = ({ title, description, meta, showCta = true, className }: Props) => (
  <div className={cn(
    'flex flex-col justify-between',
    'bg-white border border-gray-100 p-6 h-full',
    className
  )}>
    <div>
      <h3 className="text-lg font-bold">{title}</h3>
      {meta && <p className="mt-2 text-sm text-gray-500">{meta}</p>}
      <p className="mt-3 text-sm text-gray-600 leading-relaxed">{description}</p>
    </div>
    {showCta && (
      <div className="mt-6">
        <CtaButton variant="light" />
      </div>
    )}
  </div>
)

export default Card
```

---

## Navbar

```tsx
// src/components/Navbar.tsx
import { Link, useLocation } from 'react-router-dom'
import { useTranslation } from 'react-i18next'
import { useLanguage } from '@/hooks/useLanguage'
import { cn } from '@/utils/cn'

interface Props {
  transparent?: boolean
}

const Navbar = ({ transparent = false }: Props) => {
  const { t } = useTranslation()
  const { pathname } = useLocation()
  const { currentLanguage, changeLanguage } = useLanguage()

  const links = [
    { to: '/[route]', label: t('nav.[key]') },
    // добавляй маршруты проекта
  ]

  return (
    <nav className={cn(
      'flex items-center justify-between px-8 py-5 z-50',
      transparent ? 'absolute top-0 left-0 right-0' : 'relative bg-white shadow-sm'
    )}>
      <Link to="/" className="flex items-center gap-2">
        {/* Логотип — inline SVG или текст */}
        <span className="font-bold text-lg">[Логотип]</span>
      </Link>

      <div className="hidden md:flex items-center gap-8">
        {links.map(({ to, label }) => (
          <Link
            key={to}
            to={to}
            className={cn(
              'text-sm transition-colors',
              transparent ? 'text-white/80 hover:text-white' : 'text-gray-600 hover:text-black',
              pathname === to && 'font-medium opacity-100'
            )}
          >
            {label}
          </Link>
        ))}
      </div>

      <div className="flex items-center gap-6">
        <button
          onClick={() => changeLanguage(currentLanguage === 'en' ? 'ru' : 'en')}
          className="text-sm opacity-80 hover:opacity-100"
        >
          {currentLanguage.toUpperCase()} ↓
        </button>
      </div>
    </nav>
  )
}

export default Navbar
```

---

## Layout — обёртка страницы

```tsx
// src/components/Layout.tsx
import Navbar from './Navbar'
import Footer from './Footer'

interface Props {
  children: React.ReactNode
  transparentNav?: boolean
}

const Layout = ({ children, transparentNav = false }: Props) => (
  <div className="min-h-screen bg-[#f5f5f5]">
    <Navbar transparent={transparentNav} />
    <main>{children}</main>
    <Footer />
  </div>
)

export default Layout
```

---

## Иконки — только lucide-react

```tsx
import {
  Shield,       // безопасность
  Clock,        // время / поддержка 24/7
  TrendingUp,   // графики / аналитика
  DollarSign,   // финансы
  UserPlus,     // регистрация
  UserCheck,    // верификация
  ArrowRight,   // CTA стрелка
  ChevronDown,  // dropdown
  Globe,        // язык / регион
} from 'lucide-react'

// Использование
<Shield size={32} className="text-[#e8401c]" />
```
