# EME-L константы, is-функции и дополнительные возможности

<!-- Синонимы для поиска: константы EME-L, is-функции, битовые операции, контекст, типы документов, статусы заказов, типы приказов, зоны склада, фабрика классов, тестирование, заливка проекта, DBWatch, дозор базы данных -->

## EME-L константы

<!-- Синонимы: константы, constants, TRUE, FALSE, NULL_REF, NEW_LINE_MARK, NULL_RECORD -->

В языке программирования EME-L представлены константы, которые подразделяются на 13 групп.

### Логические константы и константы записей/полей

| Константа | Значение | Описание |
|-----------|----------|----------|
| `TRUE` | -1 | Истинное значение |
| `FALSE` | 0 | Ложь (возвращаемое значение по умолчанию) |
| `EMPTY` | - | Пустое значение |
| `NULL_REF` | -1 | NULL-ссылка на строку записи БД |
| `NEW_LINE_MARK` | 2000000000 | Маркер новой строки (ещё не заполненной) |
| `NULL_RECORD` | -100 | Несуществующая запись в БД |
| `NULL_FIELD` | -100 | Несуществующее поле в записи БД |

### Константы типов данных JSON

| Константа | Значение | Описание |
|-----------|----------|----------|
| `JSON_NODE_TYPE_UNKNOWN` | 0 | Неизвестный тип |
| `JSON_NODE_TYPE_NULL` | 1 | null |
| `JSON_NODE_TYPE_BOOLEAN` | 2 | Логический тип |
| `JSON_NODE_TYPE_STRING` | 3 | Строка |
| `JSON_NODE_TYPE_INTEGER` | 4 | Целое число |
| `JSON_NODE_TYPE_DOUBLE` | 5 | Число с плавающей точкой |
| `JSON_NODE_TYPE_ARRAY` | 6 | Массив "[]" |
| `JSON_NODE_TYPE_OBJECT` | 7 | Объект "{}" |

### Константы типов документов

| Константа | Значение | Описание |
|-----------|----------|----------|
| `DOCUMENT_TYPE_INCOMG` | 0 | Приход |
| `DOCUMENT_TYPE_MOVPAL` | 1 | Перемещение |
| `DOCUMENT_TYPE_MOVCEL` | 2 | Перемещение внутри регистра |
| `DOCUMENT_TYPE_CHANGE` | 3 | Изменение характеристик |
| `DOCUMENT_TYPE_OFFWRI` | 4 | Списание |
| `DOCUMENT_TYPE_OUTPPU` | 5 | Уход стандартный |
| `DOCUMENT_TYPE_OUTTRN` | 6 | Уход в транзит |
| `DOCUMENT_TYPE_INCTRN` | 7 | Приход из транзита |
| `DOCUMENT_TYPE_INVOFF` | 8 | Списание в инвентаризации |
| `DOCUMENT_TYPE_AUTOFF` | 9 | Списание по уходу |
| `DOCUMENT_TYPE_AUTMOV` | 10 | Перемещение по уходу |
| `DOCUMENT_TYPE_INVINC` | 11 | Инвентаризация |
| `DOCUMENT_TYPE_AUTCHS` | 12 | Автоматическое изменение статуса |
| `DOCUMENT_TYPE_REPACK` | 13 | Переупаковка |
| `DOCUMENT_TYPE_MOVMIS` | 14 | Перемещение недостачи |

### Константы статусов документов

| Константа | Значение | Описание |
|-----------|----------|----------|
| `docsNew` | 0 | Новый |
| `docsCreated` | 1 | Присвоен номер |
| `docsFilled` | 2 | Заполнен |
| `docsLoading` | 3 | Обрабатывается |
| `docsLoaded` | 4 | Все строки обработаны |
| `docsClosed` | 5 | Закрыт |
| `docsExporting` | 6 | Экспортируется |
| `docsExportPutOff` | 7 | Экспорт отложен |
| `docsPutAwayed` | 8 | Заполняется |

### Константы статусов заказов

