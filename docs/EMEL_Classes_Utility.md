# Классы Query, BitBuffer, Array, Map, String, PropertyTree в EME-L

<!-- Синонимы для поиска: запрос, битовый буфер, массив, хэш-таблица, строка, JSON, дерево свойств, Query class, BitBuffer class, Array class, Map class, String class, PropertyTree class, работа с данными, коллекции EME-L -->

## Обзор

В EME-L предоставлен набор утилитарных классов для работы с различными структурами данных. Эти классы используются для хранения, обработки и преобразования данных в памяти программы. В системе EME.WMS эти классы применяются повсеместно — от фильтрации записей до формирования JSON-ответов для внешних систем.

---

## Класс Query — работа с табличными данными

<!-- Синонимы: запрос, таблица в памяти, Query, табличные данные, фильтрация, группировка, агрегация, сортировка -->

### Назначение

Класс `Query` позволяет хранить данные в виде таблицы в памяти. Имеет встроенные средства добавления, сортировки, поиска, агрегирования и группировки полей. В EME-L запросы широко используются для промежуточного хранения данных перед отправкой в отчёты или на ТСД.

**Важное ограничение:** в запросе отсутствует возможность удаления строк — это надо учитывать при проектировании программ в EME.WMS. Если нужно "удалить" строку — добавьте специальное поле-признак и игнорируйте строки с этим признаком при обработке.

### Создание запроса

```EME-L
'Создаём пустой запрос'
qData = Object("Query");

'Создаём запрос с указанием колонок'
qData = Object("Query");
qData.AddColumn("Товар", "INTEGER");
qData.AddColumn("Количество", "REAL");
qData.AddColumn("Склад", "INTEGER");
```

### Основные методы класса Query

#### Добавление и получение данных

| Метод | Описание | Пример |
|-------|----------|--------|
| `AddColumn(Name, Type)` | Добавляет колонку в запрос | `q.AddColumn("Товар", "INTEGER");` |
| `AddLine()` | Добавляет новую строку, возвращает индекс | `row = q.AddLine();` |
| `GetNoOfLines()` | Возвращает количество строк | `count = q.GetNoOfLines();` |
| `SetData(Col, Row, Value)` | Устанавливает значение в ячейке | `q.SetData(0, row, 123);` |
| `GetData(Col, Row)` | Получает значение из ячейки | `val = q.GetData(0, row);` |
| `SetDataByName(Name, Row, Value)` | Устанавливает значение по имени колонки | `q.SetDataByName("Товар", row, 123);` |
| `GetDataByName(Name, Row)` | Получает значение по имени колонки | `val = q.GetDataByName("Товар", row);` |

#### Сортировка и поиск

| Метод | Описание |
|-------|----------|
| `Sort(Col, Ascending)` | Сортирует запрос по указанной колонке |
| `Find(Col, Value)` | Ищет значение в колонке, возвращает индекс строки |
| `FindLine(Col1, Val1, Col2, Val2, ...)` | Ищет строку по нескольким условиям |

#### Группировка и агрегация

| Метод | Описание |
|-------|----------|
| `GroupBy(Col)` | Группирует строки по указанной колонке |
| `Sum(Col)` | Возвращает сумму значений в колонке |
| `Avg(Col)` | Возвращает среднее значение |
| `Min(Col), Max(Col)` | Возвращают минимальное/максимальное значение |
| `Count()` | Возвращает количество строк |

### Пример использования Query

