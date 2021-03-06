EMAIL HTTP API
========

Обзор
-----

API предоставляет удобный интерфейс для автоматизации процесса отправки email-рассылок через платформу Devino Telecom. С помощью API можно отправлять массовые и транзакционные email-рассылки, управлять рассылками, получать полноценную статистику. Работа с API осуществляется в соответствии с принципами REST, посредством HTTP-запросов с использованием методов GET, POST, PUT, PATCH и DELETE.
Для использования API необходима авторизация. Авторизация происходит по логину паролю от Личного Кабинета платформы Devino Telecom.

Авторизация
-----------

API поддерживает базовую авторизацию через заголовок Authorization (https://en.wikipedia.org/wiki/Basic_access_authentication). В заголовке запроса необходимо передать логин и пароль из Личного Кабинета в формате login:password в base64 кодировке.

.. code-block:: json

                *Authorization: Basic dGVzdGVyOjExMTExMQ==*
                


Формат запроса
--------------

Запрос к API задается в следующем формате:

.. code-block:: json

        {тип_метода} https://integrationapi.net/email/v{версия}/{ресурс}?{параметры}
        

где:

.. code-block:: json

        {тип_метода} - HTTP метод GET, POST, PUT, PATCH и DELETE.
        {версия} - Версия API. Текущая версия - 1.
        {ресурс} - URL ресурса, над которым выполняется действие. Список всех ресурсов смотрите в соответствующем разделе.
        {параметры} - обязательные и необязательные параметры запроса, которые не входят в состав URL ресурса.
        Обязательный пункт: Accept */*
        

*Сервис позволяет передавать параметры и получать ответы в следующих форматах: JSON и XML.*

Формат ответа
-------------

Ответ API состоит из двух частей:

* Код с описанием - эта часть присутствует во всех ответах.
* Результат - специфичный для каждого запроса. Может отсутствовать.

.. code-block:: json
  
      {    
        "Result":
          {
              ...
          },
          "Code": "not_found",
          "Description": "user not found"
      }
      

Код можно использовать для проверки статуса запроса, а описание предназначено для диагностики возможных проблем. Описание может быть изменено в новой версии API без предупреждения о нарушении обратной совместимости. Набор кодов также может быть расширен.


Список кодов ответов:

+----------------------+-------------+---------------------------------+
| Код                  | HTTP status | Расшифровка                     |
+======================+=============+=================================+
| ok                   |  200, 201   | Запрос выполнен успешно         |
+----------------------+-------------+---------------------------------+
| not_changed          |  200        | Ресурс не изменён               |
+----------------------+-------------+---------------------------------+
| invalid_email        |  400        | Передан невалидный email адрес  |
+----------------------+-------------+---------------------------------+
| not_found            |  404        | Ресурс не найден                |
+----------------------+-------------+---------------------------------+
| empty_value          |  400        | Не передан один из обязательный |
|                      |             | параметров                      |
+----------------------+-------------+---------------------------------+
| invalid_value        |  400        | Передано невалидное значение    |
|                      |             | параметра                       |
+----------------------+-------------+---------------------------------+
| missing_macros       |  400        | Не найден один из обязательных  |
|                      |             | макросов в тексте рассылки      |
+----------------------+-------------+---------------------------------+
| size_exceeded        |  400        |Превышен допустимый размер данных|
+----------------------+-------------+---------------------------------+
| internal_error       |  500        | Внутренняя ошибка сервиса       |
+----------------------+-------------+---------------------------------+
| not_available        |  400        | Действие не доступно            |
+----------------------+-------------+---------------------------------+
| invalid_permission   |  403        | Не достаточно разрешений для    |
|                      |             | вызова метода                   |
+----------------------+-------------+---------------------------------+
| access_denied        |  403        | Нет доступа к запрошенному      |
|                      |             | ресурсу                         |
+----------------------+-------------+---------------------------------+
| authorization_failed |  401        | Ошибка авторизации              |
+----------------------+-------------+---------------------------------+

Ресурсы
-------

Список всех ресурсов, которые предоставляет API:

+----------------------------+---------+----------------------------------+
| Ресурс                     | Метод   | Описание                         |
+============================+=========+==================================+
| /Tasks/{TaskId}/State      |  PUT    | Изменение статуса рассылки       |
+----------------------------+---------+----------------------------------+
| /Tasks/{TaskId}/Attachments|  GET    | Получение аттачей рассылки       |
+----------------------------+---------+----------------------------------+
| /Tasks/{TaskId}/Attachments|  POST   | Добавление аттача в рассылку     |
+----------------------------+---------+----------------------------------+
| /Tasks/{TaskId}/Attachments|  DELETE | Удаление аттачей из рассылки     |
+----------------------------+---------+----------------------------------+
| /Tasks/{TaskId}            |  GET    | Получение рассылки               |
+----------------------------+---------+----------------------------------+
| /Tasks/{TaskId}            |  PATCH  | Редактирование рассылки          |
+----------------------------+---------+----------------------------------+
| /Tasks                     |  POST   | Создание рассылки                |
+----------------------------+---------+----------------------------------+
| /Messages                  |  POST   |Отправка транзакционного сообщения|
+----------------------------+---------+----------------------------------+

Получение рассылки
------------------

**ET /Tasks/{TaskId}**

Метод возвращает данные рассылки.

Параметры запроса:

+----------------+------------+--------------+-----------------------------------------+
|     Параметр   | Тип данных |Обязательность| Описание                                |
+================+============+==============+=========================================+
| TaskId         |  int       | Да           | Идентификатор рассылки (предаётся в url)|
+----------------+------------+--------------+-----------------------------------------+

Возвращаемый результат:

+----------------+-------------+-----------------------------------------+
| Параметр       | Тип данных  | Описание                                |
+================+=============+=========================================+
| TaskId         |  int        | Идентификатор рассылки                  |
+----------------+-------------+-----------------------------------------+
| Login          |  string     | Логин пользователя                      |
+----------------+-------------+-----------------------------------------+
| Name           | string      | Название                                |
+----------------+-------------+-----------------------------------------+
| Sender         | EmailAddress| Отправитель - адрес и имя               |
+----------------+-------------+-----------------------------------------+
| Subject        |  string     | Тема                                    |
+----------------+-------------+-----------------------------------------+
| Text           |  string     | Текст                                   |
+----------------+-------------+-----------------------------------------+
| StartDateTime  |  DateTime   | Начало отправки в UTC формате           |
+----------------+-------------+-----------------------------------------+
| Type           |  TaskType   | Тип рассылки                            |
+----------------+-------------+-----------------------------------------+
| UserCampaignId |  string     | Пользовательский идентификатор рассылки |
+----------------+-------------+-----------------------------------------+
| State          |  TaskState  | Статус рассылки                         |
+----------------+-------------+-----------------------------------------+
| Price          |  decimal    | Цена за сообщение                       |
+----------------+-------------+-----------------------------------------+
| ContactsCount  |  int        | Количество контактов                    |
+----------------+-------------+-----------------------------------------+

EmailAddress

+-----------+------------+----------+
| Параметр  | Тип данных | Описание |
+===========+============+==========+
| Name      |  string    | Имя      |
+-----------+------------+----------+
| Address   |  string    | Адрес    |
+-----------+------------+----------+

TaskType

+--------------+-------+---------------------------+
| Текст        | Число | Описание                  |
+==============+=======+===========================+
| Distribution |  1    | Одноразовая рассылка      |
+--------------+-------+---------------------------+
| Birthday     |  2    | Рассылка по дням рождения |
+--------------+-------+---------------------------+

Пример ответа:

.. code-block:: json

        {
            "Result":
            {
                "Login": "login",
                "Name": "name",
                "Sender": 
                {
                    "Address": "xxx@gmail.com",
                    "Name": "sendername"
                },
                "Subject": "subject",
                "Text": "text",
                "StartDateTime": "/Date(1440501564737-0000)/",
                "UserCampaignId": "",
                "State": "Started",
                "Price": 100000,
                "ContactsCount": 9997,
                "TaskId": 123456,
                "Type": 1
            },
            "Code": "ok",
            "Description": "found it!"
        }
        

Создание рассылки
-----------------

**POST /Tasks**

Метод создаёт рассылку. Если рассылка была успешно создана, возвращается код "ok" и http код 201. В качестве Result возвращается идентификатор рассылки и набор счётчиков. 

Валидируются:

* текст - на отсутствие стоп-слов и на наличие макросов [Unsubscribe] и [WebVersion]
* тема - на отсутствие стоп-слов
* размер текста и темы (не более 10 МБ)
* отправитель - имя на отсутствие стоп-слов и подтверждён ли адрес
* группы контактов - на существование
* тип рассылки - допустимы только 1 (Distribution) и 2 (Birthday).


Сценарии:

* Перед началом отправки необходимо подтвердить адрес отправителя ("Sender": {"Address"}).
* В текст письма должны быть включены макросы [Unsubscribe] и [WebVersion] - на их место будут подставлены ссылки на веб-версию письма и страницу отписки.
* Метод Tasks POST создает рассылку в статусе New. После этого рассылку можно редактировать с помощью методов Tasks PATCH, Attachments POST/DELETE. Когда рассылка готова к отправке, с помощью метода State PUT, необходимо присвоить рассылке статус Created, после чего рассылка будет отправлена.
* Для отправки отложенной рассылки нужно в StartDateTime указать желаемые дату и время отправки рассылки. 
* Для отправки рассылки по дням рождения, в рассылку должны быть включены контакты с заполненной датой рождения, а тип создаваемой рассылки - 2. Данная рассылка будет запускаться в одно и то же время каждый день, отправляя письма только тем контактам, у которых дата рождения совпадает с текущей датой.
* Стоп-листы. Для того, чтобы исключить получателей из рассылки, необходимо в запросе указать группу, или контакт, с параметром "Included":false. 
* Для отправки письма с встроенным аттачем, например, картинкой, необходимо в тело письма вставить тэг <img src='cid:Picture.png'/>. После этого добавить аттач методом Attachments POST c указанием ContentId, равным Picture.png. Если в теле письма не будет элемента с таким ContentId, аттач будет отправлен как обычное вложение.

Параметры запроса:

+---------------+-------------------+-----------------------------------------+--------------+
|   Параметр    | Тип данных        |Описание                                 | Обязательный |  
+===============+===================+=========================================+==============+
| Name          | string            | Название                                | Да           |
+---------------+-------------------+-----------------------------------------+--------------+
| Sender        | EmailAddress      | Отправитель - адрес и имя               | Да           |
+---------------+-------------------+-----------------------------------------+--------------+
| Subject       | string            | Тема                                    | Да           |
+---------------+-------------------+-----------------------------------------+--------------+
| Text          | string            | Текст                                   | Да           |
+---------------+-------------------+-----------------------------------------+--------------+
| StartDateTime | DateTime          | Начало отправки в UTC формате           | Да           |
+---------------+-------------------+-----------------------------------------+--------------+
| Type          | TaskType          | Тип рассылки                            | Да           |
+---------------+-------------------+-----------------------------------------+--------------+
| UserCampaignId| string            | Пользовательский идентификатор рассылки | Нет          |
+---------------+-------------------+-----------------------------------------+--------------+
| Contacts      | ContactDto[]	    | Список контактов                        | Нет          |
+---------------+-------------------+-----------------------------------------+--------------+
| ContactGroups | ContactGroupDto[]	| Список групп контактов                  | Нет          |
+---------------+-------------------+-----------------------------------------+--------------+

ContactDto:

+----------+------------+----------------------------------------------------------+--------------+
| Параметр | Тип данных |Описание                                                  | Обязательный |  
+==========+============+==========================================================+==============+
| Id       | long;      | Идентификатор контакта                                   | Да           |
+----------+------------+----------------------------------------------------------+--------------+
| included | bool;      | Включать контакт (true) или исключать (false) из рассылки| Да           |
+----------+------------+----------------------------------------------------------+--------------+

ContactGroupDto:

+----------+------------+----------------------------------------------------------+--------------+
| Параметр | Тип данных |Описание                                                  | Обязательный |  
+==========+============+==========================================================+==============+
| Id       | long;      | Идентификатор контакта                                   | Да           |
+----------+------------+----------------------------------------------------------+--------------+
| included | bool       | Включать группу (true) или исключать (false) из рассылки |              |
|          |            | (стоп-лист)                                              | Да           |
+----------+------------+----------------------------------------------------------+--------------+

Возвращаемый результат:

+---------------+------------+--------------------------------------------------+
| Параметр      | Тип данных | Описание                                         |
+===============+============+==================================================+
| TaskId        |  int       | Идентификатор рассылки                           |
+---------------+------------+--------------------------------------------------+
| TotalContacts |  int       | Количество получателей                           |
+---------------+------------+--------------------------------------------------+
| Dublicates    | int        | Количество отфильтрованных дубликатов            |
+---------------+------------+--------------------------------------------------+
| Unsubscribed  | int        | Количество отфильтрованных отписавшихся          |
+---------------+------------+--------------------------------------------------+
| Sender        | int        | Количество отфильтрованных исключённых контактов |
+---------------+------------+--------------------------------------------------+

Пример запроса:

.. code-block:: json

        {
            "Name":"name",
            "Sender":
            {
                "Address":"xxx@gmail.com",
                "Name":"sendername"
            },
            "Subject":"subject",
            "Text":"test [Unsubscribe][WebVersion]",
            "StartDateTime":"08/31/2015 13:30:38",
            "UserCampaignId":"",
            "Contacts":
            [
                {"Id":63090111,"Included":true}
            ]
            "ContactGroups":
            [
                {"Id":252,"Included":true},
                {"Id":234,"Included":true}
            ]
            "Type":1
        }
        

Пример ответа:

.. code-block:: json

        {
            "Result":
            {
                "TaskId": 133875,
                "TotalContacts": 1,
                "Dublicates": 0,
                "Unsubscribed": 0,
                "Excluded": 0
            },
            "Code": "ok",
            "Description": "new task added"
        }
        

Редактирование рассылки
-----------------------

**PATCH /EmailApi/Tasks/{TaskId}**

Метод редактирования рассылки. Если рассылка была успешно отредактирована, возвращается код "ok" и http код 200. Параметры запроса и ответ полностью идентичны Tasks POST. Редактировать можно только рассылки в статусе "New". При этом все поля являются необязательными и обновляются только переданные поля. Списки контактов и групп заменяются полностью, т.е нельзя добавить контакт к текущему списку для данной рассылки.

Получение аттачей рассылки
--------------------------

**GET /EmailApi/Tasks/{TaskId}/Attachments**

Получение аттачей по идентификатору рассылки. В качестве результата возвращается список аттачей.

Параметры запроса:

+----------+------------+----------------------------------------------------------+--------------+
| Параметр | Тип данных |Описание                                                  | Обязательный |  
+==========+============+==========================================================+==============+
| TaskId   | int        | Идентификатор рассылки (предаётся в url)                 | Да           |
+----------+------------+----------------------------------------------------------+--------------+

Возвращаемый результат:

+----------+------------+----------------------------------+
| Параметр | Тип данных | Описание                         |
+==========+============+==================================+
| TaskId   |  int       | Идентификатор рассылки           |
+----------+------------+----------------------------------+
| FileName |  string    | Имя файла                        |
+----------+------------+----------------------------------+
| Data     | string     | Данные аттача в base64 кодировке |
+----------+------------+----------------------------------+


Пример ответа:

.. code-block:: json

        {
            "Result":
            [
                {
                    "FileName": "3652099d-972d-4377-98e7-e857fa6de441_FALSE.jpg",
                    "Data": "/9j/4AAQSkZJRgABAQAAAQABAA1r52BNh5ry8gwn/2Q==",
                    "TaskId": 133794
                },      
                {
                    "FileName": "false.jpg",
                    "Data": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBry8gwn/2Q==",
                    "TaskId": 133794
                }
            ],
            "Code": "ok",
            "Description": "found them"
        }
        

Добавление аттача в рассылку
----------------------------

**POST /EmailApi/Tasks/{TaskId}/Attachments**

Метод добавление аттача в рассылку. Возвращается только стандартный ответ.
В рассылку можно добавить один, или несколько аттачей.

Валидируются:

* наличие имени файла и данных
* расширение файла, исполняемые файлы не допускаются
* размер (не более 3 МБ)
* существование рассылки

Параметры запроса:

+----------+------------+--------------------------------------------+--------------+
| Параметр | Тип данных | Описание                                   | Обязательный |  
+==========+============+============================================+==============+
| TaskId   | int        | Идентификатор рассылки (предаётся в url)   | Да           |
+----------+------------+--------------------------------------------+--------------+
| FileName | string     | Имя файла                                  | Да           |
+----------+------------+--------------------------------------------+--------------+
| Data     | string     | Аттач в base64 кодировке                   | Да           |
+----------+------------+--------------------------------------------+--------------+

Пример запроса:

.. code-block:: json

        {
            "FileName":"false.jpg",
            "Data":"/9j/4AAQSkZJRgABAQAAAQABAO1r52BNh5ry8gwn/2Q=="
        }
        

Пример ответа:

.. code-block:: json

        {
            "Code": "ok",
            "Description": "attachment added"
        }
        

Удаление аттачей из рассылки
----------------------------

**DELETE /EmailApi/Tasks/{TaskId}/Attachments**
 
Удаление всех аттачей из рассылки. Возвращается только стандартный ответ.

Пример ответа:

.. code-block:: json

        {
            "Code": "ok",
            "Description": "attachments deleted"
        }
        

Изменение статуса рассылки
--------------------------

**PUT /EmailApi/Tasks/{TaskId}/State**
 
Обновление статуса рассылки для остановки, возобновления, отмены, удаления. Возвращается только стандартный ответ.

Параметры запроса:

+----------+------------+--------------------------------------------+--------------+
| Параметр | Тип данных | Описание                                   | Обязательный |  
+==========+============+============================================+==============+
| TaskId   | int        | Идентификатор рассылки (предаётся в url)   | Да           |
+----------+------------+--------------------------------------------+--------------+
| State    | TaskState  | Текстовый или числовой статус рассылки     | Да           |
+----------+------------+--------------------------------------------+--------------+

TaskState:

+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Текст    | Число | Описание                                                                     | Можно ли использовать этот| 
|          |       |                                                                              | статус для PUT            |  
+==========+=======+==============================================================================+===========================+
| Canceled | 4     | Рассылка отменена (без возможности возобновления)                            | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Created  | 1     | Создание рассылки завершено, рассылка готова к выполнению                    | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Deleted  | 6     | Рассылка удалена                                                             | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Failed   | 7     | При отправке рассылки произошла ошибка                                       | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Finished | 5     | Оправка рассылки завершена успешно                                           | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| New      | 0     | Статус только что добавленной рассылки                                       | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Started  | 2     | Рассылка отправляется (также используется для возобновления после остановки) | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+
| Stopped  | 3     | Рассылка остановлена (с возможностью возобновления)                          | Да                        |
+----------+-------+------------------------------------------------------------------------------+---------------------------+

Пример запроса:

.. code-block:: json

        {"State":1}
        
 
Пример ответа:

.. code-block:: json

        {
            "Code": "ok",
            "Description": "task state updated to Created"
        }
        

Отправка транзакционного сообщения
----------------------------------

**POST /EmailApi/Messages**

Метод отправляет транзакционное сообщение. Если сообщение успешно добавлено в очередь, возвращается код "ok" и http код 201. В качестве Result возвращается идентификатор сообщения (string).

Валидируются:

* текст - на отсутствие стоп-слов (нецензурная лексика)
* тема - на отсутствие стоп-слов
* размер текста и темы с аттачами (не более 10 МБ)
* отправитель - имя на отсутствие стоп-слов и подтверждён ли адрес
* получатель - на валидность e-mail адреса

Аттачи валидируются на:

* наличие имени файла и данных
* расширение файла, исполняемые файлы не допускаются
* размер (не более 3 МБ каждый)

Параметры запроса:

+----------------+----------------+------------------------------------------------+--------------+
| Параметр       | Тип данных     | Описание                                       | Обязательный |  
+================+================+================================================+==============+
| Sender         | EmailAddress   | Отправитель - адрес и имя                      | Да           |
+----------------+----------------+------------------------------------------------+--------------+
| Recipient      | EmailAddress   | Получатель - адрес и имя                       | Да           |
+----------------+----------------+------------------------------------------------+--------------+
| Subject        | string         | Тема                                           | Да           |
+----------------+----------------+------------------------------------------------+--------------+
| Text           | string         | Текст                                          | Да           |
+----------------+----------------+------------------------------------------------+--------------+
| Attachments    | AttachmentDto[]| Массив аттачей                                 | Нет          |
+----------------+----------------+------------------------------------------------+--------------+
| UserMessageId  | string         | Идентификатор сообщения в системе пользователя | Нет          |
+----------------+----------------+------------------------------------------------+--------------+
| UserCampaignId | string         | Идентификатор рассылки в системе пользователя  | Нет          |
+----------------+----------------+------------------------------------------------+--------------+

AttachmentDto:

+----------------+----------------+------------------------------------------------+--------------+
| Параметр       | Тип данных     | Описание                                       | Обязательный |  
+================+================+================================================+==============+
| ContentId      | string         | ContentId в теле письма для встроенных аттачей | Нет          |
+----------------+----------------+------------------------------------------------+--------------+
| FileName       | string         | Имя файла                                      | Да           |
+----------------+----------------+------------------------------------------------+--------------+
| Data           | string         | Данные аттача в base64 кодировке               | Да           |
+----------------+----------------+------------------------------------------------+--------------+

Пример запроса:

.. code-block:: json

        {
           "Sender": {"Address":"test@test.com","Name":"name"},
           "Recipient": {"Address":"test@supertest.com", "Name":"name" },
           "Subject":"test subj",
           "Attachments":[{"ContentID": "Picture.jpg","FileName":"Picture.jpg","Data":"/9j/4AAQSkZJ"}],
           "Text":"test"
        }
        

Пример ответа:

.. code-block:: json

        {
            "Result": "kaAtrHbZ72",
            "Code": "ok",
            "Description": "message queued to send"
        }
        

Сценарии:

* Перед началом отправки необходимо подтвердить адрес отправителя("Sender": {"Address"})
* В текст письма может быть включен макрос [Unsubscribe]  - на его место будет подставлена ссылка на страницу отписки.
* Для отправки письма с встроенным аттачем, в параметре Attachments необходимо указать ContentId и вставить его в текст рассылки следующим образом: <img src='cid:<ContentId>'/> , иначе аттач придет как обычное вложение.
