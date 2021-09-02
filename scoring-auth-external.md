# Авторизация и предоставление прав учетным записям

## Авторизация

Для продолжения процесса необходимо сделать авторизацию по номеру телефона

Необходимо инициировать вызов ```POST``` ```/api/auth/otp``` с передачей номера телефона Страхователя, что приведет к отправке кода подтверждения СМС. API возвращает поле ```otpState```

Кода пользователь готов вводит СМС код, необходимо инициировать запрос ```POST``` ```/api/auth/login/jwt``` с передачей номера телефона Страхователя, ```otpState```, ```otp``` = СМС код

В ответе приходит в поле ```token``` - токен Bearer авторизации Страхователя в API MUST. 

Далее его необходимо использовать при каждом обращении в API MUST, передавая в заголовке каждого запроса: ```Authorization: Bearer token```. Токен действует 180 суток с момента выпуска. При истечении срока действия токена необходимо его перевыпустить пройдя процедуру заново.

## Заполнение профиля учетной записи 

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

## Передача данных для учетной записи FleetOwner (Директор головной компании/холдинга - Главное ответвенное лицо)

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

## Передача данных для учетной записи FleetManager (Ответственное лицо, уполномоченное подписывать Заявление на страхование ОСАГО и выпускать полисы ОСАГО)

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

## Для предоставления полномочий от FleetOwner к FleetManager необходимо подписать ПЭП

### (max: 5 FleetManeger, если будет больше, то будет ошибка запроса)

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

## Для подписи Заявления о страховании FleetManager-ом (вместо вызова POST /api​/profile​/sign с ```context === sl_osago_le```)

1. 

```
POST
​/api​/profile​/sign​/osago-request
```

```json
{
  "contextDetails": {
    "scoringId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "vin": "string",
    "plates": "string",
    "hasTrailer": true,
    "driverCount": 0,
    "isRestricted": true,
    "policyStartOn": "2021-09-02T14:42:00.256Z",
    "writtenPremium": 0,
    "isHighRiskCargo": true,
    "isStandardCargo": true,
    "insuranceCompany": "ingostrah",
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
      "startOn": "2021-09-02T14:42:00.256Z",
      "endOn": "2021-09-02T14:42:00.256Z"
    }
  }
}
```

В ответе получаем ```otpState``` и используем во втором запросе

2. 

```
POST
​/api​/profile​/sign​/osago-request/confirm
```

В запрос дублируем значение поля ```contextDetails``` из предыдущего запроса

```json
{
  "otpState": 0,
  "otp": "код из СМС",
  "contextDetails": "...."
}
```