| Константа | Значение | Описание |
|-----------|----------|----------|
| `osNew` | 0 | Новый |
| `osEdited` | 25 | Редактируется |
| `osConsolidated` | 37 | Консолидирован |
| `osIntroduced` | 50 | Введен |
| `osCreated` | 100 | В пачке |
| `osCalculated` | 200 | Просчитан |
| `osMovingToPicking` | 300 | Перемещается в пикинг/ОА |
| `osInAssembling` | 400 | В подборке |
| `osPartlyAssembled` | 450 | Частично подобран |
| `osAssembled` | 500 | Подобран |
| `osPacked` | 525 | Упакован |
| `osPartlyShipped` | 550 | Частично отгружен |
| `osHasLeftWarehouse` | 600 | Ушел со склада |
| `osDeclined` | 700 | Отклонен |
| `osExporting` | 800 | Экспортируется |
| `osExportPutOff` | 900 | Экспорт отложен |
| `osPreserved` | 1000 | Резерв |
| `osPreserveClosed` | 1100 | Резерв закрыт |
| `osConfirmed` | 1200 | Подтвержден |
| `osFinished` | 1400 | Закрыт |

### Константы типов приказов

| Константа | Значение | Описание |
|-----------|----------|----------|
| `MO_TYPE_INCOMING` | 0 | Размещение |
| `MO_TYPE_MOVING` | 1 | Перемещение |
| `MO_TYPE_ORDER_MOVING` | 2 | Перемещение по заказу |
| `MO_TYPE_PICKING_ADD` | 3 | Пополнение пикинга |
| `MO_TYPE_PPU` | 4 | Подборка из пикинга |
| `MO_TYPE_OFFWRITE` | 5 | Списание |
| `MO_TYPE_DOCUMENT_INCOMING_FOR_SCAN` | 6 | Приёмка |
| `MO_TYPE_DOCUMENT_INVENTORY` | 7 | Инвентаризация |
| `MO_TYPE_ORDER_OUTCOME` | 8 | Отгрузка заказа |
| `MO_TYPE_REPACKING` | 9 | Переупаковка |
| `MO_TYPE_ORDER_IN` | 10 | Ввод строк в заказ |
| `MO_TYPE_MOVING_IN` | 11 | Ввод строк в перемещение |
| `MO_TYPE_SHIPPING_PLACE_IN` | 12 | Приемка грузовых мест |
| `MO_TYPE_MOVING_FROM_OA` | 13 | Перемещение из ОА |
| `MO_TYPE_PACKER_ORDER` | 14 | Упаковка заказа |
| `MO_TYPE_COLLECTIONPACKER_ORDER` | 15 | Комплектовка заказа |
| `MO_TYPE_MOVING_FROM_PACKER_OA` | 16 | Перемещение из зоны упаковки в ОА |
| `MO_TYPE_MOVING_TO_OA` | 17 | Перемещение в ОА |
| `MO_TYPE_WEIGHTMETER` | 18 | Измерение товаров |
| `MO_TYPE_EXPEDITION` | 19 | Экспедиция |
| `MO_TYPE_EGAIS_ACT_TO_BALANCE` | 20 | Добавление марок в ЕГАИС |
| `MO_TYPE_COLLECTIONPACKER_SHIPMENT` | 21 | Комплектовка перевозки |
| `MO_TYPE_OPERATOR` | 22 | Задание оператору БД |
| `MO_TYPE_AGGREGATION` | 23 | Агрегация в приёмке |
| `MO_TYPE_PICK_BY_LINE` | 24 | Раскладка Пикбайлайн |
| `MO_TYPE_MEASUREMENT` | 25 | Измерение ВГХ |
| `MO_TYPE_PALLET_MERGE` | 26 | Консолидация паллет |
| `MO_TYPE_STOCK_AGGREGATION` | 27 | Агрегация грузовых мест |
| `MO_TYPE_CONTROL` | 28 | Контроль заказа |
| `MO_MAX_TYPES` | 29 | Максимальный тип приказа |

### Константы видов использования ТСД

