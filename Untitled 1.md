```
gantt
dateFormat  YYYY-MM-DD
title План первой половины 2026 года

Технические долги: done, tech_debt, 2026-01-01, 2026-01-25
Keycloak: active, keycloak, after tech_debt, 2026-01-30
Релиз для лабы 12: crit, release_lab12, after tech_debt, 2026-02-13
Инфа (КНПЗ, Новойл): active, tech_info, after release_lab12, 2026-02-27
Сервис автосбора (проектирование): sensor_service_proj, after release_lab12, 2026-03-06
Сервис автосбора (разработка): sensor_service_dev, after release_lab12, 2026-04-01
Парсинг врем.рядов: parsing, after sensor_service_dev, 2026-04-13
Автоматический сбор данных (LIMS, АСУТП, УЗТ): auto_sensor, after release_lab12, 2026-04-23
Релиз на заводы (КНПЗ, Новойл): crit, final, after auto_sensor, 2026-05-15
```
