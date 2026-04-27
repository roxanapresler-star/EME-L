# Обработчики событий в EME-L

<!-- Синонимы для поиска: обработчики, события, онлоад, ончек, OnLoad, OnCheck, OnChange, события диалога, события таблицы, события браузера, события интегратора, события отчёта, event handlers, callback, колбэки -->

## Обзор

В EME-L реализован механизм обработчиков событий — специальных методов, которые вызываются ядром EME.WMS в ответ на действия пользователя или системные события. Обработчики позволяют программировать реакцию программы на различные события: открытие диалога, изменение данных, нажатие кнопок и т.д.

Все обработчики в EME-L следуют соглашению об именовании: `ИмяОргана_ИмяСобытия()`. Например: `Dialog_OnLoad()`, `GoodsCode_OnCheck()`, `Table_OnClick()`.

---

## Обработчики диалогов

<!-- Синонимы: обработчики диалога, OnLoad, OnDestroy, OnChange, OnCommand, OnTimer, онлоад, ончейндж, события формы -->

### Dialog_OnLoad()

Вызывается при загрузке диалога после создания всех его органов.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

**Применение:**
- Инициализация органов управления
- Загрузка данных из настроек
- Установка начальных значений

```EME-L
Dialog_OnLoad()
{
    'Проверяем, есть ли настройки поиска'
    If (ContextFindParamsRec.GetNoOfLines() == 0 & ~is_ui_test_mode())
        If (is_message(tr("Внимание"), tr("Создать настройки поиска?"), "YESNO", "QUESTION") == "YES")
            CreateDefault();
        End If
    End If
    
    'Устанавливаем фильтр по умолчанию'
    If (VendorFilter.Get() == 0)
        VendorFilter.Put(DEFAULT_VENDOR);
    End If
}
```

### Dialog_OnDestroy()

Вызывается при уничтожении диалога.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

**Применение:**
- Сохранение настроек
- Освобождение ресурсов
- Очистка глобальных переменных

```EME-L
Dialog_OnDestroy()
{
    'Сохраняем настройки фильтра'
    SaveFilterSettings();
    
    'Очищаем глобальные объекты'
    m_SelectedLines = NULL;
}
```

### Dialog_OnChange()

Вызывается при изменении данных в диалоге.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — запретить изменение
- `False` (по умолчанию) — разрешить изменение

```EME-L
Dialog_OnChange()
{
    'Проверяем корректность введённых данных'
    If (QtyFld.Get() < 0)
        is_message("Ошибка", "Количество не может быть отрицательным", 1, 1);
        Return True;  'Запрещаем изменение'
    End If
}
```

### Dialog_OnCommand()

Вызывается при нажатии кнопок на панели инструментов диалога.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — отменить стандартную обработку
- `False` (по умолчанию) — выполнить стандартную обработку

```EME-L
Dialog_OnCommand()
{
    'Перехватываем нажатие кнопки "Печать"'
    If (is_command() == "ID_PRINT")
        CustomPrint();
        Return True;  'Отменяем стандартную печать'
    End If
}
```

### Dialog_OnTimer()