| Константа | Значение | Описание |
|-----------|----------|----------|
| `USED_BY_NONE` | 0 | Неопределено |
| `USED_BY_LOADER` | 1 | Водитель погрузчика |
| `USED_BY_STAFF_LOADER` | 2 | Грузчик в пикинге |
| `USED_BY_STOCKMAN_INCOME` | 3 | Кладовщик в приходе |
| `USED_BY_STOCKMAN_OUTCOME` | 4 | Кладовщик в уходе |
| `USED_BY_PACKER_ORDER` | 5 | Упаковщик заказа |
| `USED_BY_EXPEDITOR` | 6 | Экспедитор |
| `USED_BY_PICKBYLINE` | 7 | Грузчик в Пикбайлайн |
| `USED_BY_OPERATOR` | 8 | Оператор БД |
| `USED_BY_ALL` | 9 | Все |

### Константы типов ячеек

| Константа | Значение | Описание |
|-----------|----------|----------|
| `CELL_TYPE_DRIVE_IN` | 1 | Сложный (набивной стеллаж) |
| `CELL_TYPE_SELECTIVE` | 2 | Обычный (фронтальный стеллаж) |
| `CELL_TYPE_PICKING` | 3 | Ячейка пикинга |
| `CELL_TYPE_OA` | 4 | Ячейка ОА (зона готовых заказов) |
| `CELL_TYPE_COLUMN` | 5 | Столбиковая Pallet-Cage ячейка |
| `CELL_TYPE_NONADDR` | 9 | Безадресная ячейка |

### Константы функциональных зон склада

| Константа | Значение | Описание |
|-----------|----------|----------|
| `ZONE_STORAGE` | 0 | Зона хранения |
| `ZONE_ACCEPTION` | 1 | Зона приёмки |
| `ZONE_PIKING` | 2 | Зона пикинга |
| `ZONE_COMPLETE` | 3 | Зона комплектации |
| `ZONE_ORDERS_ASSEMBLED` | 4 | Зона готовых заказов |
| `ZONE_SHIPPING` | 5 | Зона отгрузки |
| `ZONE_DEFECT` | 6 | Зона брака |
| `ZONE_PIECE_PIKING` | 7 | Зона штучного пикинга |
| `ZONE_FABRICATION` | 8 | Зона производства |
| `ZONE_LONGTERMSTORAGE` | 9 | Зона длительного хранения |
| `ZONE_PIKING_A` | 10 | Зона пикинга группы А |
| `ZONE_STORAGE_AREA` | 11 | Зона накопителя Пикбайлайн |
| `ZONE_PICKBYLINE_AREA` | 12 | Зона Пикбайлайн |
| `ZONE_GATE_AREA` | 13 | Зона ворот |
| `ZONE_PACKAGING` | 14 | Зона переборки |
| `ZONE_OFF_BALANCE` | 15 | Забалансовая зона |

### Константы статусов перевозок

| Константа | Значение | Описание |
|-----------|----------|----------|
| `DELIVERY_STATUS_CREATED` | 0 | Перевозка создана |
| `DELIVERY_STATUS_IN_TRANSIT` | 1 | Перевозка в пути |
| `DELIVERY_STATUS_ARRIVED` | 2 | Идет выгрузка |
| `DELIVERY_STATUS_FINISHED` | 3 | Перевозка завершена |
| `DELIVERY_STATUS_HAS_DEFECT` | 4 | Обнаружен дефект |

### Константы пола сотрудников

| Константа | Значение | Описание |
|-----------|----------|----------|
| `GENDER_TYPE_MALE` | 1 | Мужской пол |
| `GENDER_TYPE_FEMALE` | 2 | Женский пол |

---

## is-функции в EME-L

<!-- Синонимы: is-функции, is_bit, is_and, is_or, is_hewed, is_browse, is_context, битовые операции, работа с битами -->

### is-функции группы Bit

is-функции группы Bit отвечают за работу с битовыми признаками в EME-L.