```EME-L
'Пример: расчёт итогов по товарам'
'Создаём запрос для хранения данных'
qTotals = Object("Query");
qTotals.AddColumn("Товар", "INTEGER");
qTotals.AddColumn("КоличествоСумма", "REAL");
qTotals.AddColumn("КоличествоСтрок", "INTEGER");

'Заполняем данными из записи БД'
r_Movements = Object("dsDB", "Movements");
For (r_Movements.SetFirstLine(); r_Movements.IsValidLine(); r_Movements.SetNextLine())
    'Ищем товар в запросе'
    row = qTotals.Find(0, r_Movements.GetGoodsItemRef());
    If (row < 0)
        'Товар не найден — добавляем новую строку'
        row = qTotals.AddLine();
        qTotals.SetData(0, row, r_Movements.GetGoodsItemRef());
        qTotals.SetData(1, row, 0);
        qTotals.SetData(2, row, 0);
    End If
    
    'Накапливаем данные'
    qTotals.SetData(1, row, qTotals.GetData(1, row) + r_Movements.GetQty());
    qTotals.SetData(2, row, qTotals.GetData(2, row) + 1);
End For

'Сортируем по количеству'
qTotals.Sort(1, False);  ' False — по убыванию'

'Выводим результат'
For (row = 0; row < qTotals.GetNoOfLines(); row = row + 1)
    r_Goods = Object("dsDB", "GoodsItem");
    r_Goods.SetLine(qTotals.GetData(0, row));
    is_message("Итог", is_format("Товар: %s, Сумма: %.2f, Строк: %d",
        r_Goods.GetName(),
        qTotals.GetData(1, row),
        qTotals.GetData(2, row)));
End For
```

### Сравнение запросов

В EME-L имеется группа методов для сравнения запросов друг с другом:

| Метод | Описание |
|-------|----------|
| `Compare(Query2)` | Сравнивает два запроса, возвращает различия |
| `IsEqual(Query2)` | Проверяет на полное равенство |

### Отладка запросов

Для отладки можно вывести данные запроса в дамп:

```EME-L
'Вывод содержимого запроса в лог'
qData.Dump();
```

---

## Класс BitBuffer — битовый буфер

<!-- Синонимы: битовый буфер, биты, BitBuffer, флаги, маска, плотное хранение, битовое поле -->

### Назначение

Класс `BitBuffer` представляет собой непрерывный массив в памяти с прямым доступом к элементам по индексу, где каждый элемент — бит. Обеспечивает быструю работу благодаря "плотному" хранению данных в EME.WMS.

**Важное предупреждение:** увеличение размера буфера в процессе его заполнения крайне не рекомендуется, поскольку это приведёт к перевыделению памяти и копированию содержимого. Сначала задайте размер, потом заполняйте.

### Создание и использование

```EME-L
'Создаём битовый буфер на 1000 элементов'
bb = Object("BitBuffer");
bb.SetSize(1000);

'Установка битов'
bb.Set(0, True);   'Установить бит 0 в 1'
bb.Set(1, False);  'Установить бит 1 в 0'

'Получение значения бита'
If (bb.Get(0))
    is_message("Инфо", "Бит 0 установлен");
End If

'Очистка всего буфера'
bb.Clear();

'Проверка на пустоту'
If (bb.IsEmpty())
    'Буфер пуст'
End If
```

### Применение в EME.WMS

Битовые буферы часто используются для:
- Хранения выделенных строк в браузерах
- Фильтрации записей
- Отслеживания обработанных элементов

```EME-L
'Пример: загрузка выделения из переменной диалога'
bbSelection = Object("BitBuffer");
bbSelection.LoadHewVariableSelection(Object("Dialog"), "Товарная единица");

'Проверка выделения строки'
For (line = 0; line < 1000; line = line + 1)
    If (bbSelection.Get(line))
        'Строка выделена — обрабатываем'
        ProcessLine(line);
    End If
End For
```

---

## Класс Array — массив

<!-- Синонимы: массив, Array, список, коллекция, индексированный доступ, динамический массив -->

### Назначение

Класс `Array` представляет собой непрерывный массив в памяти с прямым доступом к элементам по индексу. Элементы могут быть любого типа.

**Рекомендация:** не рекомендуется увеличивать размер массива параллельно с его заполнением — лучше сначала задать размер.

### Основные методы

| Метод | Описание |
|-------|----------|
| `SetSize(Size)` | Устанавливает размер массива |
| `GetNoOfLines()` | Возвращает размер массива |
| `SetAt(Index, Value)` | Устанавливает значение по индексу |
| `GetAt(Index)` | Получает значение по индексу |
| `Add(Value)` | Добавляет элемент в конец |
| `Clear()` | Очищает массив |

### Пример использования Array