Вызывается по таймеру для обновления диалога.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Dialog_OnTimer()
{
    'Обновляем данные с сервера'
    is_ws_align_data();
    
    'Обновляем отображение'
    UpdateStatusInfo();
}
```

---

## Обработчики органов управления

<!-- Синонимы: обработчики органов, OnCheck, OnEdit, OnInput, OnTimer, OnContextMenu, ончек, проверка ввода, валидация -->

### OnCheck()

Вызывается при проверке введённого значения.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — значение некорректно, запретить ввод
- `False` (по умолчанию) — значение корректно

```EME-L
'Проверка количества товара'
QtyFld_OnCheck()
{
    If (QtyFld.Get() <= 0)
        is_message("Ошибка", "Количество должно быть больше нуля", 1, 1);
        Return True;
    End If
    
    'Проверяем доступный остаток'
    If (QtyFld.Get() > AvailableQty.Get())
        is_message("Ввод заказов", "Количество превышает доступное", 1, 1);
        Return True;
    End If
}
```

### OnEdit()

Вызывается при редактировании значения органа.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — запретить редактирование
- `False` (по умолчанию) — разрешить редактирование

```EME-L
VendorCode_OnEdit()
{
    'Запрещаем редактирование, если документ утверждён'
    r_Doc = Object("dsDB", "Document");
    r_Doc.SetLine(Object("Dialog").GetLine());
    If (r_Doc.IsApproved())
        is_message("Внимание", "Документ утверждён — редактирование запрещено", 1, 1);
        Return True;
    End If
}
```

### OnInput()

Вызывается при вводе значения в орган управления.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Barcode_OnInput()
{
    'Обрабатываем сканирование штрих-кода'
    barcode = Barcode.Get();
    
    'Ищем товар по штрих-коду'
    r_Goods = Object("dsDB", "GoodsItem");
    If (r_Goods.FindByBarcode(barcode))
        GoodsRef.Put(r_Goods.GetLine());
        'Автоматически переходим к количеству'
        QtyFld.SetFocus();
    Else
        is_message("Ошибка", is_format("Товар со штрих-кодом %s не найден", barcode), 1, 1);
    End If
}
```

### OnTimer()

Вызывается для органов, обновляемых по таймеру.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
'Автообновление списка писем'
Timer_OnTimer()
{
    is_ws_align_data();
    r_MailFolder = Object("dsDB", "Почта папки");
    r_MailFolder.SetLine(ActiveFolder);
    If (r_MailFolder.IsValidLine() & r_MailFolder.IsFolderInbox())
        LoadMailData();
        OnSelect(r_MailFolder);
    End If
}
```

### OnContextMenu()

Вызывается при показе контекстного меню.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — запретить стандартное меню

```EME-L
GoodsCode_OnContextMenu()
{
    'Создаём своё контекстное меню'
    Menu = Object("Menu", True);
    Menu.AppendMenu("STRING", 1, tr("Наличие на складах"));
    Menu.AppendMenu("SEPARATOR");
    Menu.AppendMenu("STRING", 4, tr("Системное меню"));
    Menu.SetDefaultItem(1);
    
    Switch (Menu.TrackPopupMenuWithMousePos())
    Case 1:
        ShowGoodsRemainders();
    Case 4:
        Return;  'Показать стандартное меню'
    End Switch
    
    Return 1;  'Не показывать стандартное меню'
}
```

### OnCommand()

Вызывается при нажатии кнопки.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
CloseBtn_OnCommand()
{
    Object("Dialog").Close();
}

PrintBtn_OnCommand()
{
    PrintDocument();
}
```

### OnCheckDelete()

Вызывается при попытке удаления строки из таблицы.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — запретить удаление

```EME-L
PONoTable_OnCheckDelete()
{
    'Запрещаем удаление, если на заказ ссылаются строки документа'
    PONoTable = Object("Table", "PONoTable");
    colPO = Object("Control", , "PO");
    r_PO = Object("dsDB", "Предварительные заказы товаров");
    row = PONoTable.GetCurrentRow();
    r_PO.SetLine(colPO.GetFromRow(row));
    
    If (~r_PO.IsValidLine())
        Return True;  'Нельзя удалить несуществующий заказ'
    End If
    
    'Проверяем ссылки в документе'
    Header = Object("dsDB", "Document");
    Header.SetLine(Object("Dialog").GetLine());
    r_DocLines = Header.GetDocLines();
    For (r_DL.SetFirstLine(); r_DL.IsValidLine(); r_DL.SetNextLine())
        PORegNo = r_DL.GetPORegNo();
        If (PORegNo != "" & PORegNo == r_PO.GetRegNo())
            is_message("Внимание", "На заказ ссылаются строки документа", 1, 1);
            Return True;
        End If
    End For
    
    Return False;  'Разрешаем удаление'
}
```

### OnGetProperty(Info)

