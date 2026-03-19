# Отчёт по лабораторной работе №4
## Веб-программирование

**Студент:** Ануфриев Андрей Сергеевич  
**Группа:** P3219  
**Университет:** ИТМО  
**Дата:** 2025-2026

---

## 📋 Содержание

1. [Введение](#введение)
2. [Постановка задачи](#постановка-задачи)
3. [Технологический стек](#технологический-стек)
4. [Архитектура приложения](#архитектура-приложения)
5. [Реализованный функционал](#реализованный-функционал)
6. [Технические решения](#технические-решения)
7. [Структура проекта](#структура-проекта)
8. [Выводы](#выводы)

---

## Введение

В данной лабораторной работе разработано полнофункциональное веб-приложение для проверки точек в координатной плоскости с аутентификацией пользователей, управлением сессиями и сохранением результатов.

Приложение демонстрирует применение современных подходов к разработке веб-приложений, включая:
- **REST API архитектуру** с использованием JAX-RS
- **JWT аутентификацию** с refresh-токенами
- **Встроенную реляционную БД** (Apache Derby)
- **Фронтенд на Vue 3 с TypeScript**
- **SPA (Single Page Application)** архитектуру

---

## Постановка задачи

### Основные требования
1. **Аутентификация и авторизация**
    - Система регистрации пользователей
    - Вход с генерацией JWT-токенов
    - Безопасное хранение паролей (BCrypt хеширование)

2. **Функциональность проверки точек**
    - Проверка попадания точки в заданную область
    - Сохранение результатов в БД
    - Вывод истории проверок

3. **Управление сессиями**
    - Поддержка нескольких активных сессий
    - Отслеживание IP-адреса и User-Agent

4. **Интерактивный интерфейс**
    - Визуализация области на графике
    - Форма ввода параметров (X, Y, R)
    - Таблица результатов с сортировкой

---

## Технологический стек

### Backend

| Технология | Версия | Назначение |
|-----------|--------|-----------|
| **Java** | 17+ | Язык разработки |
| **JAX-RS (Jersey)** | 3.1.5 | REST API фреймворк |
| **Grizzly** | 4.0.2 | HTTP сервер |
| **JPA/EclipseLink** | 4.0.2 | ORM, работа с БД |
| **Apache Derby** | 10.16.1.1 | Встроенная реляционная БД |
| **JJWT** | 0.12.5 | JWT токены |
| **BCrypt** | 0.4 | Хеширование паролей |
| **Jackson** | 2.16.1 | JSON сериализация |
| **SLF4J/Logback** | Latest | Логирование |
| **Gradle** | Latest | System для сборки |

**Версия Java:** OpenJDK 17 LTS

### Frontend

| Технология | Версия | Назначение |
|-----------|--------|-----------|
| **Vue.js** | 3.4.15 | Фреймворк UI |
| **TypeScript** | 5.3.3 | Язык разработки |
| **Vue Router** | 4.3.0 | Маршрутизация |
| **Axios** | 1.6.7 | HTTP клиент |
| **Vite** | 5.0.12 | Build tool |
| **HTML5/CSS3** | Modern | Верстка и стили |

**Node.js:** 18+  
**npm:** 9+

---

## Архитектура приложения

### Трёхслойная архитектура Backend

```
┌─────────────────────────────────────────┐
│           REST API (JAX-RS)             │
│  (AuthResource, ResultResource, etc)    │
├─────────────────────────────────────────┤
│         Service Layer (Бизнес-логика)  │
│  (AuthService, ResultService, etc)      │
├─────────────────────────────────────────┤
│     Repository Layer (Доступ к БД)     │
│  (UserRepository, ResultRepository)     │
├─────────────────────────────────────────┤
│      Database Layer (Apache Derby)     │
└─────────────────────────────────────────┘
```

### MVC архитектура Frontend

```
┌──────────────────────────────────────────┐
│         Vue Components (View)            │
│  (LoginView, MainView, RegisterView)     │
├──────────────────────────────────────────┤
│      Services (Business Logic)           │
│  (AuthService, PointService)             │
├──────────────────────────────────────────┤
│      Router (Routing)                    │
│  (Vue Router с Guards)                   │
├──────────────────────────────────────────┤
│   HTTP Layer (Axios + Interceptors)      │
└──────────────────────────────────────────┘
```

### Поток взаимодействия

```
Frontend (Vue 3)          Network (HTTP)          Backend (JAX-RS)
     │                                                  │
     ├──> [Login Form] ───────────────────────────> AuthResource
     │                                                  │
     │                                            ↓ AuthService
     │                                            ↓ UserRepository
     │                                            ↓ JwtUtil
     │    ◄──────────────────── JWT Token ────────┤
     │                                                  │
     ├──> [Check Point] ────────────────────────> ResultResource
     │    + Authorization Header                       │
     │                                            ↓ ResultService
     │                                            ↓ HitChecker
     │    ◄────────────── Result JSON ──────────┤
     │
     └──> Display Results                        Storage: Derby DB
```

---

## Реализованный функционал

### 1. Аутентификация и авторизация

#### ✅ Регистрация пользователей
- Валидация логина и пароля
- Проверка уникальности пользователя
- Хеширование пароля с использованием BCrypt
- Автоматическая генерация JWT токенов

**Пример запроса:**
```bash
POST /api/auth/register
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "SecurePass123"
}
```

**Пример ответа:**
```json
{
  "success": true,
  "message": "Регистрация успешна",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "username": "john_doe",
    "email": "john@example.com"
  }
}
```

#### ✅ Вход в систему
- Проверка учётных данных
- Генерация Access Token (24 часа)
- Генерация Refresh Token (7 дней)
- Поддержка множественных сессий

#### ✅ Обновление токенов
- Валидация refresh-токена
- Одноразовое использование refresh-токена
- Автоматическое обновление при истечении access-token

#### ✅ Выход из системы
- Инвалидация сессии
- Удаление refresh-токена

### 2. Проверка точек в области

#### ✅ Основной алгоритм
Проверка попадания точки (X, Y) в область с параметром R:

```
Область состоит из трёх частей:
1. Прямоугольник: -R ≤ X ≤ 0, 0 ≤ Y ≤ R
2. Треугольник: 0 ≤ X ≤ R, 0 ≤ Y ≤ R, X + Y ≤ R
3. Полукруг: X² + Y² ≤ R², Y ≥ 0
```

**Реализация (HitChecker):**
```java
public static boolean checkHit(double x, double y, double r) {
    // Прямоугольник (-R, 0) - (0, R)
    if (x >= -r && x <= 0 && y >= 0 && y <= r) return true;
    
    // Треугольник (0, 0) - (R, 0) - (0, R)
    if (x >= 0 && y >= 0 && x + y <= r) return true;
    
    // Полукруг x² + y² ≤ R², y ≥ 0
    if (x >= 0 && y >= 0 && x*x + y*y <= r*r) return true;
    
    return false;
}
```

#### ✅ Сохранение результатов
- Сохранение в БД (Apache Derby)
- Кеширование в памяти для быстрого доступа
- Временная метка выполнения

### 3. Управление результатами

#### ✅ Получение истории
- Все результаты пользователя
- Сортировка по дате/времени
- Фильтрация по параметрам

#### ✅ Очистка результатов
- Удаление всех результатов пользователя
- Очистка кеша

### 4. Управление сессиями

#### ✅ Отслеживание активности
- Сохранение IP-адреса
- Сохранение User-Agent
- Время создания сессии

#### ✅ Множественные сессии
- Одновременно активные сессии на разных устройствах
- Независимая валидация каждой сессии

### 5. Интерфейс пользователя

#### ✅ Страница входа (LoginView)
- Форма с валидацией
- Обработка ошибок
- Переход к регистрации

#### ✅ Страница регистрации (RegisterView)
- Форма регистрации
- Валидация полей
- Обработка конфликтов (дублирующийся логин)

#### ✅ Главная страница (MainView)
- **GraphCanvas** — визуализация области на SVG
- **PointForm** — форма ввода параметров X, Y, R
- **ResultsTable** — таблица результатов с сортировкой и фильтрацией

#### ✅ Интерактивные компоненты
- Клик по графику для добавления точки
- Real-time визуализация выбранной области
- Вывод всех результатов в таблице

---

## Технические решения

### Security (Безопасность)

#### JWT (JSON Web Token)
- **Access Token**: Короткоживущий (24 часа), содержит ID пользователя и логин
- **Refresh Token**: Долгоживущий (7 дней), используется для обновления access-token
- **Алгоритм**: HMAC SHA-256
- **Секретный ключ**: Конфигурируемый, минимум 256 бит

#### Хеширование паролей
- **BCrypt**: Адаптивный алгоритм с солью
- **Функция**: `BCrypt.hashpw(password, BCrypt.gensalt())`
- **Защита**: Брутфорс атаки невозможны из-за медленности BCrypt

#### Валидация запросов
- Проверка наличия JWT в заголовках
- Валидация подписи токена
- Проверка срока действия

### Работа с БД (Database)

#### Apache Derby
- **Тип**: Встроенная реляционная БД
- **Преимущества**:
    - Не требует отдельного сервера
    - Автоматическое создание схемы
    - Встроенная транзакционность
    - Персистентное хранилище (файлы на диске)

#### JPA/EclipseLink
- **ORM маппинг** сущностей на таблицы
- **JPQL** для запросов
- **Управление транзакциями** через JPA

#### Схема БД

```sql
-- Таблица пользователей
CREATE TABLE user_entity (
    id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    username VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255),
    password_hash VARCHAR(255) NOT NULL
);

-- Таблица refresh-токенов
CREATE TABLE refresh_token_entity (
    id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    user_id BIGINT NOT NULL,
    token_hash VARCHAR(512) NOT NULL,
    expires_at BIGINT NOT NULL,
    FOREIGN KEY(user_id) REFERENCES user_entity(id)
);

-- Таблица результатов проверки
CREATE TABLE point_entity (
    id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    user_id BIGINT NOT NULL,
    x DOUBLE NOT NULL,
    y DOUBLE NOT NULL,
    r DOUBLE NOT NULL,
    hit BOOLEAN NOT NULL,
    execution_time_ms BIGINT NOT NULL,
    created_at TIMESTAMP NOT NULL,
    FOREIGN KEY(user_id) REFERENCES user_entity(id)
);
```

### REST API Design

#### Принципы
- **RESTful архитектура** с правильной семантикой HTTP методов
- **Стандартные коды ответов** (200, 201, 400, 401, 403, 500)
- **JSON** как единственный формат обмена данных
- **Versionless API** (версионирование через Content-Type)

#### Endpoints

| Метод | Endpoint | Описание | Auth |
|-------|----------|---------|------|
| POST | `/api/auth/register` | Регистрация | ❌ |
| POST | `/api/auth/login` | Вход | ❌ |
| POST | `/api/auth/refresh` | Обновить токен | ✅ |
| POST | `/api/auth/logout` | Выход | ✅ |
| POST | `/api/results/check` | Проверить точку | ✅ |
| GET | `/api/results` | Все результаты | ✅ |
| DELETE | `/api/results` | Очистить результаты | ✅ |

### Frontend (Vue 3 + TypeScript)

#### Компонентная архитектура
- **Однофайловые компоненты** (SFC — Single File Components)
- **TypeScript** для типизации
- **Composition API** для логики компонентов

#### Router Guards
```typescript
// Защита маршрутов от неавторизованного доступа
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !authService.isAuthenticated()) {
    next('/login');
  } else {
    next();
  }
});
```

#### HTTP Interceptors
```typescript
// Автоматическое добавление JWT в заголовки
axios.interceptors.request.use((config) => {
  const token = localStorage.getItem('accessToken');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

#### State Management
- **localStorage** для сохранения токенов между сеансами
- **Reactive objects** в сервисах для управления состоянием
- **Computed properties** для производных данных

### Кеширование

#### In-Memory Cache
- Результаты хранятся в памяти для быстрого доступа
- Асинхронное обновление из БД при необходимости

```java
private static final Map<Long, List<PointEntity>> resultCache = new ConcurrentHashMap<>();
```

#### Cache Invalidation
- При добавлении нового результата
- При очистке результатов пользователя
- При логауте пользователя

---

## Структура проекта

### Backend структура

```
src/main/java/com/weblab/
├── Main.java                          # Точка входа приложения
├── config/
│   ├── AppConfig.java                 # Конфигурация JAX-RS
│   ├── CorsFilter.java                # CORS фильтр
│   ├── JacksonConfig.java             # Конфигурация JSON
│   └── OptionsRequestFilter.java      # Обработка OPTIONS запросов
├── db/
│   └── Database.java                  # Инициализация JPA/Derby
├── dto/                               # Data Transfer Objects
│   ├── AuthResponse.java
│   ├── LoginRequest.java
│   ├── PointRequest.java
│   ├── RefreshTokenRequest.java
│   └── RegisterRequest.java
├── entity/                            # JPA сущности
│   ├── PointEntity.java               # Результат проверки
│   ├── RefreshTokenEntity.java        # Refresh-токен
│   └── UserEntity.java                # Пользователь
├── filter/
│   └── AuthFilter.java                # JWT валидация фильтр
├── repository/                        # Data Access Layer
│   ├── RefreshTokenRepository.java
│   ├── ResultRepository.java
│   └── UserRepository.java
├── resource/                          # REST контроллеры
│   ├── AuthResource.java              # /api/auth
│   └── ResultResource.java            # /api/results
├── service/                           # Business Logic
│   ├── AuthService.java               # Аутентификация
│   └── ResultService.java             # Работа с результатами
└── util/
    ├── HitChecker.java                # Проверка попадания
    └── JwtUtil.java                   # JWT операции

src/main/resources/
└── META-INF/
    └── persistence.xml                # JPA конфигурация
```

### Frontend структура

```
frontendVUE/
├── src/
│   ├── main.ts                        # Точка входа
│   ├── App.vue                        # Корневой компонент
│   ├── assets/
│   │   └── styles.css                 # Глобальные стили
│   ├── components/
│   │   ├── GraphCanvas.vue            # SVG график
│   │   ├── PointForm.vue              # Форма ввода
│   │   └── ResultsTable.vue           # Таблица результатов
│   ├── models/
│   │   └── types.ts                   # TypeScript типы
│   ├── router/
│   │   └── index.ts                   # Конфигурация маршрутов
│   ├── services/
│   │   ├── api.ts                     # Axios конфигурация
│   │   ├── authService.ts             # Сервис аутентификации
│   │   └── pointService.ts            # Сервис результатов
│   └── views/
│       ├── LoginView.vue              # Экран входа
│       ├── MainView.vue               # Главная страница
│       └── RegisterView.vue           # Экран регистрации
├── vite.config.ts                     # Конфигурация Vite
├── tsconfig.json                      # TypeScript конфигурация
└── package.json                       # Зависимости npm
```

---

## Выводы

### Достигнутые цели

✅ **Полностью реализовано веб-приложение** со всеми требуемыми функциями  
✅ **Безопасная аутентификация** на основе JWT токенов  
✅ **Персистентное хранилище** результатов в БД  
✅ **Интерактивный интерфейс** на современном фреймворке  
✅ **REST API** с правильной семантикой  
✅ **Типизированный код** (Java + TypeScript)

### Ключевые технические решения

1. **JAX-RS + Jersey** — простой и мощный фреймворк для REST API
2. **Apache Derby** — встроенная БД без необходимости отдельного сервера
3. **JWT** — стандартный способ аутентификации в веб-приложениях
4. **Vue 3 + TypeScript** — современный, типизированный фреймворк
5. **JPA/EclipseLink** — удобный ORM слой над БД

### Полученные навыки

- Разработка REST API на Java
- Работа с JWT аутентификацией и токенами
- Проектирование БД и работа с JPA
- Разработка SPA на Vue 3 + TypeScript
- Применение паттернов архитектуры (MVC, трёхслойная архитектура)
- Безопасность веб-приложений (BCrypt, CORS, JWT)
- Использование современных инструментов разработки (Gradle, Vite, npm)

### Возможные улучшения

- Добавить unit и integration тесты
- Реализовать rate limiting для защиты от DDoS
- Добавить двухфакторную аутентификацию (2FA)
- Миграция на Spring Boot для большей функциональности
- Добавить WebSocket для real-time обновлений
- Развернуть на облачной платформе (AWS, Azure, GCP)
- Добавить документацию API (Swagger/OpenAPI)

---

## 📚 Использованная литература

1. JAX-RS 2.1 Specification (JSR 370)
2. JWT: JSON Web Token Specification (RFC 7519)
3. Vue.js 3 Official Documentation
4. Apache Derby Reference Manual
5. OWASP Web Security Guidelines
6. RESTful Web Services Best Practices

---

**Дата завершения:** 2025-2026  
**Язык программирования:** Java 17 + TypeScript 5.3  
**Фреймворки:** JAX-RS, Vue 3  
**Базы данных:** Apache Derby (JPA/EclipseLink)


