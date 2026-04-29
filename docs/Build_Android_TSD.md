# Разработка и сборка ТСД (терминал) EME.WMS под Android

> **Поисковые синонимы:** ТСД, терминал сбора данных, Android Studio, CMake, NDK, APK, сборка ТСД, эмулятор Android, Gradle, JDK, Kotlin, Custom View, Terminal/Android, EME.WMS терминал, armDebug, armRelease, winDebug, HTTP-запрос ТСД, CThreadData, EMEGlobalData, DrawListOrders, пакетная обработка, Terminals, BatchRespond

---

## Сборка ТСД

### Подготовка директории и исходников

Для сборки ТСД подготовьте директорию (например, `SRC`), содержащую:

- `Terminal/Android` — исходники Android-составляющей терминала (`http://svn.eme.ru/metaproj/branches/Android/terminal/`)
- `Terminal/Android/core` — исходники ядра ТСД, C++ составляющая (`http://svn.eme.ru/Terminal/src/`)

Дополнительно потребуется:
- **Android Studio** — скачать с `https://developer.android.com/studio?hl=ru`
- **Qt 5.13.2** — архив на сетевом диске `H:\div\Qt\5.13.2.zip` или `I:\android\5.13.2.zip`. Распаковать и прописать переменную `QT5_DIR`
- **Java Development Kit 17** — `https://download.oracle.com/java/17/archive/jdk-17.0.12_windows-x64_bin.exe`

### Пошаговая инструкция сборки ТСД

1. Выгрузить SVN-исходники Android-составляющей в `Terminal/Android`
2. Выгрузить SVN-исходники C++ составляющей в `Terminal/Android/core`
3. Установить Android Studio с дополнительными компонентами
4. Отредактировать `CMakeLists.txt`
5. Открыть проект терминала в Android Studio
6. Настроить Gradle и JDK
7. Настроить эмулятор Android-смартфона (Device Manager)
8. Настроить параметры сборки (Build Variants)
9. Собрать ТСД (`Ctrl-F9`)
10. Протестировать на эмуляторе (`Shift-F10`)
11. Сгенерировать `.apk` файл при необходимости

### Установка Android Studio и компонентов

При установке обязательно отметьте **Android Virtual Device**. После установки выберите **Standard** набор SDK.

Дополнительные компоненты через SDK Manager (`More actions → SDK Manager`):

- **SDK Platforms:** API 18 (Android 4.3 Jelly Bean) — минимальный API для сборки ТСД
- **SDK Tools → CMake** — утилита сборки
- **SDK Tools → NDK Side by side:** версии **22.1.7171670** и **21.1.6352462**

### Редактирование CMake-файла

В файле `Terminal/Android/CMakeLists.txt` пропишите полный путь к исходникам ядра:

```cmake
set(TERMINALCORE_DIR "C:/SRC/Terminal/Android/core/core/")
```

### Настройка Gradle и JDK

1. Откройте `Project Structure` и выберите совместимую версию Gradle
2. Установите JDK 17, затем через `Ctrl-Alt-S → Build Tools → Gradle` выберите JDK 17

### Настройка эмулятора Android

1. `Tools → Device Manager`
2. Создайте `Medium phone` → системный образ **R**
3. Завершите настройку, нажав `Finish`

### Настройка параметров сборки

`Build → Select Build Variant`:

- **Для физического ТСД:** `armDebug/Release`, Active ABI: `armeabi-v7a`
- **Для эмулятора:** `winDebug/Release`, Active ABI: `x86`

### Генерация .apk файла

1. `Build → Generate Signed App Bundle or APK...` → выберите **APK**
2. Путь к хранилищу ключей: `Terminal/Android/terminal_key.s`
3. Пароли: `32543254` (key store password и key password)
4. Key alias: `terminal_key`
5. Выберите платформу (arm/win) и вариант (debug/release)

Результат — директория с `.apk` файлом внутри `SRC/Terminal/Android/`.

### Решение проблем при сборке ТСД

