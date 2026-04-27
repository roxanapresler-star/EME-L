# Практические задачи по EME-L

<!-- Синонимы для поиска: практические задачи, упражнения EME-L, задачи на понимание, обучение EME-L, оптимизация кода, задачи на циклы, задачи на цепочки, задачи на Map -->

## Задачи на понимание циклов и цепочек в EME-L

<!-- Синонимы: циклы в EME-L, оптимизация циклов, Loop, SetChainMode, SetSkipMode, производительность -->

### Задача 1: Оптимизация вложенных циклов

**Условие:** Оптимизировать код, который подсчитывает товары с заданной единицей измерения (ЕИ).

**Требование:** Не изменяйте Test1. Исправьте реализацию в Solution1.

```EME-L
'Тестовая функция - не изменять'
Test1()
{
    startTime = is_get_tick_count();
    Count = Solution1();
    endTime = is_get_tick_count();
    
    csMess = is_format("Всего товаров с заданной ЕИ: %d \nВремя выполнения: %d\n", Count, (endTime - startTime));
    is_message("Вывод", csMess);
}

'Исходная реализация - содержит ошибки производительности'
Solution1()
{
    Count = 0;
    r_GoodsItem = Object("dsDB", "GoodsItem");
    Loop (r_GoodsItem)
    {
        r_GoodsItemMU = Object("dsDB", "GoodsItemMU");
        Loop (r_GoodsItemMU)
        {
            If (r_GoodsItem.GetLine() == r_GoodsItemMU.GetGoodsItemRef())
            {
                Continue;
            End If
            
            If (r_GoodsItemMU.GetName() == "Короб")
            {
                Count = Count + 1;
            End If
        End Loop
    End Loop
    
    Return Count;
}
```

**Ответ:** Не используется цепочка по ЕИ товара, объект создаётся в цикле. Делать внутренний цикл по цепочке тоже не оптимально, так как проход по цепочке по всей записи работает медленнее, чем проход по физике по всей записи.

**Как изменить:** Внешний цикл по r_GoodsItem лишний. Внутренний цикл по r_GoodsItemMU проходит по всем товарам, нужно оставить его и переделать на SkipMode.

```EME-L
'Оптимизированная реализация'
Solution1()
{
    Count = 0;
    r_GoodsItemMU = Object("dsDB", "GoodsItemMU");
    r_GoodsItemMU.SetSkipMode();
    r_GoodsItemMU.GetNameFld().MustBeEQ("Короб");
    r_GoodsItemMU.SetFirstLine();
    
    Loop (r_GoodsItemMU)
    {
        Count = Count + 1;
    End Loop
    
    Return Count;
}
```

---

## Задачи на понимание Map в EME-L

<!-- Синонимы: Map, хэш-таблица, словарь, ключ-значение, Lookup, SetAt, IsKeyExists -->

### Задача 2: Подсчёт единиц измерения товаров

**Условие:** Нужно посчитать и записать в MapMU все ЕИ в БД и посчитать кол-во ЕИ. Дополнительно посчитать все товары в базе.

**Требование:** Не изменяйте Test2. Напишите реализацию в Solution2.

```EME-L
'Тестовая функция - не изменять'
Test2()
{
    r_GoodsItemMU = Object("dsDB", "GoodsItemMU");
    MapMU = Object("Map", r_GoodsItemMU.GetNoOfLines() + 1);
    startTime = is_get_tick_count();
    Count = Solution2(MapMU);
    endTime = is_get_tick_count();
    
    csMess = "";
    Loop (MapMU)
    {
        csMess = is_format("%sВсего найдено ЕИ %s: %d\n", csMess, MapMU.GetKey(), MapMU.GetValue());
    End Loop
    
    csMess = is_format("Всего товаров: %d \n%s \nВремя выполнения: %d\n", Count, csMess, (endTime - startTime));
    is_message("Вывод", csMess);
}

'Правильная реализация'
Solution2(MapMU)
{
    Count = 0;
    r_GoodsItem = Object("dsDB", "GoodsItem");
    Loop (r_GoodsItem)
    {
        r_GoodsItemMU = r_GoodsItem.GetGoodsItemMU();
        Loop (r_GoodsItemMU)
        {
            nNum = MapMU.Lookup(r_GoodsItemMU.GetName(), 0);
            MapMU.SetAt(r_GoodsItemMU.GetName(), nNum + 1);
        End Loop
        Count = Count + 1;
    End Loop
    
    Return Count;
}
```

