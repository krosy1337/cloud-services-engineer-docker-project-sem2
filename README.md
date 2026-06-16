# Pelmeni

## Запуск

```bash
docker-compose up -d
```

После запуска приложение доступно по адресу http://localhost/

## Оптимизация размера образов

Для backend и frontend используются multi-stage сборки и легкие базовые образы.

Backend:

- этап сборки использует `golang:1.25.7-alpine3.23`;
- итоговый образ использует `alpine:3.24.1`;
- бинарник собирается с флагами `-ldflags="-s -w"`, чтобы не переносить Go toolchain и уменьшить размер исполняемого файла.

Frontend:

- этап сборки использует `node:24.13.0-alpine`;
- итоговый образ использует `nginx:1.30-alpine3.23-slim`;
- в финальный образ копируется только собранная директория `dist`, без `node_modules` и исходников.

Размеры итоговых образов:

| Компонент | Образ              | Размер  |
| --------- | ------------------ | ------- |
| Backend   | `pelmeni-backend`  | 22.71MB |
| Frontend  | `pelmeni-frontend` | 19.8MB  |

## Сборка фронта

build-аргументы сборки образа:

- BASE_URL - базовый URL для Vue Router, по умолчанию /
- VUE_APP_API_URL - префикс адреса API, по умолчанию /api/
- NODE_ENV - режим сборки для Node, по умолчанию development
- UID - айди, под которым создаётся учётная запись frontend и запускается nginx, по умолчанию 1001

Переменные окружения:

- FRONTEND_CPUS - количество CPU для контейнера фронта
- FRONTEND_MEMORY - объем памяти для контейнера фронта

Ссылка на образ фронта: [krosy/docker-project-frontend:latest]https://hub.docker.com/repository/docker/krosy/docker-project-frontend/general

## Сборка бэка

build-аргументы сборки образа:

- UID - айди, под которым создаётся учётная запись backend и запускается бэк, по умолчанию 1001

Переменные окружения:

- BACKEND_CPUS - количество CPU для контейнера бэка
- BACKEND_MEMORY - объем памяти для контейнера бэка

Ссылка на образ бэка: [krosy/docker-project-backend:latest](https://hub.docker.com/repository/docker/krosy/docker-project-backend/general)

## Порядок запуска сервисов

В docker-compose.yml указано для фронта depends_on относительно бэка с условием service_healthy. Сначала поднимается и проходит проверку готовности бэк (эндпоинт /health), после этого запускается фронт.

## Масштабирование

Можно поднимать несколько реплик сервиса backend в Docker Compose. Запросы с фронта на /api/ проксирует nginx из контейнера frontend:

Пример запуска с тремя репликами бэка:

```bash
docker-compose up -d --build --scale backend=3
```

## Безопасность

Для запуска контейнера frontend используется непривилегированный пользователь frontend:nginx, а для запуска контейнера backend используются непривилегированный пользователь backend:www-data.

Контейнеры frontend и backend не имеют уязвимостей уровня CRITICAL.
