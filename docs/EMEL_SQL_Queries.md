# SQL-запросы в EME-L (EME-SQL)

**Описание:** EME-SQL — интерпретируемый язык запросов в EME.WMS. Позволяет формировать сложные выборки данных с использованием EME-L кода внутри запросов.
**Сленг:** SQL-запрос, EME-SQL, запрос к данным, query, селект

## Основы EME-SQL

SQL-запросы в EME.WMS хранятся в базе данных вместе с прикладными данными и могут редактироваться в любой момент через редактор запросов.

**Синонимы для поиска:** SQL запрос, EME-SQL синтаксис, SELECT запрос, создание запроса, редактор запросов

### Создание SQL-запроса

1. Открыть редактор запросов: Система → Ресурсы → Редактор запросов
2. Нажать зелёный крестик
3. Ввести имя запроса
4. Указать класс и диалог для привязки
5. Сохранить: Ctrl-S

### Основные конструкции EME-SQL

| Конструкция | Описание |
|-------------|----------|
| `SELECT` | Выбор полей |
| `FROM` | Источник данных (запись или запрос) |
| `WHERE` | Условия фильтрации (EME-L код) |
| `WITH` | Загрузка строк в запрос |
| `GROUP BY` | Группировка |
| `ORDER BY` | Сортировка |
| `JOIN` | Соединение таблиц |
| `UNION` | Объединение результатов |
| `TOTAL` | Применение операторов ко всему результату |

## Синтаксис EME-SQL

Синтаксис запросов в EME-L имеет свои особенности.

**Синонимы для поиска:** синтаксис EME-SQL, структура запроса, поля в запросе, реляционный путь в SQL

### Описание полей

```eme-sql
'Простые поля в квадратных скобках'
SELECT
    [Идентификатор],
    [Наименование],
    [Количество]

'Вычисляемые поля с указанием типа'
SELECT
    {Return is_date();}:DATE AS [Дата],
    {Return 1;}:INT AS [Признак]

'Поля-ссылки через реляционный путь'
SELECT
    [@Склад].[Наименование] AS [Склад],
    [@Клиент].[ИНН] AS [ИНН клиента]

'Агрегатные функции'
SELECT
    SUM([Количество]):FLOAT AS [Всего],
    COUNT({Return 1;}:INT) AS [Количество строк]
```

### Источник данных (FROM)

```eme-sql
'Из записи'
FROM [Документы]

'Из другого запроса'
FROM [Запросы.Отчёт по документам]

'Создание новых строк'
FROM NEW{11}  'Создаёт 11 пустых строк'
```

## Фильтрация данных

В EME-SQL фильтрация может выполняться через `WHERE`, `WITH(Rec)`, `WITH` или `HAVING`.

**Синонимы для поиска:** фильтрация в SQL, WHERE, WITH, HAVING, условия отбора

### Конструкция WHERE

```eme-sql
SELECT
    [@Клиент].[Фамилия] AS [Фамилия],
    SUM({Return 1;}:INT) AS [Количество звонков]
FROM
    [Звонки клиентов]
WHERE
{
    'EME-L код фильтрации'
    NeedData = Const(is_edit_data("Report.Date"));
    Return is_query(,"Дата") == NeedData;
}
GROUP BY
    [Фамилия]
```

### Конструкция WITH

Рекомендуется использовать `WITH` вместо `WHERE` для оптимизации производительности.

