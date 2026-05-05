# Cian Parser — Showcase

[![License](https://img.shields.io/badge/license-Commercial-orange.svg)](LICENSE)
[![Stars](https://img.shields.io/github/stars/CreatmanCEO/cian-parser-showcase?style=flat)](https://github.com/CreatmanCEO/cian-parser-showcase/stargazers)
[![Validate](https://github.com/CreatmanCEO/cian-parser-showcase/actions/workflows/validate.yml/badge.svg)](https://github.com/CreatmanCEO/cian-parser-showcase/actions/workflows/validate.yml)
![Status](https://img.shields.io/badge/status-showcase-blue)
![Platform](https://img.shields.io/badge/platform-Python-3776ab?logo=python&logoColor=white)

[English version](README.md)

> **Это витринный репозиторий с документацией. Исходный код Cian Parser проприетарный и сюда не выложен.**

Cian Parser — коммерческий инструмент автоматизации для [Cian.ru](https://cian.ru). По расписанию мониторит объявления, применяет пользовательские фильтры и шлёт новые совпадения в Telegram. Этот репозиторий показывает функциональность продукта — объём фичей, пример конфигурации, скриншоты — для портфолио и оценки заказчиками.

## Зачем этот репозиторий

Заказчики спрашивают "что он делает и как настраивается". Здесь это показано — без выкладки рабочего скрейпера, анти-детекта и прокси-инфраструктуры; всё это остаётся проприетарным.

## Как устроено (верхнеуровнево)

1. **Планировщик** запускает скрейп по расписанию (APScheduler).
2. **Фетчер** ходит за страницами поиска через ротацию прокси с рандомизацией user-agent и паузами.
3. **Парсер** разбирает объявления в единую схему (цена, площадь, комнаты, локация, дата, контакт).
4. **Фильтры** применяют пользовательские правила (см. [`examples/filters.example.json`](examples/filters.example.json)).
5. **Дедупликатор** сверяет с базой уже виденных объявлений.
6. **Нотификатор** отправляет совпадения в Telegram с фото и ссылкой.
7. **Аналитика** хранит историю цен для отчётов по трендам.

## Стек

| Слой | Инструменты |
|---|---|
| Язык | Python 3.9+ |
| Планировщик | APScheduler |
| HTTP | requests / httpx + ротация прокси |
| Парсинг | lxml / BeautifulSoup |
| Хранилище | SQLite / PostgreSQL |
| Уведомления | Telegram Bot API |
| Деплой | systemd / Docker (по выбору клиента) |

## Примеры

- [`examples/filters.example.json`](examples/filters.example.json) — форма конфигурации фильтров

## Ограничения (витрина, не продукт)

- В репозитории **нет работающего кода**. Клонирование не даст рабочий скрейпер.
- Скриншоты и демо-медиа в `media/` (если есть) могут отставать от текущей версии продукта.
- Примеры фильтров иллюстративные — в проде схема шире и меняется под клиента.
- HTML и анти-бот Cian меняются часто; продакшен поддерживается, но публичных гарантий здесь нет.

## Связаться

По коммерческим запросам и аналогичным проектам: **creatmanick@gmail.com** · [creatman.site](https://creatman.site).

## Автор

**Николай Подоляк** — независимый разработчик, автоматизация и интеграция AI.

- GitHub: [@CreatmanCEO](https://github.com/CreatmanCEO)
- Habr: [creatman](https://habr.com/ru/users/creatman/)
- Telegram: [@Creatman_it](https://t.me/Creatman_it)
- Сайт: [creatman.site](https://creatman.site)