| Функция | Описание |
|---------|----------|
| `is_bit(Field, BitNo)` | Проверяет, установлен ли бит. Возвращает TRUE/FALSE |
| `is_set_bit(Field, BitNo)` | Устанавливает бит. Возвращает обновлённое поле |
| `is_clear_bit(Field, BitNo)` | Снимает бит. Возвращает обновлённое поле |
| `is_reverse_bit(Field, BitNo)` | Инвертирует бит. Возвращает обновлённое поле |
| `is_and(...)` | Побитовое "И". Принимает любое количество параметров |
| `is_or(...)` | Побитовое "ИЛИ". Принимает любое количество параметров |
| `is_xor(...)` | Побитовое "Исключающее ИЛИ" |
| `is_not(Field)` | Унарное "НЕ" |
| `is_mask(...)` | Формирует побитовую маску |
| `is_shift_left(Field, Shift)` | Сдвиг влево |
| `is_shift_right(Field, Shift)` | Сдвиг вправо |

```EME-L
'Пример работы с битами'
flags = 0;
flags = is_set_bit(flags, 0);    ' Установить бит 0 '
flags = is_set_bit(flags, 3);    ' Установить бит 3 '

If (is_bit(flags, 0))
    'Бит 0 установлен'
End If

mask = is_mask(0, 1, 2);  ' Маска из битов 0, 1, 2 '
result = is_and(flags, mask);
```

### is-функции группы Browser

is-функции группы Browser отвечают за работу с браузерами в EME-L.

| Функция | Описание |
|---------|----------|
| `is_hewed(Var, Line, Obj)` | Проверяет выделение строки в браузере |
| `is_hew_item(Var, Line, Obj)` | Аналогично is_hewed |
| `is_empty_hew(Var, Obj)` | Проверяет отсутствие выделения |
| `is_browse(Browser, ...)` | Возвращает номер выбранной строки |
| `is_browse_data(Column, Line)` | Возвращает содержимое колонки |
| `is_browser_base_record(TemplateLine)` | Возвращает номер базовой записи браузера |
| `is_run_browse(RecordNo)` | Запускает браузер записи БД |
| `is_keydown_flag()` | Считывает нажатие клавиши |

### is-функции группы Context

is-функции группы Context отвечают за работу с текущим контекстом EME БД.

| Функция | Описание |
|---------|----------|
| `is_put_context(Name, Param)` | Формирует контекст программы |
| `is_get_context_as_string(Name)` | Получает параметр контекста как строку |
| `is_get_context_as_ref(Name)` | Получает параметр контекста как число |
| `is_del_context(Name)` | Удаляет ячейку контекста |
| `is_check_context(Type, Name)` | Проверяет текущий контекст |
| `is_get_context(Type)` | Получает имя ресурса из контекста |

```EME-L
'Пример работы с контекстом'
is_put_context("SelectedGoods", goodsRef);
savedRef = is_get_context_as_ref("SelectedGoods");

If (is_check_context("Dialog", "GoodsItem.Dialog"))
    'Контекст — диалог GoodsItem.Dialog'
End If
```

---

## Дозор базы данных (DBWatch)

<!-- Синонимы: дозор БД, DBWatch, наблюдение за изменениями, is_db_watch, проверка изменений -->

### Назначение

Иногда с целью сокращения времени в транзакции требуется выполнить "трудоемкие" предварительные процедуры до открытия транзакции. Но данные при входе в транзакцию могут измениться. Для решения этой проблемы в ядре EME.WMS реализован механизм наблюдений за изменениями в базе данных.

### Использование

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

'Завершаем дозор - больше информация об изменяемых объектах не собирается'
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

### Пример реализации

```EME-L
'Дозор базы данных'
m_DBWatch = Object("DBWatch");
m_TrueSign = False;
m_Line = 0;

'Подготовка данных вне транзакции'
OnPrepare()
{
    r_GoodsItem = Object("dsDB", "GoodsItem");
    r_GoodsItem.SetFirstLine();
    m_TrueSign = False;
    If (r_GoodsItem.IsValidLine() & ~r_GoodsItem.IsTrueSign())
        m_TrueSign = True;
        m_Line = r_GoodsItem.GetLine();
    End If
    'Добавляем поле в наблюдение за изменениями'
    m_DBWatch.WatchFieldLine(r_GoodsItem.GetGlobalFlagsThreeFld());
}

'Запись внутри транзакции'
OnChange()
{
    If (m_TrueSign)
        r_GoodsItem = Object("dsDB", "GoodsItem");
        r_GoodsItem.SetLine(m_Line);
        r_GoodsItem.SetTrueSign();
    End If
}
```

