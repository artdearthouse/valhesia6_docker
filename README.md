# Valhelsia 6 Docker Server

Докеризированный сервер Minecraft с модпаком Valhelsia 6 и автоматическими бэкапами.

## Особенности

- 🎮 Модпак Valhelsia 6 с автоматической загрузкой через CurseForge
- 💾 Автоматические ежечасные бэкапы с ротацией
- ☕ Java 21 GraalVM для оптимальной производительности
- 🐳 Простое развертывание через Docker Compose

## Требования

- Docker и Docker Compose
- Минимум 4GB оперативной памяти
- API ключ CurseForge (бесплатный)

## Быстрый старт

### 1. Получение API ключа CurseForge

1. Зарегистрируйтесь на [CurseForge Console](https://console.curseforge.com/)
2. Создайте новый API ключ
3. Скопируйте ключ для следующего шага

### 2. Настройка переменных окружения

Создайте файл `.env` в корне проекта:

```bash
cp .env.example .env
```

Отредактируйте `.env` и добавьте ваш API ключ:

```env
CF_API_KEY=$$2a$$10$$...ваш_ключ...
```

⚠️ **Важно**: Удвойте знаки доллара в ключе (`$` → `$$`)

### 3. Запуск сервера

```bash
docker compose up -d
```

Сервер будет доступен на порту `25565`.

## Конфигурация

### Параметры сервера

В `docker-compose.yml` можно настроить:

- `MEMORY`: Объем выделенной памяти (по умолчанию 3.5G)
- `RCON_PASSWORD`: Пароль для RCON подключения (используется для бэкапов)

### Настройки бэкапов

Контейнер `backup` автоматически создает резервные копии:

- **Частота**: Каждый час
- **Хранение**: 7 дней (старые удаляются автоматически)
- **Расположение**: `./backups/`
- **Формат**: tar.gz архивы с временными метками

Параметры можно изменить в `docker-compose.yml`:

```yaml
BACKUP_INTERVAL: "1h"      # Интервал бэкапов
PRUNE_BACKUPS_DAYS: 7      # Дней хранения
INITIAL_DELAY: 5m          # Задержка перед первым бэкапом
```

### Ручной бэкап

Для создания бэкапа вне расписания:

```bash
docker compose exec backup backup now
```

## Структура проекта

```
valhesia6_docker/
├── docker-compose.yml   # Конфигурация контейнеров
├── .env                 # Переменные окружения
├── data/               # Данные сервера Minecraft
└── backups/            # Резервные копии
```

## Управление сервером

### Просмотр логов

```bash
# Логи сервера Minecraft
docker compose logs -f mc-valhesia6

# Логи бэкапов
docker compose logs -f backup
```

### Остановка сервера

```bash
docker compose down
```

### Обновление модпака

Модпак обновляется автоматически при перезапуске благодаря `CF_FORCE_SYNCHRONIZE: "true"`.

## Восстановление из бэкапа

1. Остановите сервер:
   ```bash
   docker compose down
   ```

2. Найдите нужный бэкап в `./backups/`

3. Восстановите данные:
   ```bash
   tar -xzvf backups/world-YYYYMMDD-HHMMSS.tar.gz -C data/
   ```

4. Запустите сервер:
   ```bash
   docker compose up -d
   ```

## Решение проблем

### Сервер не запускается

- Проверьте правильность API ключа CurseForge
- Убедитесь, что порт 25565 не занят
- Проверьте логи: `docker compose logs mc-valhesia6`

### Бэкапы не создаются

- Проверьте, что RCON включен и пароли совпадают
- Проверьте логи бэкап-контейнера: `docker compose logs backup`
- Убедитесь, что есть права на запись в папку `./backups/`

## Технологии

- [itzg/minecraft-server](https://github.com/itzg/docker-minecraft-server) - Docker образ сервера Minecraft
- [itzg/mc-backup](https://github.com/itzg/docker-mc-backup) - Система автоматических бэкапов
- [Valhelsia 6](https://www.curseforge.com/minecraft/modpacks/valhelsia-6) - Модпак

## Лицензия

MIT