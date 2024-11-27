# Guide for installing kube-prometheus-stack

## Documentation
# Ссылка на документацию для kube-prometheus-stack
https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

## Install prometheus-community:
# Добавляем репозиторий Helm для prometheus-community
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Обновляем локальный кеш репозиториев Helm
helm repo update

# Устанавливаем kube-prometheus-stack с именем релиза 'monitoring' 
# в namespace по умолчанию (можно указать другой namespace через флаг -n)
helm install monitoring prometheus-community/kube-prometheus-stack

# 'monitoring' -> имя релиза Prometheus, используемое для обращения к нему 

## Set service
# Развертывание приложения
kubectl apply -f k8s/ititanic-deployment.yml

# Создание сервиса Kubernetes для приложения
kubectl apply -f k8s/titanic-service.yml

# Создание сервиса Kubernetes для доступа к Grafana
kubectl apply -f k8s/monitoring-titanic.yml

# Установка прав доступа (RBAC) для Prometheus
kubectl apply -f k8s/prom_rbac.yml


## Forward ports
# Проброс порта 9090 из пода Prometheus на локальный порт 9090
kubectl port-forward svc/prometheus-operated 9090:9090 -n default
# Проверяем доступность по адресу: http://127.0.0.1:9090/

# Проброс порта 80 из пода Grafana на локальный порт 8082
kubectl port-forward svc/monitoring-grafana 8082:80 -n default
# Проверяем доступность по адресу: http://127.0.0.1:8082/

## Forward service ports
# Получаем список подов для нахождения имени пода приложения
kubectl get pods

# Пробрасываем порт 8000 из контейнера приложения на локальный порт 8000
# Замените <titanic pods name> на имя пода, полученное на предыдущем шаге
kubectl port-forward <titanic pods name> 8000:8000 -n default
# Проверяем доступность приложения по адресу: http://127.0.0.1:8000/
# Проверяем доступность метрик по адресу: http://127.0.0.1:8000/metrics 

## Send request
# Запускаем скрипт для отправки запросов к приложению
python service/send_request.py