Вызывается при получении контекстного меню — позволяет добавить свои пункты.

**Входные параметры:**
- `Info` — структура с настройками контекстного меню

**Выходные параметры:**
- `True` — стандартное меню не показывать

```EME-L
GoodsCode_OnGetProperty(Info)
{
    If (Info.GetMenuItemId() == "")
        'Добавляем свои пункты меню'
        Info.AppendMenuItem("Наличие на складах", "PROP_REM");
        Info.AppendMenuItem("Свойства", "PROP_ID");
        Return True;
    Else
        'Обрабатываем выбор пункта меню'
        dbGoodsProp = Object("dsDB", "GoodsItem");
        Info.SetRecord(dbGoodsProp.GetRecord());
        dbGoodsProp.SetLine(Info.GetLine());
        
        If (Info.GetMenuItemId() == "PROP_REM")
            Object("Dialog", "RemaindersGoods.Dialog").Show(0, 0, 
                is_format("@Продукт=%d", dbGoodsProp.GetLine()));
            Return True;
        End If
    End If
}
```

---

## Обработчики таблиц

<!-- Синонимы: обработчики таблицы, Table_OnClick, Table_OnSkip, Table_OnMarkTableLine, события таблицы, ячейки таблицы -->

### Table_OnSetNextCell()

Вызывается при нажатии Enter в ячейке таблицы.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — не переносить фокус на следующую ячейку

```EME-L
Table_OnSetNextCell()
{
    Table = Object("Table", , "Table");
    If (Table.GetCurrentRow() < Table.GetNoOfLines())
        'Пропускаем столбец 1, переходим сразу к столбцу 2'
        If (Table.GetCurrentColumn() == 0)
            Table.SetFocus(2, );
            Return True;
        End If
    End If
    Return False;
}
```

### Table_OnClickColumnHeader(Info)

Вызывается при нажатии на заголовок колонки.

**Входные параметры:**
- `Info` — структура с информацией о столбце

**Выходные параметры:** отсутствуют

```EME-L
Table_OnClickColumnHeader(Info)
{
    'Сортируем таблицу по нажатой колонке'
    Table = Object("Table");
    Table.Sort(Info.GetColumn(), True);
}
```

### Table_OnMarkTableLine(Info)

Вызывается при попытке выделения строки таблицы.

**Входные параметры:**
- `Info` — структура с информацией

**Выходные параметры:**
- `True` — запретить выделение

```EME-L
Table_OnMarkTableLine(Info)
{
    Row = Info.GetCurrentRow();
    FlagsInTable = Object("Control", , "FlagsInTable");
    
    'Запрещаем выделение импортированных документов'
    If (is_and(FlagsInTable.GetFromRow(Row), 0x1))
        is_message("Внимание", "Документ уже сохранён ранее", 1, 1);
        Return True;
    End If
}
```

### Table_OnSkip()

Вызывается при заполнении таблицы — позволяет отфильтровать строки.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — не добавлять строку в таблицу

```EME-L
Table_OnSkip()
{
    'Показываем только строки с заданным поставщиком'
    VendorFilter = Object("Control", , "VendorFilter");
    MatrixRec.SetLine(is_line());
    Return (MatrixRec.GetVendorRef() != VendorFilter.Get());
}
```

### Table_OnCommandDelete()

Вызывается при нажатии кнопки "Удалить" в таблице.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — отменить удаление

```EME-L
Table_OnCommandDelete()
{
    Table = Object("Table");
    Row = Table.GetCurrentRow();
    
    If (Row < Table.GetNoOfLines())
        FlagAuthorized = Object("Control", , "FlagAuthorized");
        If (FlagAuthorized.GetFromRow(Row) != 0)
            is_message("Внимание", "Утверждённую строку удалять нельзя", 1, 1);
            Return True;
        End If
    End If
}
```

### Table_OnClick()

Вызывается при двойном клике по ячейке таблицы.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — отменить стандартную обработку

