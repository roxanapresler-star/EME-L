# Механизм работы ЕМЕ БД с PostgreSQL через ODBC

> **Поисковые синонимы:** PostgreSQL EME БД, ODBC драйвер, ExternalDB, внешняя база данных, EME-L PostgreSQL, ODBC подключение, выгрузка данных PostgreSQL, ExternalDB.Connect, ExternalDB.CreateTable, ExternalDB.InsertLines, DSN системный, PostgreSQL ANSI, SQL запрос внешняя база, EME БД ODBC, интеграция PostgreSQL WMS

---

## Обзор механизма ExternalDB

В системе ЕМЕ БД реализован EME-L класс `ExternalDB`, позволяющий взаимодействовать с внешними базами данных, включая PostgreSQL. Инструментом взаимодействия выступает ODBC-драйвер — API для формирования интерфейса доступа к базам данных.

Класс `ExternalDB` в языке EME-L предоставляет унифицированный интерфейс для подключения к произвольным СУБД через ODBC, выполнения SQL-операций по созданию таблиц и вставке данных.

---

## Настройка ODBC-драйвера PostgreSQL

### Установка ODBC-драйвера

Необходимо скачать ODBC-драйвер с сайта PostgreSQL и установить его на рабочую станцию.

### Настройка системного DSN

1. Открыть «Администратор источника данных ODBC (64-разрядная версия)».
2. Выбрать вкладку «Системный DSN» и нажать «Добавить».
3. Из списка ODBC-драйверов выбрать «PostgreSQL ANSI(x64)» и нажать «Готово».

### Параметры ODBC-драйвера

В открывшемся диалоге заполнить параметры подключения:

| Параметр | Описание |
|----------|----------|
| Название источника данных | Имя DSN для идентификации подключения |
| База данных | Имя целевой базы данных PostgreSQL |
| IP-адрес сервера | Адрес сервера PostgreSQL |
| Имя пользователя | Учетная запись PostgreSQL |
| Порт | Порт подключения PostgreSQL |
| Пароль | Пароль учетной записи |

После заполнения нажать «Save».

В системе EME.WMS настроенный DSN используется в строке подключения класса `ExternalDB` для установления соединения с внешней базой данных PostgreSQL.

---

## Пример выгрузки данных из ЕМЕ БД в PostgreSQL

После настройки ODBC-драйвера создаётся EME-L класс, в котором выполняется SQL-запрос к данным ЕМЕ БД и применяются методы класса `ExternalDB` для подключения к PostgreSQL и формирования результирующей таблицы.

```EME-L
TestCreateAndInsertTable() {
    Select (Query)
        SELECT
            [Идентификатор],
            [Дата смены пароля],
            [Срок действия пароля]
        FROM
            [Список пользователей];
    End Select

    ExternalDB = Object("ExternalDB");
    Error = ExternalDB.Connect("ODBC:DSN=(название системного DSN);Server=localhost;Database=TEST;Uid=postgres;Pwd=1;");

    If (Error != "")
        is_message("TestCreateAndInsertTable", "Connect\n\n" + Error, "OK", "STOP");
        Return;
    End If

    Error = ExternalDB.CreateTable("test", Query);
    If (Error != "")
        is_message("TestCreateAndInsertTable", "CreateTable\n\n" + Error, "OK", "STOP");
        Return;
    End If

    Error = ExternalDB.InsertLines("test", Query);
    If (Error != "")
        is_message("TestCreateAndInsertTable", "InsertLines\n\n" + Error, "OK", "STOP");
        Return;
    End If
    is_message("TestCreateAndInsertTable", "OK", "OK", "EXCLAMATION");
}
```

---

## Справочник методов класса ExternalDB

### Строка подключения (Connect)

Формат строки подключения в языке EME-L:

```
ODBC:DSN=<название_системного_DSN>;Server=<адрес_сервера>;Database=<имя_БД>;Uid=<пользователь>;Pwd=<пароль>;
```

Пример:

```EME-L
Error = ExternalDB.Connect("ODBC:DSN=PostgreSQL35W;Server=localhost;Database=TEST;Uid=postgres;Pwd=1;");
```

Метод `Connect` возвращает пустую строку при успешном подключении или текст ошибки при неудаче.

### Основные методы

| Метод | Параметры | Описание |
|-------|-----------|----------|
| `Connect(connectionString)` | Строка подключения ODBC | Подключение к внешней базе данных |
| `CreateTable(tableName, query)` | Имя таблицы, объект Query | Создание таблицы во внешней БД по структуре запроса |
| `InsertLines(tableName, query)` | Имя таблицы, объект Query | Вставка строк данных из результата запроса |

В системе EME.WMS объект `Query` формируется конструкцией `Select (Query) ... End Select`, которая выполняет SQL-запрос к внутренней базе данных ЕМЕ БД. Результаты этого запроса используются методами `CreateTable` и `InsertLines` для создания и заполнения таблицы в PostgreSQL.

### Обработка ошибок

Каждый метод класса `ExternalDB` возвращает строку с описанием ошибки. Для информирования пользователя используется EME-L функция `is_message` с параметрами: источник, текст ошибки, кнопки, тип иконки (STOP — критическая ошибка, EXCLAMATION — успешное завершение).
