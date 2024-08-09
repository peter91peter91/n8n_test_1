1)СТАВИМ SHUFFLE
git clone https://github.com/shuffle/shuffle

2)для работы с образами шаффл
cd /HELM_CHARTS_KUBER_sibintek/shuffle_test_1/shuffle/functions/kubernetes;
chmod +x generate_certs.sh
./setup_registry.sh
127.0.0.1  поставил.(default is 192.168.1.59)
-----
стартуем реестр
docker run -d -p 5000:5000 --restart=always --name shuffle-local-registry   -v /home/peter1/HELM_CHARTS_KUBER_sibintek/shuffle_test_1/shuffle/functions/kubernetes/certs:/certs   -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/reg.crt   -e REGISTRY_HTTP_TLS_KEY=/certs/reg.key   registry:2
-----
 для настройки взаимодействия containerd с частным контейнерным регистром, который находится по адресу<REGISTRY_NODE_IP:PORT>.
sudo nano  /etc/containerd/config.toml
[plugins."io.containerd.grpc.v1.cri".registry.mirrors."<REGISTRY_NODE_IP:PORT>"]
  endpoint = ["https://<REGISTRY_NODE_IP:PORT>"]
[plugins."io.containerd.grpc.v1.cri".registry.configs."<REGISTRY_NODE_IP:PORT>".tls]
  insecure_skip_verify = true
-----
для кубера
kubectl apply -f /shuffle/functions/kubernetes/all-in-one.yaml -n moiseev-namespace-shuffle;

в хелме ставим nodeport        nodePort: 30001   nodePort: 30443  
cd ~/HELM_CHARTS_KUBER_sibintek/shuffle_original/Shuffle/functions/extensions/k8s/shuffle;
helm upgrade -i shuffle  . -f "values.yaml" -n moiseev-namespace-shuffle;

удаляем  ресурсы
helm uninstall shuffle -n moiseev-namespace-shuffle;  






------------------------------------
1)здесь про перенос ДОКЕР  образов на машину без интернета
https://shuffler.io/docs/configuration#Kubernetes

2)открыть проект iris и так же как там добавить заландо-БД !!!

3)попробовать убрать докер сокет? и/или перевести всё на локальные образа

------
1) написать конкретные проблемы которые не позволяют shuffle развернуть в кубере ЕЦПК на текущий момент и затестить аналоги
2) поднять АНАЛОГИ хелмом - смотреть логи, вебку


-----------------------------------------------
-----------------------------------------------
-----------------------------------------------
ОТПРАВИТЬ КОМАНДЕ АРГУМЕНТАЦИЮ - СИБИНТЕК ПОЧТА
Аргументация, почему сейчас нельзя перевести SHUFFLE с докер-сокета на кубер:

Леониду, всем нам. 


######
Аргументация необходимости замены Shuffle на другой аналогичный инструмент.

1. Перевод Shuffle с платформы докер на k8s.
- В текущей версии Shuffle (v1.4.2) официального репозитория github и в проекте docker и в проекте helm используется Docker-сокет для управления контейнерами(предоставляет API-интерфейс, Docker-сокет монтируется кубером в под (/var/run/docker.sock)). 
- SHUFFLE при помощи docker-сокета запускает контейнеры в ответ на события(инциденты/изменения), такие как входящие веб-хуки(Контейнер с веб-сервером и обработчиком веб-хуков); триггеры безопасности(Контейнер для анализа инцидентов безопасности); Контейнер для выполнения интеграции с внешними системами; Контейнер для запуска скриптов или утилит.
- Каждый рабочий процесс запускается через докер-сокет в своем контейнере, что предотвращает конфликты между процессами и обеспечивает безопасность выполнения. 

2. Поддержка и обновление версии Shuffle
Перевод c docker-api на k8s-api(через CRI-сервисный аккаунт и роли для доступа к ресурсам через этот аккаунт) повлечёт: полную переработку архитектуры системы проекта, изменение кода программного обеспечения.
На текущий момент перевод системы на k8s разработчиком не предусмотрен.
######