```EME-L
Table_OnClick()
{
    Table = Object("Table");
    Col = Table.GetCurrentColumn();
    Row = Table.GetCurrentRow();
    
    If (Col > 0 & Row < Table.GetNoOfLines())
        'Переключаем состояние ячейки'
        Value = arValues.GetAt(Col - 1).GetAt(Row);
        Value = If (Value == 0x02, 0, Value + 1);
        arValues.GetAt(Col - 1).SetAt(Row, Value);
        Table.SetCellColor(Col, Row, arColors.GetAt(Value));
    End If
    
    Return True;
}
```

### Table_OnRClick(Info)

Вызывается при нажатии правой кнопки мыши по ячейке таблицы.

**Входные параметры:**
- `Info` — структура с информацией о контекстном меню

**Выходные параметры:**
- `True` — отменить стандартную обработку

```EME-L
Table_OnRClick()
{
    Table = Object("Table");
    If (~Table.IsValidRow())
        Return True;
    End If

    'Создаём контекстное меню'
    Menu = Object("Menu");
    Menu.CreatePopupMenu();
    Menu.AppendMenu("STRING", 0, "Выделить строку");
    Menu.AppendMenu("SEPARATOR");
    Menu.AppendMenu("STRING", 1, "Выделить всё");
    Menu.AppendMenu("STRING", 2, "Очистить выделение");

    Selected = Menu.TrackPopupMenuWithMousePos();

    Switch (Selected)
        Case 0:
            Table.MarkRow(Table.GetCurrentRow());
        Case 1:
            For (Row = 0; Row < Table.GetNoOfLines(); Row = Row + 1)
                Table.MarkRow(Row);
            End For
        Case 2:
            For (Row = 0; Row < Table.GetNoOfLines(); Row = Row + 1)
                Table.MarkRow(Row, False);
            End For
    End Switch

    Return True;
}
```

### Table_OnMoveTableLines(Obj)

Вызывается при перетаскивании строк таблицы (drag-and-drop). Доступно начиная с ревизии ядра 13708.

**Входные параметры:**
- `Obj` — объект с информацией о перемещении

**Условия работы:**
- В записи БД, к которой привязана таблица, должно быть поле сортировки по полю типа «число». Перетаскивание не работает для записи с сортировкой по физике.
- В свойствах таблицы в редакторе диалогов должна стоять галочка «Разрешить перетаскивание»
- В таблицу нужно добавить колонку, настроенную на поле из сортировки

**Включение перетаскивания:**
1. В структуре БД проверить/добавить поле сортировки (тип «число») в запись
2. В редакторе диалогов поставить галочку «Разрешить перетаскивание» в свойствах таблицы
3. Добавить колонку в таблицу, настроенную на поле сортировки
4. Добавить метод `Таблица_OnMoveTableLines(obj)` в EME-L класс (скопировать из класса `Shipments` метод `tblOrdersInShipment_OnMoveTableLines(obj)` и поменять название таблицы и сортировочной колонки)

```EME-L
'Пример: обработчик перетаскивания строк в таблице'
tblOrders_OnMoveTableLines(Obj)
{
    'Obj содержит информацию о перемещении'
    'Стандартная обработка перетаскивания уже выполнена ядром'
    'Здесь можно добавить дополнительную логику'
}
```

---

## Обработчики браузеров

<!-- Синонимы: обработчики браузера, Browser_OnSelect, Browser_OnSkip, Browser_OnTune, события браузера, выбор строки -->

### Browser_OnInit()

Вызывается при создании браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Browser_OnInit()
{
    'Запоминаем объект браузера'
    Browser = Object("Browser");
}
```

### Browser_OnLoad()

Вызывается при загрузке браузера после его создания.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Browser_OnLoad()
{
    'Снимаем выделение со всех строк'
    Object("Browser").UnMarkAll();
}
```

### Browser_OnDestroy()

