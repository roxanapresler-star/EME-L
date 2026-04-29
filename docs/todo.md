# План создания нового llms.txt для Context7

## Контекст

Старый `llms.txt` (242 строки) основан на устаревшей структуре из 11 файлов (EMEL_Language_Basics.md, EMEL_Working_with_Records.md и т.д.). Документация полностью обновлена: теперь 44 файла с новым синтаксисом, новыми разделами и уточнёнными описаниями. Необходимо создать новый `llms.txt`, отражающий актуальное состояние docs/.

---

## Этап 1: Заголовок и метаданные

- [ ] Описать назначение файла: библиотека EME-L для Context7 RAG
- [ ] Указать версию/дату на основе текущего состояния docs/
- [ ] Краткое описание языка (проприетарный, Pascal-style, интерпретатор в ядре EME.WMS)

**Источник:** старый llms.txt строки 1-3

---

## Этап 2: Правила для AI (обязательные)

- [ ] Перенести и актуализировать правила из старого llms.txt
- [ ] Проверить каждое правило на соответствие новой документации:
  - `SetChainMode` вместо `SetSkipMode` для перебора строк документа
  - `Const` для константных объектов в циклах
  - Транзакции: `is_transaction(1)` / `is_transaction(-1)`
  - `ByRef`/`ByVal` для возврата значений
  - `SetSkipMode` НЕ внутри цикла
  - EME-SQL: `WITH` вместо `WHERE`
  - Только подтверждённые примеры из документации
  - Правила KPI/NPM комментирования
  - `As "Invariant"` предпочтительнее `As "Dynamic"`
- [ ] Добавить новые правила, если появились в новой документации

**Источники:** старый llms.txt строки 5-16, EME_L_Syntax.md, EME_L_Advanced_Features.md, EME_L_Advanced_API_and_Patterns.md

---

## Этап 3: Обзор файловой структуры (таблица)

- [ ] Создать таблицу всех 44 файлов с кратким описанием содержания
- [ ] Группировать по категориям (как в текущем todo.md):
  - Бизнес и архитектура (2)
  - Развёртывание и сопровождение (5)
  - Администрирование (2)
  - Язык EME-L — Основы (5)
  - Язык EME-L — Данные и память (5)
  - Язык EME-L — Продвинутые (5)
  - Интеграция (3)
  - Сборка и DevOps (4)
  - GUI (5)
  - ТСД и складские операции (4)
  - Спецмодули (2)
  - Платформа (2)

