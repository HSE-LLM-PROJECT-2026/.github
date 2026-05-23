Сервис для управления жизненным циклом LLM-моделей и inference-инфраструктурой. Платформа автоматизирует деплой моделей, валидацию перед выкладкой, маршрутизацию трафика, canary-релизы, квоты, расчёт затрат и мониторинг.

![Kubernetes](http://img.shields.io/badge/kubernetes-%23326ce5.svg?style=flat&logo=kubernetes&logoColor=white) ![Python](http://img.shields.io/badge/python-3670A0?style=flat&logo=python&logoColor=ffdd54) ![React](http://img.shields.io/badge/react-%2320232a.svg?style=flat&logo=react&logoColor=%2361DAFB)

> Если возникают проблемы с инфраструктурой или любые другие сбои при работе сервиса, пожалуйста, напишите сюда: [@igmalysh](https://t.me/igmalysh)

## 🚀 Доступные интерфейсы

| Интерфейс | URL | Назначение |
|-----------|-----|------------|
| Web UI | [https://frontend.hse-llm-project-2026.ru/](https://frontend.hse-llm-project-2026.ru/) | Основной интерфейс платформы |
| Grafana | [https://grafana.hse-llm-project-2026.ru/](https://grafana.hse-llm-project-2026.ru/) | Дашборды мониторинга |

## 🧩 Основные компоненты системы

### Пользовательский слой

| Компонент | Назначение |
|-----------|------------|
| Frontend | Web UI для управления деплойментами, релизами, трафиком, квотами, затратами, доступами и аудитом |

### Control Plane

| Сервис | Назначение |
|--------|------------|
| Deployment service | Жизненный цикл LLM-деплойментов и запись `LLMDeployment CR` |
| Routing service | Управление маршрутами и весами трафика через `TrafficRoute CRD` |
| Inference gateway | OpenAI-compatible входная точка для inference-запросов |

### Automation

| Сервис | Назначение |
|--------|------------|
| Validation service | Проверка модели перед переводом в рабочий статус |
| Release controller | Canary rollout, pause/resume и rollback |
| Autoscaler service | Масштабирование реплик по метрикам нагрузки |

### Governance и FinOps

| Сервис | Назначение |
|--------|------------|
| Quota service | Квоты, лимиты, приоритеты и сценарии block/throttle/warn |
| Cost service | Расчет стоимости inference и FinOps-аналитика |
| Security & Audit service | Auth, RBAC, технические токены и журнал аудита |
| State facade | Агрегированное состояние платформы для realtime UI |

### Kubernetes Runtime

| Компонент | Назначение |
|-----------|------------|
| LLMDeployment CRD | Декларативное описание LLM-сервиса |
| TrafficRoute CRD | Декларативное описание маршрута и весов трафика |
| Kubernetes operator | Приведение CRD к Kubernetes Deployment, Service и HTTPRoute |
| vLLM | Runtime для OpenAI-compatible inference |

### Инфраструктура

| Компонент | Назначение |
|-----------|------------|
| PostgreSQL | Хранение бизнес-состояния платформы |
| Prometheus | Сбор метрик сервисов, runtime и инфраструктуры |
| Grafana | Визуализация метрик и дашборды |
| Loki | Сбор и хранение логов |
| Gateway API + Cilium | Сетевой слой и маршрутизация HTTP-трафика |
| MetalLB / ExternalDNS / CoreDNS | Внешняя публикация и DNS |

## 🛠️ Технологический стек

- **Бекенд**: Python 3.11, FastAPI
- **Фронтенд**: React, TypeScript, Vite
- **База данных**: PostgreSQL
- **Оркестрация**: Kubernetes, Docker, Helm
- **LLM runtime**: vLLM
- **Сетевая часть**: Gateway API, Cilium, MetalLB, ExternalDNS
- **Мониторинг**: Prometheus, Grafana, Loki

## 📊 Что мониторится

- инфраструктурные метрики кластера и нод;
- метрики inference: latency, throughput, queue, error rate;
- метрики релизов и автоскейлинга;
- бизнес-метрики затрат и квот.

## 🗂️ Репозитории проекта

### Management APIs
| Репозиторий | Описание |
|-------------|----------|
| [deployment_service](https://github.com/HSE-LLM-PROJECT-2026/deployment_service) | Deployment lifecycle service |
| [routing_service](https://github.com/HSE-LLM-PROJECT-2026/routing_service) | Управление маршрутами и весами трафика |
| [inference_gateway](https://github.com/HSE-LLM-PROJECT-2026/inference_gateway) | OpenAI-compatible inference gateway |

### Automation controllers
| Репозиторий | Описание |
|-------------|----------|
| [validation_service](https://github.com/HSE-LLM-PROJECT-2026/validation_service) | Pre-release validation и SLO checks |
| [release_controller](https://github.com/HSE-LLM-PROJECT-2026/release_controller) | Canary rollout и rollback логика |
| [autoscaler_service](https://github.com/HSE-LLM-PROJECT-2026/autoscaler_service) | Политики autoscaling |

### Governance и FinOps
| Репозиторий | Описание |
|-------------|----------|
| [quota_service](https://github.com/HSE-LLM-PROJECT-2026/quota_service) | Квоты и throttle/block/warn |
| [cost_service](https://github.com/HSE-LLM-PROJECT-2026/cost_service) | FinOps и расчет стоимости inference |
| [state_facade](https://github.com/HSE-LLM-PROJECT-2026/state_facade) | Realtime state facade для frontend |
| [security-and-audit-serivce](https://github.com/HSE-LLM-PROJECT-2026/security-and-audit-serivce) | Auth, RBAC, аудит |

### Frontend и Kubernetes runtime
| Репозиторий | Описание |
|-------------|----------|
| [frontend](https://github.com/HSE-LLM-PROJECT-2026/frontend) | Web UI платформы |
| [CRD-LLMDeployment-vllm](https://github.com/HSE-LLM-PROJECT-2026/CRD-LLMDeployment-vllm) | Kubernetes CRD/operator для LLMDeployment/TrafficRoute |
| [sequence-diags](https://github.com/HSE-LLM-PROJECT-2026/sequence-diags) | Sequence и state диаграммы |

### Инфраструктурные репозитории
| Репозиторий | Описание |
|-------------|----------|
| [cluster-config](https://github.com/HSE-LLM-PROJECT-2026/cluster-config) | Terraform/Talos и инфраструктурные скрипты |
| [k8s-dns-pipeline](https://github.com/HSE-LLM-PROJECT-2026/k8s-dns-pipeline) | DNS/Gateway/network pipeline |
| [monitoring-deployment](https://github.com/HSE-LLM-PROJECT-2026/monitoring-deployment) | Grafana/Prometheus/Loki deployment и dashboards |
| [postgresql-deployment](https://github.com/HSE-LLM-PROJECT-2026/postgresql-deployment) | PostgreSQL deployment |

## ⚠️ Важная информация

**Проект является учебно-исследовательской разработкой. Автор не несет ответственности за:**

1. Использование системы как production-grade решения без дополнительного hardening.
2. Прямое использование результатов inference для критичных решений.
3. Любые последствия эксплуатации без резервирования и backup-политик.

Использование осуществляется на свой риск.

## 📬 Контакт

По вопросам проекта:
- **Telegram**: [@igmalysh](https://t.me/igmalysh)
- **Email**: [iimalysh@edu.hse.ru](mailto:iimalysh@edu.hse.ru)
