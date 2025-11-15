# E-Commerce Platform

[![Java](https://img.shields.io/badge/Java-17-blue?logo=openjdk)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-green?logo=springboot)](https://spring.io/projects/spring-boot)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-green?logo=spring)](https://spring.io/projects/spring-cloud)
[![Keycloak](https://img.shields.io/badge/Keycloak-21.x-orange?logo=keycloak)](https://www.keycloak.org/)
[![Kafka](https://img.shields.io/badge/Apache%20Kafka-3.x-black?logo=apachekafka)](https://kafka.apache.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue?logo=postgresql)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-7.x-red?logo=redis)](https://redis.io/)
[![AWS S3](https://img.shields.io/badge/Amazon%20S3-Latest-orange?logo=amazons3)](https://aws.amazon.com/s3/)
[![Docker](https://img.shields.io/badge/Docker-Latest-blue?logo=docker)](https://www.docker.com/)

Современная платформа электронной коммерции, построенная на основе микросервисной архитектуры. Система обеспечивает безопасную аутентификацию, гибкое управление товарами, автоматическую модерацию контента с использованием AI и асинхронную коммуникацию между сервисами.

## 🏗️ Архитектура

Система состоит из нескольких слабосвязанных сервисов, взаимодействующих через REST API и асинхронные сообщения (Kafka).

### Стек технологий

*   **API Gateway**: Spring Cloud Gateway
*   **Service Discovery**: Netflix Eureka
*   **Authentication & Authorization**: Keycloak
*   **Message Broker**: Apache Kafka
*   **Cache**: Redis
*   **Object Storage**: AWS S3 (или совместимый)
*   **Базы данных**: Каждый сервис использует свою собственную БД (PostgreSQL)
*   **Фреймворк**: Spring Boot 3.x, Spring Cloud

### Статус и Планы

Проект находится в стадии активной разработки.

Запланировано к реализации:

* warehouse-service: Имитация логистики и управления складскими запасами.

* order-service: Полный цикл обработки заказов.

* payment-service: Интеграция с платежными шлюзами.

* Централизованное логирование (ELK Stack).

* Мониторинг и трейсинг (Grafana, Prometheus, Zipkin).

* Настройка CI/CD (GitHub Actions/GitLab CI).

### Схема взаимодействия сервисов

```mermaid
graph TD
    Client[Клиент Web/Mobile] --> API[API Gateway];
    
    subgraph "Сервисы приложения"
        Auth[Auth Service]
        User[User Service]
        Product[Product Service]
        Cart[Cart Service]
        Ingestion[Product Ingestion Service]
        Moderation[Product Moderation Service]
        Notification[Email Notification Service]
    end

    subgraph "Внешние системы"
        Keycloak[Keycloak]
        S3[AWS S3]
        AI[AI Модель]
    end

    subgraph "Kafka Cluster"
        K[Kafka Brokers]
    end

    subgraph "Кеширование Redis"
        Redis[Redis]
    end

    %% Аутентификация
    Auth --> Keycloak;
    
    %% Основные потоки данных
    Client --> API --> Ingestion;
    Ingestion --> K;
    K --> Moderation;
    Moderation --> S3;
    Moderation --> AI;
    Moderation --> K;
    K --> Product;
    K --> Notification;
    
    %% События регистрации
    Auth --> K;
    K --> Notification;
    
    %% Взаимодействие сервисов
    API --> Auth;
    API --> User;
    API --> Product;
    API --> Cart;
    
    %% Кеширование
    Product --> Redis;
    Cart --> Redis;
