### author webber (web-ber12@yandex.ru)

### evoSearch - indexing and searching based on morphology (version 0.1)

### DONATE
---------
если считаете данный продукт полезным и хотите отблагодарить автора материально,
либо просто пожертвовать немного средств на развитие проекта - 
можете сделать это на любой удобный Вам электронный кошелек<br><br>
<strong>Яндекс.Деньги</strong> 410011544038803<br>
<strong>Webmoney WMR:</strong> R133161482227<br>
<strong>Webmoney WMZ:</strong> Z202420836069<br><br>
with an optional note from whom and for what exactly


### Package composition:
- **plugin `evoSearch`** - used to index search results. Fields are indexed `pagetitle`, `longtitle` ,`description`, `introtext`, `content` and indicated `TV-параметры`. Требуемые `TV` are specified in the plugin configuration via the admin panel in the field **TV names to search** comma separated. When used to build a list of possible values `TV` snippet `multiParams` (from the kit `eFilter`) not indexed `id` resources, and their titles.
- **сниппет `evoSearch`** - используется для вывода результатов поиска. Может работать в двух режимах: использовать для вывода результатов `DocLister` или возвращать список `id` ресурсов (режим выбирается параметром `&action='ids'`). В последнем случае генерируется и возвращается список `id` ресурсов, подходящих под условия поиска. Эти данные могут использоваться в любом другом сниппете через плейсхолдер `evoSearchIDs`, который содержим массив `id` из результатов поиска.


### Необходимые компоненты для работы:
* `DocLister`

### Установка
Самый просто способ установки - использоваться модуль `Extras` в админке.
В результате установятся необходимые компоненты: plugin, сниппет, чанки.

Для плагина требуется событие `onDocFormSave`.

Options:
* `&offset=First line of reindexing;text;0`
* `&rowsperonce=Rows per session to index;text;1`
* `&reindex=Re-index all;text;0`
* `&excludeTmpls=Exclude templates;text;`
* `&excludeIDs=Exclude resource IDs;text;`
* `&TvNames=TV names to search;text;`
* `&unpublished=Index unpublished;text;0`
* `&deleted=Index deleted;text;0`
* `&dicts=Use dictionaries;text;rus,eng`

 ## Важно
 * До первого запуска сниппета на фронтэнде сайта необходимо провести индексацию.
 * Cоздание необходимых полей `content_with_tv` и `content_with_tv_index`, а также нужных индексов производится **автоматически** при первом запуске индексации.
 
## Первый запуск и переиндексация

1. При первом запуске или переиндексации выставить параметры в плагине:
* Переиндексировать все = 1
* Первая строка переиндексации = 0 *
* Строк за сеанс индексировать = 10 000 *
2. открыть и пересохранить любой документ - необходимо для вызова события `onDocFormSave`.
3. Установить *Переиндексировать все = 0*, *Строк за сеанс индексировать = 1* 

\* Первая строка и количество строк за сеанс устанавливать в зависимости от возможностей хостинга.
Например, **первая строка 0** и **строк за сеанс 10 000** проиндексирует в БД строки с 0 в количестве 10 000 штук.

### Пример вызова
    для вывода результатов [!evoSearch? &tpl=`evoSearch`!]
Чанк `evoSearch` создается при установке дополнения.

### ПАРАМЕТРЫ СНИППЕТА
Сниппет `evoSearch`является оберткой для `DocLister`, поэтому то он принимает все параметры `DocLister`.
 + **&action = ids** - возвращает список найденных `ids`, которые можно подставить в другой сниппет. По-умолчанию - отрабатывает полностью с выводом результатов
 + **&noResult** - шаблон строки, которая выводится при отсутствии результата поиска. Значение по-умолчанию:
 ```
 &noResult = "По запросу <u>[+stat_request+]</u> ничего не найдено. Смягчите условия поиска")
 ```
 + **&extract** - отключить экстрактор. Формирует нужную часть текста с подсветкой из результатов поиска. Плейсхолдер `[+extract+]` в чанке вывода результатов `DocLister`. Значение по-умолчанию:
  ```
 &extract = 1
 ```
 + **&maxlength** - максимальная длина извлекаемой части текста в результатах поиска. Значение по-умолчанию:
 ```
 &maxlength = 350
 ```
 + **&show_stat** - показ статистики. Значение по-умолчанию:
 ```
 &show_stat = 1
 ```
 + **&statTpl** - шаблон показа статистики. Значение по-умолчанию:
 ```html
<div class="evoSearch_info">По запросу <b>[+stat_request+]</b> найдено всего <b>[+stat_total+]</b>. Показано <b>[+stat_display+]</b>, c [+stat_from+] по [+stat_to+]</div>
```
где
* `[+stat_request+]` - запрос из строки `$_GET['search']`
* `[+stat_total+]` - количество найденных документов
* `[+stat_display+]` - показано на текущей странице с `[+stat_from+]` по `[+stat_to+]`

 + **&rel** - релевантность поиска.по умолчанию 0.01, Чем выше цифра - тем более релевантные результаты и тем их меньше. Значение по-умолчанию:
 ```
 &rel = 0.01
 ```
 + **&search_field** - поле `$_GET` для запроса. По-умолчанию запрос ищется в `$_GET['search']`.
 + **&minlength** - минимальная длина слова, которое будет участвовать в полнотекстовом поиске. Значение по-умолчанию:
  ```
 &minlength = 2
 ```

подсветка найденных слов в pagetitle и extract в результатах поиска осуществляется тегом <span class="evoSearch_highlight"> - т.е. возможна ее стилизация через css-файлы
 
### Дополнительная информация.
* Подсветка найденных слов в `pagetitle` и `extract` в результатах поиска осуществляется тегом `<span class="evoSearch_highlight">`. Возможна ее стилизация через css-файлы.
* Т.к. при полнотекстовом поиске *MySQL* без дополнительных настроект обрабатываются только слова не короче 4 символов. Для улучшения результатов поиска используется дополнительный поиск средствами фильтров `DocLister`, что улучшает результаты. Особенно при их отстутствии в результате обычного поиска.
* Совместим с `DocLister` версии **1.4.1 и ниже, 1.4.8 и выше**.
* В версиях **1.4.5, 1.4.6, 1.4.7** встречается некорректный сброс строки `$_GET`, из-за чего некорректно срабатывает обработка пустых результатов.

### Сотрудничество:
---------
По вопросам сотрудничества обращайтесь на электронный ящик web-ber12@yandex.ru