```eme-sql
SELECT
    [Номер документа],
    [Дата создания],
    [Количество]
FROM
    [Строки документа]
WITH
{
    'Создание объекта запроса'
    objQuery = is_context("Query");
    
    'Выбор заголовков документов через SkipMode'
    r_Document = Object("dsDB", "Document");
    r_Document.SetSkipMode();
    
    'Фильтрация по дате создания'
    r_Document.GetCreateDateFld().MustBeGE(is_edit_data("Report.StartDate"));
    r_Document.GetCreateDateFld().MustBeLE(is_edit_data("Report.EndDate"));
    
    'Фильтрация по типу документа'
    r_Document.GetDocumentTypeRefFld().MustBeRefEQ(0);
    
    'Загрузка подходящих документов в битовый буфер'
    bbDocs = Object("BitBuffer", is_No_of_lines("Документы"));
    bbDocs.LoadLines(r_Document);
    
    'Фильтрация строк документа'
    r_DocLines = Object("dsDB", "DocLines");
    r_DocLines.SetSkipMode();
    r_DocLines.GetDocumentRefFld().MustBeRefInBitBuffer(bbDocs);
    
    'Загрузка строк в запрос'
    objQuery.LoadLines(r_DocLines);
}
```

### Важные функции для фильтрации

```eme-l
'is_edit_data("Орган") — получение значения из органа диалога'
'is_query("Поле") — получение значения поля текущей строки запроса'
'Const() — оптимизация для константных значений'

'Пример с Const:'
WHERE
{
    NeedDate = Const(is_edit_data("Report.Date"));  'Вычисляется один раз'
    Return is_query(,"Дата") == NeedDate;
}
```

## Соединение таблиц (JOIN)

EME-SQL поддерживает различные типы соединений таблиц.

**Синонимы для поиска:** JOIN, соединение таблиц, LEFT JOIN, CHAIN JOIN, объединение данных

### LEFT JOIN

```eme-sql
SELECT
    {Time=is_time(8+is_line(),0); Return Time;}:TIME AS [Время],
    [Количество звонков],
    [Фамилия]
FROM
    NEW{11}
LEFT JOIN
    [Запросы.Звонки по времени]
ON
    [Время] = [Запросы.Звонки по времени]![Время]
```

### CHAIN JOIN

`CHAIN JOIN` автоматически подтягивает все ссылки из связанной записи.

```eme-sql
SELECT
    [Номер документа],
    [@Склад].[Наименование] AS [Склад],
    [@Клиент].[Наименование] AS [Клиент]
FROM
    [Документы]
CHAIN JOIN
    [Строки документа]
```

## Объединение результатов (UNION)

Оператор `UNION` объединяет результаты нескольких запросов.

**Синонимы для поиска:** UNION, объединение запросов, TOTAL, множественный SELECT

### Пример UNION

```eme-sql
SELECT
    {Time=is_time(8+is_line(),0); Return Time;}:TIME AS [Время],
    [Количество звонков],
    [Фамилия]
FROM
    NEW{11}
LEFT JOIN
    [Запросы.Звонки по времени]
ON
    [Время] = [Запросы.Звонки по времени]![Время]

UNION

SELECT *
FROM
    [Запросы.Звонки по времени]

TOTAL

GROUP BY
    [Время], [Фамилия]
```

**Важно:** `TOTAL` применяется после `UNION`, чтобы последующие операторы (`GROUP BY`, `ORDER BY`) применялись ко всему объединённому результату.

## Группировка данных

Группировка выполняется конструкцией `GROUP BY`.

**Синонимы для поиска:** GROUP BY, группировка, агрегатные функции, SUM, COUNT

### Пример группировки

```eme-sql
SELECT
    [@Клиент].[Фамилия] AS [Фамилия],
    SUM({Return 1;}:INT) AS [Количество звонков],
    MAX([Дата]):DATE AS [Последний звонок]
FROM
    [Звонки клиентов]
WHERE
{
    Return is_query(,"Дата") >= startDate;
}
GROUP BY
    [Фамилия]
ORDER BY
    [Количество звонков] DESC
```

### Агрегатные функции

| Функция | Описание |
|---------|----------|
| `SUM({выражение}:ТИП)` | Сумма |
| `COUNT({выражение}:ТИП)` | Количество |
| `MAX(поле)` | Максимум |
| `MIN(поле)` | Минимум |
| `AVG({выражение}:ТИП)` | Среднее |

## Встроенные функции в SQL

В вычисляемых полях можно использовать EME-L функции.

**Синонимы для поиска:** функции в SQL, is_line, is_date, is_time, вычисляемые поля

