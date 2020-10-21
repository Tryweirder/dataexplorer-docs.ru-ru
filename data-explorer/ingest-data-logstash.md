---
title: Прием данных из Logstash в Azure Data Explorer
description: Из этой статьи вы узнаете, как принимать (загружать) данные в Azure обозреватель данных из Logstash.
author: orspod
ms.author: orspodek
ms.reviewer: takamara
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 228722bab25dc19532860f92b8194c4fd2d79a38
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343102"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Прием данных из Logstash в Azure Data Explorer

[Logstash](https://www.elastic.co/products/logstash) — это конвейер с открытым кодом на стороне сервера для обработки данных, который одновременно принимает данные из множества источников, преобразовывает данные, а затем отправляет их в избранный "тайник". В этой статье вы отправите эти данные в Azure обозреватель данных, которая является быстрой и высокой масштабируемой службой просмотра данных для данных журналов и телеметрии. Изначально вы создадите таблицу и сопоставление данных в тестовом кластере, а затем поручите Logstash отправить данные в таблицу и проверить результаты.

## <a name="prerequisites"></a>Обязательные условия

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начать работу.
* [Тестовый кластер и база данных](create-cluster-database-portal.md) Azure Data Explorer.
* [Инструкции по установке](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) Logstash версии 6 или более поздней.

## <a name="create-a-table"></a>Создание таблицы

При наличии кластера и базы данных можно приступать к созданию таблицы.

1. Чтобы создать таблицу, выполните следующую команду в окне запроса базы данных:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Чтобы убедиться, что таблица `logs` создана и является пустой, выполните следующую команду:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Создание сопоставления

Сопоставление используется службой Azure Data Explorer для преобразования входящих данных в схему целевой таблицы. Следующая команда создает сопоставление с именем `basicmsg`, которое извлекает свойства из входящего JSON, как отмечено в `path`, и выводит их в `column`.

Выполните следующую команду в окне запроса:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Установка подключаемого модуля выходных данных

Подключаемый модуль выходных данных Logstash взаимодействует с Azure Data Explorer и отправляет данные в службу.
Чтобы установить подключаемый модуль, выполните следующую команду в корневом каталоге Logstash:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Настройка Logstash для создания примера набора данных

Logstash может создавать примеры событий, которые можно использовать для тестирования сквозного конвейера.
Перейдите к следующему разделу, если вы уже используете Logstash и имеете доступ к собственному потоку событий. 

> [!NOTE]
> Если вы используете собственные данные, измените объекты таблицы и сопоставления, определенные на предыдущих шагах.

1. Измените новый текстовый файл, который будет содержать необходимые параметры конвейера (используя vi):

    ```sh
    vi test.conf
    ```

1. Вставьте следующие параметры, которые сообщат Logstash о создании 1000 тестовых событий:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Эта конфигурация также включает подключаемый модуль входных данных `stdin`, который позволит вам самостоятельно писать дополнительные сообщения (обязательно используйте *Ввод*, чтобы отправить их в конвейер).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Настройка Logstash для отправки данных в Azure Data Explorer

Вставьте следующие параметры в тот же файл конфигурации, который использовался на предыдущем шаге. Замените все заполнители соответствующими значениями для настройки. Дополнительные сведения см. в статье [HowTo: Creating an AAD Application](./provision-azure-ad-app.md) (Создание приложения AAD). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Имя параметра | Описание |
| --- | --- |
| **путь** | Подключаемый модуль Logstash записывает события во временные файлы перед их отправкой в Azure Data Explorer. Этот параметр включает в себя путь, куда следует записывать файлы, и выражение времени для смены файлов, чтобы активировать отправку в службу Azure Data Explorer.|
| **ingest_url** | Конечная точка Kusto для обмена данными, связанными с приемом.|
| **app_id**, **app_key**, и **app_tenant**| Учетные данные, необходимые для подключения к Azure Data Explorer. Обязательно используйте приложение с привилегиями приема. |
| **database**| Имя базы данных для размещения событий. |
| **table** | Имя целевой таблицы для размещения событий. |
| **mapping** | Сопоставление используется для сопоставления строки в формате JSON входящего события в правильный формат строки (определяет, какое свойство входит в какой столбец). |

## <a name="run-logstash"></a>Запуск Logstash

Теперь мы готовы запустить Logstash и протестировать параметры.

1. В корневом каталоге Logstash выполните следующую команду:

    ```sh
    bin/logstash -f test.conf
    ```

    Вы должны увидеть информацию на экране, а затем 1000 сообщений, созданных нашим примером конфигурации. На этом этапе вы также можете вручную ввести дополнительные сообщения.

1. Через несколько минут выполните следующий запрос Data Explorer, чтобы просмотреть сообщения в определенной вами таблице:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Нажмите клавиши Ctrl + C, чтобы выйти из Logstash.

## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните следующую команду в базе данных, чтобы очистить таблицу `logs`:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Дальнейшие действия

* [Написание запросов](write-queries.md)