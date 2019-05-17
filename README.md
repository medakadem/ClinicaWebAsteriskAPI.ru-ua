# API для подключения Asterisk / FreePBX к Clinica Web

Для подключения собственной АТС построенной на [Asterisk] (https://www.asterisk.org/) / [FreePBX] (https://www.freepbx.org/) необходимо провести следующие настройки.

## Создать методы POST для сообщения о следующих событиях

- CRM
    - Входящий звонок
    - Исходящий звонок
    - Подняли трубку
    - Положили трубку
- Asterisk
    - Данные о звонке по идентификатору звонка
    - Инициирование двустороннего звонка
    - Получение записи звонка
    - Инициирование двустороннего звонка

### Все сообщения должны отправлять запросы на следующий адрес

```Markdown
https://example.com/api/Binotel/Push
```

Вместо _example.com_ необходимо указать адрес вашей _CRM Clinica Web_

## Описание методов CRM

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
## Описание методов Asterisk

### Данные о звонке по идентификатору звонка

Request=> Post: "https://Host/stats/call-details.json"

```Markdown
{
  "generalCallID": [
    744924436
  ],
  "key": "701151-a3a9ca8",
  "signature": "7eca1077cec5bb8522d9ff925a68911f"
}
```


Response =>

```Markdown
{
  "status": "success",
  "callDetails": {
    "744924436": {
      "companyID": "10881",
      "generalCallID": "744924436",
      "callID": "744924436",
      "startTime": "1535457189",
      "callType": "0",
      "internalNumber": "902",
      "internalAdditionalData": "801 > ",
      "externalNumber": "0965074747",
      "srcNumber": "0961111111",
      "customerData": {
        "id": "17698964",
        "name": " Врач"
      },
      "employeeName": "Наталия Сторожук",
      "employeeEmail": "nataliiastorozhuk@medakadem.com",
      "waitsec": 4,
      "billsec": "68",
      "disposition": "ANSWER",
      "isNewCall": "0",
      "did": "0981884404",
      "didNumber": "0981884404",
      "didName": ""
    }
  },
  "signature": "26da6eb7bbfa1ae8a7a999669bdcff6b"
}
```


Разъяснения данных в информации о звонке:
- companyID - идентификатор компании в АТС
- generalCallID  - главный идентификатор звонка
- callID  - идентификатор записи разговора (используется для получения ссылки на запись разговора)
- startTime  - время начала звонка
- callType  - тип звонка: входящий - 0, исходящий  - 1
- internalNumber  - внутренний номер сотрудника / группы в виртуальной АТС (если звонок не был принят)
- internalAdditionalData  - номер группы в виртуальной АТС (если звонок был принят)
- externalNumber  - телефонный номер клиента
- customerData:
    - id  - идентификатор клиента в Мои клинтах
    - name  - имя клиента в Мои клинтах
- employeeName  - имя сотрудника
- employeeEmail  - email сотрудника
- dstNumbers  - спискок номеров которые были в обработке звонка (когда звонок входящий это будет список попыток звонков)
    - dstNumber  - номер кому звонили (когда звонок входящий это будет внутренняя линия сотрудника или группа при груповом звонке, при исхощяем звонке это будет номер на который звонит сотрудник)
- waitsec  - ожидание до соединения
- billsec  - длительность разговора
- disposition  - состояние звонка (ANSWER - успешный звонок, TRANSFER - успешный звонок который был переведен, ONLINE - звонок в онлайне, BUSY - неуспешный звонок по причине занятости, NOANSWER - неуспешный звонок по причине не ответа, CANCEL - неуспешный звонок по причине отмены звонка, CONGESTION - неуспешный звонок, CHANUNAVAIL - неуспешный звонок, VM - голосовая почта без сообщения, VM-SUCCESS - голосовая почта с сообщением, SMS-SENDING - SMS сообщение на отправке, SMS-SUCCESS - SMS сообщение успешно отправлено, SMS-FAILED - SMS сообщение не отправлено, SUCCESS - успешно принятый факс, FAILED - непринятый факс)
- isNewCall  - был ли входящий звонок новым
- didNumber  - номер на который пришел вызов во входящем звонке
- didName  - имя номера на который пришел вызов во входящем звонке
- trunkNumber  - номер через который совершался исходящий звонок
	

### Получение записи звонка

    * stats/call-record - ссылка на запись разговора.

    Request=> Post: "https://Host/stats/call-record.json" 
```Markdown 
{
  "callID": "744924436",
  "key": "701151-a3a9ca8",
  "signature": "e7a253e3fbebe31598b95c4d20d0448c"
}
```

Response =>
```Markdown
{
  "status": "success",
  "url": "https://cdn0993.s3.amazonaws.com/74/744924436.mp3?AWSAccessKeyId=AKIAJAXVY477KWWB5YJA&Expires=1557224957&Signature=2R1U9g8uiu044QGFdWFsSRlw%3D&companyID=10757881",
  "signature": "7883f74525cf277bfebce29fc79e1330"
}
```

### Инициирование двустороннего звонка

* calls/ext-to-phone - инициирование двустороннего звонка (внутреннюю линию с внешним номером)
Request=> Post: "https://Host/stats/calls/ext-to-phone.json"    
    
calls/ext-to-phone.json
```Markdown
{
  "key": "701151-a3a9ca8",
  "signature": "1732291bc66ac4297eb7e87ccf5f2549",
  "ext_number": "902",
  "phone_number": "0639798793"
}
```

- для авторизации используется: key, secret
- метод HTTP запроса: POST
- формат передачи данных: raw post data в json
- обезательные параметры для передачи: key, signature
- алгоритм генерации signature: сортируем параметры по ключу, далее делаем конкатинацию secret с переводом отсортированных параметров в json, и эта вся строка подается в функцию хеширования md5, её результат и будет подписью (важно чтобы сгенерируемая строка signature была в нижнем регистре).