```EME-L
'Создаём массив для хранения чисел'
arValues = Object("Array");
arValues.SetSize(10);

'Заполняем значениями'
For (i = 0; i < 10; i = i + 1)
    arValues.SetAt(i, i * 2);
End For

'Обрабатываем значения'
sum = 0;
For (i = 0; i < arValues.GetNoOfLines(); i = i + 1)
    sum = sum + arValues.GetAt(i);
End For

is_message("Сумма", is_format("Сумма элементов: %d", sum));
```

### Двумерные массивы (Array of Array)

```EME-L
'Создаём двумерный массив (матрица 5x5)'
arMatrix = Object("Array");
arMatrix.SetSize(5);

For (i = 0; i < 5; i = i + 1)
    arRow = Object("Array");
    arRow.SetSize(5);
    arMatrix.SetAt(i, arRow);
End For

'Заполняем значениями'
For (i = 0; i < 5; i = i + 1)
    For (j = 0; j < 5; j = j + 1)
        arMatrix.GetAt(i).SetAt(j, i * 5 + j);
    End For
End For
```

---

## Класс MultiArray — двумерный массив

<!-- Синонимы: двумерный массив, MultiArray, матрица, таблица в памяти, колонки с именами -->

### Назначение

Класс `MultiArray` — двумерный массив. Рекомендуется использовать, когда по одному индексу нужно хранить несколько значений. Колонкам второго измерения можно присвоить имена и получать по ним значения.

### Пример использования MultiArray

```EME-L
'Создаём двумерный массив'
maData = Object("MultiArray");
maData.SetSize(100);  '100 строк'
maData.AddCol("Товар", "INTEGER");
maData.AddCol("Количество", "REAL");
maData.AddCol("Комментарий", "STRING");

'Заполняем данными'
For (i = 0; i < 100; i = i + 1)
    maData.SetData("Товар", i, i);
    maData.SetData("Количество", i, i * 1.5);
    maData.SetData("Комментарий", i, is_format("Строка %d", i));
End For

'Получаем данные'
For (i = 0; i < maData.GetNoOfLines(); i = i + 1)
    goodsRef = maData.GetData("Товар", i);
    qty = maData.GetData("Количество", i);
End For
```

---

## Класс Map — хэш-таблица

<!-- Синонимы: хэш-таблица, словарь, Map, ключ-значение, поиск по ключу, ассоциативный массив -->

### Назначение

Класс `Map` представляет собой список элементов, состоящих из пары данных "ключ – значение". Доступ к значению осуществляется по ключу.

**Важно:** перед использованием класса `Map` нужно задать размер хэш-таблицы методом `SetSize()`.

**Рекомендованный размер:** 1,3 × (предполагаемое количество элементов).

**Предупреждение:** с ростом числа данных из-за возникающих коллизий появляется нелинейное замедление. Неудачный выбор размера хэш-таблицы может сильно замедлить выполнение программы в EME.WMS.

### Основные методы

| Метод | Описание |
|-------|----------|
| `SetSize(Size)` | Устанавливает размер хэш-таблицы |
| `Put(Key, Value)` | Добавляет пару ключ-значение |
| `Get(Key)` | Получает значение по ключу |
| `Remove(Key)` | Удаляет элемент по ключу |
| `Clear()` | Очищает таблицу |
| `SetFirstLine()` | Начинает итерацию по элементам |
| `SetNextLine()` | Переходит к следующему элементу |
| `IsValidLine()` | Проверяет корректность текущего элемента |
| `GetKey()` | Получает ключ текущего элемента |
| `GetValue()` | Получает значение текущего элемента |

### Пример использования Map

```EME-L
'Создаём хэш-таблицу для хранения цен товаров'
'Ожидаем около 10000 товаров, задаём размер 13000'
mPrices = Object("Map");
mPrices.SetSize(13000);

'Заполняем данными'
r_Goods = Object("dsDB", "GoodsItem");
For (r_Goods.SetFirstLine(); r_Goods.IsValidLine(); r_Goods.SetNextLine())
    'Ключ — ссылка на товар, значение — цена'
    mPrices.Put(r_Goods.GetLine(), r_Goods.GetPrice());
End For

'Ищем цену по ключу'
price = mPrices.Get(goodsRef);
If (price != EMPTY)
    'Цена найдена'
Else
    'Товар не найден в таблице'
End If

'Итерация по всем элементам'
For (mPrices.SetFirstLine(); mPrices.IsValidLine(); mPrices.SetNextLine())
    key = mPrices.GetKey();
    value = mPrices.GetValue();
    'Обрабатываем пару'
End For
```