### Полезные функции

```eme-sql
'is_line() — номер текущей строки'
'is_date() — текущая дата'
'is_time() — текущее время'
'is_query("Поле") — значение поля текущей строки'
'is_edit_data("Орган") — значение из диалога'

SELECT
    {Return is_line();}:INT AS [Номер строки],
    {Return is_date();}:DATE AS [Дата],
    {Return is_time();}:TIME AS [Время]
FROM NEW{10}
```

### Пример с константами

```eme-sql
'Константные значения'
SELECT
    {Return is_date() - 1;}:DATE AS [Дата начала],
    {Return is_time(0,0,0);}:TIME AS [Время начала],
    {Return is_date();}:DATE AS [Дата завершения],
    {Return is_time();}:TIME AS [Время завершения]
FROM NEW{1}
```

## Создание запроса без источника

Конструкция `FROM NEW{}` позволяет создать запрос без обращения к записям БД.

**Синонимы для поиска:** NEW в SQL, запрос без записи, пустой запрос, генерация строк

### Примеры

```eme-sql
'Генерация диапазона времени'
SELECT
    {Time=is_time(8+is_line(),0); Return Time;}:TIME AS [Время]
FROM NEW{11}

'Константные значения'
SELECT
    {Return "Значение";}:STRING AS [Столбец]
FROM NEW{1}
```

## Использование EME-L кода в запросах

Внутри фигурных скобок `{}` можно писать любой EME-L код.

**Синонимы для поиска:** EME-L в SQL, код в запросе, вычисляемое поле, Return в SQL

### Правила написания кода

1. Код заключается в фигурные скобки `{}`
2. Результат возвращается через `Return`
3. После закрывающей скобки указывается тип `:ТИП`
4. Пробелы после `}` и до типа недопустимы

```eme-sql
'Правильно:'
{Return is_date();}:DATE

'Неправильно:'
{Return is_date();} :DATE  'Пробел недопустим!'
```

### Сложные вычисления

```eme-sql
SELECT
    {
        'Сложный расчёт в EME-L'
        qty = is_query(,"Количество");
        price = is_query(,"Цена");
        total = qty * price;
        Return total;
    }:FLOAT AS [Сумма]
FROM
    [Строки документа]
```

## Оптимизация SQL-запросов

Для оптимизации производительности SQL-запросов следуйте рекомендациям.

**Синонимы для поиска:** оптимизация SQL, производительность запроса, Const, оптимизация EME-SQL

### Использование Const

```eme-sql
'НЕОПТИМАЛЬНО: is_edit_data вызывается для каждой строки'
WHERE
{
    Return is_query(,"Дата") == is_edit_data("Report.Date");
}

'ОПТИМАЛЬНО: значение вычисляется один раз'
WHERE
{
    NeedDate = Const(is_edit_data("Report.Date"));
    Return is_query(,"Дата") == NeedDate;
}
```

### Оптимизация объектов

```eme-sql
'НЕОПТИМАЛЬНО: объект создаётся в каждой строке'
{
    dbRegs = Object("dsDB", "Registers");
    dbRegs.SetLine(is_query(,"@"));
    Return dbRegs.GetQtyN();
}:FLOAT

'ОПТИМАЛЬНО: объект создаётся один раз'
{
    dbRegs = Const(Object("dsDB", "Registers"));
    dbRegs.SetLine(is_query(,"@"));
    Return dbRegs.GetQtyN();
}:FLOAT
```

### Использование WITH вместо WHERE

```eme-sql
'ПРЕДПОЧТИТЕЛЬНО: загрузка строк через WITH'
FROM [Строки документа]
WITH
{
    'Предварительная фильтрация через SkipMode'
    r_DocLines = Object("dsDB", "DocLines");
    r_DocLines.SetSkipMode();
    r_DocLines.GetDocumentRefFld().MustBeRefEQ(docRef);
    objQuery.LoadLines(r_DocLines);
}

'МЕНЕЕ ЭФФЕКТИВНО: фильтрация через WHERE'
FROM [Строки документа]
WHERE
{
    Return is_query(,"@Документ") == docRef;
}
```

