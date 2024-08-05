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


######
КВЕСТ С ПОНЕДЕЛЬНИКА 5 АВГУСТА
Подключить n8n по api http-request к Dfir-Iris
https://docs.dfir-iris.org/operations/api/
1)поднимаем iris - helm, берем оттуда параметры Api, api-key
2)
3)


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