---

## Тестирование в EME.WMS

<!-- Синонимы: тестирование, тест интерфейса, робот-тестировщик, is_ui_test_mode, тест диалогов -->

### Тест интерфейса диалогов

В EME БД имеется модуль, который проверяет работоспособность всех диалогов в программе путём их последовательного открытия. Запускается по пунктам меню "Система -> Ресурсы -> Тестирование -> Тест интерфейса".

Проверка всех диалогов занимает примерно три минуты.

Во время проведения теста не нажимаются кнопки и не закрываются окна сообщений, поэтому программа должна пройти тест без ручного вмешательства. Режим тестирования можно проверить функцией `is_ui_test_mode()`.

```EME-L
Dialog_OnLoad()
{
    If (ContextFindParamsRec.GetNoOfLines() == 0 & ~is_ui_test_mode())
        If (is_message(tr("Внимание"), tr("Создать настройки поиска?"), "YESNO", "QUESTION") == "YES")
            CreateDefault();
        End If
    End If
}
```

### Роботы-тестировщики

Робота-тестировщика можно запустить по пунктам меню "Система -> Ресурсы -> Тестирование -> Робот". База данных должна быть запущена в моно-режиме.

Для добавления нового диалога для тестирования роботом:
1. Создать новый класс
2. Реализовать метод `RobotBatchGo_RST(Robot AS "CIRobot")`
3. Прописать класс в конструкторе класса `AllRobots`

Требования к классам:
- Класс тестирования классификаторов — наследуется от `GuideRobots`
- Класс тестирования операций — наследуется от `OperationRobots`
- Класс тестирования отчётов — наследуется от `ReportsRobots`

---

## Диалог "Заливка проекта"

<!-- Синонимы: заливка проекта, контроль версий, TortoiseSVN, совместная разработка, экспорт изменений -->

### Назначение

Диалог "Заливка проекта" предназначен для контроля совместной разработки проекта группой программистов. Доступен по пунктам меню "Утилиты БД -> Заливка проекта -> Заливка изменений". Любые изменения объектов ЕМЕ БД разрешены только в моно-режиме.

### Основные функции

- **Базовая выгрузка** — фиксирует текущее состояние всех объектов ЕМЕ БД
- **Строки заливки** — просмотр истории всех изменений
- **Сравнить проект с базовой выгрузкой** — просмотр собственных изменений
- **Заливка в сеть** — загрузка изменений в сетевую версию
- **Откат изменений** — восстановление состояния

### Рекомендации

Перед заливкой желательно запустить базу с ключом `/nodllalign`, иначе произойдет выравнивание с неактуальными DLL с сервера.

---

## Фабрика классов C++

<!-- Синонимы: фабрика классов, C++, MetC_WMS, MetC_Sys, p_wms.h, INNER_DECLARE, DECLARE_FLD, DECLARE_REF -->

### Назначение

Фабрика классов используется для реализации различных механизмов доступа к записям ЕМЕ БД. Классы программируются на C++ и являются частью компонентов метапроекта MetC_WMS и MetC_Sys.

### Объявление класса

```C++
BEGIN_DECLARE_INNER(CRecDepartament)
    BEGIN_ENUM
        Employees, EmployeesRef, Name, VacanciesCnt
    END_ENUM
    
    DECLARE_FLD(CString, Name)
    DECLARE_REF(CRecEmployees, Employees)
    
    DECLARE_FN0(bool, OnComplete, A)
    DECLARE_FN2(CString, CreateNewTTNNumber, A, CString, csTypeTTN, CString, csPrefix)
END_DECLARE_INNER(CRecDepartament, System)
```