Вызывается при уничтожении браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Browser_OnDestroy()
{
    'Сохраняем выделение в битовый буфер'
    bbFilter.LoadHewVariableSelection(Object("Dialog"), "Товарная единица");
}
```

### Browser_OnSelect(Selected)

Вызывается при попытке пометить строку браузера.

**Входные параметры:**
- `Selected` — текущее состояние строки

**Выходные параметры:**
- `True` — запретить изменение состояния

```EME-L
SectorsList_OnSelect(Selected)
{
    If (~Selected)
        dbSect = Object("dsDB", "Sectors");
        dbSect.SetLine(is_line());
        
        Switch (dbSect.GetInventoryStatus())
            Case 1:
                is_message("Внимание!", "Сектор уже инвентаризуется", 1, 3);
                Return 1;
            Case 2:
                If (~is_message("Внимание!", "Сектор уже был инвентаризован. Повторить?", 4, 2, 1))
                    Return 1;
                End If
        End Switch
    End If
}
```

### Browser_OnAfterSelect()

Вызывается после пометки строки браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Browser_OnAfterSelect()
{
    'Снимаем выделение со строк, не принадлежащих владельцу'
    For (Line = Browser.GetSelected(0); Line != NULL_REF; Line = Browser.GetSelected(Line + 1))
        PartsRecord.SetLine(Line);
        If (~PartsRecord.IsOwner())
            Object("Browser").Select(Line, 0);
        End If
    End For
    
    Update();
}
```

### Browser_OnCommand()

Вызывается при выборе строки браузера (Enter).

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Browser_OnCommand()
{
    'Открываем диалог на выбранной строке'
    ClientsDialog = Object("Dialog", "Clients.Main.Dialog");
    ClientsDialog.AddFlags(0x0004, False);  'Без браузера'
    ClientsDialog.AddFlags(0x0008, True);   'Без листания'
    ClientsDialog.ShowLine(False, Object("Browser").GetFocusLine());
}
```

### Browser_OnTune()

Вызывается перед заполнением браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
Browser_OnTune()
{
    'Настраиваем браузер на цепочку'
    Browser = Object("Browser");
    Browser.SetChain("@Склад", Object("Dialog").GetLine());
}
```

### Browser_OnSkip(Record)

Вызывается для каждой строки при заполнении браузера — фильтрация.

**Входные параметры:**
- `Record` — объект записи для добавляемой строки

**Выходные параметры:**
- `True` — не добавлять строку

```EME-L
Browser_OnSkip(Record)
{
    'Не показываем текущую строку диалога'
    If (Record.GetLine() == Object("Dialog").GetLine())
        Return True;
    End If
    
    'Фильтруем по родительскому поставщику'
    Return Record.GetParentVendorRef() != NULL_REF;
}
```

### Browser_OnSkipEx()