-----------------------------------------------
-----------------------------------------------
-----------------------------------------------
АНАЛОГИ  SHUFFLE
Shuffle automation platform alternatives for security operations
----
1) (+) n8n - поддерживается-коммиты свежие, бесплатная.   https://github.com/8gears/n8n-helm-chart
(-) МИНУС В ТОМ ЧТО keyclock и роли ТОЛЬКО В ПЛАТНОЙ ВЕРСИИ enterprise
- попытаться внедрить ПРОСЛОЙКУ - модуль keyclock GATE KEEPER в shuffle, iris  (уже пробовали, непросто - проблемы кейклока с портами и прокси) - не совсем подходит- этот модуль для тех приложений где вообще нет никакой формы аутентификации.
- СМОТРЕТЬ что можно взять с вебхука- какие данные
- REST API - ВМЕСТО APPLICATION использовать, так как n8n не имеет application безопасности(iris)
- аутентификация будет через коннекторы в СКДПП(через них в веб консоль попадать будем для управления iris и другим ПО)
"""
установка в default namespace из коробки 
helm install my-n8n oci://8gears.container-registry.com/library/n8n --version 0.23.1
"""
2) - Argo Workflows + Argo Events:
3) - -!!-  Apache NiFi  -нет поддержки с февраля (https://github.com/cetic/helm-nifi/blob/master/README.md)
4) (-) Tines - поддерживается, платная. - (Community Edition - Always free) -3 builders -1 team -Unlimited viewers -3 stories -5000 daily events
5) (-) TheHive - специализация на безопасности, но 2-3 года назад последний коммит. https://github.com/jaredjennings/helm-thehive
6) KnowBe4 PhishER/PhishER Plus.
7) Microsoft Sentinel.
8) Sumo Logic.
9) Google Security Operations.
10) SIRP.
11) Intezer.



1. StackStorm
StackStorm — это платформа автоматизации и управления событиями, которая позволяет интегрировать и автоматизировать множество различных систем и инструментов. Она поддерживает множество интеграций и позволяет создавать сложные рабочие процессы для DevSecOps.

Развертывание StackStorm с помощью Helm:
helm repo add stackstorm https://stackstorm.github.io/helm-charts/
helm repo update
helm install st2 stackstorm/stackstorm

2. Apache NiFi
Apache NiFi — это система управления потоком данных, которая позволяет автоматизировать передачу и трансформацию данных между различными системами. Она поддерживает множество интеграций и позволяет создавать сложные рабочие процессы.

Развертывание Apache NiFi с помощью Helm:
helm repo add cetic https://cetic.github.io/helm-nifi/
helm repo update
helm install nifi cetic/nifi

3. Airflow
Apache Airflow — это платформа для программного управления рабочими процессами, часто используемая для автоматизации различных задач, включая DevSecOps. Airflow позволяет легко создавать, планировать и контролировать рабочие процессы.

Развертывание Apache Airflow с помощью Helm:
helm repo add apache-airflow https://airflow.apache.org
helm repo update
helm install airflow apache-airflow/airflow

4. Argo Workflows
Argo Workflows — это платформа для создания и управления контейнеризированными рабочими процессами на Kubernetes. Она позволяет создавать сложные рабочие процессы и легко интегрируется с различными инструментами DevSecOps.

Развертывание Argo Workflows с помощью Helm:
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argo argo/argo-workflows



//////////////////////////////////////////////////////
//////////////////////////////////////////////////////
//////////////////////////////////////////////////////
######
КВЕСТ С ПОНЕДЕЛЬНИКА 5 АВГУСТА
Подключить n8n по api http-request к Dfir-Iris
https://docs.dfir-iris.org/operations/api/
1)поднимаем iris - helm, берем оттуда параметры Api, api-key
поднимаем ИРИС  ---и--- поднимаем базу BITNAMI
поднимаем n8n  (пока что всё в одном будем неймспейсе... kubectl create namespace moiseev-namespace-n8n;)
    cd ~/HELM_CHARTS_KUBER_sibintek/iris_test_3/iris_3;
    helm upgrade -i iris  ./chart -f "custom_values.yaml" -n moiseev-namespace-iris;

    cd ~/HELM_CHARTS_KUBER_sibintek/iris_test_2_external_postgres/postgresql_test_1;
  helm upgrade -i  postgresql . -f "values.yaml" -n moiseev-namespace-iris;

    cd ~/HELM_CHARTS_KUBER_sibintek/n8n_test_1/n8n_test_1/charts/n8n;
        helm dependency build;
    helm upgrade -i n8n . -f values.yaml -n moiseev-namespace-iris