## Примеры сложных запросов

### Запрос с соединением и группировкой

```eme-sql
'Отчёт по продажам по клиентам'
SELECT
    [@Клиент].[Наименование] AS [Клиент],
    COUNT({Return 1;}:INT) AS [Количество заказов],
    SUM([Сумма]):FLOAT AS [Общая сумма],
    MAX([Дата отгрузки]):DATE AS [Последняя отгрузка]
FROM
    [Документы]
WITH
{
    objQuery = is_context("Query");
    
    r_Document = Object("dsDB", "Document");
    r_Document.SetSkipMode();
    r_Document.GetDocumentTypeRefFld().MustBeRefEQ(1);  'Отгрузка'
    r_Document.GetCreateDateFld().MustBeGE(startDate);
    r_Document.GetCreateDateFld().MustBeLE(endDate);
    r_Document.MustBeValid();
    
    objQuery.LoadLines(r_Document);
}
GROUP BY
    [Клиент]
ORDER BY
    [Общая сумма] DESC
```

### Запрос с подзапросом

```eme-sql
SELECT
    [Товар],
    [Количество]
FROM
    [Строки документа]
WHERE
{
    'Проверка через подзапрос'
    r_Document = Object("dsDB", "Document");
    r_Document.SetLine(is_query(,"@Документ"));
    Return r_Document.GetDocumentTypeRefFld() == 0;
}
```

## Свободные запросы в EME-SQL

Для создания запросов без обращения к конкретной записи можно использовать конструкцию `FROM NEW{N}`, где N — количество строк.

**Синонимы для поиска:** свободный запрос, NEW, пустой запрос, запрос без записи, FROM NEW

### Создание запроса с одной строкой

```eme-sql
SELECT
    {Return is_date() - 1;}:DATE AS [Дата начала],
    {Return is_time(0,0,0);}:TIME AS [Время начала],
    {Return is_date();}:DATE AS [Дата завершения],
    {Return is_time();}:TIME AS [Время завершения]
FROM NEW{1}
```

### Создание пустого запроса (только структура)

```eme-sql
SELECT
    {is_date();}:DATE AS [Дата начала],
    {is_time();}:TIME AS [Время начала],
    {is_date();}:DATE AS [Дата завершения],
    {is_time();}:TIME AS [Время завершения]
FROM NEW{0}
```

---

## Условные фильтры во входных данных

Входные фильтры можно делать условными с помощью конструкции `IF {условие}`.

**Синонимы для поиска:** условный фильтр, IF в SQL, is_get_context, входной фильтр

```eme-sql
'Условный фильтр — работает только при запуске из отчёта'
IF {is_get_context("Report") != ""}
    SKIP * BY "qs_skip_rel_time_outside" WITH
    {is_edit_data("Report.StartDate") + is_edit_data("Report.StartTime") < Дата отгрузки заказа # Время отгрузки заказа < is_edit_data("Report.EndDate") + is_edit_data("Report.EndTime") < 0}
```

---

## Работа с внешними базами данных (ExternalDB)

Класс `ExternalDB` позволяет подключаться к внешним базам данных (PostgreSQL и др.) из EME-L для обмена данными с внешними системами.

**Синонимы для поиска:** ExternalDB, PostgreSQL, внешняя база, ODBC, Connect, Execute, CreateTable, InsertLines

### Основные методы ExternalDB

| Метод | Описание |
|-------|----------|
| `Connect(ConnectionString)` | Подключение к внешней БД. Возвращает пустую строку при успехе или описание ошибки |
| `CreateTable(TableName, Query)` | Создание таблицы в внешней БД по структуре запроса |
| `InsertLines(TableName, Query)` | Вставка строк из запроса в таблицу внешней БД |
| `Execute(SQL)` | Выполнение произвольного SQL-запроса |
| `UpdateLine(TableName, Query)` | Обновление строки в таблице внешней БД |
| `InsertLine(TableName, Query)` | Добавление одной строки в таблицу |

