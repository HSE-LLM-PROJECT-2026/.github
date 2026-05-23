Платформа для управления жизненным циклом LLM-сервисов в Kubernetes. Система закрывает полный процесс работы с моделью: создание деплоймента, проверку перед вводом в эксплуатацию, маршрутизацию инференс-запросов, канареечные релизы, квоты, расчёт стоимости, аудит и мониторинг.

![Kubernetes](http://img.shields.io/badge/kubernetes-%23326ce5.svg?style=flat&logo=kubernetes&logoColor=white) ![Python](http://img.shields.io/badge/python-3670A0?style=flat&logo=python&logoColor=ffdd54) ![React](http://img.shields.io/badge/react-%2320232a.svg?style=flat&logo=react&logoColor=%2361DAFB)

> Если возникают проблемы с инфраструктурой или любые другие сбои при работе сервиса, пишите в Telegram: [@igmalysh](https://t.me/igmalysh)

## 🚀 Демо

| Интерфейс | URL | Назначение |
|-----------|-----|------------|
| Web UI | [https://frontend.hse-llm-project-2026.ru/](https://frontend.hse-llm-project-2026.ru/) | Основной интерфейс платформы |
| Grafana | [https://grafana.hse-llm-project-2026.ru/](https://grafana.hse-llm-project-2026.ru/) | Дашборды мониторинга, доступ `admin / admin` |

## 🧭 Общая идея

Платформа построена по принципу разделения control plane и runtime plane.

Control plane отвечает за бизнес-логику: деплой моделей, маршрутизацию, релизы, валидацию, автоскейлинг, квоты, расчёт стоимости и аудит. Runtime plane — это Kubernetes-кластеры, в которых запускаются LLM-сервисы.

Пользователь работает с платформой через frontend и API доменных backend-сервисов. Kubernetes напрямую пользователю не открывается: все операции с LLMDeployment, TrafficRoute, HTTPRoute, Deployment и Service выполняются через backend-логику и операторы.

## 🧩 Основные компоненты системы

### 👤 Пользовательский слой

| Компонент | Назначение |
|-----------|------------|
| Frontend | Web UI для управления деплойментами, релизами, трафиком, квотами, затратами, доступами и аудитом |

### 🕹️ Control Plane

| Сервис | Назначение |
|--------|------------|
| Deployment service | Отвечает за жизненный цикл LLM-деплойментов: создание, удаление, обновление, redeploy и запуск валидации |
| Routing service | Управляет маршрутами и весами трафика между backend-деплойментами через TrafficRoute CRD |
| Inference gateway | Принимает OpenAI-compatible инференс-запросы, проверяет доступ и проксирует вызов в vLLM |

### ⚙️ Automation

| Сервис | Назначение |
|--------|------------|
| Validation service | Проверяет модель перед переводом в рабочий статус: readiness, endpoints, load test и SLO-метрики |
| Release controller | Управляет канареечными релизами, поэтапно меняет веса трафика и выполняет rollback при нарушении SLO |
| Autoscaler service | Масштабирует реплики LLM-деплойментов на основе метрик нагрузки из Prometheus |

### 🧾 Governance и FinOps

| Сервис | Назначение |
|--------|------------|
| Quota service | Хранит квоты, проверяет лимиты потребления и применяет сценарии block, throttle и warn |
| Cost service | Считает стоимость инференс-запросов и формирует FinOps-агрегации |
| Security & Audit service | Отвечает за JWT, RBAC, технические токены и журнал аудита |
| State facade | Отдаёт frontend агрегированное состояние платформы и runtime-сущностей |

### ☸️ Kubernetes Runtime

| Компонент | Назначение |
|-----------|------------|
| LLMDeployment CRD | Декларативное описание LLM-сервиса: модель, версия, кластер, реплики и параметры инференса |
| TrafficRoute CRD | Декларативное описание маршрута и весов трафика между деплойментами |
| Kubernetes operator | Следит за CRD и приводит фактическое состояние Kubernetes к желаемому |
| vLLM | Runtime для OpenAI-compatible инференса |

### 🏗️ Инфраструктура

| Компонент | Назначение |
|-----------|------------|
| PostgreSQL | Хранение бизнес-состояния платформы: деплойменты, релизы, квоты, затраты, пользователи и аудит |
| Prometheus | Сбор метрик сервисов, vLLM-подов, Kubernetes и инфраструктуры |
| Grafana | Визуализация метрик и дашборды |
| Loki | Сбор и хранение логов |
| Gateway API + Cilium | Сетевой слой и маршрутизация HTTP-трафика |
| MetalLB / ExternalDNS / CoreDNS | Внешняя публикация и DNS |

## 🛠️ Технологический стек

- Бекенд: Python 3.11, FastAPI
- Фронтенд: React, TypeScript, Vite
- База данных: PostgreSQL
- Оркестрация: Kubernetes, Docker, Helm
- LLM runtime: vLLM
- Kubernetes operator: kopf
- Сетевая часть: Gateway API, Cilium, MetalLB, ExternalDNS
- Мониторинг: Prometheus, Grafana, Loki

## 📊 Что мониторится

- состояние сервисов control plane;
- состояние LLM-деплойментов и pod-ов;
- latency, throughput, queue length и error rate инференс-запросов;
- метрики канареечных релизов и автоскейлинга;
- потребление токенов, квоты и стоимость инференса;
- инфраструктурные метрики нод и кластеров.

## 🗂️ Репозитории проекта

### 🔌 Management APIs
| Репозиторий | Описание |
|-------------|----------|
| [deployment_service](https://github.com/HSE-LLM-PROJECT-2026/deployment_service) | Сервис жизненного цикла LLM-деплойментов |
| [routing_service](https://github.com/HSE-LLM-PROJECT-2026/routing_service) | Сервис маршрутов и весов трафика |
| [inference_gateway](https://github.com/HSE-LLM-PROJECT-2026/inference_gateway) | OpenAI-compatible gateway для инференс-запросов |

### ⚙️ Automation controllers
| Репозиторий | Описание |
|-------------|----------|
| [validation_service](https://github.com/HSE-LLM-PROJECT-2026/validation_service) | Сервис проверки модели перед вводом в эксплуатацию |
| [release_controller](https://github.com/HSE-LLM-PROJECT-2026/release_controller) | Контроллер канареечных релизов и rollback |
| [autoscaler_service](https://github.com/HSE-LLM-PROJECT-2026/autoscaler_service) | Сервис политик горизонтального масштабирования |

### 🧾 Governance и FinOps
| Репозиторий | Описание |
|-------------|----------|
| [quota_service](https://github.com/HSE-LLM-PROJECT-2026/quota_service) | Сервис квот и учёта потребления |
| [cost_service](https://github.com/HSE-LLM-PROJECT-2026/cost_service) | Сервис расчёта стоимости инференса |
| [state_facade](https://github.com/HSE-LLM-PROJECT-2026/state_facade) | Realtime facade для frontend |
| [security_and_audit_serivce](https://github.com/HSE-LLM-PROJECT-2026/security-and-audit-serivce) | Сервис аутентификации, RBAC и аудита |

### 🖥️ Frontend и Kubernetes runtime
| Репозиторий | Описание |
|-------------|----------|
| [frontend](https://github.com/HSE-LLM-PROJECT-2026/frontend) | Web UI платформы |
| [CRD-LLMDeployment-vllm](https://github.com/HSE-LLM-PROJECT-2026/CRD-LLMDeployment-vllm) | CRD и Kubernetes operator для LLMDeployment и TrafficRoute |

### 🏗️ Инфраструктурные репозитории
| Репозиторий | Описание |
|-------------|----------|
| [cluster-config](https://github.com/HSE-LLM-PROJECT-2026/cluster-config) | Terraform, Talos и конфигурация кластеров |
| [k8s-dns-pipeline](https://github.com/HSE-LLM-PROJECT-2026/k8s-dns-pipeline) | DNS, Gateway API, Cilium и внешняя публикация сервисов |
| [monitoring-deployment](https://github.com/HSE-LLM-PROJECT-2026/monitoring-deployment) | Prometheus, Grafana, Loki и дашборды |
| [postgresql-deployment](https://github.com/HSE-LLM-PROJECT-2026/postgresql-deployment) | Развёртывание PostgreSQL |

## ⚠️ Важная информация

Проект является учебно-исследовательской разработкой. Автор не несет ответственности за:

1. использование системы как промышленного решения без дополнительного усиления безопасности;
2. прямое использование результатов инференса для критичных решений;
3. любые последствия эксплуатации без резервирования и backup-политик.

Использование осуществляется на свой риск.

## 📬 Контакты

По вопросам проекта:
- **Telegram**: [@igmalysh](https://t.me/igmalysh)
- **Email**: [iimalysh@edu.hse.ru](mailto:iimalysh@edu.hse.ru)