---

## Класс String — работа со строками

<!-- Синонимы: строка, String, текст, конкатенация, поиск в строке, форматирование, преобразование строк -->

### Назначение

Класс `String` в EME-L предназначен для работы со строковыми данными. Предоставляет методы для выполнения базовых операций со строками, конвертаций/преобразований, форматирования, проверок символов, поиска данных и их редактирования.

### Создание объекта String

```EME-L
'Создание пустой строки'
csString = Object("String");

'Создание с инициализацией'
csString = Object("String", "Начальное значение");

'Задание строки после создания'
csString = Object("String");
csString.SetString("Значение");
```

### Базовые операции

| Метод | Описание | Пример |
|-------|----------|--------|
| `Add(Str)` | Конкатенация строк | `str.Add(" текст");` |
| `Compare(Str)` | Сравнение с учётом регистра | `if (str.Compare("тест"))` |
| `CompareNoCase(Str)` | Сравнение без учёта регистра | `if (str.CompareNoCase("ТЕСТ"))` |
| `GetLength()` | Длина строки | `len = str.GetLength();` |
| `IsEmpty()` | Проверка на пустоту | `if (str.IsEmpty())` |
| `IsNumericOnly()` | Проверка на число | `if (str.IsNumericOnly())` |

### Преобразования и форматирование

| Метод | Описание |
|-------|----------|
| `AsDateTime()` | Преобразует строку в дату/время |
| `AsInteger()` | Преобразует строку в целое число |
| `AsReal()` | Преобразует строку в число с плавающей точкой |
| `AsString()` | Конвертирует объект в строку |
| `Format(FormatStr, ...)` | Форматирует строку с параметрами |
| `MakeUpper()` | Преобразует в верхний регистр |
| `MakeLower()` | Преобразует в нижний регистр |
| `MakeCapitalLetter()` | Делает первую букву заглавной |
| `MakeReverse()` | Переворачивает строку |

### Примеры форматирования

```EME-L
'Форматирование с параметрами'
str = Object("String");
str.Format("Заказ %s от %s, сумма: %.2f", orderNo, date, sum);

'Использование встроенной функции is_format'
result = is_format("Товар: %s, Количество: %d", name, qty);
```

### Поиск в строке

| Метод | Описание | Возвращает |
|-------|----------|------------|
| `Find(SubStr)` | Ищет подстроку | Индекс или -1 |
| `FindNoCase(SubStr)` | Ищет без учёта регистра | Индекс или -1 |
| `FindChar(Char, Start)` | Ищет символ с позиции | Индекс или -1 |
| `ReverseFind(Char)` | Ищет с конца | Индекс или -1 |
| `FindOneOf(Chars)` | Ищет любой из символов | Индекс или -1 |

### Извлечение подстрок

| Метод | Описание |
|-------|----------|
| `Left(Count)` | Возвращает N символов слева |
| `Right(Count)` | Возвращает N символов справа |
| `Mid(Start, Count)` | Возвращает подстроку с позиции |
| `ExtractUpto(Char)` | Извлекает до указанного символа |
| `ExtractUptoString(Str)` | Извлекает до указанной подстроки |

### Редактирование строки

| Метод | Описание |
|-------|----------|
| `Replace(From, To)` | Заменяет все вхождения подстроки |
| `RemoveSymbol(Char)` | Удаляет указанный символ |
| `TrimLeft()` | Удаляет пробелы слева |
| `TrimRight()` | Удаляет пробелы справа |
| `SetAt(Index, Char)` | Заменяет символ по индексу |
| `Split(Array, Delimiter)` | Разделяет строку на массив |

### Примеры работы со строками