Вызывается один раз для полного заполнения браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
ZoneBrowser_OnSkipEx()
{
    'Заполняем браузер зонами склада'
    Browser = Object("Browser");
    Browser.UnloadAllLines();
    
    r_Zone = Object("dsDB", "Zone");
    r_Zone.SetChain("@Склад", Object("Dialog").GetLine());
    Browser.LoadLines(r_Zone);
}
```

### Browser_OnStartAddDialog()

Вызывается при открытии привязанного к браузеру диалога.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — отменить открытие диалога

```EME-L
ClientBrowser_OnStartAddDialog()
{
    'Открываем свой диалог вместо стандартного'
    Object("Dialog", "Clients.Dialog").ShowLine(0, NEW_LINE_MARK, 1);
    Return True;
}
```

---

## Обработчики ячеек браузера

### OnPut()

Вызывается при заполнении ячейки браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

```EME-L
OrdersBrwPOD_OnPut()
{
    'Отображаем +, если заказ требует POD (подтверждения доставки)'
    r_Orders = Object("dsDB", "Orders");
    r_Orders.SetLine(is_line());
    If (r_Orders.IsPOD())
        Object("Control").Put("+");
    End If
}
```

### OnEdit()

Вызывается при редактировании ячейки браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:**
- `True` — запретить редактирование

### OnInput()

Вызывается при вводе значения в ячейку браузера.

**Входные параметры:** отсутствуют

**Выходные параметры:** отсутствуют

---

## Обработчики интегратора

<!-- Синонимы: обработчики интегратора, Integrator_OnGetChild, Integrator_OnSelect, древовидное меню, дерево навигации -->

Интегратор — это древовидный элемент управления. Имена методов для интегратора могут содержать имя записи: `Integrator_OnGetChild{ИмяЗаписи}(Rec)`.

### Integrator_OnGetChild{ИмяЗаписи}(Rec)

Возвращает дочерний элемент.

```EME-L
Integrator_OnGetChildDetails(Rec)
{
    r = Object("dsDB", "DetailsRepository").Duplicate();
    r.SetChain("@Категория", Rec.GetLine());
    r.SetFirstLine();
    If (r.IsValidLine())
        Return r;
    End If
    Return NULL;
}
```

### Integrator_OnGetSibling{ИмяЗаписи}(Rec)

Возвращает соседний элемент.

```EME-L
Integrator_OnGetSiblingDetailsRepository(Rec)
{
    r = Rec.Duplicate();
    r.SetChain("@Категория", Rec.GetCategoryRef());
    r.SetLine(Rec.GetLine());
    r.SetNextLine();
    If (r.IsValidLine())
        Return r;
    End If
    Return NULL;
}
```

### Integrator_OnGetParent{ИмяЗаписи}(Rec)

Возвращает родительский элемент.

```EME-L
Integrator_OnGetParentDetails(Rec)
{
    r = Object("dsDB", "Groups").Duplicate();
    r.SetLine(Rec.GetGroupRef());
    If (r.IsValidLine())
        Return r;
    End If
    Return NULL;
}
```

### Integrator_OnGetItemText{ИмяЗаписи}(Rec)

Возвращает текст элемента.

```EME-L
Integrator_OnGetItemTextDetails(Rec)
{
    Return Rec.GetName();
}
```

### Integrator_OnGetItemIcon{ИмяЗаписи}(Rec)

Возвращает иконку элемента.

```EME-L
Integrator_OnGetItemIconSchoolBook(Rec)
{
    r_SchoolBook = Object("dsDB", "SchoolBook");
    r_SchoolBook.SetChain("@Родитель", Rec.GetLine());
    r_SchoolBook.SetFirstLine();
    If (r_SchoolBook.IsValidLine())
        Return "MetC_Sys.dll#249";  'Иконка папки'
    End For
    Return "MetC_Sys.dll#251";  'Иконка документа'
}
```

### Integrator_OnSelect(Rec)

Вызывается при выделении элемента.

```EME-L
Integrator_OnSelect(Rec)
{
    'Раскрываем элемент при выделении'
    Integrator.Expand(Integrator.GetSelectedItem());
}
```

### Integrator_OnEdit(Rec)

Вызывается при двойном клике или Enter.

```EME-L
Integrator_OnEdit(Rec)
{
    Line = Rec.GetLine();
    Object("Dialog").SetLine(Line);
}
```

### Integrator_OnGetContextMenu(Rec)

Создаёт контекстное меню для элемента.

```EME-L
Integrator_OnGetContextMenu(Rec)
{
    Menu = Object("Menu");
    Menu.CreatePopupMenu();
    Menu.AppendMenu("STRING", 1, "Переименовать...");
    Menu.AppendMenu("STRING", 2, "Свойства...");
    Menu.AppendMenu("STRING", 3, "Удалить");
    
    Res = Menu.TrackPopupMenuWithMousePos();
    Switch (Res)
        Case 1: DoRename();
        Case 2: DoProperties();
        Case 3: DoDelete();
    End Switch
}
```

---

## См. также

- [EMEL_Dialogs_Reports.md](EMEL_Dialogs_Reports.md) — создание диалогов и отчётов
- [EMEL_Language_Basics.md](EMEL_Language_Basics.md) — основы языка EME-L
- [EMEL_Working_with_Records.md](EMEL_Working_with_Records.md) — работа с записями БД