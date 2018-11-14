# API для подключения Asterisk / FreePBX к Clinica Web

Для подключения собственной АТС построенной на [Asterisk] (https://www.asterisk.org/) / [FreePBX] (https://www.freepbx.org/) необходимо провести следующие настройки.

## Создать методы POST для сообщения о следующих событиях

- Входящий звонок
- Исходящий звонок
- Подняли трубку
- Положили трубку

### Все сообщения должны отправлять запросы на следующий адрес

```Markdown
https://example.com/api/Binotel/Push
```

Вместо _example.com_ необходимо указать адрес вашей _CRM Clinica Web_

## Описание методов

### Входящий звонок

Пример исходного кода на PHP для отправки POST запроса

```Markdown
$ PostData = array (
    "DidNumber '=>' 0443334000 ',
    "ExternalNumber '=>' 0670219424 ',
    "InternalNumber '=>' 801 ',
    "GeneralCallID '=>' 2744830 ',
    "CallType '=>' 0 ',
    "CompanyID '=>' 3041 ',
    "RequestType '=>' receivedTheCall"
)
```

### Исходящий звонок

Пример исходного кода на PHP для отправки POST запроса

```Markdown
$ PostData = array (
    "ExternalNumber '=>' 0670219424 ',
    "InternalNumber '=>' 904 ',
    "GeneralCallID '=>' 2744830 ',
    "CallType '=>' 1 ',
    "CompanyID '=>' 3041 ',
    "RequestType '=>' receivedTheCall"
)
```

### Подняли трубку

Пример исходного кода на PHP для отправки POST запроса

```Markdown
$ PostData = array (
    "DidNumber '=>' 0442334000 ',
    "ExternalNumber '=>' 0670219424 ',
    "InternalNumber '=>' 904 ',
    "GeneralCallID '=>' 2744830 ',
    "CallType '=>' 0 ',
    "CompanyID '=>' 3041 ',
    "RequestType '=>' answeredTheCall"
)
```

### Положили трубку

Пример исходного кода на PHP для отправки POST запроса

```Markdown
$ PostData = array (
    "GeneralCallID '=>' 2744830 ',
    "Billsec '=>' 35 ',
    "CompanyID '=>' 3041 ',
    "RequestType '=>' hangupTheCall"
)
```

## Описание параметров

```Markdown
- externalNumber - телефонный номер клиента
- internalNumber - внутренний номер сотрудника / группы в виртуальной АТС
- requestType - тип PUSH запроса (receivedTheCall-поступления звонка, answeredTheCall-поднятия трубки (ответ на звонок), hangupTheCall- завершения звонка)
- generalCallID - главный идентификатор звонка (уникальное для одного звонка)
- callType - тип звонка: входной - 0, выходной - 1
- companyID - идентификатор компании (не обязательно)
- billsec - продолжительность разговора
```