# Получение информации о ТС

## Получение токена

Токен выдаётся вам нашей кампанией. Для получения токена свяжитесь с нами.

## Получение информации о ТС:
```
POST
/api/ExportData/GetVehicleInfo
```

```json
{
    "vin":"VLUR4X20009034464",
    "IncludeInResponse":[ "M01Specifications","M02Owners","M03OwnHistory","M04Dk","M05Insurance","M06AccidentRate"],
    "token":"токен полученый ранее"
}
```
или
```json
{
    "plates":"К860ММ21",
    "IncludeInResponse":[ "M01Specifications","M02Owners","M03OwnHistory","M04Dk","M05Insurance","M06AccidentRate"],
    "token":"токен полученый ранее"
}
```

Response:
```json
{
    "vehicle": {
        "vin": "VLUR4X20009034464",
        "engineNumber": "5738777",
        "make": "SCANIA",
        "model": "R",
        "vehicleBodyTypeGroup": "Седельный тягач",
        "weightGross": 18500,
        "yearManufacturedOn": 1998,
        "color": "WHITE",
        "engineType": "Diesel",
        "euroClass": "Euro 2",
        "engineVolumeCm": 11700,
        "enginePowerHp": 400
    },
    "currentOwner": {
        "ownerType": "Individual",
        "vehicleRegistrationRegionCode": "23",
        "vehicleRegistrationRegionTitle": "Краснодарский Край",
        "isLeasingCompany": false,
        "stsNumber": "9909382777"
    },
    "ownersHistory": [
        {
            "from": "2005-05-20T00:00:00Z",
            "to": "2005-05-20T00:00:00Z",
            "ownertype": "Individual"
        },
        {
            "from": "2005-05-20T00:00:00Z",
            "to": "2005-06-22T00:00:00Z",
            "ownertype": "Individual",
            "lastOperationCode": "15",
            "lastOperationType": "Первичная регистрация"
        },
        {
            "from": "2005-06-22T00:00:00Z",
            "to": "2013-10-11T00:00:00Z",
            "ownertype": "Individual",
            "lastOperationCode": "62",
            "lastOperationType": "Прекращение регистрации (учета)"
        },
        {
            "from": "2013-10-16T00:00:00Z",
            "to": "2019-02-06T00:00:00Z",
            "ownertype": "Individual",
            "lastOperationCode": "94",
            "lastOperationType": "Вторичная регистрация со сменой собственника"
        },
        {
            "from": "2019-02-06T00:00:00Z",
            "ownertype": "Individual",
            "lastOperationCode": "94",
            "lastOperationType": "Вторичная регистрация со сменой собственника"
        }
    ],
    "technicalInspections": {},
    "insurance": {
        "policySeries": "ХХХ",
        "insuranceCompanyTitle": "INGOSSTRAH",
        "endOn": "2021-08-13T00:00:00Z",
        "price": 3804.3,
        "policyNumber": "0132590777"
    },
    "accidentRate": {
        "accidentProbability": 22.38,
        "accidentProbabilityNorm": 22.38,
        "accidentRiskStatus": "Низкий риск ДТП",
        "safetyRating": 2,
        "vehicleCountInGroup": 1
    },
    "ownersCount": 5,
    "limits": {
        "currentCallsCount": 1,
        "maxCallsCount": 10,
        "timeIntervalSeconds": 10
    }
}
```

### Ошибки

- 429 - превышен допустимый лимит запросов
- 400 - пользователь/токен не найдены
- 1000 - ошибка валидации vin или грз

### Лимит 

Количество вызовов API ограничено, при достижении лимита будет выслан ответ с кодом 429. В ответе так же указана информация о лимитах:

 `limits.currentCallsCount` текущее количество вызовов
 `limits.maxCallsCount` максимальное количество вызовов в интервале времени
 `limits.timeIntervalSeconds`интервал времени (секунды)