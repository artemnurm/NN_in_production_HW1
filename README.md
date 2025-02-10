```markdown:NN_in_Production/HW_1/README.md
# Сервис нейронной сети с использованием RabbitMQ

Этот проект демонстрирует микросервисную архитектуру системы машинного обучения с использованием RabbitMQ для организации очередей сообщений. Проект является частью курса "Нейронные сети в продакшене" в ТГУ (Магистратура НЛП, 2024/2025).

## Архитектура системы

Система состоит из четырех основных сервисов:

1. **Сервис признаков** (`features.py`):
   - Генерирует тестовые признаки и истинные метки
   - Отправляет данные в очереди RabbitMQ (`features` и `y_true`)
   - Работает непрерывно с интервалом в 5 секунд

2. **Сервис модели** (`model.py`):
   - Получает данные признаков из очереди `features`
   - Запускает простую тестовую модель (сумма признаков)
   - Отправляет предсказания в очередь `y_pred`

3. **Сервис метрик** (`metrics.py`):
   - Получает истинные метки и предсказания
   - Вычисляет абсолютную ошибку
   - Записывает результаты в CSV файл

4. **Сервис визуализации** (`plot.py`):
   - Отслеживает файл с метриками
   - Создает и обновляет гистограмму распределения ошибок
   - Сохраняет график каждые 10 секунд

## Требования

- Docker
- Docker Compose

## Структура проекта

```
HW_1/
├── Dockerfile
├── docker-compose.yml
├── features.py
├── model.py
├── metrics.py
├── plot.py
├── logs/
│   ├── metric_log.csv
│   └── error_distribution.png
└── README.md
```

## Запуск проекта

1. Клонируйте репозиторий
2. Перейдите в директорию проекта
3. Запустите сервисы с помощью Docker Compose:

```bash
docker-compose up --build
```

Это запустит все сервисы и RabbitMQ. Система автоматически начнет генерировать предсказания и записывать метрики.

## Мониторинг

- Интерфейс управления RabbitMQ: http://localhost:15672 (guest/guest)
- Метрики записываются в: `logs/metric_log.csv`
- График распределения ошибок: `logs/error_distribution.png`

## Детали сервисов

### Поток данных
1. Сервис признаков генерирует тестовые данные и отправляет их в RabbitMQ
2. Сервис модели обрабатывает признаки и делает предсказания
3. Сервис метрик объединяет предсказания с истинными значениями и вычисляет ошибки
4. Сервис визуализации отображает распределение ошибок

### Переменные окружения
- `RABBITMQ_HOST`: Адрес сервера RabbitMQ (по умолчанию: rabbitmq)
- `CSV_FILE`: Путь к файлу логов метрик (по умолчанию: /app/logs/metric_log.csv)
- `IMG_FILE`: Путь к графику распределения ошибок (по умолчанию: /app/logs/error_distribution.png)

## Конфигурация Docker

Проект использует Docker для контейнеризации и включает:
- Базовый образ Python 3.9 slim
- Автоматическое монтирование томов для логов
- Проверки работоспособности для RabbitMQ
- Управление зависимостями сервисов

## Примечания

- Это демонстрационный проект, использующий тестовые данные и простую модель
- Система спроектирована для демонстрации принципов микросервисной архитектуры
- Все сервисы контейнеризированы и могут масштабироваться независимо
- Сохранение данных осуществляется через Docker volumes

## Автор

Студент магистратуры ТГУ, программа НЛП 2024/2025
```
