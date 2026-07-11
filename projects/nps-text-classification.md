# Классификация тематик клиентских отзывов (Delimobil, прод)

Мультилейбл-классификация тематик NPS-отзывов на transformers.

## ML

- Дообучение русскоязычных моделей (sbert / ruBERT / ruELECTRA), HuggingFace transformers.
- Focal loss под дисбаланс классов.
- K-fold кросс-валидация.

## Инженерия

- Композируемый training-фреймворк на DI-контейнерах (dependency_injector).
- DVC-версионирование моделей и данных.
- CLI (click), Docker, GitLab CI, pytest.

Выход модели используется в том числе в [приоритизации машин на мойку](wash-prioritization.md).