### Реализация класса

```C++
IMPLEMENT_INNER_CREATOR(CRecDepartament, System)
BEGIN_IMPLEMENT_INNER(CRecDepartament)
    IMPLEMENT_FLD(Employees, "Сотрудники", "Краткое описание")
    IMPLEMENT_FNC(CreateNewTTNNumber, A, "Описание функции")
END_IMPLEMENT_INNER(CRecDepartament)
```

---

## Обработчики бандов отчёта

<!-- Синонимы: обработчики бандов, OnNewItem, OnSkip, банд отчёта, полосы отчёта -->

### OnNewItem для банда отчёта

Метод вызывается в момент создания банда.

```EME-L
BandLines_OnNewItem()
{
    'Банд выводит не более 30 строк за раз'
    For (Line = 0; Line < 30; Line = Line + 1)
        If (CurrentQueryLine >= Query.GetNoOfLines())
            break;
        End If
        is_create_band_line(CurrentQueryLine);
        CurrentQueryLine = CurrentQueryLine + 1;
    End For
}
```

### OnSkip для банда отчёта

Метод вызывается при генерации строк банда. Предназначен для фильтрации строк.

```EME-L
Band_OnSkip()
{
    Control = Object("Control");
    ClientLinesRecord = Object("dsDB", "ClientLines");
    If (ClientLinesRecord.GetRecord() == PDRecordColumn.GetFromRow(PersFileLine))
        ClientLinesRecord.SetLine(PDLineColumn.GetFromRow(PersFileLine));
        If (ClientLinesRecord.IsAccepted())
            Control.SetColorBk(is_RGB(127, 127, 255));
        End If
    End If
    Return False;
}
```

---

## Обработчики отчётов

### Report_OnBeginReport()

Вызывается перед формированием отчёта.

```EME-L
Report_OnBeginReport()
{
    is_do_wait_cursor(1);
    Object("System").InitQueryCache();
    Query = Object("Query");
    
    If (Query.GetNoOfLines() == 0)
        is_message("Внимание", "Данные не найдены", 1, 1);
        Return True;
    End If
}
```

### Report_OnEndReport()

Вызывается после формирования отчёта.

```EME-L
Report_OnEndReport()
{
    Query.Delete();
    Object("System").ClearQueryCache();
    is_do_wait_cursor(-1);
}
```

### Report_OnCloseReport()

Вызывается при закрытии окна отчёта.

```EME-L
Report_OnCloseReport()
{
    ReportShow = False;
}
```

---

## Обработчики элементов отчёта

### OnPut для элемента отчёта

```EME-L
Client_OnPut()
{
    MatrixRec = Object("dsDB", "Матрица заказа");
    MatrixRec.SetLine(is_report_line());
    If (MatrixRec.IsValidLine())
        Object("Control").Put(MatrixRec.GetClient().GetName());
    End If
}
```

### OnCommand для элемента отчёта

```EME-L
MU_OnCommand()
{
    GoodsItemRef = is_cell_data("Report.GoodsItemRef");
    MUArray = Object("Array");
    
    r_MU = Object("dsDB", "Единицы измерения товара");
    r_MU.SetChain("@Товарная единица", GoodsItemRef);
    For (r_MU.SetFirstLine(); r_MU.IsValidLine(); r_MU.SetNextLine())
        MUArray.Add(r_MU.GetName());
    End For
    
    Menu = Object("Menu");
    Menu.CreatePopupMenu();
    For (i = 0; i < MUArray.GetSize(); i = i + 1)
        Menu.AppendMenu("STRING", i, MUArray.GetAt(i));
    End For
    
    SelItem = Menu.TrackPopupMenu(Rect.GetLeft(), Rect.GetBottom());
    If (SelItem >= 0)
        is_put_cell_data("Report.MU", MUArray.GetAt(SelItem));
    End If
}
```

### OnFillGraphWindow для графического слайда

