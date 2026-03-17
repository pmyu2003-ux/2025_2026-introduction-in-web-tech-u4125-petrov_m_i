# Отчёт по лабораторной работе №3

**Университет:** [ITMO University](https://itmo.ru)  
**Факультет:** [Факультет технологического менеджмента и инноваций](https://ftmi.itmo.ru)  
**Курс:** [Введение в веб технологии](https://itmo-ict-faculty.github.io/introduction-in-web-tech/)  
**Год:** 2025/2026  
**Группа:** U4125  
**Автор:** Петров Михаил Юрьевич  
**Лабораторная:** Lab3  
**Дата начала:** 14.03.2026  
**Дата защиты:** 17.03.2026  

## Цель работы
Научиться настраивать локальную систему мониторинга, собирать метрики с помощью Prometheus и создавать дашборды в Grafana для визуализации данных.

## Ход работы

1. **Подготовка конфигурации Prometheus**  
   - Создана папка `prometheus` и файл `prometheus.yml` со следующим содержимым:
   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']

     - job_name: 'node-exporter'
       static_configs:
         - targets: ['node-exporter:9100']
2. **Запуск Node Exporter**
   - Запущен контейнер Node Exporter в сети `monitoring`:
     ```bash
     docker run -d \
       --name node-exporter \
       --network monitoring \
       -p 9100:9100 \
       prom/node-exporter
Проверена доступность метрик по адресу http://localhost:9100/metrics.

3. **Запуск Prometheus**
   - Создан том `prometheus-data`.
   - Запущен контейнер Prometheus с монтированием конфигурации:
     ```bash
     docker run -d \
       --name prometheus \
       --network monitoring \
       -p 9090:9090 \
       -v prometheus-data:/prometheus \
       -v C:\Users\misha\devops-lab-petrov\prometheus:/etc/prometheus \
       prom/prometheus \
       --config.file=/etc/prometheus/prometheus.yml
Проверено состояние целей в веб-интерфейсе Prometheus (http://localhost:9090/targets) – обе цели UP.

4. **Запуск Grafana**
   - Создан том `grafana-data`.
   - Запущен контейнер Grafana:
     ```bash
     docker run -d \
       --name grafana \
       --network monitoring \
       -p 3000:3000 \
       -v grafana-data:/var/lib/grafana \
       -e "GF_SECURITY_ADMIN_PASSWORD=admin" \
       grafana/grafana
Интерфейс доступен по адресу http://localhost:3000 (логин/пароль: admin/admin).

5. **Настройка Grafana**
   - Добавлен источник данных Prometheus с URL `http://prometheus:9090`.
   - Создан дашборд с панелью для метрики `node_cpu_seconds_total`.
   - Сохранён дашборд с названием "System Monitoring".

6. **Проверка работоспособности**
   - Все три контейнера (`node-exporter`, `prometheus`, `grafana`) запущены.
   - Prometheus успешно собирает метрики с Node Exporter.
   - В Grafana отображаются графики.

## Выводы
В ходе работы была развёрнута система мониторинга на основе Docker. Настроен сбор метрик хоста с помощью Node Exporter, их хранение в Prometheus и визуализация в Grafana. Полученные навыки позволяют мониторить состояние серверов и приложений.
