# Инференс-платформа: cvfront + models_server (Delimobil, прод)

Двухуровневая платформа сервинга ML-моделей: FastAPI-фронт (CPU-препроцессинг + бизнес-логика) → TorchServe-бэкенд (CPU/GPU-инференс) по REST.

## cvfront (фронт-сервис)

- FastAPI + uvicorn (ASGI, мультипроцесс), pydantic; ~15 эндпоинтов.
- Тонкий сабкласс uvicorn-супервизора: свой liveness-таймаут воркера (20 с) + логирование (kill/restart зависшего воркера — штатное поведение uvicorn); контроль конкурентности.
- Структурированные JSON-логи с `request_id` через contextvars, перехват uvicorn access/error.

## models_server (инференс-бэкенд)

- TorchServe с кастомными хендлерами (torchscript / YOLO).
- CPU и GPU инференс: выбор устройства, батчинг per-model.
- Prometheus-метрики; клиент с retry/timeout.

## CI и поставка моделей

- GitLab CI: мульти-стейдж Docker, `torch-model-archiver` (.mar), pytest + junit, версия из `CI_PIPELINE_IID`.
- Выкат в 3 странах × stg/prd (werf + Helm — devops; докеризация моя).
- Общий внутренний пакет через приватный GitLab PyPI.

## Нагрузочное профилирование

Самописный бенчмарк (`multiprocessing.Pool`, 50 параллельных клиентов, per-request латентность и RPS) для подбора конфигурации TorchServe: число воркеров, `batch_size`, `max_batch_delay`. Отдельно измерял вклад препроцессинга и «чистой сети», сравнивал CPU vs GPU. Locust/k6 не использовал.

## Latency-кейс

Синхронная серверная обёртка фильтрации фото с бюджетом ответа 1 секунда: поведение give-up без retry под latency-бюджет, нагрузочное тестирование. Это синхронный сервис под SLA; потокового видео-инференса в проекте не было.