```EME-L
'Пример: парсинг строки с разделителями'
str = Object("String", "Товар1;100;50.5");
arParts = Object("Array");
str.Split(arParts, ";");

goodsName = arParts.GetAt(0);
quantity = arParts.GetAt(1);
price = arParts.GetAt(2);

'Пример: форматирование ФИО'
str = Object("String");
str.Format("%s %c. %c.", lastName, firstName[0], middleName[0]);

'Пример: проверка на числовое значение'
str = Object("String", "12345");
If (str.IsNumericOnly())
    num = str.AsInteger();
End If
```

### Конвертация кодировок

| Метод | Описание |
|-------|----------|
| `AnsiToOem()` | ANSI → DOS-кодировка |
| `OemToAnsi()` | DOS-кодировка → ANSI |
| `AnsiToUTF8()` | ANSI → UTF-8 |
| `UTF8ToAnsi()` | UTF-8 → ANSI |
| `AnsiToISO88595()` | ANSI → ISO88595 |

---

## Класс PropertyTree — работа с JSON

<!-- Синонимы: PropertyTree, JSON, дерево свойств, парсинг JSON, формирование JSON, иерархические данные, Boost Property Tree -->

### Назначение

Класс `PropertyTree` в EME-L предназначен для работы с древовидными структурами данных в формате JSON. Класс предоставляет методы для добавления/удаления узлов дерева, получения значений по иерархии, формирования строк/хэш-таблиц из дерева.

Широко используется в EME.WMS для:
- Обмена данными с внешними системами
- Формирования ответов HTTP-запросов
- Хранения конфигураций

### Создание объекта

```EME-L
'Создаём пустое дерево'
pt = Object("PropertyTree");
```

### Добавление данных в дерево

| Метод | Описание |
|-------|----------|
| `AddChild(Name)` | Добавляет дочерний узел, возвращает ссылку |
| `Put(Name, Value)` | Добавляет элемент в узел |
| `PutArray(Name, Array)` | Добавляет массив в узел |
| `PutMap(Name, Map)` | Добавляет хэш-таблицу в узел |
| `PutJSON(JSONString)` | Добавляет строку в формате JSON |
| `PushBack()` | Добавляет пустой узел в массив |
| `AddQuery(Query)` | Добавляет узел с данными запроса |

### Пример формирования JSON

```EME-L
'Создаём JSON для отправки на сервер'
json = Object("PropertyTree");

'Добавляем простые поля'
json.Put("version", "1.0");
json.Put("timestamp", is_now());

'Добавляем массив товаров'
goodsArray = json.AddChild("goods");
r_Goods = Object("dsDB", "GoodsItem");
For (r_Goods.SetFirstLine(); r_Goods.IsValidLine(); r_Goods.SetNextLine())
    item = goodsArray.PushBack();
    item.Put("id", r_Goods.GetLine());
    item.Put("name", r_Goods.GetName());
    item.Put("price", r_Goods.GetPrice());
End For

'Получаем JSON-строку'
jsonStr = json.GetJSON();  'По умолчанию UTF-8'
```

### Получение данных из дерева

| Метод | Описание |
|-------|----------|
| `Get(Name)` | Получает значение узла |
| `GetArray(Name, Type)` | Получает массив из узла |
| `GetMap(Name)` | Получает хэш-таблицу из узла |
| `GetJSON()` | Получает строковое представление JSON |
| `GetParams(Params)` | Получает параметры из дерева |
| `GetQuery(Query)` | Заполняет запрос данными из дерева |
| `GetKey()` | Получает имя текущего узла при итерации |

### Итерация по дереву

```EME-L
'Итерация по узлам дерева'
For (pt.SetFirstLine(); pt.IsValidLine(); pt.SetNextLine())
    key = pt.GetKey();
    value = pt.Get();
    'Обрабатываем узел'
End For
```

### Пример парсинга JSON

```EME-L
'Парсим JSON-ответ от внешней системы'
json = Object("PropertyTree");
json.PutJSON(responseString);

'Получаем поля'
status = json.Get("status");
If (status == "ok")
    'Получаем массив данных'
    items = json.AddChild("items");
    For (items.SetFirstLine(); items.IsValidLine(); items.SetNextLine())
        id = items.Get("id");
        name = items.Get("name");
        'Обрабатываем элемент'
    End For
End If
```

### Проверки дерева

