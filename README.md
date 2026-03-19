# Программирование (Java) - Лабораторная №4

Полноценное веб-приложение с аутентификацией, хранением результатов проверок точек и адаптивным клиентским интерфейсом.

**Студент:**
- Ануфриев Андрей Сергеевич
- Группа: P3219



## 📁 Материалы проекта
- [**Задание**](https://se.ifmo.ru/courses/programming)
- [**Код Backend**](./src/)
- [**Код Frontend**](./front/)

---

## 📋 Описание проекта

Проект состоит из двух основных частей:

- **Backend** на Java (Spring Boot) — предоставляет REST API для аутентификации, управления сессиями и работы с результатами
- **Frontend** на Angular — реализует интерфейс пользователя с формами авторизации, графиком, формой ввода параметров и таблицей результатов

---

## 🛠️ Технологии

### Backend
- **Java 17+**
- **Spring Boot 3.1.5** — основной фреймворк
- **Spring Web** — REST API
- **Spring Security** — JWT-аутентификация
- **Spring Data JPA** — работа с БД (Hibernate)
- **Spring AOP** — логирование запросов к БД
- **PostgreSQL** — основная база данных
- **JWT (jjwt)** — JSON Web Token для безопасности
- **BCrypt** — хеширование паролей
- **Lombok** — сокращение кода
- **Gradle** — система сборки

### Frontend
- **Angular 21.0** — основной фреймворк
- **TypeScript 5.9** — язык программирования
- **RxJS 7.8** — реактивное программирование
- **HTML5/CSS3** — верстка и стили
- **Responsive Design** — адаптивный дизайн для всех экранов

### Архитектурные решения
- **JWT-аутентификация** с access и refresh токенами
- **Многосессионность** — несколько браузеров одновременно
- **In-memory кеш** результатов для производительности
- **HTTP-фильтры** для аутентификации и логирования
- **Многослойная архитектура**: Controller → Service → Repository

---

## ✨ Реализованный функционал

### 1️⃣ Аутентификация и авторизация
- ✅ Регистрация новых пользователей с валидацией
- ✅ Вход с выдачей JWT-токенов
- ✅ **Access Token** (короткоживущий, 24 часа)
- ✅ **Refresh Token** (для обновления access-токена)
- ✅ Безопасное обновление токенов (одноразовое использование)
- ✅ Выход из сессии (logout)

### 2️⃣ Управление сессиями
- ✅ Несколько активных сессий пользователя
- ✅ Валидация сессии при каждом запросе
- ✅ Завершение одной или нескольких сессий
- ✅ Периодическая очистка устаревших сессий (Scheduled)
- ✅ Отслеживание IP-адреса и User-Agent

### 3️⃣ Работа с результатами
- ✅ Добавление результатов проверки точек
- ✅ Получение всех результатов пользователя
- ✅ Очистка всех результатов пользователя
- ✅ Кеширование результатов в памяти
- ✅ Хранение времени выполнения операции

### 4️⃣ HTTP-фильтрация и логирование
- ✅ Фильтр проверки JWT и сессии
- ✅ Логирование всех HTTP-запросов
- ✅ Конфигурация CORS для кросс-доменных запросов
- ✅ Логирование SQL-запросов на уровне Hibernate

### 5️⃣ Клиентская часть
- ✅ Экран логина с валидацией
- ✅ Экран регистрации
- ✅ Главная страница с:
  - Графиком визуализации точек
  - Формой ввода параметров (X, Y, R)
  - Таблицей результатов с сортировкой
- ✅ Модаль управления активными сессиями
- ✅ HTTP-интерсептор для добавления JWT-токена
- ✅ Адаптивный интерфейс для всех экранов

---

## 📁 Структура проекта

```
web_lab_4/
├── src/main/java/com/weblab/
│   ├── Application.java                      # Точка входа приложения
│   ├── config/
│   │   ├── WebConfig.java                    # CORS и веб-конфигурация
│   │   └── RepositoryLoggingAspect.java      # AOP для логирования БД
│   ├── controller/
│   │   ├── AuthController.java               # API: login/register/logout
│   │   ├── ResultController.java             # API: работа с результатами
│   │   └── SessionController.java            # API: управление сессиями
│   ├── dto/
│   │   ├── AuthResponse.java                 # Ответ при входе
│   │   ├── LoginRequest.java                 # Запрос логина
│   │   ├── RegisterRequest.java              # Запрос регистрации
│   │   ├── PointRequest.java                 # Запрос проверки точки
│   │   ├── RefreshTokenRequest.java          # Обновление токена
│   │   ├── ResultResponse.java               # Ответ с результатом
│   │   └── SessionClosedDTO.java             # Информация о сессии
│   ├── entity/
│   │   ├── User.java                         # Сущность пользователя
│   │   ├── Session.java                      # Сущность сессии
│   │   └── Result.java                       # Сущность результата проверки
│   ├── filter/
│   │   ├── JwtAuthenticationFilter.java      # Проверка JWT и сессии
│   │   └── RequestLoggingFilter.java         # Логирование HTTP-запросов
│   ├── repository/
│   │   ├── UserRepository.java               # JPA для пользователей
│   │   ├── SessionRepository.java            # JPA для сессий
│   │   └── ResultRepository.java             # JPA для результатов
│   ├── service/
│   │   ├── AuthService.java                  # Логика аутентификации
│   │   ├── JwtTokenProvider.java             # Создание/проверка JWT
│   │   ├── SessionService.java               # Управление сессиями
│   │   ├── UserService.java                  # Работа с пользователями
│   │   ├── ResultService.java                # Работа с результатами
│   │   ├── ResultCacheService.java           # In-memory кеш результатов
│   │   └── SessionCleanupService.java        # Очистка устаревших сессий
│   └── util/
│       └── RequestUtils.java                 # Утилиты для работы с запросами
├── src/main/resources/
│   ├── application.properties                # Конфигурация (PostgreSQL)
│   └── application-dev.properties            # Конфигурация (H2)
├── front/                                    # Frontend (Angular)
│   ├── src/
│   │   ├── index.html
│   │   ├── main.ts
│   │   ├── styles.css
│   │   └── app/
│   │       ├── app.ts                        # Корневой компонент
│   │       ├── app.routes.ts                 # Маршруты приложения
│   │       ├── app.config.ts                 # Конфигурация Angular
│   │       ├── components/
│   │       │   ├── login/                    # Компонент входа
│   │       │   ├── register/                 # Компонент регистрации
│   │       │   ├── main/                     # Главная страница
│   │       │   └── session-modal/            # Модаль сессий
│   │       ├── services/
│   │       │   ├── auth.ts                   # Сервис аутентификации
│   │       │   ├── point.ts                  # Сервис результатов
│   │       │   └── session-monitor.ts        # Мониторинг сессии
│   │       ├── interceptors/
│   │       │   └── auth.ts                   # HTTP-интерсептор для JWT
│   │       └── models/
│   │           └── types.ts                  # TypeScript-типы и интерфейсы
│   ├── package.json                          # Зависимости npm
│   ├── proxy.conf.json                       # Проксирование на backend
│   └── angular.json                          # Конфигурация Angular CLI
├── build.gradle                              # Конфигурация Gradle
├── settings.gradle
├── gradlew & gradlew.bat                     # Gradle wrapper
└── README.md

```

---

## 🚀 Установка и запуск

### ✋ Предварительные требования

- **Java 17+** (для backend)
- **Node.js 18+** и **npm** (для frontend)
- **PostgreSQL 12+** (опционально, можно использовать H2)
- **Gradle** (автоматически через `gradlew`)

### 1️⃣ Подготовка базы данных

#### Вариант A: PostgreSQL (production)

```bash
# Убедитесь, что PostgreSQL запущена
# Linux/Mac:
psql -U postgres -c "CREATE DATABASE lab_4;"

# Windows (cmd/PowerShell):
psql -U postgres -c "CREATE DATABASE lab_4;"
```

#### Вариант B: H2 (разработка, в памяти)

Используется по умолчанию при профиле `dev`, не требует дополнительной установки.

### 2️⃣ Запуск Backend (Spring Boot)

```bash
# Перейдите в корневую директорию проекта
cd web_lab_4

# Вариант 1: С PostgreSQL (по умолчанию)
./gradlew bootRun

# Вариант 2: С H2 в памяти (для разработки)
./gradlew bootRun --args='--spring.profiles.active=dev'

# Вариант 3: Windows (cmd)
gradlew.bat bootRun
```

**Ожидаемый вывод:**
```
Started Application in 5.234 seconds
Application 'web-lab-auth' running on http://localhost:8080
```

✅ Backend доступен по адресу: **http://localhost:8080**

### 3️⃣ Запуск Frontend (Angular)

```bash
# В новом терминале, перейдите в директорию frontend
cd web_lab_4/front

# Установите зависимости (первый раз)
npm install

# Запустите dev-сервер
npm start
# или
ng serve
```

**Ожидаемый вывод:**
```
✔ Compiled successfully.
Local: http://localhost:4200/
```

✅ Frontend доступен по адресу: **http://localhost:4200**

#### ⚠️ Важно!
- Backend должен быть запущен **перед** фронтом
- Frontend использует `proxy.conf.json` для перенаправления запросов на backend
- Оба сервера должны быть запущены одновременно для полной работы

### 4️⃣ Сборка для production

#### Backend (JAR файл)
```bash
./gradlew bootJar
# JAR-файл будет в build/libs/web-lab-auth-1.0.0.jar

# Запуск
java -jar build/libs/web-lab-auth-1.0.0.jar
```

#### Frontend (Static assets)
```bash
cd front
npm run build
# Результат в front/dist/
```

---

## 🔐 Конфигурация

### Backend (application.properties)

**src/main/resources/application.properties** (PostgreSQL):
```properties
server.port=8080
spring.application.name=web-lab-auth

# PostgreSQL Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/lab_4
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA/Hibernate
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQL10Dialect
spring.jpa.hibernate.ddl-auto=none
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Logging
logging.level.com.weblab=DEBUG
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate.SQL=DEBUG

# JWT Configuration
jwt.secret=my-super-secret-key-that-is-at-least-256-bits-long
jwt.expiration=86400000  # 24 часа в миллисекундах
```

**src/main/resources/application-dev.properties** (H2):
```properties
server.port=8080

# H2 Database (in-memory)
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Hibernate
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
```

### Frontend (proxy.conf.json)

**front/proxy.conf.json** (разработка):
```json
{
  "/api": {
    "target": "http://localhost:8080",
    "changeOrigin": true,
    "logLevel": "debug"
  }
}
```

---

## 📝 REST API Endpoints

### Аутентификация (`/api/auth`)

| Метод | Endpoint | Описание | Требует JWT | Пример тела |
|-------|----------|---------|-----------|-----------|
| POST | `/register` | Регистрация | ❌ | `{username, password, email, deviceName}` |
| POST | `/login` | Вход | ❌ | `{username, password, deviceName}` |
| POST | `/refresh` | Обновить токен | ✅ | `{refreshToken}` |
| POST | `/logout` | Выход | ✅ | - |

**Пример регистрации:**
```bash
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john_doe",
    "password": "SecurePass123!",
    "email": "john@example.com",
    "deviceName": "Chrome on Windows"
  }'
```

**Ответ (успех):**
```json
{
  "success": true,
  "message": "Регистрация успешна",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "sessionId": 1,
  "user": {
    "id": 1,
    "username": "john_doe",
    "email": "john@example.com"
  }
}
```

### Результаты (`/api/results`)

| Метод | Endpoint | Описание | Требует JWT |
|-------|----------|---------|-----------|
| POST | `/check` | Проверить точку | ✅ |
| GET | `/` | Все результаты | ✅ |
| DELETE | `/` | Очистить результаты | ✅ |

**Пример проверки точки:**
```bash
curl -X POST http://localhost:8080/api/results/check \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer TOKEN" \
  -d '{"x": 1.5, "y": 2.0, "r": 3.0}'
```

### Сессии (`/api/sessions`)

| Метод | Endpoint | Описание | Требует JWT |
|-------|----------|---------|-----------|
| GET | `/` | Активные сессии | ✅ |
| DELETE | `/{id}` | Завершить сессию | ✅ |

---

## 🧪 Тестирование

### Backend
```bash
./gradlew test
```

### Frontend
```bash
cd front
npm test
```

---

## 🔑 Ключевые особенности

### Backend
✅ Spring Security с JWT-токенами  
✅ JPA/Hibernate для работы с БД  
✅ Spring AOP для логирования  
✅ Многослойная архитектура (Controller → Service → Repository)  
✅ Валидация входных данных  
✅ In-memory кеширование результатов  
✅ Scheduled очистка сессий  
✅ HTTP-фильтры для аутентификации  

### Frontend
✅ Angular Standalone Components  
✅ RxJS Observables и реактивное программирование  
✅ HTTP Interceptors для JWT-токенов  
✅ Route Guards для защиты страниц  
✅ Валидация форм  
✅ TypeScript для типизации  
✅ Адаптивный дизайн  

---

## 🐛 Troubleshooting

### ❌ Ошибка подключения к PostgreSQL
```
ERROR: Connection to localhost:5432 refused
```
**✅ Решение:**
1. Проверьте, что PostgreSQL запущена: `psql -U postgres`
2. Или используйте H2: `./gradlew bootRun --args='--spring.profiles.active=dev'`

### ❌ Ошибка CORS
```
Access to XMLHttpRequest blocked by CORS policy
```
**✅ Решение:**
1. Проверьте `WebConfig.java` — CORS настроен там
2. Убедитесь, что используется `proxy.conf.json` при разработке

### ❌ Frontend не видит backend
```
Failed to fetch http://localhost:4200/api/auth/login
```
**✅ Решение:**
1. Backend должен быть на `http://localhost:8080` (./gradlew bootRun)
2. Frontend должен быть на `http://localhost:4200` (npm start)
3. Используется `proxy.conf.json` для перенаправления

### ❌ JWT-токен истёк
```
{"error": "Token has expired"}
```
**✅ Решение:**
1. AuthService на фронте автоматически обновляет токен через refresh-token
2. Или заново войдите в систему

### ❌ Port 8080/4200 занят
```
Address already in use
```
**✅ Решение:**
```bash
# Backend на другом порту
./gradlew bootRun --args='--server.port=8081'

# Frontend на другом порту
ng serve --port 4201
```

### ❌ Node modules не установлены
```
Cannot find module '@angular/core'
```
**✅ Решение:**
```bash
cd front
npm install
```

---

## 📊 Компоненты системы

### Backend контроллеры
- **AuthController** — регистрация, вход, выход, обновление токена
- **ResultController** — добавление результатов, получение, очистка
- **SessionController** — управление сессиями пользователя

### Backend сервисы
- **AuthService** — бизнес-логика аутентификации
- **JwtTokenProvider** — создание и валидация JWT-токенов
- **SessionService** — управление сессиями и токенами
- **ResultService** — работа с результатами и кешем
- **UserService** — управление пользователями
- **SessionCleanupService** — периодическая очистка

### Frontend компоненты
- **LoginComponent** — форма входа
- **RegisterComponent** — форма регистрации
- **MainComponent** — главная страница с графиком и таблицей
- **SessionModalComponent** — управление сессиями

### Frontend сервисы
- **AuthService** — аутентификация и токены
- **PointService** — работа с результатами
- **SessionMonitorService** — мониторинг активности

---

## 📚 Дополнительные ресурсы

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Angular Documentation](https://angular.io/docs)
- [JWT RFC 7519](https://tools.ietf.org/html/rfc7519)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Spring Security](https://spring.io/projects/spring-security)
- [Hibernate ORM](https://hibernate.org/orm/)

---

## 📄 Информация о проекте

**Создано:** 2024-2025  
**Язык программирования:** Java + TypeScript  
**Версия Java:** 17+  
**Версия Angular:** 21+  
**Версия Spring Boot:** 3.1.5  
**Тип проекта:** Учебный

Проект создан в рамках курса **"Программирование"** в университете **ИТМО** под руководством:
- **Практик:** Ермаков Михаил Константинович
- **Лектор:** Гаврилов Антон Валерьевич

**Студент:** Ануфриев Андрей Сергеевич (Группа P3119)

---

## 📄 Лицензия

Проект создан в образовательных целях как часть курса программирования ИТМО.