1. **«resource color not found»** — удалите строку `android:background="@color/zxing_transparent"` из файла `layout_eme_qty_and_unit_input.xml` (строки 9 и 67)
2. **«test_classes error»** — удалите файл `MainActivityTest.java` по пути `/Terminal/Android/src/androidTest/java/ru/eme/terminal/android/tests`

---

## Создание собственного View элемента в Android (Kotlin)

Создание собственного View элемента на Kotlin формирует более читаемую и масштабируемую разметку интерфейса ТСД по сравнению с ConstraintLayout.

### Шаг 1. Формирование разметки (custom_test_view.xml)

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:orientation="horizontal">
  <ImageView
      android:id="@+id/imageView"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:layout_marginEnd="8dp"/>
  <TextView
      android:id="@+id/mainTextView"
      android:layout_width="0dp"
      android:layout_height="wrap_content"
      android:layout_weight="1"
      android:text="Main Text"
      android:textSize="16sp" />
  <TextView
      android:id="@+id/suffixTextView"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Suffix"
      android:textSize="14sp"
      android:layout_marginStart="8dp" />
</LinearLayout>
```

### Шаг 2. Формирование атрибутов (attrs.xml)

Название блока `declare-styleable` должно совпадать с названием класса:

```xml
<declare-styleable name="CustomTestView">
    <attr name="imageSrc" format="reference" />
    <attr name="mainText" format="string" />
    <attr name="mainTextColor" format="color"/>
    <attr name="mainTextStyle" format="enum">
        <enum name="normal" value="0"/>
        <enum name="bold" value="1"/>
        <enum name="italic" value="2"/>
    </attr>
    <attr name="suffixText" format="string" />
</declare-styleable>
```

### Шаг 3. Формирование класса (CustomTestView.kt)

Класс наследуется от `FrameLayout`. В блоке `init` выполняется инициализация ViewBinding и чтение XML-атрибутов:

```kotlin
class CustomTestView @JvmOverloads constructor(
  context: Context,
  attrs: AttributeSet? = null,
  defStyleAttr: Int = 0
) : FrameLayout(context, attrs, defStyleAttr) {
  private val binding: CustomTestViewBinding
  init {
      val inflater = LayoutInflater.from(context)
      binding = CustomTestViewBinding.inflate(inflater, this, true)
      context.withStyledAttributes(attrs, R.styleable.CustomTestView) {
          val imageResId = getResourceId(R.styleable.CustomTestView_imageSrc, -1)
          if (imageResId != -1) {
              setImageResource(imageResId)
          }
          val mainText = getString(R.styleable.CustomTestView_mainText)
          setMainText(mainText?:"")
          if (hasValue(R.styleable.CustomTestView_suffixText)) {
              val suffixText = getString(R.styleable.CustomTestView_suffixText)
              setSuffixText(suffixText?:"")
          }
          when (getInt(R.styleable.CustomTestView_mainTextStyle, 0)) {
              0 -> setMainTextStyle(Typeface.NORMAL)
              1 -> setMainTextStyle(Typeface.BOLD)
              2 -> setMainTextStyle(Typeface.ITALIC)
          }
          setMainTextColor(getColor(R.styleable.CustomTestView_mainTextColor,
              ContextCompat.getColor(context, R.color.color_black)))
      }
  }
  fun setImageResource(resId: Int) {
      binding.imageView.setImageResource(resId)
  }
  fun setMainText(text: String) {
      binding.mainTextView.text = text
  }
  fun setSuffixText(text: String) {
      binding.suffixTextView.text = text
  }
  fun setMainTextStyle(typeface: Int) {
      binding.mainTextView.setTypeface(binding.mainTextView.typeface, typeface)
  }
  fun setMainTextColor(colorRes: Int) {
      binding.mainTextView.setTextColor(colorRes)
  }
}
```

### Применение собственного View в интерфейсе ТСД

```xml
<ru.eme.terminal.android.ui.elements.CustomTestView
    android:layout_width="match_parent"
    app:imageSrc="@drawable/arrow_left"
    app:mainText="Main TEXT"
    android:layout_height="wrap_content"/>