| Метод | Описание |
|-------|----------|
| `IsEmpty()` | Проверяет на отсутствие узлов |
| `IsConst()` | Проверяет, является ли дерево константным |
| `IsValidLine()` | Проверяет корректность текущего узла при обходе |

### Управление деревом

| Метод | Описание |
|-------|----------|
| `Delete()` | Удаляет текущий узел |
| `Empty()` | Полностью очищает дерево |
| `SetConst(True/False)` | Делает дерево константным |

---

## dsQUERY — работа с запросом в памяти

<!-- Синонимы: dsQUERY, запрос в памяти, SetQuery, ConnectFields, DisconnectFields, блочная запись, оптимизация импорта -->

### Назначение

Источник данных `dsQUERY` работает с запросом в памяти, в отличие от `dsDB` (работа с БД) и `dsDIALOG` (работа с диалогами). Используется для подготовки данных вне транзакции (на `OnPrepare`) и последующей быстрой записи в БД блочным методом (на `OnImport`).

### Создание и заполнение запроса

```EME-L
' Создаём запрос
m_QueryHeader = Object("Query");
m_QueryHeader.Create();

' Добавляем необходимые поля
m_QueryHeader.AddREF("@", q_PO.GetRecordName());
m_QueryHeader.AddField(q_PO.GetFld("ID"));
m_QueryHeader.AddField(q_PO.GetRegNoFld());
m_QueryHeader.AddField(q_PO.GetDateFld());
m_QueryHeader.AddField(q_PO.GetTimeFld());

' Поле "@" — ссылка на строку в БД
```

### Перенаправление dsDB на запрос

```EME-L
' Указываем объекту dsDB работать с запросом
q_PO = Object("dsDB", "PurchaseOrder");
q_PO.SetQuery(m_QueryHeader);

' Теперь этот код добавит строку в запрос, а не в БД
q_PO.AppendAndSetLine();
q_PO.Put("ID", GetHeaderData("id"));
q_PO.PutOrderNo(GetHeaderData("po_reg_no"));
q_PO.PutDate(is_dos_date);
q_PO.PutTime(is_dos_date);
```

### Блочная запись (ConnectFields / DisconnectFields)

Для быстрой записи подготовленных данных в БД используется связывание полей запроса с полями записи:

```EME-L
' Связываем поля запроса с полями записи
r_PO = Object("dsDB", "PurchaseOrder");

m_QueryHeader.ConnectFields(r_PO.GetFld("ID"));
m_QueryHeader.ConnectFields(r_PO.GetRegNoFld());
m_QueryHeader.ConnectFields(r_PO.GetFld("Date"));
m_QueryHeader.ConnectFields(r_PO.GetTimeFld());

' Блочная запись
m_QueryHeader.Sort("@");
Loop (m_QueryHeader)
    r_PO.SetLine(m_QueryHeader.Get("@"));
    m_QueryHeader.PutConnectData(m_QueryHeader.GetLine());
End Loop

' Обязательно! DisconnectFields сбрасывает остатки буферов в БД
m_QueryHeader.DisconnectFields();
```

**Важно:** вызов `DisconnectFields()` строго обязателен — он сбрасывает остатки буферов в БД.

**Ограничения блочной записи:**
- Поля переменной длины и признаки не поддерживаются — их нужно записывать отдельно в цикле
- Необходимо обеспечить проверку, что критические данные не изменились между `OnPrepare` и `OnImport`

Примеры реализации: классы `ERPPoComponent` и `ERPOrdersComponent` в проекте «21-й век».

---

## Рекомендации по выбору класса

| Задача | Рекомендуемый класс |
|--------|---------------------|
| Табличные данные с группировкой | `Query` |
| Хранение флагов/выделений | `BitBuffer` |
| Простые списки | `Array` |
| Поиск по ключу | `Map` |
| Текстовые операции | `String` |
| JSON-данные | `PropertyTree` |
| Матрицы с именованными колонками | `MultiArray` |

---

## См. также

- [EME_Language_Basics.md](EME_Language_Basics.md) — основы языка EME-L
- [EMEL_SQL_Queries.md](EMEL_SQL_Queries.md) — EME-SQL запросы
- [EME_WMS_Architecture.md](EME_WMS_Architecture.md) — архитектура EME.WMS