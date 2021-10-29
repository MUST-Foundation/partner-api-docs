# Описание процесса Individual flow (obsolete RGS)

## Prescoring

## Scoring (scoringId)

```/api/user/scoring/{scoringId}```
Получить информацию о процессе

## Улучшения UX для flow
### Методы

```/api/user/scoring/{scoringId}/documents/types-required```

передаем инфу по процессу
получаем список доков для загрузки


```/api/user/blob```
Загрузить файлы (blob) на сервер > blobid

```/api/user/blob/classify```
классифицируем документы
blobid -> class

```/api/user/scoring/{scoringId}/documents```
Загрузить документы в группировке по запрошенным типам

```/api/user/scoring/{scoringId}/documents/validate```
Проверить валидность загруженных документов



### Загрузка контракта для его прогрузки в СК

```/api/user/scoring/{scoringId}/contract```

### Создания договора страхования на стороне СК

```/api/user/scoring/{scoringId}/agreement```

### Произвести оплату

    - Оплата картой

```/api/user/scoring/{scoringId}/payment-link``` получить ссылку на оплату


    <!-- - Оплата по счету 
      - Процеду подтверждения ПП -->

### Скачать полис

```/api/user/{scoringId}/policy```