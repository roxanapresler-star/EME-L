# Web-сервисы и API в EME.WMS

> **Поисковые синонимы:** web-сервис EME.WMS, htmJSON, JSON API, SOAP сервис, HTTP API, REST EME.WMS, PropertyTree, веб-объекты, Классификатор веб-объектов, HTTP-приложение, EME-L web-сервис, WMSWebService, C# интеграция, SoapUI, wsdl, SOAP-протокол, HTTP-сервер ЕМЕ БД, точка входа JSON, метки транзакций, веб-метод

---

## Работа с web-сервисами в EME.WMS

Данные в системе EME.WMS из web-сервисов передаются в формате JSON. Для работы web-сервисов необходимо запустить рабочую станцию с ключом `/http` и добавить точку входа для HTTP-приложений.

### Настройка точки входа HTTP-приложений

Настройка производится в диалоге «Классификатор веб-объектов» (меню: «Утилиты БД → Система → Классификатор веб-объектов»). В нём должна присутствовать строка с названием «JSON».

Если строка «JSON» отсутствует:

1. Нажать кнопку «Системные модули Web».
2. Добавить новую строку с обработчиком: библиотека `metc_sys.dll`, процедура `htm_JSON`.
3. Добавить строку «JSON» в диалог, следуя указаниям интерфейса.

В системе EME.WMS классификатор веб-объектов определяет точки входа для обработки HTTP-запросов, поступающих от внешних систем.

### Класс htmJSON и точка входа GetTemplate

За работу с web-сервисами в EME.WMS отвечает EME-L класс `htmJSON`. Точка входа сервиса — метод `GetTemplate()`, принимающий три параметра:

- **`command`** — название web-метода, вызванного на стороне сервиса.
- **`treeRequest`** — переданные параметры в виде JSON-объектов.
- **`treeAnswer`** — ответ в формате JSON.

Для маршрутизации запросов внутри `GetTemplate()` используется конструкция `Case`, сопоставляющая значение `command` с именем соответствующего EME-L метода.

### Класс PropertyTree для работы с JSON

В языке EME-L для работы с JSON используется специализированный класс `PropertyTree`. Он предоставляет методы для формирования JSON-структур: добавление полей, создание массивов, сериализация в строку.

**Пример формирования JSON-структуры в EME-L:**

```EME-L
json()
{
    'создание объекта json'
    json = Object ("PropertyTree");
    'добавили поле с текстом'
    json.Put("about", "This is JSON!");
    'добавили поле с числом'
    json.Put("number", 10);
    'добавили массив с именем Array'
    arrayJson = json.AddChild("Array");
    'добавили первый элемент массива'
    elem = arrayJson.PushBack();
    'добавили в первый элемент поле about'
    elem.Put("about", "This is array in JSON!");
    'смотрим JSON в виде строки'
    str = json.GetJSON();
}
```

Результат выполнения — строковое представление объекта `PropertyTree`:

```json
{"about": "This is JSON!", "number": 10, "Array":[{"about": "This is array in JSON!"}]}
```

**Основные методы PropertyTree:**

| Метод | Описание |
|-------|----------|
| `Put(key, value)` | Добавляет поле с ключом и значением |
| `Get(key, default)` | Получает значение по ключу |
| `AddChild(name)` | Создаёт дочерний узел (массив/объект) |
| `PushBack()` | Добавляет элемент в конец массива |
| `GetJSON()` | Сериализует объект в JSON-строку |

В языке EME-L класс PropertyTree является основным инструментом для сериализации и десериализации данных при работе с web-сервисами.

---

## Основы разработки web-сервисов для EME.WMS

### Пример проекта на C# (SOAP-протокол)

Пример проекта web-сервиса на языке C#, взаимодействующего с WMS по протоколу SOAP, доступен по адресу: `http://svn.eme.ru/metaproj/trunk/metaproj/src/.NET/WMS.WebService`. Файл решения: `WMS.WebService.sln`.

**Настройка подключения:**

В файле `web.config` найти строку:

```xml
<add key="ServerAddress" value="10.1.6.41:9834"/>
```

и заменить IP-адрес с портом на значения запущенного HTTP-сервера ЕМЕ БД.

Исходный код хранится в файле `WMSWebService.asmx.cs` (внутри `WMSWebService.asmx` в обозревателе решений Visual Studio). Для запуска проекта нажать `F5` — в браузере откроется приветственная страница WMSWebService с формой для тестирования методов.

WSDL-описание сервиса доступно по кнопке «Описание службы».

### Ключевые правила разработки web-сервисов для EME.WMS

При разработке web-сервисов для системы ЕМЕ БД необходимо учитывать:

1. **Совпадение имён методов.** Название метода в C# обязательно должно совпадать с EME-L методом, прописанным в классе `htmJSON`.

2. **Транзакции.** Сервис должен сразу выдавать результат клиенту. Нельзя дожидаться открытия транзакции — данные будут потеряны. Для проверки используется EME-L метод `is_open_transaction`. Если транзакция не открыта — её нужно открыть и сохранить данные. Если уже открыта — вернуть клиенту ошибку.

