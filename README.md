# Сценарий проверки всего стека **Clickhouse** + **Vector** + **Lighthuse**

## Зависимости

- Сторонняя роль установки и настройки [Clickhouse](https://github.com/AlexeySetevoi/ansible-clickhouse) от **AlexeySetevoi**
- Собственная роль установки и настройки [Vector](https://github.com/ArtemShtepa/vector-role)
- Собственная роль установки и настройки [Lighthouse](https://github.com/ArtemShtepa/lighthouse-role)

> Также в файле зависимостей для информации указана коллекция, содержащая собственные роли - коллекции автоматически не устанавливаются

## Описания контейнеров

Для тестирования используется драйвер **Docker**.

Роли поддерживают **CentOS 7**, **CentOS 8**, **Ubuntu**, но в данной задаче используется только **CentOS 7**

Создаются три контейнера:
- `clickhouse` с открытыми портами **9000** и **8123** для возможности ручной проверки через GUI
- `vector`
- `lighthouse` с открытым портом **8080** для возможности ручного контроля

## Предназначение файлов

- `cleanup.yml` - **playbook Molecule** для этапа **Cleanup** - предназначен для чистки сервисов от следов проверок, а именно обнуляется таблица в **Clickhouse** и удаляются контролируемые **Vector** файлы
- `converge` - **playbook Molecule** для этапа **Converge** - подготовка инфраструктуры сервисов: устанавливает скрипт эмулятор SystemD во все контейнеры и применяет все роли к соответствующим контейнерам
- `molecule` - конфигурационный файл **Molecule**
- `requirements` - файл зависимостей **Molecule** для **Ansible-galaxy**
- `verify` - основной **playbook Molecule** для этапа **verify** - верифицирует установку **Vector** и **Lighthouse**, создаёт контролируемые **Vector** файлы и наполняет их содержимым
- `verify_clickhouse` - **playbook** запрашивает данные из **Clickhouse** и сравнивает их с эталоном. Реализован отдельно с поддержкой нескольких итераций, так как из-за особенностей реализации **Vector** не все данные сразу отправляются в **Clickhouse**.

## Примечания

Для успешного функционирования всего стека ролям сервисов **Vector** и **Lighthouse** нужно передать IP адрес сервиса **Clickhouse** внутри сети контейнеров **Docker**.
По умолчанию **Molecule** создаёт контейнеры в сети в драйвером **bridge**. Определение IP адреса контейнера **Clickhouse** и передача его другим осуществляется в **play** с именем `Detect Clickhouse IP`.
Для ручного контроля используемые порты проброшены в основную систему и доступны по локальному IP адресу `127.0.0.1`

## Автоматизация Jenkins

1. Файл `DeclarativeJenkinsfile` для **Declarative pipeline**
1. Файл `Jenkinsfile` для **Multibranch pipeline** (репозиторий содержит две ветки: `main` и `second` - с разными версиями `Jenkinsfile` и `converge.yml`)
1. Файл `ScriptedJenkinsfile` для простого **Scripted pipeline** тестировании ролей в **Molecule**
1. Файл `ScriptedCVLstackJenkinsfile` для **Scripted pipeline** автоматизации создания машинок в **Яндекс.Облаке** и разворачивании на них всего **CVL** стека
1. Файл `playbooks/demo.yml` - **Ansible playbook** для разворачивания **CVL** стека. Требует **inventory** файл. Используется в скрипте автоматизации `ScriptedCVLstackJenkinsfile`