### Пример подключения к PostgreSQL

```eme-l
'Подключение к PostgreSQL из EME-L'
ExternalDB = Object("ExternalDB");

Error = ExternalDB.Connect("ODBC:Driver={PostgreSQL ANSI(x64)};Server=localhost;Database=TEST;Uid=postgres;Pwd=***;");

If (Error != "")
    is_message("Ошибка", "Connect\n\n" + Error, "OK", "STOP");
    Return;
End If

'Создание таблицы'
Error = ExternalDB.CreateTable("test", Query);

If (Error != "")
    is_message("Ошибка", "CreateTable\n\n" + Error, "OK", "STOP");
    Return;
End If

'Вставка данных'
Error = ExternalDB.InsertLines("test", Query);

If (Error != "")
    is_message("Ошибка", "InsertLines\n\n" + Error, "OK", "STOP");
    Return;
End If

is_message("Успех", "OK", "OK", "EXCLAMATION");
```

**Важно:** Имена баз данных в PostgreSQL чувствительны к регистру. Название БД `TEST` и `test` — разные базы.

---

## dsQUERY и блочная запись

Источник данных `dsQUERY` работает с запросом в памяти, в отличие от `dsDB` (работа с БД) и `dsDIALOG` (работа с диалогами).

**Синонимы для поиска:** dsQUERY, Query, блочная запись, ConnectFields, DisconnectFields, SetQuery, оптимизация импорта

### Использование dsQUERY для подготовки данных вне транзакции

```eme-l
'Создаём запрос в памяти'
m_QueryHeader = Object("Query");
m_QueryHeader.Create();

'Добавляем поля'
m_QueryHeader.AddREF("@", q_PO.GetRecordName());
m_QueryHeader.AddField(q_PO.GetFld("ID"));
m_QueryHeader.AddField(q_PO.GetRegNoFld());
m_QueryHeader.AddField(q_PO.GetFld("Date"));
m_QueryHeader.AddField(q_PO.GetFld("Time"));

'Указываем, что объект работает с запросом'
q_PO = Object("dsDB", "PurchaseOrder");
q_PO.SetQuery(m_QueryHeader);

'Теперь AppendAndSetLine работает с запросом, а не с БД'
q_PO.AppendAndSetLine();
q_PO.Put("ID", GetHeaderData("id"));
q_PO.PutOrderNo(GetHeaderData("po_reg_no"));
```

### Блочная запись в БД через ConnectFields

```eme-l
'Связываем поля запроса с полями записи БД'
r_PO = Object("dsDB", "PurchaseOrder");

m_QueryHeader.ConnectFields(r_PO.GetFld("ID"));
m_QueryHeader.ConnectFields(r_PO.GetRegNoFld());
m_QueryHeader.ConnectFields(r_PO.GetFld("Date"));
m_QueryHeader.ConnectFields(r_PO.GetFld("Time"));

'Блочная запись'
m_QueryHeader.Sort("@");
Loop (m_QueryHeader)
{
    r_PO.SetLine(m_QueryHeader.Get("@"));
    m_QueryHeader.PutConnectData(m_QueryHeader.GetLine());
End Loop

'Сбрасываем буферы в БД — ОБЯЗАТЕЛЬНО!'
m_QueryHeader.DisconnectFields();
```

**Важно:** Вызов `DisconnectFields()` строго обязателен — он сбрасывает остатки буферов в БД.

**Ограничения блочной записи:** поля переменной длины и признаки нужно записывать отдельно в цикле.

---

## Связанные разделы

- [EME-L Language Basics](./EMEL_Language_Basics.md) — основы языка EME-L
- [Working with Records in EME-L](./EMEL_Working_with_Records.md) — работа с записями БД
- [Dialogs and Reports in EME-L](./EMEL_Dialogs_Reports.md) — диалоги и отчёты