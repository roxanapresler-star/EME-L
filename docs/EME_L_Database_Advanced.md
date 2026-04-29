# Дозор базы данных и расширенные операции в EME-L

> Синонимы для поиска: DBWatch дозор БД, is_db_watch наблюдение за изменениями, SetSkipMode фильтрация записей, транзакция EME-L, is_transaction, предварительная обработка данных, MustBeEQ MustBeGE MustBeLE, BitBuffer фильтрация, оптимизация транзакций EME.WMS, дозор базы данных, Check() DBWatch, WatchFieldLine, CEMERec SetSkipMode, SetChainMode

## Дозор базы данных (DBWatch) в языке EME-L

Иногда с целью сокращения времени в транзакции требуется выполнить «трудоёмкие» предварительные процедуры до открытия транзакции. Но при этом возникает проблема: данные при входе в транзакцию могут измениться.

Для решения этой проблемы в ядре EME.WMS реализован механизм наблюдений за изменениями в базе данных — **дозор базы данных**. Механизм основан на сохранении информации об изменениях в записях, полях, строках при разворачивании транзакций. в системе EME.WMS дозор БД является стандартным паттерном для оптимизации транзакций.

### Общая схема дозора базы данных

```EME-L
    'Начинаем дозор (до чтения данных БД)'
    'is_db_watch поднимет данные на верхний уровень, чтобы не пропустить фоновые транзакции'
    is_db_watch(1);

    'Проводим подготовку данных'
    If (is_workstation_mode() != "MONO")
        OnPrepare();
    End If

    'Открываем транзакцию'
    is_transaction(1, "Устанавливаем ЧЗ");

    'Завершаем дозор — больше информация об изменяемых объектах не собирается'
    is_db_watch(-1);

    'Проверяем изменения и если они есть делаем подготовку заново'
    If (is_workstation_mode() == "MONO" | m_DBWatch.Check() != "")
        OnPrepare();
    End If

    'Производим изменения в БД на основе ранее подготовленных данных'
    OnChange();

    'Закрываем транзакцию'
    is_transaction(-1);
```

### Пример реализации дозора базы данных в EME-L

Рассмотрим пример — нужно установить у товара признак Честного знака, если он по каким-то причинам не был установлен.

**Создание объекта DBWatch в конструкторе класса:**

```EME-L
'Дозор базы данных'
m_DBWatch = Object("DBWatch");
m_TrueSign;
m_Line;
```

Сама процедура подразделяется на две функциональные части: подготовка данных и запись данных.

**Подготовка данных вне транзакции:**

```EME-L
OnPrepare()
{
    r_GoodsItem = Object("dsDB", "GoodsItem");
    r_GoodsItem.SetFirstLine();
    m_TrueSign = False;
    If (r_GoodsItem.IsValidLine() & ~r_GoodsItem.IsTrueSign())
        m_TrueSign = True;
        m_Line = r_GoodsItem.GetLine();
    End If
    'Добавляем поле GlobalFlagsThreeFld на текущей строке в наблюдение за изменениями'
    m_DBWatch.WatchFieldLine(r_GoodsItem.GetGlobalFlagsThreeFld());
}
```

**Запись данных внутри транзакции:**

```EME-L
OnChange()
{
    If (m_TrueSign)
        r_GoodsItem = Object("dsDB", "GoodsItem");
        r_GoodsItem.SetLine(m_Line);
        r_GoodsItem.SetTrueSign();
    End If
}
```

в языке EME-L объект `DBWatch` отслеживает изменения в конкретных полях записей. Метод `WatchFieldLine()` добавляет поле на наблюдение, а метод `Check()` возвращает непустую строку, если были обнаружены изменения.

## Фильтрация данных с помощью SetSkipMode в EME-L

Метод `SetSkipMode()` у объекта записи (`CEMERec`) в языке EME-L устанавливает режим фильтрации, при котором из перебора строк исключаются те, что не удовлетворяют заданным условиям. в системе EME.WMS это основной механизм фильтрации данных перед загрузкой в запрос.

### Методы условий фильтрации в SetSkipMode

Для формирования условий фильтрации в EME-L используются следующие методы полей:

- `MustBeEQ(значение)` — поле должно быть равно значению.
- `MustBeGE(значение)` — поле должно быть больше или равно значению.
- `MustBeLE(значение)` — поле должно быть меньше или равно значению.
- `MustBeRefEQ(значение)` — ссылка должна быть равна значению.
- `MustBeRefInBitBuffer(bb)` — ссылка должна присутствовать в битовом буфере.
- `MustBeBitSet(бит)` — в поле должен быть установлен указанный бит.
- `MustBeValid()` — строка должна быть действительной (не удалённой).

### Пример сложной фильтрации с SetSkipMode

```EME-L
'Выбор заголовков документа, подходящих под условия при помощи SkipMode'
r_Document = Object("dsDB", "Document");
r_Document.SetSkipMode();

'Формирование диапазона дат создания документов'
r_Document.GetCreateDateFld().MustBeGE(is_edit_data("ReportIncoming.StartDate"));
r_Document.GetCreateDateFld().MustBeLE(is_edit_data("ReportIncoming.EndDate"));

'Фильтрация документов по типу "Приход"'
r_Document.GetDocumentTypeRefFld().MustBeRefEQ(0);

'Загрузка подходящих документов в битовый буфер'
bbDocs = Object("BitBuffer", is_No_of_lines("Документы"));
bbDocs.LoadLines(r_Document);

'Выбор строк записи "Строки документов"'
r_DocLines = Object("dsDB", "DocLines");
r_DocLines.SetSkipMode();

'Отсечение строк документа, не ссылающихся на заголовки'
r_DocLines.GetDocumentRefFld().MustBeRefInBitBuffer(bbDocs);

'Фильтрация по выбранным товарам'
bbGI = Object("BitBuffer");
bbGI.LoadHewVariableSelection(Object("Dialog"), "Товар");

If (bbGI.GetNoOfHewedItems() > 0)
    r_DocLines.GetGoodsItemRefFld().MustBeRefInBitBuffer(bbGI);
End If

'Проверка: строка должна быть в буфере или отгружена'
r_DocLines.GetFlagsFld().MustBeBitSet(is_or(r_DocLines.GetGoodsInBufferBit(), r_DocLines.GetGoodsLoadedBit()));

'Загрузка результирующих строк в запрос'
objQuery = is_context("Query");
objQuery.LoadLines(r_DocLines);
```

## Работа с большими объёмами данных в EME-L

При обработке больших массивов данных в языке EME-L рекомендуется:

1. **Использовать `SetSkipMode()`** для предварительной фильтрации записей до загрузки в запрос — это значительно сокращает объём обрабатываемых данных.
2. **Применять дозор базы данных (DBWatch)** для вынесения трудоёмких операций за пределы транзакции.
3. **Использовать оператор `Const`** в SQL-запросах для однократного вычисления выражений, возвращающих одинаковое значение для каждой строки.
4. **Группировать данные** своевременно при помощи `GROUP BY` — это уменьшает объём памяти для хранения запроса и количество вычислений.

## Транзакции в EME-L

Транзакции в языке EME-L обеспечивают целостность данных при параллельном доступе. Открытие транзакции выполняется функцией `is_transaction(1, "Описание")`, закрытие — `is_transaction(-1)`. в системе EME.WMS все изменения в БД должны производиться внутри транзакций.

## См. также

- [EME_L_Database.md](./EME_L_Database.md) — основные классы данных Query, BitBuffer, Array, Map
- [EME_SQL_Advanced.md](./EME_SQL_Advanced.md) — SQL-запросы с оператором WITH и фильтрацией
