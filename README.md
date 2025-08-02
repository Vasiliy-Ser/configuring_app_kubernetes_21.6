# Домашнее задание к занятию 21.6 «Настройка приложений и управление доступом в Kubernetes» - Падеев Василий  

### Примерное время выполнения задания

120 минут

### Цель задания

Научиться:
- Настраивать конфигурацию приложений с помощью **ConfigMaps** и **Secrets**
- Управлять доступом пользователей через **RBAC**

Это задание поможет вам освоить ключевые механизмы Kubernetes для работы с конфигурацией и безопасностью. Эти навыки необходимы для уверенного администрирования кластеров в реальных проектах. На практике навыки используются для:
- Хранения чувствительных данных (Secrets)
- Гибкого управления настройками приложений (ConfigMaps) 
- Контроля доступа пользователей и сервисов (RBAC)

------

## **Подготовка**
### **Чеклист готовности**
- Установлен Kubernetes (MicroK8S, Minikube или другой)
- Установлен `kubectl`
- Редактор для YAML-файлов (VS Code, Vim и др.)
- Утилита `openssl` для генерации сертификатов

------

### Инструменты, которые пригодятся для выполнения задания

1. [Инструкция](https://microk8s.io/docs/getting-started) по установке MicroK8S
2. [Инструкция](https://minikube.sigs.k8s.io/docs/start/) по установке Minikube
3. [Инструкция](https://kubernetes.io/docs/tasks/tools/) по установке kubectl
4. [Инструкция](https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools) по установке VS Code

### Дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/configuration/secret/) Secret.
2. [Описание](https://kubernetes.io/docs/concepts/configuration/configmap/) ConfigMap.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.
4. [Описание](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) RBAC.
5. [Пользователи и авторизация RBAC в Kubernetes](https://habr.com/ru/company/flant/blog/470503/).
6. [RBAC with Kubernetes in Minikube](https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b).

------

## **Задание 1: Работа с ConfigMaps**
### **Задача**
Развернуть приложение (nginx + multitool), решить проблему конфигурации через ConfigMap и подключить веб-страницу.

### **Шаги выполнения**
1. **Создать Deployment** с двумя контейнерами
   - `nginx`
   - `multitool`
3. **Подключить веб-страницу** через ConfigMap
4. **Проверить доступность**

### **Что сдать на проверку**
- Манифесты:
  - `deployment.yaml`
  - `configmap-web.yaml`
- Скриншот вывода `curl` или браузера


### Решение:

Создал [deployment](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/nginx-multitool_1.yaml) и [service](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/service-nginx.yaml), подключил веб-страницу через [ConfigMap](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/configmap.yaml).  
![answer1](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/1.png)  
Выполним проверку:  
![answer2](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/2.png)  


---
## **Задание 2: Настройка HTTPS с Secrets**  
### **Задача**  
Развернуть приложение с доступом по HTTPS, используя самоподписанный сертификат.

### **Шаги выполнения**  
1. **Сгенерировать SSL-сертификат**
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout tls.key -out tls.crt -subj "/CN=myapp.example.com"
```
2. **Создать Secret**
3. **Настроить Ingress**
4. **Проверить HTTPS-доступ**

### **Что сдать на проверку**  
- Манифесты:
  - `secret-tls.yaml`
  - `ingress-tls.yaml`
- Скриншот вывода `curl -k`


### Решение:

Использую [deployment](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/nginx-multitool_2.yaml) и [service](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/service-nginx_2.yaml) без configmap. Создаю сертификат и ключ. Создал Kubernetes Secret из сертификата и запустил ingress, а также необходимо добавить запись в hosts
![answer3](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/4.png)  
![answer4](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/5.png)  
На ВМ включаем ingress  
```
microk8s enable ingress

```
Проверяем доступность: 
![answer5](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/6.png)  


---
## **Задание 3: Настройка RBAC**  
### **Задача**  
Создать пользователя с ограниченными правами (только просмотр логов и описания подов).

### **Шаги выполнения**  
1. **Включите RBAC в microk8s**
```bash
microk8s enable rbac
```
2. **Создать SSL-сертификат для пользователя**
```bash
openssl genrsa -out developer.key 2048
openssl req -new -key developer.key -out developer.csr -subj "/CN={ИМЯ ПОЛЬЗОВАТЕЛЯ}"
openssl x509 -req -in developer.csr -CA {CA серт вашего кластера} -CAkey {CA ключ вашего кластера} -CAcreateserial -out developer.crt -days 365
```
3. **Создать Role (только просмотр логов и описания подов) и RoleBinding**
4. **Проверить доступ**

### **Что сдать на проверку**  
- Манифесты:
  - `role-pod-reader.yaml`
  - `rolebinding-developer.yaml`
- Команды генерации сертификатов
- Скриншот проверки прав (`kubectl get pods --as=developer`)


### Решение:

На ВМ включаем rbac  и создаем сертификат для полшьзователя developer  
![answer6](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/7.png)  
Перенесем созданные сертификаты на хостовую машину  
![answer7](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/8.png)  
Создал [Конфигурацию поьзователя developer](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/developer.config)  
![answer8](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/9.png)  
![answer9](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/10.png)  
Создал [Role](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/developer-role.yaml) в котором описал разрешенные команды и [Rolebinding](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/src/developer-rolebinding.yaml) который привязывает пользователя к Role в определённом namespace.  
![answer10](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/11.png)  
Посмотрим информацию о подах и логах:  
![answer11](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/12.png)  
![answer12](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/13.png)  
Попробуем удалить под  
![answer13](https://github.com/Vasiliy-Ser/configuring_app_kubernetes_21.6/blob/968acd28d769b87b4b2bd8e1dc2e045ef3789ea8/png/14.png)  
Операция запрещена, контроль доступа работает.  