**Пояснение:** 
- Map создаётся с размером, превышающим ожидаемое количество элементов
- Lookup возвращает значение по ключу или второй параметр, если ключ не найден
- SetAt устанавливает значение по ключу
- Проход по Map выполняется через Loop

---

### Задача 3: Подсчёт заданных единиц измерения

**Условие:** Нужно посчитать и записать в MapMU кол-во заданных в мапе ЕИ. Дополнительно посчитать все товары в базе с одним из заданных ЕИ.

**Требование:** Не изменяйте Test3. Напишите реализацию в Solution3.

```EME-L
'Тестовая функция - не изменять'
Test3()
{
    MapMU = Object("Map");
    MapMU.SetAt("Короб", 0);
    MapMU.SetAt("Штука", 0);
    startTime = is_get_tick_count();
    Count = Solution3(MapMU);
    endTime = is_get_tick_count();
    
    csMess = "";
    Loop (MapMU)
    {
        csMess = is_format("%sВсего найдено ЕИ %s: %d\n", csMess, MapMU.GetKey(), MapMU.GetValue());
    End Loop
    
    csMess = is_format("Всего товаров с данными ЕИ: %d \n%s \nВремя выполнения: %d\n", Count, csMess, (endTime - startTime));
    is_message("Вывод", csMess);
}

'Правильная реализация'
Solution3(MapMU)
{
    Count = 0;
    r_GoodsItem = Object("dsDB", "GoodsItem");
    Loop (r_GoodsItem)
    {
        bFind = False;
        r_GoodsItemMU = r_GoodsItem.GetGoodsItemMU();
        Loop (r_GoodsItemMU)
        {
            If (MapMU.IsKeyExists(r_GoodsItemMU.GetName()))
            {
                nNum = MapMU.Lookup(r_GoodsItemMU.GetName(), 0);
                MapMU.SetAt(r_GoodsItemMU.GetName(), nNum + 1);
                bFind = True;
            End If
        End Loop
        
        If (bFind)
        {
            Count = Count + 1;
        End If
    End Loop
    
    Return Count;
}
```

**Пояснение:**
- IsKeyExists проверяет наличие ключа в Map
- Lookup и SetAt используются для чтения и обновления значения
- Флаг bFind отслеживает, найдена ли хотя бы одна ЕИ для товара

---

## Задачи на разбор кода и поиск ошибок

<!-- Синонимы: разбор кода, ошибки EME-L, анализ кода, постановка в буфер, MoveSrcToBuf -->

### Задача 4: Разбор кода постановки строк документа подборки в буфер

**Условие:** При нажатии кнопки «Просчёт» на 4-м статусе программа сообщает, что просчёт невозможен, а после закрытия окна с сообщением появляется ошибка «MoveSrcToBuf метод не реализован».

**Исходный код:**

```EME-L
RunBtn_OnCommand()
{
    CreateDocumentsForOrders(true);
    PrintReportShipmentPlaces();
    
    'Постановка в буфер после просчёта'
    If(is_sys_param("M://Предпочтения.Уход.Выключить постановку в буфер после просчета", "", 1) != "ДА")
        dbBatch = Object("dsDB", "OrdersBatch");
        dbBatch.SetLine(is_dlg_line());
        If(dbBatch.IsValidLine())
            BatchCreator = Object("IClass", ,"OrderBatchCreator",false);
            BatchCreator.OrderLinesMoveSrcToBuf(dbBatch);
        End If
        dlg.Update();
    End If
}
```

**Найденные ошибки:**

1. **Нет проверки на тип документа.** Цикл сделан по всем документам пачки, но в пачке могут быть не только документы подборки, но и перемещение в ОА, списание и возвраты. Метод `MoveSrcToBuf()` пытается поставить в буфер всё подряд, что и вызывает ошибку «метод не реализован».

2. **Неполная проверка строк в буфере.** `IsGoodsInBuffer()` проверяет, что строка уже в буфере, но отгруженные строки тоже нужно пропускать — для них `IsGoodsInBuffer()` вернёт false. Нужно дополнительно проверять методом `IsGoodsLoaded()`.

3. **Отсутствие транзакции или избыточные транзакции.** Если в пачке тысяча строк, будет тысяча транзакций? Нужно использовать класс `Mover`, оптимизированный для работы с массивом строк.

