# Error Handling

## Figma MCP — API Error 400

**Причина:** Figma MCP не читает бинарные файлы (PNG, JPG, SVG как файл).
**Симптом:** агент уходит в петлю повторяющихся ошибок.

Никогда не вызывать:
- get_image, read_image, export_image
- любые инструменты возвращающие бинарные данные

Замены:
- Иконки → lucide-react
- Фото → https://picsum.photos/[w]/[h]
- Логотип → inline SVG
- Реальные фото → экспортировать вручную → src/assets/images/

Если агент застрял:
```
СТОП. Не читай изображения через Figma MCP.
Иконки → lucide-react, фото → picsum.photos
Продолжай без чтения изображений.
```

---

## Компоненты — async операции

Три обязательных состояния: loading, error, success.

```tsx
const [data, setData] = useState<Item[]>([])
const [isLoading, setIsLoading] = useState(true)
const [error, setError] = useState<string | null>(null)

useEffect(() => {
  const load = async () => {
    try {
      const result = await fetchItems()
      setData(result)
    } catch (e) {
      setError(e instanceof Error ? e.message : t('common.error'))
    } finally {
      setIsLoading(false)
    }
  }
  load()
}, [])

if (isLoading) return <div>{t('common.loading')}</div>
if (error) return <div className="text-red-500">{error}</div>
```

---

## API клиент — централизованная обработка

```tsx
// src/api/client.ts
export class ApiError extends Error {
  constructor(public status: number, message: string) {
    super(message)
    this.name = 'ApiError'
  }
}

export const apiClient = async <T>(endpoint: string): Promise<T> => {
  const response = await fetch(`${import.meta.env.VITE_API_URL}${endpoint}`)
  if (!response.ok) {
    throw new ApiError(response.status, `API error: ${response.status}`)
  }
  return response.json() as Promise<T>
}
```

---

## Что не нужно обрабатывать

- Статичные секции без данных — ошибок быть не может
- Переводы i18next — при отсутствии ключа показывает key как fallback