------------------  
------------------  
------------------  
в n8n 
"name": "Authorization",
"value": "Bearer NLQ3rN6JwdguxjHPWHtYIzo0difV8_IYFLV6hgOzoOSntyo25904Z0HgdS8keIk4uJ3Dc3cltwPH_76cPZZ3Og"

тело запроса 
{
  "cid": 2,
  "task_assignees_id": [1],
  "task_description": "это описание",
  "task_status_id": 1,
  "task_tags": "это тэг",
  "task_title":  "это тайтл",
  "custom_attributes": {}
}

!!!---ВМЕСТО ПОЧТЫ ПОКА ЧТО СДЕЛАТЬ КУДА ТО ЕЩЕ. НЕ ЛОГИНИТСЯ.
SMTP account
peter91peter91peter91@gmail.com
smtp.gmail.com
!!! gmail Пароль приложения создан Пароль приложения для вашего устройства
czhf sftt bubo evnc


описание... {{ $json.data.task_description }} 
сообщение... {{ $json.message }}
task_uuid далее... {{ $json.data.task_uuid }}

удаляем
    helm uninstall postgresql -n moiseev-namespace-iris;
    helm uninstall iris -n moiseev-namespace-iris;
    helm uninstall n8n -n moiseev-namespace-iris;    
------------------  
------------------  
------------------  
2)настроить n8n для выполнения HTTP-запросов к IRIS API

-ПИСАТЬ РЕГУЛЯРКИ ВАЗУХ - ДЕКОДЕРЫ
-ЕСТЬ КАСТОМНЫЕ RULES  ДЛЯ АЙРИСА
-ОТПРАВЛЯТЬ КУРЛ С ПРИМЕРОМ ВАЗУХА В N8N - ДАЛЕЕ ПОЛЯ ПАРСИТЬ НУЖНЫЕ- И ПЕРЕНОСИТЬ ИХ В ЗАПРОС СОЗДАНИЯ ЗАДАЧИ
-ДАЛЕЕ передаётся в БЛОК ОТПРАВКИ ОТБИВКИ(ПИСЬМА)

-ЕЩЕ поднять и ПРОБОВАТЬ ZAMMAD



//////////////////////////////////////////////////////
//////////////////////////////////////////////////////
 буду вместо shuffler внедрять n8n. n8n не поддерживает инструменты безопасности такие как iris - в n8n нет application iris как в шаффл. и мне нужно будет использовать api для доступа к iris. Как мне использовать api для доступа из n8n к iris? 

Чтобы интегрировать n8n с IRIS через API, вам нужно настроить n8n для выполнения HTTP-запросов к IRIS API. В n8n есть нода HTTP Request, которая позволяет отправлять запросы к любому RESTful API. Вот как это можно сделать:
Шаг 1: Получите данные для подключения к IRIS API

    URL API: Определите URL вашего IRIS API.
    Авторизация: Узнайте, какой метод авторизации используется (например, Basic Auth, Bearer Token, API Key и т.д.).
    Документация: Ознакомьтесь с документацией IRIS API, чтобы знать, какие конечные точки вам нужно использовать и какие параметры требуется передать.

Шаг 2: Настройка ноды HTTP Request в n8n

    Откройте n8n и создайте новый рабочий процесс.
    Добавьте ноду HTTP Request.
    Настройте ноду HTTP Request для подключения к IRIS API.