4. **Проблема с просчётом на сервере.** Кнопкой «Просчёт пачки» можно запустить просчёт не только на рабстанции, но и на сервере. Код отработает раньше, чем просчитается пачка.

5. **Экспорт.** После просчёта заказ может поставиться в очередь на экспорт — строки подборки в буфер ставить нельзя до завершения экспорта.

**Проблема с названием настройки:** «Выключить постановку в буфер после просчета» — плохое название (отрицание). Хорошее: «Ставить строки в буфер после просчета пачки» — значение «Да» включает, «Нет» выключает.

```EME-L
'Пример метода с ошибками:'
OrderLinesMoveSrcToBuf(r_OrderBatch)
{
    r_Document = r_OrderBatch.GetPalletPickingUp();
    context = Object("OperationContext", TRUE);
    
    If (~is_empty(m_bDisableFindTerminal) & m_bDisableFindTerminal)
        context.SetDisableFindTerminal(True);
    End If
    
    csMess = "";
    For (r_Document.SetFirstLine(); r_Document.IsValidLine(); r_Document.SetNextLine())
        r_DocLines = r_Document.GetDocLines();
        For (r_DocLines.SetFirstLine(); r_DocLines.IsValidLine(); r_DocLines.SetNextLine())
            GoodsItem = r_DocLines.GetGoodsItem();
            If(~GoodsItem.IsValidLine())
                Continue;
            End If
            If (r_DocLines.IsGoodsInBuffer())
                Continue;  'ОШИБКА: IsGoodsLoaded() не проверяется!'
            End If
            Res = r_DocLines.MoveSrcToBuf(0, context);  'ОШИБКА: нет проверки типа документа!'
            If (Res!="Ok")
                csMess = Res;
            End If
        End For
    End For
    Return csMess;
}
```

---

## Ключевые принципы оптимизации в EME-L

<!-- Синонимы: оптимизация, производительность, ускорение кода, эффективность EME-L -->

### 1. Создание объектов вне циклов

```EME-L
'НЕПРАВИЛЬНО: объект создаётся в каждой итерации'
Loop (r_Document)
{
    r_DocLines = Object("dsDB", "DocLines");
    '... обработка ...'
}

'ПРАВИЛЬНО: объект создаётся один раз'
r_DocLines = Object("dsDB", "DocLines");
Loop (r_Document)
{
    '... обработка ...'
}
```

### 2. Использование Const для кэширования

```EME-L
'НЕПРАВИЛЬНО: выражение вычисляется для каждой строки'
WHERE
{
    Return is_query(,"Дата") == is_edit_data("Report.Date");
}

'ПРАВИЛЬНО: значение кэшируется'
WHERE
{
    NeedDate = Const(is_edit_data("Report.Date"));
    Return is_query(,"Дата") == NeedDate;
}
```

### 3. Выбор правильного режима обхода

| Режим | Когда использовать | Скорость |
|-------|-------------------|----------|
| SetChainMode | Перебор строк одного документа | Очень быстро |
| SetSkipMode с индексом | Поиск по индексированному полю | Быстро |
| SetSkipMode без индекса | Фильтрация по нескольким условиям | Средне |
| SetRealMode | Полный перебор без условий | Медленно |

### 4. Правильный размер Map

```EME-L
'Рекомендованный размер: 1.3 * ожидаемое количество элементов'
mapGoods = Object("Map", 13000); 'Если ожидается ~10000 товаров'

'С ростом данных из-за коллизий появляется нелинейное замедление'
'Неудачный выбор размера может сильно замедлить программу'
```

### 5. Цепочки вместо SkipMode для связанных данных

```EME-L
'НЕОПТИМАЛЬНО: SkipMode для строк документа'
r_DocLines = Object("dsDB", "DocLines");
r_DocLines.SetSkipMode();
r_DocLines.GetDocumentRefFld().MustBeRefEQ(docRef);
r_DocLines.SetFirstLine();

'ОПТИМАЛЬНО: ChainMode для строк документа'
r_Document = Object("dsDB", "Document");
r_Document.SetLine(docRef);
r_DocLines = r_Document.GetDocLines();
'Цепочка уже настроена автоматически'
```

---

## См. также

- [EMEL_Classes_Utility.md](EMEL_Classes_Utility.md) — утилитарные классы Query, BitBuffer, Array, Map
- [EMEL_Working_with_Records.md](EMEL_Working_with_Records.md) — работа с записями БД
- [EMEL_Language_Basics.md](EMEL_Language_Basics.md) — основы языка EME-L