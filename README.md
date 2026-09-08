# Carxtest-job

### Тестовое задание на позицию DevOps-Инженера

```
carxtest-job/
├── ansible/           # плейбук, роли, инвентарь
├── microservices/
│   ├── webapp-helloworld/
│   └── webapp-proxy/
├── k8s/manifests/
└── .github/workflows/
```

## Ansible

### [Ansible directory](ansible/)

Плейбук лежит по пути `ansible/playbook.yml`

В данном плейбуке подключены роли, разделенные на логические этапы
К каждой роли приложен README-файл

Файл инвентаря лежит по пути `ansible/inventory/dev/hosts.yaml`

Пароль для создания пользователя через роль `useradd` заранее захэширован и лежит в зашифрованном vault, предусмотрен запуск через CI

Для локального запуска пароль от vault.yml направил через почту в ответном письме

### Роли
Для каждой роли собраны **molecule** тесты под **rocky9** и **ubuntu22** образы через **docker**, все роли могут быть использованы на *Ubuntu* и *RHEL-like* системах


## Docker

### [Webapp](microservices/webapp-helloworld/)
**Nodejs** приложение, обернутое в **Docker**

### [Webapp-proxy](microservices/webapp-proxy/)
**nginx**, слегка модифицирован конфиг, добавлен healthcheck endpoint `/healthz`

Также собран простенький **CI** для ручного запуска сборки и пуша в `ghcr.io` *(v1.0.1 стабильная для обоих образов)*

Можно самому собрать образы и закинуть в локальный registry, либо же использовать готовые артефакты из репозитория

## K8s

### [Манифесты](k8s/manifests/)
Общий неймспейс **webapp-ns** для двух сервисов.

**webapp** запускается в 3 реплики, приложение просто отдает Hello World!

**webapp-proxy** запускается в одном экземпляре.

**webapp-proxy** сервис с типом NodePort *(в манифесте порт зафиксирован: 30792)*, сам он принимает трафик и отсылает его на сервис **webapp-svc**

### Liveness и Readiness пробы

Для **webapp-proxy** заданы liveness/readiness пробы через `/healthz`.

Для **webapp** через корневой `/`.

### Деплой

Для локального запуска использовался minikube:
```
minikube start
kubectl apply -f k8s/manifests/namespace.yaml
kubectl apply -R -f k8s/manifests/
curl $(minikube ip):30792
```
Схема деплоя в любой другой кластер:
```
kubectl apply -f k8s/manifests/namespace.yaml
kubectl apply -R -f k8s/manifests/
```

В продакшене с подобным приложением я бы еще сделал ConfigMap, а еще лучше полноценный Helm чарт, но на данный момент я считаю это overkill

Requests/limits на контейнерах сознательно не выставлены.
В проде я бы прогнал нагрузочный тест и выставил значения, основываясь на `kubectl top`.