Пример настройки ноды HTTP Request:

    HTTP Method: Укажите метод HTTP-запроса, который требуется для обращения к IRIS API (GET, POST, PUT, DELETE и т.д.).
    URL: Введите URL вашего IRIS API. Например: https://api.iris.example.com/v1/resource.
    Authentication: Укажите метод авторизации, если требуется:
        No Authentication: Если не требуется авторизация.
        Basic Auth: Введите логин и пароль.
        OAuth2: Настройте OAuth2 параметры, если это нужно.
        Header Auth: Введите необходимые заголовки, например, Authorization: Bearer YOUR_TOKEN.
    Query Parameters: Если ваш запрос требует дополнительных параметров, укажите их в разделе "Query Parameters".
    Body Parameters: Если вы отправляете POST или PUT запрос, укажите тело запроса в разделе "Body Parameters". В зависимости от типа данных это может быть JSON, Form Data и т.д.

Пример конфигурации ноды HTTP Request

Предположим, вам нужно выполнить GET-запрос к IRIS API, используя Bearer Token для авторизации:

    HTTP Method: GET
    URL: https://api.iris.example.com/v1/resource
    Authentication:
        Type: Header Auth
        Name: Authorization
        Value: Bearer YOUR_TOKEN

Шаг 3: Проверка и обработка ответа

После выполнения запроса, вы получите ответ от IRIS API, который можно обработать в n8n. Вы можете использовать другие ноды n8n для работы с ответом, например, для фильтрации данных, их преобразования или отправки на другие сервисы.
Пример работы с ответом

    Добавьте ноду Set, чтобы сохранить или преобразовать данные ответа.
    Используйте ноду Function, чтобы обработать ответ программно.
    Используйте ноду HTTP Request для отправки данных в другой сервис, если это необходимо.

Пример кода для отправки POST-запроса с телом в формате JSON

json

{
  "method": "POST",
  "url": "https://api.iris.example.com/v1/resource",
  "headers": {
    "Authorization": "Bearer YOUR_TOKEN",
    "Content-Type": "application/json"
  },
  "body": {
    "key1": "value1",
    "key2": "value2"
  },
  "json": true
}

В данном примере, вы отправляете JSON-данные в теле POST-запроса и используете Bearer Token для авторизации.
Заключение

Интеграция n8n с IRIS API через HTTP Request достаточно гибкая и позволяет выполнять различные операции. Важно убедиться, что вы правильно настроили авторизацию и передаете все необходимые параметры. Если IRIS API требует особого формата запросов или ответов, обязательно изучите документацию API, чтобы корректно настроить интеграцию.


/////////////////////////////////////

Шаги для настройки ноды в n8n:

    Добавьте ноду HTTP Request в ваш рабочий процесс.

    Настройте ноду следующим образом:
        URL: http://iriswebapp-app:8000/case/tasks/add
        Метод: POST
        Заголовки:
            Authorization: Bearer NLQ3rN6JwdguxjHPWHtYIzo0difV8_IYFLV6hgOzoOSntyo25904Z0HgdS8keIk4uJ3Dc3cltwPH_76cPZZ3Og
            Content-Type: application/json
        Тело запроса:
            task_assignees_id: [1] (замените на реальные IDs)
            task_description: ""
            task_status_id: 1 (замените на реальный статус ID)
            task_tags: ""
            task_title: "dummy title"
            custom_attributes: {}

    Сохраните и выполните ваш рабочий процесс в n8n, чтобы отправить запрос на создание задачи в iris.

Теперь ваш n8n должен корректно отправлять запрос на создание задачи в приложении iris.



-----------------------------
-----------------------------
-----------------------------
что сделано

1) рассмотрел аналоги компоненты для замены shuffle, подобран инструмент n8n 
2) протестирована работа n8n в связке с iris 
-в n8n данные-JSON с примером из "события Wazuh" записываю в статический узел формирования http-request
-там выбираю нужные поля, формирую HTTP-запрос по IRIS API для создания задачи
-далее необходимые поля передаю в узел формирования отбивки-письма на почту(задача пересылается в тестовую почту peter91peter91peter91@gmail.com

3) в iris поступает  POST http-request и создается TASK. (доступ к iris планируется через аутентификацию через коннекторы в СКДПП)