**Источники:** docs/* (все файлы), текущий todo.md

---

## Этап 4: Создание объектов Object()

- [ ] Актуализировать таблицу классов Object()
- [ ] Проверить наличие новых классов в EME_L_Classes.md, EME_L_Database.md
- [ ] Обновить описания существующих классов:
  - `dsDB` — Запись БД
  - `Dialog` — Диалог
  - `Control` — Орган управления
  - `Query` — Табличные данные
  - `Map` — Хэш-таблица
  - `Array` — Динамический массив
  - `MultiArray` — Двумерный массив (НОВЫЙ)
  - `BitBuffer` — Битовый буфер
  - `String` — Работа со строками
  - `PropertyTree` — JSON-дерево
  - `dsQUERY` — Запрос для блочной записи
  - `DBWatch` — Дозор БД
  - `IClass` — Доступ к другому классу (уточнить описание)

**Источники:** EME_L_Classes.md, EME_L_Database.md, EME_L_Records_and_Fields.md

---

## Этап 5: Режимы обхода записей

- [ ] Актуализировать таблицу SetRealMode / SetSkipMode / SetChainMode / SetFindMode
- [ ] Добавить новые режимы/нюансы из EME_L_Records_and_Fields.md
- [ ] Проверить описание MustBeInRange, SetLinesLimit (если есть в новой доке)

**Источники:** EME_L_Records_and_Fields.md, EME_L_Database_Advanced.md

---

## Этап 6: Ключевые методы dsDB (CEMERec)

- [ ] Обновить таблицу методов CreateNewLine/ApplyNewLine/DeleteLine/SetLine
- [ ] Проверить PutXxxFld/GetXxxFld — уточнить типы полей из новой документации
- [ ] Обновить методы MustBe* — добавить новые MustBeInRange и др.
- [ ] Проверить GetРеляционныйПуть, SetQuery
- [ ] Добавить LoadAllLines/UnloadAllLines, LoadLine/UnloadLine, LoadLines/UnloadLines

**Источники:** EME_L_Records_and_Fields.md, EME_L_Database.md, EME_L_Database_Advanced.md

---

## Этап 7: Контроль транзакций

- [ ] Актуализировать пример `is_transaction(1, "Имя")` / `is_transaction(-1)`
- [ ] Добавить DBWatch паттерн (is_db_watch)
- [ ] Проверить новые нюансы транзакций из EME_L_Database_Advanced.md

**Источники:** EME_L_Database_Advanced.md, EME_L_Advanced_API_and_Patterns.md

---

## Этап 8: Класс Query

- [ ] Обновить методы Create/AddColumn/AddREF/AddLine/SetData/GetData/Find/Sort
- [ ] Добавить LoadAllLines/UnloadAllLines, LoadLine/UnloadLine, LoadLines/UnloadLines
- [ ] Добавить методы агрегирования и группировки (если описаны)
- [ ] Проверить методы сравнения запросов

**Источники:** EME_L_Database.md, EME_SQL_Advanced.md

---

## Этап 9: Классы Map, Array, MultiArray, BitBuffer, String

- [ ] Map: актуализировать Put/Get/SetAt/Lookup/итерацию, размер 1.3×
- [ ] Array: обновить методы (добавить новые если есть)
- [ ] MultiArray: НОВЫЙ раздел — описание двумерного массива
- [ ] BitBuffer: актуализировать создание, LoadLines
- [ ] String: обновить методы работы со строками

**Источники:** EME_L_Database.md, EME_L_Syntax.md

---

## Этап 10: Класс PropertyTree (JSON)

- [ ] Актуализировать Put/AddChild/PushBack/Get/GetJSON/PutJSON
- [ ] Проверить новые методы из EME_L_Advanced_Features.md или EME_L_HTTP_Integration.md

**Источники:** EME_L_Advanced_Features.md, EME_L_HTTP_Integration.md

---

## Этап 11: is-функции

- [ ] Основные: is_date, is_time, is_transaction, is_message, is_format, is_trim_all, is_query, is_edit_data, is_line, is_workstation_mode, is_report_param, is_dlg_line, is_No_of_lines, is_type
- [ ] Группа Bit: is_bit, is_set_bit, is_clear_bit, is_reverse_bit, is_and, is_or, is_xor, is_not, is_mask, is_shift_left, is_shift_right
- [ ] Группа Browser: is_hewed, is_hew_item, is_empty_hew, is_browse, is_browse_data, is_browser_base_record, is_run_browse, is_keydown_flag
- [ ] Группа Context: is_put_context, is_get_context_as_string, is_get_context_as_ref, is_del_context, is_check_context
- [ ] Проверить новые is-функции в EME_L_IS_Functions.md (204 строки — возможны новые)

**Источники:** EME_L_IS_Functions.md, EME_L_Advanced_API_and_Patterns.md

---

## Этап 12: Статическая линковка и Dynamic/Invariant

- [ ] Актуализировать описание статической линковки (Const, As "Document")
- [ ] Обновить As "Dynamic" vs As "Invariant"
- [ ] Проверить новые паттерны из EME_L_Advanced_Features.md

**Источники:** EME_L_Advanced_Features.md, EME_L_Classes.md

---

## Этап 13: Обработчики событий

- [ ] Создать сводную таблицу всех обработчиков:
  - Диалоги: Dialog_OnInit, Dialog_OnClose, Dialog_OnLoad, Dialog_OnCheck, Dialog_OnClick и др. (24 шт.)
  - Таблицы: Table_OnClick, Table_OnSetNextCell и др.
  - Браузеры: Browser_OnInit, Browser_OnSelect, Browser_OnCommand и др.
  - Интегратор: Integrator_OnSelect, Integrator_OnEdit
  - Отчёты: Report_OnBeginReport, Report_OnEndReport, Band_OnSkip, Band_OnNewItem
- [ ] Указать входные/выходные параметры для каждого (кратко)

**Источники:** EME_L_Events.md (1822 строки — самый объёмный файл)

---

## Этап 14: Константы

- [ ] Типы документов: DOCUMENT_TYPE_INCOMG, DOCUMENT_TYPE_MOVPAL, DOCUMENT_TYPE_OFFWRI, DOCUMENT_TYPE_OUTPPU
- [ ] Статусы заказов: osNew, osCalculated, osAssembled, osHasLeftWarehouse, osFinished
- [ ] Типы приказов: MO_TYPE_INCOMING, MO_TYPE_PPU, MO_TYPE_DOCUMENT_INVENTORY
- [ ] Зоны склада: ZONE_STORAGE, ZONE_ACCEPTION, ZONE_PIKING, ZONE_COMPLETE, ZONE_SHIPPING
- [ ] Проверить новые константы в EME_L_Constants.md

**Источники:** EME_L_Constants.md

---

## Этап 15: HTTP-интеграция и Web API

- [ ] НОВЫЙ раздел — описание HTTP-запросов (SendWebRequest, обработка ответов)
- [ ] НОВЫЙ раздел — Web Services и API

**Источники:** EME_L_HTTP_Integration.md, Web_Services_and_API.md

---

## Этап 16: EME-SQL расширенные возможности

- [ ] Актуализировать EME-SQL: WITH, Const, is_query/is_edit_data, ExternalDB, PostgreSQL, dsQUERY
- [ ] Добавить продвинутые SQL-паттерны из EME_SQL_Advanced.md

**Источники:** EME_SQL_Advanced.md, EME_L_Database_Advanced.md

---

## Этап 17: Память и оптимизация

- [ ] НОВЫЙ раздел — управление памятью, утечки, оптимизация
- [ ] Правила работы с объектами в циклах

**Источники:** EME_L_Memory.md

---

## Этап 18: dsQUERY и блочная запись

- [ ] Актуализировать порядок: Create → AddField → SetQuery → AppendAndSetLine → ConnectFields → Sort → Loop → DisconnectFields
- [ ] Обновить ограничения

**Источники:** EME_L_Database_Advanced.md, EME_L_Query_Reports.md

---

## Этап 19: DBWatch — дозор базы данных

- [ ] Актуализировать паттерн: is_db_watch(1) → OnPrepare → transaction → is_db_watch(-1) → check → transaction end

**Источники:** EME_L_Database_Advanced.md

---

## Этап 20: Отчёты по будильнику

- [ ] Актуализировать ограничения (запрещённые функции)
- [ ] Обновить работу с ReportRunner

**Источники:** EME_L_Query_Reports.md

---

## Этап 21: Правила оформления диалогов

- [ ] Актуализировать: шрифты, отступы, размеры, цвета кнопок, обязательные поля
- [ ] Проверить новые правила из GUI_Dialog_Editor.md

**Источники:** GUI_Dialog_Editor.md, EME_L_Advanced_Features.md

---

## Этап 22: Правила комментирования кода EME-L

- [ ] Перенести правила KPI/NPM
- [ ] Добавить новые правила из обновлённой документации

**Источники:** EME_L_Syntax.md, AI_Programming_Assistant.md

---

## Этап 23: Финальная сборка

- [ ] Собрать все разделы в единый файл `llms.txt` в корне проекта
- [ ] Проверить, что все ссылки на docs/ файлы корректны
- [ ] Убедиться, что формат таблиц единообразен
- [ ] Проверить размер файла — стремиться к компактности (Context7 лимиты)
- [ ] Удалить из старого llms.txt всё, что не подтверждено новой документацией

---

## Порядок работы

1. Последовательно читать каждый docs/ файл, начиная с EME_L_*
2. Извлекать ключевые таблицы, методы, примеры кода
3. Формировать соответствующий раздел llms.txt
4. После прохода всех файлов — собрать итоговый файл
5. Верифицировать: сравнить с содержимым docs/ файлов

## Оценка размера нового llms.txt

Старый: 242 строки. Новый: ориентировочно 400-500 строк (за счёт новых разделов: Events, HTTP, Memory, MultiArray, расширенные SQL, больше обработчиков).
