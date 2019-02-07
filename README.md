# Документация Mfork
Адрес публичного api `https://ffo24.ru/public_api/`

### Содержание
| Путь                                                                  | Метод | Описание                    |            
| --------------------------------------------------------------------- | ----- | --------------------------- |
| [Документация API для поставщиков](#документация-api-для-поставщиков) |       |                             |
| [/suppliers/leads/](#создание-лида)                                   | POST  | Создание лида               |
| [/suppliers/leads/<EXT_ID>/](#получение-информации-о-лиде)            | GET   | Получение информации о лиде |
| [Примеры обращения к API](#примеры-обращения-к-api)                   |       |                             |

## Документация API для поставщиков
Подключившись к системе, партнёр с помощью API может создавать лидов с CRM системе, а так же отслеживать статус работы с лидом

### Авторизация

Авторизация в API происходит с помощью http заголовка Authorization + идентификация по ip

Каждый запрос должен содержать заголовок Authorization: bearer <token>


##### Пример запроса:
```curl
GET https://ffo24.ru/public_api/suppliers/
Cache-Control: no-cache
Content-Type: application/json
Authorization: bearer <token>
```

При успешном запросе статус ответа всегда будет 200

##### Пример отвера:
```curl
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS, PUT, PATCH, DELETE
Access-Control-Allow-Headers: X-Requested-With,content-type
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Content-Length: 89
ETag: W/"59-ppx65oTepz6mfJkZCQyxUi4gR+E"
Date: Wed, 05 Dec 2018 15:08:27 GMT
Connection: keep-alive

{
  "scope": "suppliers.api",
  "data": "Suppliers API. Look API docs for use suppliers methods"
}
```

При ошибке проверки авторизации http код ответа будет 401

##### Пример ответа при ошибке авторизации:
```curl
GET https://ffo24.ru/public_api/suppliers/leads/

HTTP/1.1 401 Unauthorized
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS, PUT, PATCH, DELETE
Access-Control-Allow-Headers: X-Requested-With,content-type
Access-Control-Allow-Credentials: true
WWW-Authenticate: Bearer realm="Users", error="invalid_token"
Date: Thu, 06 Dec 2018 08:05:58 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```


### Создание лида
> Method: `POST`
>
> URL: `https://ffo24.ru/public_api/suppliers/leads/`

##### Параметры запроса
| Параметр | Тип    | Описание                                                                  | Обязательный |
| -------- | ------ | ------------------------------------------------------------------------- | ------------ |
| name     | string | ФИО лида                                                                  | Да           |
| phone    | string | Телефон лида                                                              | Да           |
| sum      | int    | Сумма кредита                                                             | Да           |
| period   | int    | Срок кредита (мес)                                                        | Нет          |
| ext_id   | string | Внешний ID по которому потом можно будет получить информацию о лиде       | Да           |


##### Пример запроса:
```curl
POST https://ffo24.ru/public_api/suppliers/leads/
Cache-Control: no-cache
Content-Type: application/json
Authorization: bearer <token>

{   
  "name": "Test Tester",   
  "phone": "+79112223344",   
  "sum": 500000,   
  "period": 12,   
  "ext_id": "id123" 
}
```

##### Параметры ответа
| Параметр | Тип    | Описание                                                                  |
| -------- | ------ | ------------------------------------------------------------------------- |
| success  | bool   | Флаг успешного создания лида                                              |
| id       | string | ID лида в системе Mfork                                                   |

##### Пример ответа:
```curl
POST https://ffo24.ru/public_api/suppliers/leads/

HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS, PUT, PATCH, DELETE
Access-Control-Allow-Headers: X-Requested-With,content-type
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Content-Length: 51
ETag: W/"33-0nm1cfkA57aRGPtr+BTIzVyGaMU"
Date: Thu, 06 Dec 2018 11:18:02 GMT
Connection: keep-alive

{
  "scope": "suppliers.api",
  "success": true,
  "id": 00001
}
```

##### Ошибки:
| HTTP статус | Код      | Текст                       | Описание                               |
| ----------- | -------- | --------------------------- | -------------------------------------- |
| 400         | 40001    | Field '<field>' not allowed | Ошибка при передаче некорректного поля |
| 400         | 40002    | Field '<field>' is required | Ошибка остутсвия обязательного поля    |
| 400         | 40003    | Ext ID already exists       | Лид с таким `ext_id` уже существует    |
| 400         | 40004    | Incorrect phone number      | Некорректный номер телефона лида       |





### Получение информации о лиде
> Method: `GET`
>
> URL: `https://ffo24.ru/public_api/suppliers/leads/<EXT_ID>/`

##### Параметры запроса
| Параметр | Тип    | Описание                                                                  | Обязательный |
| -------- | ------ | ------------------------------------------------------------------------- | ------------ |
| EXT_ID   | string | Идентификатор лида, который был передан при создании лида в поле `ext_id` | Да           |

##### Пример запроса:
```curl
GET https://ffo24.ru/public_api/suppliers/leads/id123/
Cache-Control: no-cache
Content-Type: application/json
Authorization: bearer <token>
```

##### Параметры ответа
| Параметр        | Тип    | Описание                                   |
| --------------- | ------ | ------------------------------------------ |  
| name            | string | ФИО лида                                   |
| phone           | string | Номер телефона                             |
| sum             | int    | Сумма кредита                              |
| period          | int    | Срок кредита                               |
| status          | int    | Статус в системе Mfork                     |
| ext_id          | string | Внешний ID переданный при создании лида    |
| deleted         | bool   | Признак удалённого лида                    |
| contracted      | bool   | Признак сделки по лиду                     |
| deleted_date    | string | Дата удаления, если лид удалён             |
| delete_reason   | string | Причина удаления лида                      |
| contracted_date | string | Дата сделки, если по лиду заключена сделка |

##### Статусы лида
| Код  | Описание         |
| ---- | ---------------- |  
| 1    | Новый лид        |
| 2    | В работе         |
| 3    | Удалён           |
| 4    | Заключена сделка |

##### Пример ответа:
```curl
GET https://ffo24.ru/public_api/suppliers/leads/id123/

HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS, PUT, PATCH, DELETE
Access-Control-Allow-Headers: X-Requested-With,content-type
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Content-Length: 165
ETag: W/"a5-tuwu6RDBh9kPJWZChJTXI4hQJ3s"
Date: Thu, 06 Dec 2018 11:22:28 GMT
Connection: keep-alive

{
  "scope": "suppliers.api",
  "data": {
    "name": "Test Tester",
    "phone": "79112223344",
    "sum": 500000,
    "status": 2,
    "ext_id": "id123",
    "deleted": false,
    "contracted": false,
    "period": 12
  }
}

```

##### Ошибки:
| HTTP статус | Код      | Текст          | Описание                       |
| ----------- | -------- | -------------- | ------------------------------ |
| 404         | 40401    | Lead not found | Лид с таким `ext_id` не найден |




## Примеры обращения к API 

### PHP
```php
    $payload = json_encode([
        "name" => "Lead name"
    ]);

    $ch = curl_init();
    curl_setopt($ch, CURLOPT_HEADER, 0);
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);

    $headers = array();
    $headers[] = 'Accept: application/json';
    $headers[] = 'Content-Type: application/json';
    $headers[] = 'Authorization: bearer <TOKEN>';
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    curl_setopt( $ch, CURLOPT_POSTFIELDS, $payload );
    curl_setopt($ch, CURLOPT_URL, "https://ffo24.ru/public_api/suppliers/leads/");
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    $resp = curl_exec($ch);
    $resp = json_decode($resp, true);
    curl_close($ch);
    print_r($resp);
```
