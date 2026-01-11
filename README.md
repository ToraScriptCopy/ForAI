# LuaLingo — PRD + прототипный план

Ниже — полный продуктовый план и рабочий прототип (спецификация) для веб‑приложения в стиле Duolingo, но для изучения Lua‑кодинга с собственным маскотом.

## 1) PRD (требования) + user stories

### Цели продукта
- Быстрое и ежедневное обучение Lua в формате 2 урока/день.
- Геймификация: XP, стрики, бейджи, эмоции маскота.
- Безопасная проверка кода прямо в браузере.
- Упрощённый онбординг, адаптивный план обучения.

### Пользовательские истории (ключевые)
- Как новый пользователь, я выбираю язык интерфейса и указываю возраст/цели, чтобы получить персональный план из 20 уровней.
- Как пользователь с опытом, я прохожу проверочный тест при выборе уровня 3/4 и получаю корректную рекомендацию уровня.
- Как ученик, я прохожу урок, где есть объяснение, выбор ответа, исправление ошибок и написание кода.
- Как пользователь, я вижу прогресс (XP, стрик, уровни), бейджи и статистику ошибок.
- Как возвращающийся пользователь, я получаю экран с грустным маскотом и бейдж “Возвращение” при отсутствии ≥ 7 дней.
- Как обучающийся, я хочу безопасно запускать Lua‑код и получать проверку результатов.

### Нефункциональные требования
- Время запуска страницы ≤ 2 сек на мобайле.
- Вся проверка кода безопасна: таймауты, лимиты памяти, блокировка опасных API.
- Совместимость с Netlify (деплой фронтенда + Functions).

---

## 2) Карты пользовательских сценариев (user flows)

### Flow A — Онбординг
1. Welcome/Language → выбор языка (5 вариантов) → сохранение в профиле/localStorage.
2. Вопросы: возраст, цели, таймзона (авто), расписание (2 урока/день).
3. Выбор уровня (1–4). Для 3/4 → модалка теста → успех/провал → рекомендация.
4. Выбор цели обучения (личное/Roblox/работа/мобайл).
5. Генерация персонального плана 20 уровней → Home.

### Flow B — Урок
1. Home → открытие урока → Lesson player.
2. Часть 1: объяснение → запуск кода.
3. Часть 2: Multiple choice.
4. Часть 3: Debug (исправить ошибку).
5. Часть 4: Write (написать код) → проверка → результат.
6. Начисление XP, обновление прогресса.

### Flow C — Возвращение после отсутствия
1. При входе проверка `last_active`.
2. Если ≥ 7 дней → Return screen с грустным маскотом.
3. Hover по кнопке → маскот радуется.
4. Нажатие → бейдж “Возвращение” (с точной длительностью) → Home.

---

## 3) Структура экранов и UX‑логика (wireframes текстом)

### 3.1 Welcome/Language
- Заголовок: “Выберите язык интерфейса”
- 5 карточек: EN / RU / ES / DE / FR
- CTA: “Продолжить”

### 3.2 Onboarding (вопросы)
- Возраст (input)
- Цель (chips)
- Таймзона (auto detect)
- Расписание: 2 урока/день (toggle)
- CTA: “Далее”

### 3.3 Выбор уровня + модалка теста
- 4 карточки: Новичок/Знаю/Прошаренный/Эксперт
- Для 3/4 → кнопка запуска теста
- Модалка: код, кнопка “Проверить”, вывод ошибок

### 3.4 Выбор цели обучения
- 4 карточки цели
- Кнопка “Создать план”

### 3.5 Home (дерево уровней)
- Прогресс (2/2 сегодня)
- Карта уровней 1–20
- Маскот (реагирует на статус)

### 3.6 Lesson player
- Прогресс урока (4 части)
- Explanation → MCQ → Debug → Write
- CTA “Далее/Проверить”

### 3.7 Profile
- Статистика XP, стрики
- Список бейджей с датами
- История ошибок по темам

### 3.8 Return screen
- Грустный маскот
- Текст “Мы скучали!”
- Кнопка “Вернуться…?” (hover меняет эмоцию)

### 3.9 Auth
- Регистрация/логин

---

## 4) Дизайн‑система + маскот

### Цвета (Duolingo‑style)
- Primary: #58CC02
- Primary Dark: #44A300
- Secondary: #1CB0F6
- Accent: #FFD300
- Error: #FF4B4B
- Background: #F7F7F7
- Text: #2E2E2E

### Типографика
- Заголовки: “Poppins”
- Тело: “Inter”

### Компоненты
- Button (primary/secondary/ghost)
- Card
- ProgressBar
- Badge
- Modal
- CodeEditor
- Toast

### Маскот
- Имя: **Lu** (зелёный “геккон‑учитель”) — минималистичный, милый.
- Состояния:
  - нейтральный
  - радость
  - поддержка
  - гордость
  - грусть
- Реализация: SVG + CSS анимации (шевеление хвоста, мигание).

---

## 5) Модель данных

### Сущности
- User
- Profile/Settings
- Lesson
- Progress
- Attempt
- Badge
- Streak
- Absence

---

## 6) Архитектура фронтенда/бэкенда (Netlify)

### Рекомендуемый стек (MVP)
- Frontend: React + TypeScript + Vite
- Styling: Tailwind
- State: Zustand
- Auth: Netlify Identity
- Backend: Netlify Functions
- DB: Supabase (Postgres)
- Lua runner: fengari (Lua VM in JS)

