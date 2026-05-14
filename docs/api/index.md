**Almaz API** используется для интеграции внешних систем с сервисом **«Алмаз-Онлайн»**.

Через API можно выполнять авторизацию, получать список доступных платёжных шлюзов, проверять баланс, создавать документы, прикреплять сканы, выполнять оплату и получать статус платежа.

---

## Авторизация

Для работы с API необходимо выполнить авторизацию.

Полученный токен используется в остальных методах API.

!!! info "Важно"
    Авторизация доступна пользователю, у которого есть право доступа к API.

---


## Базовый адрес

```http
https://api.almaz-online.ru
```

---

## Основные методы API

| Метод | Назначение |
|---|---|
| [`/api/Auth/Login`](#auth-login) | Авторизация пользователя по email и паролю |
| [`/api/Auth/Login2FA`](#auth-login-2fa) | Подтверждение авторизации через двухфакторную аутентификацию |
| [`/api/Auth/RefreshToken`](#auth-refresh-token) | Обновление `Access Token` с помощью `Refresh Token` |
| [`/api/Auth/RevokeToken`](#auth-revoke-token) | Удаление `Refresh Token` |
| [`/api/Gateway/GetBalance`](#gateway-get-balance) | Проверка баланса платёжного шлюза |
| [`/api/Gateway/GetGateList`](#gateway-get-gate-list) | Получение списка доступных платёжных шлюзов |
| [`/api/Document/Create`](#document-create) | Создание платёжного документа |
| [`/api/Attachment/GetTypeList`](#attachment-get-type-list) | Получение списка типов сканов |
| [`/api/Attachment/Create`](#attachment-create) | Добавление сканов к документу |
| [`/api/Payments/Create`](#payments-create) | Создание оплаты по документу |
| [`/api/payments/SBPConfirm`](#payments-sbp-confirm) | Подтверждение или отмена платежа по СБП |
| [`/api/Payments/GetStatus`](#payments-get-status) | Получение статуса оплаты |
| [`/api/payment/GetLimits`](#payment-get-limits) | Получение списка доступных шлюзов и лимитов |
| [`/api/payment/ChangeLimit`](#payment-change-limit) | Изменение существующего лимита |
| [`/api/payment/AddLimit`](#payment-add-limit) | Добавление нового лимита |

---

## Токен авторизации

Методы API, требующие авторизации, вызываются с заголовком:

```http
Authorization: Bearer <token>
```

где `<token>` — токен, полученный после успешной авторизации.

---

## 1. Авторизация пользователя { #auth-login }

### POST `/api/Auth/Login`

Метод используется для авторизации пользователя по email и паролю.

```http
POST https://api.almaz-online.ru/api/Auth/Login
```

---

## Описание

Метод выполняет первый этап авторизации пользователя.

Пользователь передаёт email и пароль от учётной записи **«Алмаз-Онлайн»**.

После успешной проверки данных система может запросить подтверждение входа через двухфакторную аутентификацию.

---

## Тело запроса

```json
{
  "Email": "string",
  "Password": "string"
}
```

---

## Описание полей запроса

| Поле | Тип | Обязательное | Описание |
|---|---|---:|---|
| `Email` | `string` | Да | Email пользователя |
| `Password` | `string` | Да | Пароль пользователя |

---

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Auth/Login' \
  --header 'Content-Type: application/json' \
  --data '{
    "Email": "string",
    "Password": "string"
  }'
```

---

## Успешный ответ

**HTTP 200 OK**

```json
{
  "tokenExpiresAt": "string",
  "refreshTokenExpiresAt": "string",
  "code": 0,
  "codeTitle": "string",
  "description": "string",
  "requestId": "string"
}
```

---

## Описание полей ответа

| Поле | Тип | Описание |
|---|---|---|
| `tokenExpiresAt` | `string` | Дата и время окончания действия access token |
| `refreshTokenExpiresAt` | `string` | Дата и время окончания действия refresh token |
| `code` | `int` | Внутренний код ответа |
| `codeTitle` | `string` | Текстовое обозначение кода ответа |
| `description` | `string` | Описание результата или ошибки |
| `requestId` | `string` | Идентификатор запроса |

---

## Ошибка авторизации

**HTTP 400 Bad Request**

```text
Bad Request Uncorrect Email or Password
```

Ошибка возникает, если email или пароль указаны неверно.

---

## 2. Подтверждение авторизации через 2FA { #auth-login-2fa }

### POST `/api/Auth/Login2FA`

Метод используется для подтверждения входа через двухфакторную аутентификацию.

```http
POST https://api.almaz-online.ru/api/Auth/Login2FA
```

---

## Описание

Метод выполняет второй этап авторизации.

После успешного вызова `/api/Auth/Login` пользователь должен подтвердить вход кодом двухфакторной аутентификации.

Код может быть получен:

- по SMS;
- из приложения-аутентификатора, если используется OTP.

---

## Тело запроса

```json
{
  "Email": "string",
  "code": "string"
}
```

---

## Описание полей запроса

| Поле | Тип | Обязательное | Описание |
|---|---|---:|---|
| `Email` | `string` | Да | Email пользователя |
| `code` | `string` | Да | Код подтверждения 2FA |

---

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Auth/Login2FA' \
  --header 'Content-Type: application/json' \
  --data '{
    "Email": "string",
    "code": "string"
  }'
```

---

## Успешный ответ

**HTTP 200 OK**

```json
{
  "token": "string",
  "tokenExpiresAt": "string",
  "refreshToken": "string",
  "refreshTokenExpiresAt": "string",
  "code": 0,
  "codeTitle": "string",
  "description": "string",
  "requestId": "string"
}
```

---

## Описание полей ответа

| Поле | Тип | Описание |
|---|---|---|
| `token` | `string` | Access Token для вызова защищённых методов API |
| `tokenExpiresAt` | `string` | Дата и время окончания действия Access Token |
| `refreshToken` | `string` | Refresh Token для обновления Access Token |
| `refreshTokenExpiresAt` | `string` | Дата и время окончания действия Refresh Token |
| `code` | `int` | Внутренний код ответа |
| `codeTitle` | `string` | Текстовое обозначение кода ответа |
| `description` | `string` | Описание результата или ошибки |
| `requestId` | `string` | Идентификатор запроса |

---

## Ошибка подтверждения

**HTTP 400 Bad Request**

```text
Bad Request Uncorrect Email or Code
```

Ошибка возникает, если email или код подтверждения указаны неверно.

---

## 3. Обновление токена { #auth-refresh-token }

### POST `/api/Auth/RefreshToken`

Метод используется для обновления `Access Token` с помощью `Refresh Token`.

```http
POST https://api.almaz-online.ru/api/Auth/RefreshToken
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тело запроса

```json
{
  "RefreshToken": "string"
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Auth/RefreshToken' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "RefreshToken": "string"
  }'
```

## Успешный ответ

```json
{
  "token": "string",
  "tokenExpiresAt": "string",
  "refreshToken": "string",
  "refreshTokenExpiresAt": "string",
  "code": 0,
  "codeTitle": "string",
  "description": "string",
  "requestId": "string"
}
```

---

## 4. Удаление Refresh Token { #auth-revoke-token }

### POST `/api/Auth/RevokeToken`

Метод используется для удаления `Refresh Token`.

```http
POST https://api.almaz-online.ru/api/Auth/RevokeToken
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тело запроса

```json
{
  "UserId": "string"
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Auth/RevokeToken' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "UserId": "string"
  }'
```

## Успешный ответ

```json
{
  "code": 0,
  "codeTitle": "string",
  "requestId": "string"
}
```

---

## 5. Получение баланса шлюза { #gateway-get-balance }

### POST `/api/Gateway/GetBalance`

Метод используется для получения баланса платёжного шлюза.

```http
POST https://api.almaz-online.ru/api/Gateway/GetBalance
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тело запроса

```json
{
  "gatewayId": "e879bcad-7af1-4452-b028-abad8ba3a17c"
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Gateway/GetBalance' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "gatewayId": "e879bcad-7af1-4452-b028-abad8ba3a17c"
  }'
```

## Успешный ответ

```json
{
  "balance": 0,
  "isHidden": false,
  "code": 0,
  "codeTitle": "Ok",
  "requestId": "0HNLGQGFGJ3JV:00000001"
}
```

---

## 6. Получение списка шлюзов { #gateway-get-gate-list }

### POST `/api/Gateway/GetGateList`

Метод используется для получения списка доступных платёжных шлюзов.

Полученный `id` шлюза используется в методах получения баланса и оплаты.

```http
POST https://api.almaz-online.ru/api/Gateway/GetGateList
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тело запроса

```json
{}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Gateway/GetGateList' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{}'
```

## Успешный ответ

```json
{
  "gatewayList": [
    {
      "id": "151b38af-d9db-4551-b4c0-b77a08de7a4b",
      "name": "РСХБ",
      "displayName": "РСХБ",
      "gatewayName": "РСХБ",
      "gatewayNameSys": "RshbPayoutService",
      "isSbpActive": false,
      "isHidden": true
    },
    {
      "id": "28bb0eff-d44c-4301-9d26-ad1bfb910f68",
      "name": "Vtb",
      "displayName": "VTB",
      "gatewayName": "ВТБ",
      "gatewayNameSys": "VtbPayoutService",
      "isSbpActive": false,
      "isHidden": false
    }
  ],
  "code": 0,
  "codeTitle": "Ok",
  "requestId": "0HNHJ6RCQ569U:00000001"
}
```

---

## 7. Создание документа { #document-create }

### POST `/api/Document/Create`

Метод используется для создания платёжного документа.

Полученный `documentId` используется:

- для добавления сканов;
- для оплаты;
- для проверки статуса оплаты.

```http
POST https://api.almaz-online.ru/api/Document/Create
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Ключ идемпотентности

Для работы ключа идемпотентности необходимо передавать в `Header` значение в формате `UUID`.

```http
Idempotency-Key: db6de35f-b0d6-4d68-a994-d8222f17dea7
```

Ключ идемпотентности используется для предотвращения дублирования документов.

## Тело запроса

```json
{
  "Person": {
    "LastName": "string",
    "FirstName": "string",
    "MiddleName": "string",
    "IsResident": "string",
    "Phone": "string",
    "Passport": {
      "Serial": "string",
      "Number": "string",
      "DateIssue": "2019-08-24",
      "RegName": "string",
      "RegCode": "string",
      "Birthday": "2019-08-24",
      "BirthPlace": "2019-08-24",
      "BirthCountry": "string",
      "RegAddress": "string",
      "PostalAddress": "string"
    }
  },
  "Document": {
    "Number": "string",
    "Date": "2019-08-24",
    "Sum": "string"
  }
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Document/Create' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --header 'Idempotency-Key: db6de35f-b0d6-4d68-a994-d8222f17dea7' \
  --data '{
    "Person": {
      "LastName": "string",
      "FirstName": "string",
      "MiddleName": "string",
      "IsResident": "string",
      "Phone": "string",
      "Passport": {
        "Serial": "string",
        "Number": "string",
        "DateIssue": "2019-08-24",
        "RegName": "string",
        "RegCode": "string",
        "Birthday": "2019-08-24",
        "BirthPlace": "2019-08-24",
        "BirthCountry": "string",
        "RegAddress": "string",
        "PostalAddress": "string"
      }
    },
    "Document": {
      "Number": "string",
      "Date": "2019-08-24",
      "Sum": "string"
    }
  }'
```

## Успешный ответ

```json
{
  "documentId": "69154ed6-5e95-4960-9d04-08685b96eb02",
  "code": 0,
  "codeTitle": "Ok",
  "requestId": "0HNHJ6RCQ569R:00000001"
}
```

---

## 8. Получение списка типов сканов { #attachment-get-type-list }

### POST `/api/Attachment/GetTypeList`

Метод используется для получения списка типов сканов.

Полученный `id` типа скана используется при загрузке вложений.

```http
POST https://api.almaz-online.ru/api/Attachment/GetTypeList
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Пример запроса

```bash
curl --location --request POST 'https://api.almaz-online.ru/api/Attachment/GetTypeList' \
  --header 'Authorization: Bearer <token>'
```

## Успешный ответ

```json
{
  "attachTypesList": [
    {
      "id": "string",
      "name": "string"
    }
  ],
  "code": 0,
  "codeTitle": "string",
  "requestId": "string"
}
```

---

## 9. Добавление скана { #attachment-create }

### POST `/api/Attachment/Create`

Метод используется для добавления скана к документу.

```http
POST https://api.almaz-online.ru/api/Attachment/Create
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тип запроса

```http
multipart/form-data
```

## Поля формы

| Поле | Описание |
|---|---|
| `File` | Файл скана |
| `request` | Данные `DocumentId`, `TypeId` |

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Attachment/Create' \
  --header 'Authorization: Bearer <token>' \
  --form 'File=@"/path/to/file"' \
  --form 'request="DocumentId, TypeId"'
```

## Успешный ответ

```text
OK
```

---

## 10. Создание оплаты { #payments-create }

### POST `/api/Payments/Create`

Метод используется для оплаты созданного документа.

```http
POST https://api.almaz-online.ru/api/Payments/Create
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тело запроса

```json
{
  "DocumentId": "898a2182-7164-4cc9-b9cf-8f84afe980ce",
  "GatewayId": "2388b642-943c-4d74-9af7-35ee23218bc3",
  "BankCard": {
    "Number": "string",
    "Holder": "string"
  }
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Payments/Create' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "DocumentId": "898a2182-7164-4cc9-b9cf-8f84afe980ce",
    "GatewayId": "2388b642-943c-4d74-9af7-35ee23218bc3",
    "BankCard": {
      "Number": "string",
      "Holder": "string"
    }
  }'
```

## Успешный ответ

```json
{
  "documentId": "69154ed6-5e95-4960-9d04-08685b96eb02",
  "status": "Init",
  "code": 0,
  "codeTitle": "Ok",
  "description": "",
  "requestId": "0HNHJ6RCQ56A4:00000001"
}
```

---

## 11. Подтверждение или отмена платежа по СБП { #payments-sbp-confirm }

### POST `/api/payments/SBPConfirm`

Метод используется для подтверждения или отмены платежа по СБП.

```http
POST https://api.almaz-online.ru/api/payments/SBPConfirm
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Значения `WasConfirmed`

| Значение | Описание |
|---|---|
| `true` | Подтверждение платежа |
| `false` | Отмена платежа |

## Тело запроса

```json
{
  "docActId": "3daebb6e-078c-4d81-a628-47bacba7bf6c",
  "WasConfirmed": true
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/payments/SBPConfirm' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "docActId": "3daebb6e-078c-4d81-a628-47bacba7bf6c",
    "WasConfirmed": true
  }'
```

## Успешный ответ

```json
{
  "documentId": "69154ed6-5e95-4960-9d04-08685b96eb02",
  "status": "Init",
  "code": 0,
  "codeTitle": "Ok",
  "description": "",
  "requestId": "0HNHJ6RCQ56A4:00000001"
}
```

---

## 12. Получение статуса оплаты { #payments-get-status }

### POST `/api/Payments/GetStatus`

Метод используется для получения статуса оплаты по документу.

```http
POST https://api.almaz-online.ru/api/Payments/GetStatus
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Тело запроса

```json
{
  "documentId": "string"
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/Payments/GetStatus' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "documentId": "string"
  }'
```

## Успешный ответ

```json
{
  "gatewayId": "38b883d2-2c92-4e08-bcef-2a5f8c8c30f4",
  "sum": 1,
  "state": 1,
  "documentId": "e268f539-e698-42c0-9cf0-91e701cf2f29",
  "status": "Successful",
  "code": 0,
  "codeTitle": "Ok",
  "requestId": "0HNHJ6RCQ56A8:00000001"
}
```

## Статусы оплаты

| Статус | Описание |
|---|---|
| `init` | Создан. Платёжный документ создан |
| `successful` | Оплачен. Платёжный документ успешно оплачен |
| `canceled` | Отмена. Оплата отменена пользователем при подтверждении платежа по СБП |
| `error` | Отказ. Банк отказал в проведении платежа |
| `process` | В процессе. Обработка на стороне системы Алмаз |
| `processing` | В обработке. Ожидается ответ от банка |
| `awaitingConfirmation` | Ожидание. Требуется подтверждение пользователем выплаты по СБП |

---

## 13. Получение лимитов { #payment-get-limits }

### GET `/api/payment/GetLimits`

Метод используется для получения списка доступных шлюзов и лимитов.

```http
GET https://api.almaz-online.ru/api/payment/GetLimits
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/payment/GetLimits' \
  --header 'Authorization: Bearer <token>'
```

## Успешный ответ

```json
{
  "availableCompanyGateways": [
    {
      "settingId": "100ac584-c716-4abe-a005-6d19a6fb0722",
      "companyId": "96a6a627-e263-41fd-ab9f-82231581a379",
      "name": "ФИНСТАР",
      "gatewayLimits": [
        {
          "id": "0a044021-715f-4fe0-bf2a-d385e2c34da6",
          "limit": 10,
          "spentLimit": 4,
          "limitSettingType": 1,
          "gatewayTimeLimit": 8,
          "isActive": true
        }
      ]
    }
  ],
  "code": 0,
  "codeTitle": "Ok",
  "requestId": "0HN17U8JKL100:00000002"
}
```

---

## 14. Изменение лимита { #payment-change-limit }

### POST `/api/payment/ChangeLimit`

Метод используется для изменения существующего лимита.

```http
POST https://api.almaz-online.ru/api/payment/ChangeLimit
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Значения `LimitSettingType`

| Значение | Описание |
|---|---|
| `1` | Постоянный лимит |
| `2` | Одноразовый лимит |

## Значения `GatewayTimeLimit`

| Значение | Описание |
|---|---|
| `0` | Один день |
| `7` | Неделя |
| `8` | Месяц |

## Тело запроса

```json
{
  "name": "LimitNameHere",
  "comment": "LimitCommentHere",
  "limit": 140,
  "limitSettingType": 2,
  "gatewayTimeLimit": 0,
  "isActive": false,
  "id": "971f03fe-2b5e-4ef9-9350-98d4865b74b1"
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/payment/ChangeLimit' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "name": "LimitNameHere",
    "comment": "LimitCommentHere",
    "limit": 140,
    "limitSettingType": 2,
    "gatewayTimeLimit": 0,
    "isActive": false,
    "id": "c9a83d21-8b47-4072-bef2-6c5619317e76"
  }'
```

## Успешный ответ

```json
{
  "id": "0a044021-715f-4fe0-bf2a-d385e2c34da6",
  "settingId": "100ac584-c716-4abe-a005-6d19a6fb0722",
  "companyId": "96a6a627-e263-41fd-ab9f-82231581a379",
  "name": "Name",
  "comment": "Comment",
  "limit": 140,
  "spentLimit": 0,
  "limitSettingType": 1,
  "gatewayTimeLimit": 8,
  "isActive": false,
  "code": 0,
  "codeTitle": "Ok",
  "requestId": "0HN17U8JKL100:00000007"
}
```

---

## 15. Добавление лимита { #payment-add-limit }

### POST `/api/payment/AddLimit`

Метод используется для добавления нового лимита.

```http
POST https://api.almaz-online.ru/api/payment/AddLimit
```

## Авторизация

```http
Authorization: Bearer <token>
```

## Значения `LimitSettingType`

| Значение | Описание |
|---|---|
| `1` | Постоянный лимит |
| `2` | Одноразовый лимит |

## Значения `GatewayTimeLimit`

| Значение | Описание |
|---|---|
| `0` | Один день |
| `7` | Неделя |
| `8` | Месяц |

## Тело запроса

```json
{
  "name": "LimitName",
  "comment": "LimitComment",
  "limit": 20,
  "limitSettingType": 1,
  "gatewayTimeLimit": 7,
  "settingId": "c92b9737-da32-4b9f-9c6d-536ae3b7f207",
  "companyId": "d4f7970d-41c8-462e-bc63-39e92853db29"
}
```

## Пример запроса

```bash
curl --location 'https://api.almaz-online.ru/api/payment/AddLimit' \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '{
    "name": "LimitName",
    "comment": "LimitComment",
    "limit": 20,
    "limitSettingType": 1,
    "gatewayTimeLimit": 7,
    "settingId": "c92b9737-da32-4b9f-9c6d-536ae3b7f207",
    "companyId": "d4f7970d-41c8-462e-bc63-39e92853db29"
  }'
```

## Пример ответа с ошибкой

```json
{
  "code": 6,
  "codeTitle": "ValidationError",
  "description": "Лимит не найден",
  "requestId": "0HN17U8JKL100:00000003"
}
```