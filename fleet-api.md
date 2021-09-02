# Авторизация

Для продолжения процесса необходимо сделать авторизацию по номеру телефона

Необходимо инициировать вызов ```POST``` ```/api/auth/otp``` с передачей номера телефона Страхователя, что приведет к отправке кода подтверждения СМС. API возвращает поле ```otpState```

Кода пользователь готов вводит СМС код, необходимо инициировать запрос ```POST``` ```/api/auth/login/jwt``` с передачей номера телефона Страхователя, ```otpState```, ```otp``` = СМС код

В ответе приходит в поле ```token``` - токен Bearer авторизации Страхователя в API MUST. 

Далее его необходимо использовать при каждом обращении в API MUST, передавая в заголовке каждого запроса: ```Authorization: Bearer token```. Токен действует 180 суток с момента выпуска. При истечении срока действия токена необходимо его перевыпустить пройдя процедуру заново.

# Заполнение профиля учетной записи 

```
POST
​/api​/profile
```

```json
{
  "firstName": "string",
  "patronymic": "string",
  "lastName": "string",
  "position": "string",
  "email": "string",
  "bornOn": "2021-09-02T13:47:53.439Z",
  "gender": "male",
 }
```

# Передача данных для учетной записи FleetOwner (Директор головной компании/холдинга - Главное ответвенное лицо)

```
POST
​/api​/profile​/external-user
```

```json
{
  "generalCompanyId": "ИНН головной компании/холдинга",
  "externalUserId": "Внешний ID пользователя в учетной системе (произвольная значимая строка)",
  "generalCompanyRole": "owner"
}
```

# Передача данных для учетной записи FleetManager (Ответственное лицо, уполномоченное подписывать Заявление на страхование ОСАГО и выпускать полисы ОСАГО)

```
POST
​/api​/profile​/external-user
```

```json
{
  "generalCompanyId": "ИНН головной компании/холдинга",
  "externalUserId": "Внешний ID пользователя в учетной системе (произвольная значимая строка)",
  "generalCompanyRole": "manager"
}
```

# Для предоставления полномочий от FleetOwner к FleetManager необходимо подписать ПЭП

## (max: 5 FleetManeger, если будет больше, то будет ошибка запроса)

1.

```
POST
​/api​/profile​/sign​/insurance-permission-grant
```

В запросе указывается полный список телефонных номеров FleetManager-ов

```json
{
  "contextDetails": [
    {
      "assignee": "+7 (111) 111-11-11"
    },
    {
      "assignee": "+7 (222) 222-22-22"
    }
  ]
}
```

В ответе получаем ```otpState``` и используем во втором запросе

2. 

```
POST
​/api​/profile​/sign​/insurance-permission-grant​/confirm
```

В запрос дублируем значение поля ```contextDetails``` из предыдущего запроса

```json
{
  "otpState": 0,
  "otp": "код из СМС",
  "contextDetails": [
    {
      "assignee": "string"
    }
  ]
}
```

# Инициация группового расчета

```
POST
​/api​/debug​/fleet​​/init
```

```json
{
  "insurerInn": "string",
  "insurerKpp": "string",
  "rowsData": [
    "string"
  ]
}
```

В ответе получаем ```fleetCalculationId```

# Получение информации по групповому расчету

```
GET
​/api​/debug​/fleet​/{fleetCalculationId}
```

Если есть значение info.completedAt, значит расчет группы завершен 

# Получение структурированной информации о Cover offer (TBD)

## TBD определить структуру сообщения в ответе

```
GET
​/api​/debug​/fleet​/{fleetCalculationId}​/cover-offer
```

Позволяет получить в json данные о Cover offer

# Скачивание файла Cover offer

```
GET
​/api​/debug​/fleet​/{fleetCalculationId}​/file​/cover-offer
```

Позволяет скачать файл Cover offer в формате Excel (.xlsx)

# Создание аддендума (группового оформления полисов из расчета - на подмножество ТС)

```
POST
​/api​/debug​/fleet​/{fleetCalculationId}​/addendum
```

```json
[
    {
        "vin": "string",
        "technicalInspectionNumber": "string",
        "technicalInspectionStartOn": "Date",
        "technicalInspectionEnOn": "Date",
        "ownerInn": "string",
        "ownerKpp": "string",
        "policyStartOn": "Date"
    },
    {
        "vin": "string",
        "technicalInspectionNumber": "string",
        "technicalInspectionStartOn": "Date",
        "technicalInspectionEnOn": "Date",
        "ownerInn": "string",
        "ownerKpp": "string",
        "policyStartOn": "Date"
    }
]
```

# Запуск создания договоров в СК по созданному аддендуму 

```
POST
​/api​/debug​/fleet​/addendum​/{addendumId}​/submit
```

# Подписание Заявление о страховании для аддендума с помощью ПЭП (TBD)

1. ! В поле ```items``` должен быть полный перечень всех ТС внутри аддендума

```
POST
/api/profile/sign/addendum-osago-request
```

```json
{
  "contextDetails": {
    "items": [
        {
            "scoringId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
            "vin": "string",
            "plates": "string",
            "hasTrailer": true,
            "driverCount": 0,
            "isRestricted": true,
            "policyStartOn": "2021-09-02T14:00:46.089Z",
            "writtenPremium": 0,
            "isHighRiskCargo": true,
            "isStandardCargo": true,
            "insuranceCompany": "insurance_company",
            "owner": {
                "inn": "string",
                "kpp": "string"
            },
            "insurer": {
                "inn": "string",
                "kpp": "string"
            },
            "technicalInspection": {
                "series": "string",
                "number": "string",
                "startOn": "2021-09-02T14:00:46.089Z",
                "endOn": "2021-09-02T14:00:46.089Z"
            }
        }
    ]
  }
}
```

В ответе получаем ```otpState``` и используем во втором запросе

2. 

```
POST
​/api​/profile​/sign​/addendum-osago-request/confirm
```

В запрос дублируем значение поля ```contextDetails``` из предыдущего запроса

```json
{
  "otpState": 0,
  "otp": "код из СМС",
  "contextDetails": "..."
}
```

# Запуск формирования счета на оплату по аддендуму

```
POST
​/api​/debug​/fleet​/addendum​/{addendumId}​/payment-invoice
```

# Подтверждение оплаты и инициация выпуска полисов по аддендуму

```
POST
​/api​/debug​/fleet​/addendum​/{addendumId}​/issue
```

# Прочее. Получение статусов аддендумов

Каждый аддендум имеет id (addendumId), групповой расчет может иметь несколько аддендумов. Информация о каждом из них доступна в поле ```addendums``` запроса ```GET ​/api​/debug​/fleet​/{fleetCalculationId}```

Аддендум можем имемть один из нескольких статусов в поле ```status```: ```new``` (создан, но не передан запрос на создание договора в СК), ```submitted``` (договоры отправлены на создание в СК), ```agreementsCreated``` (договоры в СК успешно созданы), ```issuing``` (оплата подтверждена, полисы выпускаются), ```issued``` (полисы выпущены)

Счет на оплату по аддендуму можно генерировать когда созданы договоры в СК, т.е. ```staus === agreementsCreated```

Подтвердить оплату так же можно только если статус аддендума в состоянии ```staus === agreementsCreated```

Скачать счет на оплату можно по blobId, который доступен в поле ```paymentInvoiceBlobId```

Скачать выпущенный полис можно по blobId, который доступен в поле ```vehicles[].policyBlobId```

По blobId можно скачать файл, передав его в метод ```GET /api​/blob​/{blobId}```