```EME-L
GraphCell_OnFillGraphWindow()
{
    GC = Object("GraphCell");
    
    Months = Object("Array");
    Months.Add("янв"); Months.Add("фев"); Months.Add("мар");
    Months.Add("апр"); Months.Add("май"); Months.Add("июн");
    Months.Add("июл"); Months.Add("авг"); Months.Add("сен");
    Months.Add("окт"); Months.Add("ноя"); Months.Add("дек");
    
    CurrMonth = is_month(is_dos_date());
    For (y = 0; y < 2; y = y + 1)
        For (x = 0; x < 6; x = x + 1)
            Rect = GC.CreateRectangle(x * 30, y * 20, 30, 20);
            Text = Rect.CreateText(Months.GetAt(y * 6 + x));
            If (CurrMonth == 6 * y + x)
                Rect.SetBackgroundColor(224, 224, 224);
                GC.CreateCross(x * 30, y * 20, 30, 20);
            End If
        End For
    End For
    
    GC.SetAnisotropicMode();
}
```

---

## Мультипротоколы EMEL для экспорта в несколько ERP

<!-- Синонимы: мультипротоколы, экспорт в несколько ERP, RegistryExportProtocolEMEL, протокол экспорта, независимый экспорт, EMEL_SAP, EMEL_1C -->

### Назначение

Иногда требуется экспортировать документы или заказы в несколько независимых друг от друга систем ERP. Сводить этот экспорт в единый обработчик протокола EMEL крайне неудобно: проблемы с экспортом в одну систему ERP косвенно приводят к проблемам с экспортом в другую систему ERP, так как документ или заказ остаётся в очереди.

Для решения этих проблем служат мультипротоколы EMEL, которые обеспечивают независимый экспорт в разные ERP.

### Регистрация дополнительного протокола экспорта EMEL

```EME-L
' Регистрация дополнительного протокола экспорта EMEL с постфиксом "_SAP"
RegistryExportProtocolEMELExtra()
{
    RegistryExportProtocolEMEL("_SAP");
}
```

После регистрации появляется новый тип экспорта "EMEL_SAP".

### Проверка наличия типов экспорта

После регистрации дополнительного протокола в системе появляются:
- Стандартный тип экспорта "EMEL"
- Дополнительный тип экспорта "EMEL_SAP"

### Настройка обработчиков EME-L

В карточке организации для дополнительных соответствий прописываются свои обработчики EME-L с постфиксом:

| Стандартный протокол | Дополнительный протокол |
|---------------------|------------------------|
| RUN_EMEL_CLOSE_DOCUMENT | RUN_EMEL_CLOSE_DOCUMENT_SAP |
| RUN_EMEL_NEXT_ORDER_STATUS | RUN_EMEL_NEXT_ORDER_STATUS_SAP |

### Проверка выгрузки по нескольким протоколам

В очереди документов проверяется выгрузка по двум протоколам:
- "EMEL" — стандартный протокол
- "EMEL_SAP" — дополнительный протокол

Каждый протокол работает независимо: ошибки в одном не влияют на другой.

### Преимущества мультипротоколов

1. **Независимость** — проблемы с экспортом в одну ERP не влияют на экспорт в другую
2. **Разделение очередей** — каждый протокол имеет свою очередь
3. **Гибкость** — можно настраивать разные обработчики для разных ERP
4. **Масштабируемость** — можно регистрировать любое количество протоколов

### Пример использования

```EME-L
' Регистрация протоколов для нескольких ERP систем
RegistryExportProtocols()
{
    ' Стандартный протокол (регистрируется автоматически)
    ' EMEL
    
    ' Протокол для SAP
    RegistryExportProtocolEMEL("_SAP");
    
    ' Протокол для 1С
    RegistryExportProtocolEMEL("_1C");
}
```

После регистрации будут доступны типы экспорта:
- EMEL
- EMEL_SAP
- EMEL_1C

---

## См. также

- [EMEL_Language_Basics.md](EMEL_Language_Basics.md) — основы языка EME-L
- [EMEL_EventHandlers.md](EMEL_EventHandlers.md) — обработчики событий
- [EMEL_Classes_Utility.md](EMEL_Classes_Utility.md) — утилитарные классы