```

---

## Обновление списка приказов на ТСД с HTTP-сервера

ТСД работает со списком приказов, загруженных в память с сервера. Фоновый поток периодически отправляет запросы на обновление — по таймеру каждые 10 секунд или после действий пользователя.

HTTP-запросы поступают в ядро, и оно определяет обработчик (C-код или EME-L). Все запросы от ТСД идут на виртуальный каталог `terminals.html`, используется класс **Terminals** в системе EME.WMS.

### Механизм обновления списка приказов

1. `CThreadData::UpdateData()` — отправка запроса с ТСД на сервер (формирование очереди запросов, параллельная отправка на транзакционный и нетранзакционный порты)
2. Проверка регистрации ТСД (`Terminals::CheckTerminal()`) и авторизации кладовщика (`CommonTerminal::CheckOperator()`)
3. `DrawListOrders::DrawListOrdersEx()` — формирование списка настроек системы и вызов методов в зависимости от типа приказов
4. Для каждого приказа формируется блок с параметрами (тип, номер документа). Начало: `Order=Start`, окончание: `Order=End`
5. `CThreadData::OnRequestDone()` — обработка ответа сервера (данные в объекте `CHttpData`)
6. `EMEGlobalData::SaveServerData()` — сохранение данных в глобальный объект. При изменении — сигнал `emit ServerDataChanged()` для обновления экрана
7. Анализ необходимости немедленного запроса (`CHttpData::IsSendRequestImmediately()`)

---

## Оптимизация обмена данными между ТСД и сервером

Каждые 10 секунд ТСД отправляет `GetListOrders`, сервер отвечает `TakeListOrders`. Сервер может присылать «урезанные» данные — только номера строк и изменяемую информацию. Полные данные запрашиваются при расхождениях.

Для добавления нового поля в оптимизацию в языке EME-L:

1. В классе `DrawListOrders` добавьте поле через метод `AddOutputKeys`
2. В методе `IsSameOrders()` (класс `EMEGlobalData`) добавьте сравнение для требуемого поля

Алгоритм оптимизации:

1. `EMEGlobalData::IsGetOnlyOrdersKeys()` — определяет, нужны ли полные данные. Если нет — передаётся признак `GetListOrdersKeys`
2. Сервер формирует ответ с учётом признака `GetListOrdersKeys`
3. В классе `DrawListOrders` устанавливается признак `m_bGetListOrdersKeys`
4. ТСД анализирует ответ и `EMEGlobalData::IsSameOrders()` сравнивает с локальным списком
5. При расхождениях с `GetListOrdersKeys` — немедленный запрос полных данных (`CHttpData::IsSendRequestImmediately()`)

---

## Пакетная обработка HTTP-запросов от ТСД

На больших складах ежесуточно выполняется более 150 000 серверных транзакций с ТСД. Для снижения нагрузки запросы с разных ТСД объединяются в один с общей транзакцией. Обработка перенесена из потоков HTTP-сервера в поток сервера заданий.

### Формирование HTTP-запросов со стороны ТСД

- `CHttpData::GetRequestString()` — формирует строку запроса (транзакционные и нетранзакционные)
- `CEMEHttp::HttpRequest` — формирует порт, URL, отправляет запрос. Сервер возвращает ID запроса, ТСД запоминает его в `ServerJobNo` для цикличного запроса статуса
- `CThreadData::OnRequestDone` — формирует интервал между запросами статусов

### Обработка HTTP-запросов на стороне сервера в EME-L

- `Document_OnBatchRespond(Doc As "HTMLDocument")` — сервер заданий получает наименование класса по команде
- `Document_OnBatchRespond(arrHtmlDocs As "Array", arrEMELObjects As "Array", ErrArray As "Array")` — обработка пачки запросов
- `ParseCommand()` — определяет EME-L класс-обработчик по наименованию команды
- `CheckTerminalBatch()` — пакетный поиск терминалов и проверка версии программы на ТСД