### Архитектура
- Netlify deploy → статический фронтенд + Functions.
- Functions → обращения в Supabase (через service role ключ).
- Клиент использует Netlify Identity JWT.

---

## 7) API спецификация (Netlify Functions)

### POST /api/auth/register
- body: { email, password, nickname }
- response: { userId }

### GET /api/profile
- headers: Authorization: Bearer <token>
- response: { profile, settings }

### POST /api/progress/update
- body: { lessonId, xp, status }
- response: { updatedProgress }

### POST /api/attempts
- body: { lessonId, taskId, answer, result }

### POST /api/badges/award
- body: { badgeId, meta }

---

## 8) БД (Supabase Postgres) + SQL

```sql
create table users (
  id uuid primary key,
  email text not null,
  nickname text,
  created_at timestamp default now()
);

create table profiles (
  user_id uuid references users(id),
  ui_language text,
  age int,
  goal text,
  timezone text,
  daily_lessons int default 2,
  level int,
  created_at timestamp default now()
);

create table lessons (
  id uuid primary key,
  level int,
  title text,
  content jsonb
);

create table progress (
  user_id uuid references users(id),
  lesson_id uuid references lessons(id),
  status text,
  xp int,
  updated_at timestamp default now()
);

create table attempts (
  id uuid primary key,
  user_id uuid references users(id),
  lesson_id uuid references lessons(id),
  task_type text,
  answer jsonb,
  is_correct boolean,
  created_at timestamp default now()
);

create table badges (
  id uuid primary key,
  user_id uuid references users(id),
  badge_key text,
  meta jsonb,
  earned_at timestamp default now()
);

create table streaks (
  user_id uuid references users(id),
  current_streak int,
  last_active date
);

create table absences (
  user_id uuid references users(id),
  last_active timestamp,
  returned_at timestamp,
  duration_days int
);
```

---

## 9) Алгоритмы проверки и генерации

### 9.1 Проверка кода (write)
- Выполнение Lua через fengari.
- Timeout 1–2s, лимит памяти.
- Блокировка os.execute, io.*
- Проверка:
  1) expected_output
  2) проверка ключевых конструкций (regex/AST эвристика)

### 9.2 Проверка debug (исправь ошибку)
- Unit tests по вход/выход.
- Если вывод совпал → pass.

### 9.3 Генерация “исправить ошибку”
- Шаблонные мутации: заменить оператор, забыть `end`, неверный индекс.

### 9.4 Spaced repetition
- Ошибка → вес темы +1
- Correct → вес темы -1
- Повторение выбирается по весу.

---

## 10) Контент‑план 20 уровней

1. Введение: print, комментарии, строка/число
2. Переменные и типы, nil, конкатенация
3. Арифметика, приоритеты, math
4. Условия: if/elseif/else, сравнения
5. Логика: and/or/not, truthy/falsey
6. Цикл while, базовые паттерны
7. Цикл for (numeric), диапазоны
8. Таблицы как массивы
9. Таблицы как словари
10. Функции: параметры, return
11. Область видимости: local/global, замыкания
12. Строки: pattern basics, gsub, format
13. Модули: require
14. Ошибки: pcall
15. Итераторы
16. Метатаблицы: __index
17. Файлы/JSON (симуляция)
18. Мини‑проект (todo/quest)
19. Профилирование/оптимизация basics
20. Финальный проект (4 ветки)

---

## 11) Пример JSON‑урока

```json
{
  "metadata": { "level": 1, "title": "Привет, Lua" },
  "explanation": { "code": "print('Lua!')", "output": "Lua!" },
  "mcq": { "question": "Что выведет код?", "options": ["Lua!", "Lua?"], "answer": "Lua!" },
  "debug_task": { "code": "print('Lua)" },
  "write_task": { "prompt": "Выведи слово Hello", "expected_output": "Hello" },
  "hints": ["Используй print"]
}
```

---

## 12) Система бейджей

- Старт (за онбординг)
- 20 уровней пройдено
- Отшельник (завершил обучение)
- Возвращение (≥ 7 дней отсутствия)

---

## 13) Поведение “грустного маскота”

- Если `last_active` ≥ 7 дней → Return screen
- Hover по кнопке → смена эмоции на радость
- Клик → выдаётся бейдж “Возвращение” с точной длительностью отсутствия

---

## 14) Безопасность

- Пароли не хранятся в открытом виде.
- Netlify Identity + JWT.
- Серверные функции ограничивают доступ к сервисным ключам.
- Ограничение времени выполнения Lua.

---

## 15) Компоненты фронтенда (минимально)

- LanguageSelector
- OnboardingForm
- LevelSelector + PlacementTestModal
- GoalSelector
- HomeMap
- LessonPlayer
- Profile
- ReturnScreen
- AuthForm

---

## 16) Контент‑план: 20 уровней (подробно)

1. Введение (print, комментарии)
2. Переменные, nil
3. Арифметика
4. Условия if
5. Логика
6. while
7. for
8. Таблицы‑массивы
9. Таблицы‑словари
10. Функции
11. local/global
12. Строки/pattern
13. Модули
14. Ошибки pcall
15. Итераторы
16. Метатаблицы
17. JSON/файлы
18. Мини‑проект
19. Оптимизация
20. Финальный проект (ветки)

---

Готово: документ с PRD, wireframes, дизайн‑системой, архитектурой, БД, API и контент‑планом.