3. **Сторонние сервисы.** В проект можно добавлять внешние сервисы через «Service References» → «Добавить ссылку на службу», указав URL WSDL-описания.

---

## Тестирование web-сервисов с SoapUI

Для тестирования SOAP-сервисов EME.WMS используется программа SoapUI.

**Процедура тестирования:**

1. Меню «File → New SOAP Project».
2. В поле «Initial WSDL» вставить ссылку на сервис, например: `http://localhost:4836/WMSWebService.asmx?WSDL`.
3. В левой части интерфейса появится перечень методов сервиса.
4. Выбрать метод, нажать правой кнопкой мыши → «New request».
5. Заполнить параметры запроса и нажать зелёную стрелочку для отправки.

В системе EME.WMS HTTP-сервер обрабатывает входящие SOAP-запросы и направляет их в соответствующие EME-L методы через класс `htmJSON`.

---

## Пример создания метода web-сервиса на C#

Рассмотрим пример формирования метода на языке C# для передачи массива контрагентов в запись «Клиенты-партнеры» EME.WMS. Все наработки вносятся в файл `WMSWebService.asmx.cs`.

### Структура данных клиента

```C#
public class Client
{
    public string Code;
    public string Name;
    public string FullName;
    public string INN;
    public string KPP;
    public string PaymentAccount;
    public string Bank;
    public string BIK;
    public string CorrespondentAccount;
    public string PhysicalAdress;
    public string LegalAdress;
    public string Director;
}
```

### Класс для сериализации массива клиентов

```C#
public class RequestClient
{
    public List<Client> ListClient;
    public RequestClient() { }

    public RequestClient(List<Client> listClient)
    {
        if (listClient != null)
            this.ListClient = new List<Client>(listClient);
    }
}
```

### Класс ответа WMS

```C#
public class WmsResponse
{
    public int state;
    public List<string> listErrors;
}
```

### Метод загрузки контрагентов в WMS

```C#
[WebMethod(Description = "Загрузка контрагентов в WMS")]
public WmsResponse LoadClient(List<Client> listClients)
{
    RequestClient requestClient = new RequestClient(listClients);
    WMSRequest<RequestClient, WmsResponse> wmsRequest = new WMSRequest<RequestClient, WmsResponse>();
    WmsResponse response = wmsRequest.RequestToServer(requestClient, System.Reflection.MethodBase.GetCurrentMethod().Name);
    return response;
}
```

---

## Пример интеграции метода web-сервиса в EME.WMS (EME-L)

### Реализация EME-L метода в классе htmJSON

```EME-L
LoadClient(treeRequest, treeAnswer)
{
    is_transaction("Сохранение контрагентов из web сервиса");

    r_Client = Object("dsDB", "Clients");

    'Из сервиса приходит JSON массив клиентов'
    ListClient = treeRequest.Get("ListClient");
    For (ListClient.SetFirstLine(); ListClient.IsValidLine(); ListClient.SetNextLine())
        Client = ListClient.Get();
        Code = is_trim_all(Client.Get("Code", ""));
        Name = Client.Get("Name", "");
        FullName = Client.Get("FullName", "");
        INN = Client.Get("INN", "");
        KPP = Client.Get("KPP", "");
        PaymentAccount = Client.Get("PaymentAccount", "");
        Bank = Client.Get("Bank", "");
        BIK = Client.Get("BIK", "");
        CorrespondentAccount = Client.Get("CorrespondentAccount", "");
        PhysicalAdress = Client.Get("PhysicalAdress", "");
        LegalAdress = Client.Get("LegalAdress", "");
        Director = Client.Get("Director", "");

        r_Client.FindAndCreateClientByCode(Code, Name, PhysicalAdress, 1);
        r_Client.PutINN(INN);
        r_Client.PutKPP(KPP);
        r_Client.PutSettlementAccount(PaymentAccount);
        r_Client.PutBankName(Bank);
        r_Client.PutBIK(BIK);
        r_Client.PutKorrAccount(CorrespondentAccount);
        r_Client.Put("Адрес юридический", LegalAdress);
        r_Client.Put("Представитель клиента", Director);
    End For
    is_transaction(-1);
    treeAnswer.Put("state", 0);
}
```

### Маршрутизация в GetTemplate

```EME-L
Case "LoadClient":
    LoadClient(treeRequest, treeAnswer);
```

В языке EME-L метод `GetTemplate()` класса `htmJSON` выполняет роль маршрутизатора: получая параметр `command`, он вызывает соответствующий EME-L обработчик. Метод `is_transaction` открывает транзакцию для атомарного сохранения данных, а `is_transaction(-1)` фиксирует её.

В системе EME.WMS класс `dsDB` с указанием записи «Clients» предоставляет доступ к справочнику клиентов с методами поиска, создания и обновления реквизитов контрагентов.
