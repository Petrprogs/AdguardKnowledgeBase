---
title: 'Как составлять свои фильтры'
taxonomy:
    category:
        - docs
visible: true
---

* [Введение](#introduction)
* [Комментарии](#comments)
* [Примеры](#examples)
    * [Блокировка по имени домена](#example-blocking-by-domain-name)
    * [Блокировка конкретного адреса](#example-blocking-exact-address)
    * [Базовые модификаторы правил](#example-basic-rule-modifiers)
    * [Разблокировка адреса](#example-unblocking-an-address)
    * [Разблокировка всего сайта](#example-unblocking-website)
    * [Косметические правила](#example-cosmetic-rule)
* [Базовые правила](#basic-rules)
    * [Синтаксис базовых правил](#basic-rules-syntax)
    * [Специальные символы](#basic-rules-special-characters)
    * [Поддержка регулярных выражений](#regexp-support)
    * [Примеры базовых правил](#basic-rules-examples)
    * [Модификаторы](#basic-rules-modifiers)
        * [Базовые модификаторы](#basic-rules-common-modifiers)
            * [$domain](#domain-modifier)
            * [$third-party](#third-party-modifier)
            * [$popup](#popup-modifier)
            * [$match-case](#match-case-modifier)
            * [$header](#header-modifier)
        * [Ограничение по типу контента](#content-type-modifiers)
            * [Примеры ограничений](#content-type-modifiers-examples)
            * [$document](#document-modifier)
            * [$image](#image-modifier)
            * [$stylesheet](#stylesheet-modifier)
            * [$script](#script-modifier)
            * [$object](#object-modifier)
            * [$font](#font-modifier)
            * [$media](#media-modifier)
            * [$subdocument](#subdocument-modifier)
            * [$ping](#ping-modifier)
            * [$xmlhttprequest](#xmlhttprequest-modifier)
            * [$websocket](#websocket-modifier)
            * [$webrtc](#webrtc-modifier)
            * [$other](#other-modifier)
            * [$object-subrequest (устаревший)](#object-subrequest-modifier)
        * [Модификаторы правил-исключений](#exceptions-modifiers)
            * [$elemhide](#elemhide-modifier)
            * [$content](#content-modifier)
            * [$jsinject](#jsinject-modifier)
            * [$urlblock](#urlblock-modifier)
            * [$extension](#extension-modifier)
            * [$stealth](#stealth-modifier)
            * [Generic правила](#generic-rules)
                * [$generichide](#generichide-modifier)
                * [$genericblock](#genericblock-modifier)
            * [$specifichide](#specifichide-modifier)
    * [Расширенные возможности](#advanced-modifiers)
        * [$important](#important-modifier)
        * [$badfilter](#badfilter-modifier)
        * [$replace](#replace-modifier)
        * [$csp](#csp-modifier)
        * [$all](#all-modifier)
        * [$cookie](#cookie-modifier)
        * [$network](#network-modifier)
        * [$app](#app-modifier)
        * [$redirect](#redirect-modifier)
        * [$redirect-rule](#redirect-rule-modifier)
        * [$denyallow](#denyallow-modifier)
        * [$removeparam](#removeparam-modifier)
        * [$removeheader](#removeheader-modifier)
        * [$hls](#hls-modifier)
        * [$jsonprune](#jsonprune-modifier)
        * [noop](#noop-modifier)
        * [$empty (устаревший)](#empty-modifier)
        * [$mp4 (устаревший)](#mp4-modifier)
* [Другие правила](#non-basic-rules)
    * [Поддержка wildcard для TLD](#wildcard-for-tld)
    * [Косметические правила](#cosmetic-rules)
        * [Правила скрытия элементов](#cosmetic-elemhide-rules)
        * [Правила CSS-стилей](#cosmetic-css-rules)
        * [Расширенные CSS-селекторы](#extended-css-selectors)
            * [Псевдокласс `:has()`](#extended-css-has)
            * [Псевдокласс `:if-not()`](#extended-css-has)
            * [Псевдокласс `:contains()`](#extended-css-contains)
            * [Псевдокласс `:matches-css()`](#extended-css-matches-css)
            * [Псевдокласс `:matches-attr()`](#extended-css-matches-attr)
            * [Псевдокласс `:matches-property()`](#extended-css-matches-property)
            * [Псевдокласс `:xpath()`](#extended-css-xpath)
            * [Псевдокласс `:nth-ancestor()`](#extended-css-nth-ancestor)
            * [Псевдокласс `:upward()`](#extended-css-upward)
            * [Псевдокласс `:remove()` и псевдосвойство `remove`](#remove-pseudos)
        * [Приоритет косметических правил](#cosmetic-rules-priority)
    * [Правила фильтрации HTML](#html-filtering-rules)
        * [Синтаксис](#html-filtering-rules-syntax)
        * [Специальные атрибуты](#html-filtering-rules-attributes)
            * [tag-content](#tag-content-attribute)
            * [wildcard](#wildcard-attribute)
            * [max-length](#max-length-attribute)
            * [min-length](#min-length-attribute)
        * [Исключения](#html-filtering-rules-exceptions)
    * [JavaScript-правила](#javascript-rules)
    * [Скриптлеты](#scriptlets)
    * [Модификаторы](#non-basic-rules-modifiers)
        * [Синтаксис](#non-basic-rules-modifiers-syntax)
        * [$app](#non-basic-modifiers-app)
        * [$domain](#non-basic-rules-modifiers-domain)
        * [$path](#non-basic-rules-modifiers-path)
        * [$url](#non-basic-rules-modifiers-url)
* [Информация для разработчиков фильтров](#for_maintainers)
    * [Директивы пре-процессора](#pre_processor)
    * [Hints](#hints)
        * [Синтаксис Hints](#hints_syntax)
        * [Оптимизация фильтров и NOT_OPTIMIZED hint](#not_optimized)
        * [PLATFORM и NOT_PLATFORM hints](#platform_not_platform)
* [Как отлаживать работу фильтров](#how-to-debug)
    * [Журнал фильтрации](#debig-filtering-log)
    * [Режим отладки селекторов](#selectors-debugging-mode)
        * [Тестирование расширенных селекторов](#testing-extended-selectors)
    * [Отладка скриптлетов](#debug-scriptlets)
* [Удачи в составлении своих фильтров!](#good-luck)

<a id="introduction"></a>
## Введение

Фильтр — это набор правил фильтрации рекламного контента (баннеров, всплывающих окон и тому подобного). Вместе с AdGuard поставляется набор стандартных фильтров, создаваемых нами. Они постоянно дорабатываются и дополняются, и, как мы надеемся, удовлетворяют требованиям большинства пользователей.

Вместе с тем, AdGuard позволяет создать ваш собственный пользовательский фильтр, используя те же самые правила, которые используем мы в наших фильтрах.

Для описания синтаксиса правил мы используем [Augmented BNF for Syntax Specifications](https://tools.ietf.org/html/rfc5234), но мы не всегда строго следуем этой спецификации.

> Синтаксис правил AdGuard изначально основан на синтаксисе правил Adblock Plus, но расширяет его, добавляя новые типы правил для улучшения фильтрации. Часть информации в этой статье об общих для ABP и AdGuard типах правил взята из [этой статьи](http://adblockplus.org/ru/filters).

<a id="comments"></a>
## Комментарии

Любое правило, начинающееся с восклицательного знака, содержит комментарий. Оно отображается в списке правил серым цветом. AdGuard будет игнорировать это правило, так что можете спокойно писать там всё, что хотите. Вы можете, например, расположить комментарий выше реального правила, чтобы описать для чего оно нужно.

Например:
```
! Это текст комментария. Под ним, для сравнения, текст реального правила фильтрации.
||example.org^
```

<a id="examples"></a>
## Примеры

<a id="example-blocking-by-domain-name"></a>
### Блокировка по имени домена

<object data="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/0_blocking_domain.svg" type="image/svg+xml">
    <img src="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/0_blocking_domain.svg" />
</object>

**Это правило блокирует:**

* `http://example.org/ad1.gif`
* `http://subdomain.example.org/ad1.gif`
* `https://ads.example.org:8000/`

**Это правило не блокирует:**

* `http://ads.example.org.us/ad1.gif`
* `http://example.com/redirect/http://ads.example.org/`

<a id="example-blocking-exact-address"></a>
### Блокировка конкретного адреса

<object data="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/1_exact_address.svg" type="image/svg+xml">
    <img src="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/1_exact_address.svg" />
</object>

**Это правило блокирует:**

* `http://example.org/`

**Это правило не блокирует:**

* `https://example.org/banner/img`

<a id="example-basic-rule-modifiers"></a>
### Базовые модификаторы правил

Правила фильтрации поддерживают множество модификаторов, которые позволяют вам точно настраивать поведение правила. Вот пример правила с некоторыми простыми модификаторами.

<object data="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/2_basic_rule_options.svg" type="image/svg+xml">
    <img src="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/2_basic_rule_options.svg" />
</object>

**Это правило блокирует:**

* `http://example.org/script.js` если этот скрипт загружен с `example.com`.

**Это правило не блокирует:**

* `https://example.org/script.js` если этот скрипт загружен с `example.org`.
* `https://example.org/banner.png` потому что это не является скриптом.

<a id="example-unblocking-an-address"></a>
### Разблокировка адреса

<object data="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/3_basic_rule_exception.svg" type="image/svg+xml">
    <img src="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/3_basic_rule_exception.svg" />
</object>

**Это правило разблокирует:**

* `http://example.org/banner.png` даже если для этого адреса есть правило блокировки.

> Обратите внимание, что правила блокировки с модификатором [`$important`](#important-modifier) все равно будут приоритетнее, чем обычные правила разблокировки.

<a id="example-unblocking-website"></a>
### Разблокировка всего сайта

<object data="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/4_unblock_entire_website.svg" type="image/svg+xml">
    <img src="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/4_unblock_entire_website.svg" />
</object>

**Это правило разблокирует:**

* Оно отключает все косметические правила на `example.com`
* Оно блокирует все запросы, отправленные с этого веб-сайта, даже если есть правила блокировки, соответствующие этим запросам

<a id="example-cosmetic-rule"></a>
### Косметические правила

<object data="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/5_cosmetic_rules.svg" type="image/svg+xml">
    <img src="https://cdn.adguard.com/public/Adguard/kb/ru/rules_syntax/5_cosmetic_rules.svg" />
</object>

Косметические правила применяются с использованием CSS — специального языка программирования, который понимает каждый браузер. В основном, он добавляет новый стиль CSS на сайт, целью которого является скрыть определенные элементы. Узнать больше о CSS [Подробнее](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Selectors).

> [Расширенный CSS](#extended-css-selectors) позволяет разработчикам фильтров справляться с гораздо более сложными случаями. Однако, чтобы использовать эти расширенные правила, вы должны хорошо понимать, что такое CSS.

<a id="example-popular-css-selectors"></a>
#### Популярные CSS-селекторы


| Имя | CSS-селектор | Описание|
| ------| ------ | ----------- |
| ID selector | `#banners`   | Соответствует всем элементам с атрибутом `id` равным `banners`.<br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_id_selector.png) |
| Class selector | `.banners`   | Соответствует всем элементам типа `class` содержащих `banners`.<br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_class_selector.png) |
| Attribute selector | `div[class="banners"]`   | Соответствует всем `div` элементам с `class` **равным**  `banners`.<br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_class_attr.png) |
| Attribute substring selector | `div[class^="advert1"]`   | Соответствует всем `div` элементам класс `class` которых **начинается** с `advert1` строки.<br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_class_attr_start.png) |
| Attribute substring selector | `div[class$="banners_ads"]`   | Соответствует всем `div` элементам `class` которых **заканчивается** на `banners_ads`.<br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_class_attr_end.png) |
| Attribute substring selector | `a[href^="http://example.com/"]`   | Соответствует всем ссылкам загруженным с `http://example.com/` <br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_attr_start.png) |
| Attribute selector | `a[href="http://example.com/"]`   | Соответствует всем ссылкам **конкретно** `http://example.com/` .<br/>![](https://cdn.adguard.com/public/Adguard/kb/en/rules_syntax/css_attr_equal.png)

<a id="basic-rules"></a>
## Базовые правила

Так называемые _"Базовые правила"_ — самый простой вид правил. Эти правила предназначены для блокировки запросов на определенный адрес. Либо, при наличии специального маркера `@@` в начале правила, для разблокировки запроса. Основной принцип для этого типа правил достаточно прост: необходимо указать адрес и дополнительные параметры, которые ограничивают или расширяют область действия правила.

> #### Подзапросы
> Базовые правила, блокирующие запросы, применяются **только к подзапросам**. То есть, они не будут блокировать загрузку страницы в браузере.

> #### Статус ответа
> Браузер определяет заблокированный подзапрос как выполненный с ошибкой.

<a id="basic-rules-syntax"></a>
### Синтаксис базовых правил

```
      rule = ["@@"] pattern [ "$" modifiers ]
 modifiers = [modifier0, modifier1[, ...[, modifierN]]]
```

* **`pattern`** — маска адреса. URL каждого запроса сопоставляется с этой маской. В шаблоне вы можете использовать некоторые специальные символы, описание которых будет дано [ниже](#basic-rules-special-characters). Обратите внимание, что AdGuard обрезает URL до длины 4096 символов, чтобы ускорить процесс подбора и избежать проблем с длинными URL.
* **`@@`** — маркер, который используется для обозначения правил-исключений. С такого маркера должны начинаться правила, отключающие фильтрацию для запроса.
* **`modifiers`** — параметры, используемые для "уточнения" базового правила. Некоторые параметры ограничивают область действия правила, а некоторые могут полностью изменить принцип его работы.

<a id="basic-rules-special-characters"></a>
### Специальные символы

* ```*``` — wildcard-символ. Символ, обозначающий "произвольный набор символов". Это может быть как пустая строка, так и строка любой длины.
* **`||`** — соответствие началу адреса. Этот специальный символ позволяет не указывать конкретный протокол и поддомен в маске адреса. То есть, `||` соответствует сразу `http://*.`, `https://*.`, `ws://*.`, `wss://*.`.
* **`^`** — указатель для разделительного символа. Разделителем может быть любой символ кроме буквы, цифры и следующих символов: `_` `-` `.` `%`. Например, в адресе `http:`**`//`**`example.com`**`/?`**`t=1`**`&`**`t2=t3` жирным выделены разделительные символы. Конец адреса также принимается в качестве разделителя.
* **`|`** — указатель на начало или конец адреса. Значение зависит от того, находится этот символ в начале или конце маски. Например, правило `swf|` соответствует `http://example.com/annoyingflash.swf` , но не `http://example.com/swf/index.html`. `|http://example.org` соответствует `http://example.org`, но не `http://domain.com?url=http://example.org`.

> **Визуальное представление**. Также советуем ознакомиться с [этой статьей](https://adblockplus.org/filter-cheatsheet#blocking), чтобы лучше понять, как должны строиться такие правила.

<a id="regexp-support"></a>
### Поддержка регулярных выражений

Если вы хотите добиться еще большей гибкости при составлении правил, вы можете использовать [регулярные выражения](https://developer.mozilla.org/ru/docs/Web/JavaScript/Guide/Regular_Expressions) вместо упрощенной маски со специальными символами, которая используется по умолчанию.

> **Производительность**. Такие правила работают медленнее обычных, поэтому рекомендуется избегать их, или хотя бы ограничивать их область действия конкретными доменами.

Чтобы блокировщик определил, что вы хотите использовать регулярное выражение, необходимо, чтобы `pattern` имел особый вид:
```
pattern = "/" regexp "/"
```

Например, правило `/banner\d+/$third-party` применит регулярное выражение `banner\d+` ко всем сторонним запросам. Правила-исключения с использованием регулярных выражений выглядят вот так: `@@/banner\d+/`.

> **Совместимость с разными версиями AdGuard.** AdGuard для Safari и AdGuard для iOS не полностью поддерживают регулярные выражения в силу [ограничений Content Blocking API](https://webkit.org/blog/3476/content-blockers-first-look/) (в статье по ссылке найдите раздел "The Regular expression format").

<a id="basic-rules-examples"></a>
### Примеры базовых правил

* `||example.com/ads/*` — простое правило, которое соответствует адресам типа `http://example.com/ads/banner.jpg` и даже `http://subdomain.example.com/ads/otherbanner.jpg`.

* `||example.org^$third-party` — правило, которое блокирует сторонние запросы к домену `example.org` и его поддоменам.

* `@@||example.com$document` — наиболее общее правило-исключение. Такое правило полностью отключает фильтрацию на домене `example.com` и всех его поддоменах. Существует ряд параметров, которые также можно использовать в правилах-исключениях. Более подробно о правилах-исключениях и параметрах, которые могут в таких правилах использоваться, написано [ниже](#exceptions-modifiers).

<a id="basic-rules-modifiers"></a>
### Модификаторы

> **Обратите внимание!** Возможности, описанные в этом разделе, обычно используются опытными пользователями. Они расширяют возможности «Общих правил», но для их применения необходимо иметь начальное представление о работе браузера.

Вы можете изменить поведение «общего правила», используя дополнительные модификаторы. Список этих параметров располагается в конце правила за знаком доллара `$` и разделяется запятыми.

Пример использования модификаторов:
```
||domain.com^$popup,third-party
```

> **Визуальное представление**. также советуем ознакомиться с [этой статьей](https://adblockplus.org/filter-cheatsheet#options), чтобы лучше понять, как должны применяться модификаторы.

<a id="basic-rules-common-modifiers"></a>
#### Базовые модификаторы

Приведенные ниже модификаторы являются наиболее простыми для понимания и часто применяемыми.

<a id="domain-modifier"></a>
##### **`domain`**

Модификатор `domain` ограничивает область действия правила списком доменов (и их поддоменов).

###### Синтаксис

Модификатор представляет собой список из одного или нескольких выражений, разделённых символом `|`, каждое из которых сопоставляется с доменом определённым образом в зависимости от типа (см. ниже).

```
 domains = ["~"] entry_0 ["|" ["~"] entry_1 ["|" ["~"]entry_2 ["|" ... ["|" ["~"]entry_N]]]]
 entry_i = ( regular_domain / any_tld_domain / regexp )
```

* **`regular_domain`** — обычное имя домена (`domain.com`). Соответствует указанному домену и его поддоменам. Сопоставляется лексикографически.
* **`any_tld_domain`** — имя домена, оканчивающееся wildcard-символом в качестве TLD (`domain.*`). Соответствует указанному домену и его поддоменам с любым TLD. Сопоставляется лексикографически.
* **`regexp`** — регулярное выражение, начинается и заканчивается символами `/`. Паттерн работает так же, как в базовых URL правилах, однако символы `/`, `$`, и `|` необходимо экранировать с помощью `\`.

> **Совместимость с разными версиями AdGuard.** Правила с регулярными выражениями в модификаторе `$domain` поддерживаются в AdGuard для Windows, Mac, и Android, **с CoreLibs версии 1.11 или выше**.
> Правила с `regular_domain` и `any_tld_domain` в модификаторе `$domain` поддерживаются во всех продуктах AdGuard.

Символ `~` перед выражением используется для обозначения исключений. Запросы с доменов, соответствующих таким выражениям, не попадают под действие правил, содержащих их.

**Примеры:**

* `||baddomain.com^$domain=example.org` — правило для блокировки запросов, которые соответствуют указанной маске, и отправленных с домена или поддомена `example.org`.
* `||baddomain.com^$domain=example.org|example.com` — такое же правило, но срабатывать оно будет как для домена `example.org`, так и для `example.com`.
* `||baddomain.com^$domain=example.*` — такое же правило, но срабатывать оно будет как для домена `example` с любым TLD: `example.org`, `example.com`, `example.co.uk` и т.д.
* `||baddomain.com^$domain=~example.org` — правило для блокировки запросов, которые соответствуют указанной маске, и отправленных с любого домена, кроме `example.org` и его поддоменов.
* `||baddomain.com^$domain=example.org|~foo.example.org` — в данном примере правило будет соответствовать запросам, отправленным с домена `example.org` и всех его поддоменов, кроме поддомена `foo.example.org`.
* `||baddomain.com^$domain=/(^\|.+\.)example\.(com\|org)\$/` — в данном примере правило будет соответствовать запросам, отправленным с доменов `example.org` и `example.com` и всех их поддоменов.
* `||baddomain.com^$domain=~a.com|~b.*|~/(^\|.+\.)c\.(com\|org)\$/` — в данном примере правило будет соответствовать запросам, отправленным с любых доменов, кроме `a.com`, `b` с любым TLD (`b.com`, `b.co.uk` и т.д.), `c.com` и `c.org`, а также всех поддоменов, всех указанных доменов.

###### Когда `domain` соответствует целевому домену

В некоторых случаях модификатор `$domain` может соответствовать не только домену-рефереру, но и целевому домену. Это происходит в случае, когда всё из перечисленного верно:

1) Запрос имеет тип `document`
2) Паттерн правила не соответствует какому-либо или каким-либо конкретным доменам
3) Паттерн правила не содержит регулярных выражений
4) Модификатор `$domain` содержит только исключённые домены

Если будет выполняться условие `1 и ((2 и 3) или 4)`, то модификатор `$domain` будет соответствовать как домену-рефереру, так и целевому домену. То есть, если модификатор `$domain` содержит только исключённые домены, то правилу не нужно выполнять второе и третье условия, чтобы соответствовать целевому домену `$domain`.

Если какие-либо из условий выше не выполнены, но правило содержит модификатор [`$cookie`](#cookie-modifier) или [`$csp`](#csp-modifier), модификатор `$domain` всё равно будет соответствовать целевому домену.

Если реферер соответствует правилу с `$domain`, которое явно исключает домен реферера, то правило не сработает, даже если целевой домен тоже ему соответствует. Это также касается правил с модификаторами [`$cookie`](#cookie-modifier) и [`$csp`](#csp-modifier).

**Примеры:**

* `*$cookie,domain=example.org|example.com` заблокирует cookies для всех запросов от и к `example.org` и `example.com`.
* `*$document,domain=example.org|example.com` заблокирует все запросы от и к `example.org` и `example.com`.

В следующих примерах предполагается, что запросы отправляются от `http://example.org/page`(реферер), а целевой URL — `http://targetdomain.com/page`.

* `page$domain=example.org` сработает, так как соответствует рефереру.
* `page$domain=targetdomain.com` сработает, так как соответствует целевому домену, но выполнены все условия, перечисленные выше.
* `||*page$domain=targetdomain.com` не сработает, поскольку паттерн `||*page` указывает на конкретные домены.
* `||*page$domain=targetdomain.com,cookie` сработает несмотря на то, что паттерн `||*page` указывает на конкретные домены, поскольку правило содержит модификатор `$cookie`.
* `/banner\d+/$domain=targetdomain.com` не сработает, поскольку правило содержит регулярное выражение.
* `page$domain=targetdomain.com|~example.org` не сработает, так как домен реферера явно исключён.

> **Ограничения:** Safari не поддерживает одновременно разрешенные и запрещенные домены, поэтому правила вида `||baddomain.com^$domain=example.org|~foo.example.org` не работают в AdGuard для Safari.

<a id="third-party-modifier"></a>
##### **`third-party`**

Ограничение на сторонние или собственные запросы. Сторонним является запрос, отправленный с другого домена. Например, запрос к домену `example.org`, отправленный с домена `domain.com`, является сторонним.

> Чтобы считаться таковым, сторонний запрос должен удовлетворять одному из следующих условий:
> 1) Он не должен быть отправлен с поддомена того же домена или наоборот.  Например, запрос к `subdomain.example.org`, отправленный с домена `example.org`, не является сторонним.
> 2) Его заголовок `Sec-Fetch-Site` имеет значение `cross-site`.

Если указан модификатор `$third-party`, то правило применяется только к сторонним запросам.

###### Примеры `third-party`

* `||domain.com^$third-party` — правило применяется на всех сайтах, кроме `domain.com` и его поддоменов. Пример стороннего запроса: `http://example.org/banner.jpg`.

Если указан модификатор `$~third-party`, то правило применяется только к запросам, которые не являются сторонними. То есть, эти запросы отправлены с того же домена.

###### Примеры `~third-party`

* `||domain.com$~third-party` — такое правило уже будет применяться только на самом `domain.com`, но не на других сайтах. Пример запроса, который не является сторонним: `http://domain.com/icon.ico`.

<a id="popup-modifier"></a>
##### **`popup`**

AdGuard будет пытаться закрыть браузерную вкладку с любым адресом, подходящим под правило с этим модификатором. Обратите внимание, что закрыть можно не любую вкладку.

###### Примеры `popup`

* `||domain.com^$popup` — при попытке перехода на сайт `http://domain.com` с любой страницы в браузере, новая вкладка, в которой должен открыться указанный сайт, будет закрыта.

>Модификатор может не работать, если всплывающая страница закеширована браузером. Также модификатор может не сработать, если для показа попапа используются нестандартные методы. В такие случаях лучше использовать расширение [AdGuard Popup Blocker](https://github.com/AdguardTeam/PopupBlocker).

>**Обратите внимание:** В отличие от Браузерного расширения AdGuard, модификатор `$popup` крайне нестабильно работает с AdGuard для Windows, Mac и Android. В AdGuard для Safari и iOS `$popup`-правила просто заблокируют страницу.


<a id="match-case-modifier"></a>
##### **`match-case`**

Определяет правило, которое применяется только к адресам с совпадением регистра символов. По умолчанию регистр символов не учитывается.

###### Примеры `match-case`

* `*/BannerAd.gif$match-case` — такое правило будет блокировать `http://example.com/BannerAd.gif`, но не `http://example.com/bannerad.gif`.

<a id="header-modifier"></a>
##### **`header`**

Модификатор `$header` ограничивает область действия правила запросами, ответы которых содержат заголовки, соответствующие правилу.

###### Синтаксис

```
$header "=" h_name [":" h_value]
h_value = string / regexp
```

где

  * `h_name` — имя HTTP заголовка. Сопоставляется лексикографически без учёта регистра символов.
  * `h_value` — выражения для сопоставления значения HTTP заголовка, может быть одним из:
    * `string` — последовательность символов. Сопоставляется лексикографически с учётом регистра символов.
    * `regexp` — регулярное выражение, начинается и заканчивается символом `/`. Выражение работает аналогично [маске адреса](#basic-rules-syntax) правила, символы `/`, `$` и `,` должны быть экранированы с помощью `\`.

Часть модификатора со значением заголовка (`":" h_value`) может быть опущена. В этом случае модификатор сопоставляет только имя заголовка.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$header` поддерживаются в AdGuard для Windows, Mac, и Android, **с CoreLibs версии 1.11 или выше**.

###### Примеры

`||example.com^$header=set-cookie:foo` — блокирует запрос, ответ которого содержит заголовок `Set-Cookie` со значением `foo`.
`||example.com^$header=set-cookie` — блокирует запрос, ответ которого содержит заголовок `Set-Cookie`.
`@@||example.com^$header=set-cookie:/foo\, bar\$/` — разблокирует запрос, ответ которого содержит заголовок `Set-Cookie` со значением, соответствующим регулярному выражению `foo, bar$`.
`@@||example.com^$header=set-cookie` — разблокирует запрос, ответ которого содержит заголовок `Set-Cookie`.


<a id="content-type-modifiers"></a>
#### Ограничение по типу контента

Существует целый набор модификаторов, которые ограничивают действие правила только запросами к ресурсам определенного типа. Эти модификаторы можно комбинировать, например распространить правило одновременно на картинки и скрипты.

> **Совместимость с разными версиями AdGuard.** Существует большая разница в том, как разные версии AdGuard определяют тип контента. В случае Браузерного расширения AdGuard тип контента для каждого запроса предоставляется самим браузером. В случае AdGuard для Windows, Mac, Android для определения используется следующая методика: вначале мы пытаемся определить тип запроса по расширению загружаемого файла. Если запрос не заблокирован на этом этапе, то тип запроса уточняется с использованием заголовка `Content-Type` в начале ответа, полученного от сервера.

<a id="content-type-modifiers-examples"></a>
##### Примеры ограничений

* `||example.org^$image` — соответствует всем картинкам с домена `example.org`.
* `||example.org^$script,stylesheet` — соответствует всем скриптам и стилям с домена `example.org`.
* `||example.org^$~image,~script,~stylesheet` — соответствует всем запросам к домену `example.org` кроме картинок, скриптов и стилей.

<a id="document-modifier"></a>
##### **`document`**

Правило соответствует запросам основного документа страницы, т.е. HTML-документа, который загружается во вкладке браузера. Оно не подходит для frame-элементов, для которых существует модификатор `$subdocument`.

По умолчанию AdGuard не будет блокировать запросы, которые загружаются во вкладке браузера. Идея заключается в том, чтобы не препятствовать загрузке страниц, поскольку пользователь явно указал, что он хочет, чтобы эта страница была загружена. Однако, если использовать модификатор `$document`, то AdGuard будет предотвращать загрузку страницы, то есть заблокирует её.

Если этот модификатор используется в правиле-исключении (`@@`), то оно полностью отключает блокировку на соответствующих страницах. Это равносильно одновременному использованию модификаторов `$elemhide`, `$content`, `$urlblock`, `$jsinject` и `$extension`.

###### Примеры `document`

* `@@||example.com^$document` — полностью отключает фильтрацию на всех страницах сайта `example.com` и всех его поддоменах.
* `@@|||example.com^$document,~extension` — полностью отключает блокировку на любых страницах сайта `example.com` и всех его поддоменах, кроме применения пользовательских скриптов.

* `|||example.com^$document` — блокирует запрос HTML-документа на `example.com`.
* `||example.com^$document,redirect=noopframe` — перенаправляет запрос HTML-документа сайта `example.com` на пустой HTML-документ.
* `||example.com^$document,removeparam=test` — удаляет параметр `test` из запроса HTML-документа к `example.com`.
* `||example.com^$document,replace=/test1/test2/` — заменяет `test1` на `test2` в запросе HTML-документа к `example.com`.

> **Совместимость с разными версиями AdGuard.** Правила блокировки с модификатором `$document` не поддерживаются в AdGuard Content Blocker.

<a id="image-modifier"></a>
##### **`image`**

Правило будет соответствовать запросам к изображениям.

<a id="stylesheet-modifier"></a>
##### **`stylesheet`**

Правило будет соответствовать запросам к файлам CSS-стилей.

<a id="script-modifier"></a>
##### **`script`**

Правило будет соответствовать запросам к файлам скриптов, например, javascript или vbscript.

<a id="object-modifier"></a>
##### **`object`**

Правило применяется к ресурсам, управляемым браузерными плагинами, например, Java или Flash.

<a id="font-modifier"></a>
##### **`font`**

Правило будет соответствовать запросам к файлам шрифтов (например, файлам с расширением .woff).

<a id="media-modifier"></a>
##### **`media`**

Правило будет соответствовать запросам к медиа-файлам — музыка и видео, например, файлы с расширением .mp4.

<a id="subdocument-modifier"></a>
##### **`subdocument`**

Правило будет соответствовать запросам к встроенным страницам — HTML-теги `frame` и `iframe`.

###### Примеры `subdocument`

* `||example.com^$subdocument` - отключает загрузку встроенных страниц (`frame` и `iframe`) с `example.com` и его поддоменов.
* `||example.com^$subdocument,domain=domain.com` - отключает загрузку встроенных страниц (`frame` и `iframe`) с `example.com`(и поддоменов) на `domain.com` и его поддоменах.

<a id="ping-modifier"></a>
##### **`ping`**

Правило будет соответствовать запросам, сделанным либо `navigator.sendBeacon()`, либо атрибутом ссылки `ping`.

> **Совместимость с разными версиями AdGuard.** AdGuard для Windows, Mac, Android не всегда могут корректно определить `navigator.sendBeacon()`. Для надёжного определения используйте Браузерное расширение AdGuard.

<a id="xmlhttprequest-modifier"></a>
##### **`xmlhttprequest`**

Правило применяется только к ajax-запросам. То есть к запросам, отправленным с помощью javascript-объекта `XMLHttpRequest`.

> **Совместимость с разными версиями AdGuard.** AdGuard для Windows, Mac и Android не всегда могут корректно определить этот тип контента, и определяют его как [`$other`](#other-modifier) или [`$script`](#script-modifier). Для надёжного определения используйте Браузерное расширение AdGuard.

<a id="websocket-modifier"></a>
##### **`websocket`**

Правило применяется только к WebSocket-соединениям.

> **Совместимость с разными версиями AdGuard.** AdGuard для Safari и iOS не могут правильно применять правила с модификатором `$websocket` из-за ограничений Safari.

<a id="webrtc-modifier"></a>
##### **`webrtc`**

> **Предупреждение об устаревании.** Модификатор `$webrtc` устарел и в будущем его поддержка прекратиться. Если вы хотите блокировать WebRTC, рассмотрите возможность использования [скриптлета](#scriptlets) `nowebrtc`.

Правило применяется только к WebRTC-соединениям.

> Обратите внимание, что блокировка WebRTC может мешать работе некоторых браузерных приложений, таких как мессенджеры, чаты, кинотеатры, игры и др.

###### Примеры `webrtc`

* `||example.com^$webrtc,domain=example.org` — это правило блокирует WebRTC-соединения c `example.com` для `example.org`.
* `@@*$webrtc,domain=example.org` — это правило отменяет блокировку всех WebRTC-соединений для `example.org`.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$webrtc` всё ещё поддерживаются Браузерным расширением AdGuard.

<a id="other-modifier"></a>
##### **`other`**

Правило применяется к запросам, для которых не был определен ни один из перечисленных выше типов.

<a id="object-subrequest-modifier"></a>
##### **`object-subrequest` (устаревший)**

> **Предупреждение об устаревании.** Модификатор `$object-subrequest` устарел и больше не поддерживается. Правила с его использованием не работают.

Запросы, инициированные плагинами (чаще всего это Flash).


<a id="exceptions-modifiers"></a>
#### Модификаторы правил-исключений

Правила-исключения отключают действие других базовых правил для адресов, которым они соответствуют. Отличаются правила-исключения тем, что они начинаются со специального маркера `@@`. Для таких правил работают все базовые модификаторы, перечисленные выше. Также добавляется несколько специальных модификаторов, которые будут описаны ниже.

> **Визуальное представление**. Также советуем ознакомиться с [этой статьей](https://adblockplus.org/filter-cheatsheet#exceptions), чтобы лучше понять, как должны строиться правила-исключения.

<a id="elemhide-modifier"></a>
##### **`elemhide`**

Отключает [косметические правила](#cosmetic-rules) на страницах, подходящих под правило.

###### Примеры `elemhide`

* `@@||example.com^$elemhide` — отменяет все косметические правила для страниц на сайте `example.com` и на всех его поддоменах.

<a id="content-modifier"></a>
##### **`content`**

Отключает [правила фильтрации HTML](#html-filtering-rules) и [replace-правила](#replace-modifier) на страницах, подходящих под правило.

###### Примеры `content`

* `@@||example.com^$content` — отменяет все правила фильтрации HTML-элементов для страниц на сайте `example.com` и на всех его поддоменах.

<a id="jsinject-modifier"></a>
##### **`jsinject`**

Запрещает добавление javascript-кода на страницу. О javascript правилах речь пойдет ниже.

###### Примеры `jsinject`

* `@@||example.com^$jsinject` — отменяет все javascript правила для страниц на сайте `example.com` и на всех его поддоменах.

<a id="urlblock-modifier"></a>
##### **`urlblock`**

Отключает блокировку всех запросов, отправленных со страниц, подходящих под это правило.

###### Примеры `urlblock`

* `@@||example.com^$urlblock` — запросы, отправленные со страниц на сайте `example.com` и всех его поддоменов, не будет блокироваться.

<a id="extension-modifier"></a>
##### **`extension`**

Отключает все пользовательские скрипты на страницах, подходящих под это правило.

###### `extension` example

* `@@||example.com^$extension` — пользовательские скрипты не будут работать на всех страницах сайта `example.com`.

> **Совместимость с разными версиями AdGuard.** Только AdGuard для Windows, Mac, Android имеют технические возможности для поддержки правил с модификатором `$extension`.

<a id="stealth-modifier"></a>
##### **`stealth`**

Отключает модуль "Антитрекинг" для всех страниц и запросов, подходящих под это правило.

###### Синтаксис

```
$stealth [= opt1 [| opt2 [| opt3 [...]]]]
```

где `opt(i)` обозначают опции "Антитрекинга", отключаемые правилами. Модификатор может содержать любое количество опций (см. ниже) или не содержать их вообще. В последнем случае модификатор отключает модуль "Антитрекинг" полностью.

Список доступных опций модификатора:

* `1p-cookie` — отключает [**Самоуничтожающиеся куки посещаемого сайта**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BC%D0%B5%D1%82%D0%BE%D0%B4%D1%8B-%D1%81%D0%BB%D0%B5%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F)
* `3p-cookie` — отключает [**Самоуничтожающиеся сторонние куки**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BC%D0%B5%D1%82%D0%BE%D0%B4%D1%8B-%D1%81%D0%BB%D0%B5%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F)
* `3p-cache` — отключает [**Отключить кеш-память для сторонних запросов**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BC%D0%B5%D1%82%D0%BE%D0%B4%D1%8B-%D1%81%D0%BB%D0%B5%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F)
* `xclientdata` — отключает [**Убрать заголовок X-Client-Data из HTTP-запросов**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BE%D0%B1%D1%89%D0%B8%D0%B5-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B8)
* `donottrack` — отключает [**Отправлять заголовок Do-Not-Track**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BE%D0%B1%D1%89%D0%B8%D0%B5-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B8)
* `ip` — отключает [**Скрыть IP адрес**](https://kb.adguard.com/ru/general/stealth-mode#%D1%80%D0%B0%D0%B7%D0%BD%D0%BE%D0%B5)
* `3p-auth` — отключает [**Блокировать заголовок Authorization**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BC%D0%B5%D1%82%D0%BE%D0%B4%D1%8B-%D1%81%D0%BB%D0%B5%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F)
* `searchqueries` - отключает [**Скрыть поисковые запросы**](https://kb.adguard.com/ru/general/stealth-mode#%D0%BE%D0%B1%D1%89%D0%B8%D0%B5-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B8)
* `referrer` — отключает [**Скрыть Referer от сторонних ресурсов**](https://kb.adguard.com/ru/general/stealth-mode#%D1%80%D0%B0%D0%B7%D0%BD%D0%BE%D0%B5)
* `useragent` — отключает [**Скрыть User-Agent**](https://kb.adguard.com/ru/general/stealth-mode#%D1%80%D0%B0%D0%B7%D0%BD%D0%BE%D0%B5)
* `webrtc` — отключает [**Блокировать WebRTC**](https://kb.adguard.com/ru/general/stealth-mode#api-%D0%B1%D1%80%D0%B0%D1%83%D0%B7%D0%B5%D1%80%D0%B0)
* `push` — отключает [**Блокировать Push API**](https://kb.adguard.com/ru/general/stealth-mode#api-%D0%B1%D1%80%D0%B0%D1%83%D0%B7%D0%B5%D1%80%D0%B0)
* `location` — отключает [**Блокировать Location API**](https://kb.adguard.com/ru/general/stealth-mode#api-%D0%B1%D1%80%D0%B0%D1%83%D0%B7%D0%B5%D1%80%D0%B0)
* `flash` — отключает [**Блокировать Flash**](https://kb.adguard.com/ru/general/stealth-mode#api-%D0%B1%D1%80%D0%B0%D1%83%D0%B7%D0%B5%D1%80%D0%B0)
* `java` — отключает [**Блокировать Java**](https://kb.adguard.com/ru/general/stealth-mode#api-%D0%B1%D1%80%D0%B0%D1%83%D0%B7%D0%B5%D1%80%D0%B0)
* `dpi` — отключает [**Защищать от DPI**](https://kb.adguard.com/ru/general/stealth-mode#%D1%80%D0%B0%D0%B7%D0%BD%D0%BE%D0%B5)

###### Примеры `stealth`

* `@@||example.com^$stealth` — полностью отключает модуль `Антитрекинг` для запросов к `example.com` и поддоменам (кроме блокировки cookies и скрытия параметров отслеживания, см.ниже).
* `@@||domain.com^$script,stealth,domain=example.com` — отключает модуль `Антитрекинг` только для script-запросов к `domain.com` (и поддоменам) на `example.com` и всех его поддоменах.
* `@@||example.com^$stealth=3p-cookie|dpi` — отключает **Самоуничтожающиеся сторонние куки** и **Защищать от DPI** для запросов к `example.com`.
* Блокировка cookies и скрытие параметров отслеживания достигается использованием правил с модификаторами `$cookie` и `$removeparam`. Правила-исключения только с модификатором `$stealth` не дадут желаемого результата. Если вы хотите полностью отключить все функции Антитрекинга для определённого домена, вам надо включить в правило все три модификатора: `@@||example.org^$stealth,removeparam,cookie`

> **Совместимость с разными версиями AdGuard.**
> * Антитрекинг доступен в AdGuard для Windows, Mac, Android и в Браузерном расширении AdGuard для Chrome, Firefox, Edge. Все остальные продукты будут игнорировать правила с модификатором `$stealth`.
> * Модификатор `$stealth` с опциями поддерживаются в AdGuard для Windows, Mac, и Android, **с CoreLibs версии 1.10 или выше**.

<a id="generic-rules"></a>
##### Generic правила

Перед тем, как перейти к описанию следующих модификаторов, необходимо ввести определение _"generic правил"_. Правило является "generic", если его действие не ограничено конкретными доменами.
Также поддерживается wildcard-символ `*`.

Например, следующие правила являются "generic":
```
###banner
*###banner
#@#.adsblock
*#@#.adsblock
~domain.com###banner
||domain.com^
||domain.com^$domain=~example.com
```

А вот такие правила уже не являются "generic":
```
domain.com###banner
||domain.com^$domain=example.com
```

<a id="generichide-modifier"></a>
###### **`generichide`**

Отключает все "generic" [косметические правила](#cosmetic-rules) на страницах, подходящих под правило-исключение.

* `@@||example.com^generichide` — отключает "generic" косметические правила на страницах сайта `example.com` и всех его поддоменах.

<a id="genericblock-modifier"></a>
###### **`genericblock`**

Отключает все generic базовые правила на страницах, подходящих под правило-исключение.

* `@@||example.com^$genericblock` — отключает "generic" базовые правила на страницах сайта `example.com` и всех его поддоменах.

<a id="specifichide-modifier"></a>
##### **`specifichide`**

Имеет эффект, противоположный [`$generichide`](#generichide-modifier). Отключает все "specific" правила сокрытия элементов и CSS-правила, но не отключает "general"-правила.

* `@@||example.org^$specifichide` — отключит `example.org##.banner`, но не `##.banner`.

> Обратите внимание, что [модификатор `$elemhide`](#elemhide-modifier) может отключить все косметические правила разом.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$specifichide` поддерживаются в AdGuard для Windows, Mac, Android и Браузерном расширении AdGuard для Chrome, Firefox, Edge.

<a id="advanced-modifiers"></a>
### Расширенные возможности

Модификаторы, описанные в этом разделе, полностью меняют поведение базовых правил, к которым эти модификаторы применены.

<a id="important-modifier"></a>
##### **`important`**

Модификатор `$important`, применённый к правилу, повышает его приоритет относительно любого другого правила без модификатора `$important`, даже относительно базовых правил-исключений.

###### Примеры:

```
! блокирующее правило заблокирует все запросы, несмотря на правило-исключение.

||example.org^$important
@@||example.org^
```

```
! если правило-исключение тоже имеет модификатор `$important`, оно будет иметь более высокий приоритет, и запросы не будут заблокированы

||example.org^$important
@@||example.org^$important
```

```
! если применяется правило-исключение на уровне документа, модификатор `$important` будет проигнорирован;
! если запрос к `example.org` отправлен с домена `test.org`, блокирующее правило не будет применено, несмотря на наличие модификатора `$important`

||example.org^$important
@@||test.org^$document
```

<a id="badfilter-modifier"></a>
##### **`badfilter`**

Правила, содержащие модификатор `$badfilter`, отключают базовые правила, на которые они ссылаются. Это означает, что текст отключенного правила должен соответствовать тексту `$badfilter`-правила (за исключением самого модификатора `$badfilter`).

**Примеры:**

* `||example.com$badfilter` отключает `||example.com`
* `||example.com$image,badfilter` отключает `||example.com,image`
* `@@||example.com$badfilter` отключает `@@||example.com`
* `||example.com$domain=domain.com,badfilter` отключает `||example.com$domain=domain.com`

Правила с модификатором `$badfilter` могут отключать другие базовые правила для определённых доменов, если они выполняют следующие условия:

* Правило имеет модификатор `$domain`
* Правило не имеет отрицания домена `~` в значении модификатора `$domain`.

В этом случае, правило с `$badfilter` отключит соответствующее базовое правило для доменов, указанных как в правиле с `$badfilter`, так и в базовом правиле. Обратите внимание, что [логика wildcard для доменов верхнего уровня (TLD)](#wildcard-for-tld) здесь также применима.

**Примеры:**

* `/some$domain=example.com|example.org|example.io` отключено для `example.com` правилом `/some$domain=example.com,badfilter`
* `/some$domain=example.com|example.org|example.io` отключено для `example.com` и `example.org` правилом `/some$domain=example.com|example.org,badfilter`
* `/some$domain=example.com|example.org` и `/some$domain=example.io` полностью отключены правилом `/some$domain=example.com|example.org|example.io,badfilter`
* `/some$domain=example.com|example.org|example.io` полностью отключено правилом `/some$domain=example.*,badfilter`
* `/some$domain=example.*` отключено для `example.com` и `example.org` правилом `/some$domain=example.com|example.org,badfilter`
* `/some$domain=example.com|example.org|example.io` НЕ отключено для `example.com` правилом `/some$domain=example.com|~example.org,badfilter`, поскольку в значении модификатора `domain` содержится отрицание домена

> **Совместимость с разными версиями AdGuard.** Правила блокировки с модификатором `$badfilter` не поддерживаются в AdGuard Content Blocker.

<a id="replace-modifier"></a>
##### **`replace`**

Этот модификатор полностью меняет поведение страницы. Когда он применяется, правило не блокирует запрос. Вместо этого ответ будет модифицирован.

###### Особенности правил `$replace`

> Вам потребуется знание регулярных выражений, чтобы использовать этот модификатор.

* Правила `$replace` применяются к любому текстовому ответу, но не будут применяться к binary (`media`, `image`, `object` и т.д.).
* Правила `$replace` не применяются, если размер оригинального ответа превышает 3Мб.
* Правила `$replace` имеют более высокий приоритет, чем другие базовые правила (**включая** правила-исключения). Так что если запрос соответствует двум различным правилам, одно из которых имеет модификатор `$replace`, применится именно это правило.
* Правила-исключения уровня document с модификаторами `$content` или `$document` отменяют срабатывание правил `$replace`, даже если запрос им соответствует.
* Прочие правила-исключения уровня document (модификаторы `$generichide`, `$elemhide` или `$jsinject`) применяются вместе с правилами `$replace`. Это означает, что вы можете изменять содержимое страницы при помощи правила `$replace` и одновременно отменять косметические правила.

> Значение `$replace` может быть пустым в случае правил-исключений. Смотрите раздел с примерами за дополнительной информацией.

> **Несколько правил, соответствующих одному запросу**. В случае, когда несколько правил `$replace` соответствуют одному запросу, мы применим каждое из них. **Правила будут применяться в алфавитном порядке.**

###### **Синтаксис $replace**

Как правило, синтаксис `replace` похож на синтаксис замены при помощи регулярных выражений [в Perl](http://perldoc.perl.org/perlrequick.html#Search-and-replace).

```
replace = "/" regexp "/" replacement "/" modifiers
```

* `regexp` — регулярное выражение.
* `replacement` — строка, которая будет использована для замены строки в соответствии с `regexp`.
* `modifiers` — флаги регулярных выражений. Например, `i` — поиск без учёта регистра, или `s` — режим одной строки.

В значении `$replace` необходимо экранировать два символа: запятую (`,`) и (`$`). Для экранирования используйте (`\`). Например, экранированная запятая будет выглядеть так: `\,`.

###### Пример `$replace`

```
||example.org^$replace=/(<VAST[\s\S]*?>)[\s\S]*<\/VAST>/\$1<\/VAST>/i
```

У этого правила три части:

* Регулярное выражение: `(<VAST(.|\s)*?>)(.|\s)*<\/VAST>`
* Замена `\$1<\/VAST>` (заметьте, что символ `$` экранирован)
* Флаги регулярного выражения: `i` (поиск без учёта регистра)

Здесь вы можете увидеть, как работает это правило:
http://regexr.com/3cesk

###### Пример нескольких правил `$replace`

1. `||example.org^$replace=/X/Y/`
2. `||example.org^$replace=/Z/Y/`
3. `@@||example.org/page/*$replace=/Z/Y/`

* И правило 1, и правило 2 будут применены ко всем запросам, отправленным к `example.org`.
* Правило 2 отключено для запросов, соответствующих `||example.org/page/`, **но правило 1 при этом всё равно работает!**.

###### Отключение правил `$replace`

* `@@||example.org^$replace` отключит все правила `$replace`, соответствующие `||example.org^`.
* `@@||example.org^$document` или `@@||example.org^$content` отключит все правила `$replace`, **исходящие** со страниц домена `example.org`, **включая саму эту страницу**.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$replace` поддерживаются в AdGuard для Windows, Mac, Android и Браузерном расширении AdGuard для Firefox. Такие правила не работают в расширениях для других браузеров, потому что они не могут модифицировать содержимое страниц на сетевом уровне.

> **Ограничения.** Правила с модификатором `$replace` могут быть использованы **только в доверенных фильтрах**. Эта категория включает в себя **Пользовательские правила**, а также все фильтры, разработанные командой AdGuard.

<a id="csp-modifier"></a>
##### **`csp`**

Этот модификатор полностью меняет поведение правила. Будучи применённым к правилу, он не заблокирует попавший под него запрос. Вместо этого будут изменены заголовки ответа.

> Чтобы использовать правила этого типа, необходимо базовое понимание слоя безопасности [Политики Безопасности Контента](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy).

Для запросов, подходящих под `$csp`-правило, мы усилим политику безопасности ответа, добавив дополнительную политику, равнозначную содержимому модификатора `$csp`. Правила `$csp` применяются независимо от правил любого другого типа. Прочие базовые правила никак на них не влияют, **кроме правил исключений уровня document** (смотрите примеры ниже).

> **Когда несколько правил соответствуют одному запросу**. В случае, когда несколько `$csp`-правил соответствуют одному запросу, мы применяем их все.

**Синтаксис `$csp`**

Синтаксис значений `$csp` похож на синтаксис заголовков Политики Безопасности Контента.

Значение `$csp` может быть пустым в случае правил-исключений. Смотрите примеры ниже для более подробной информации.

>Ограничения

>1. Некоторые символы запрещены в значении `$csp`: `,`, `$`
>2. `$csp`-правила поддерживают ограниченный список модификаторов: `$domain`, `$important`, `$subdocument`;
>3. Правила с директивами `report-*` считаются некорректными.

###### Примеры `$csp`

* `||example.org^$csp=frame-src 'none'` — запрещает все фреймы на `example.org` и его поддоменах.
* `@@||example.org/page/*$csp=frame-src 'none'` — отключает все правила с модификатором `$csp`, в точности соответствующим `frame-src 'none'` на всех страницах, подходящих под паттерн правила. Например, правило выше.
* `@@||example.org/page/*$csp` — отключает все `$csp`-правила на всех страницах, подходящих под паттерн правила.
* `||example.org^$csp=script-src 'self' 'unsafe-eval' http: https:` — отключает инлайн-скрипты на всех страницах, подходящих под паттерн правила.
* `@@||example.org^$document` или `@@||example.org^$urlblock` — отключает все `$csp`-правила на всех страницах, подходящих под паттерн правила.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$csp` не поддерживается в AdGuard Content Blocker, AdGuard для iOS и для Safari.

<a id="all-modifier"></a>

##### **`all`**

Модификатор `$all` является сборным из модификаторов [`$document`](#document-modifier), [`$popup`](#popup-modifier), [`$csp`](#csp-modifier). Например, правило `||example.org^$all` конвертируется в такой набор правил:
```
||example.org^$document,popup
||example.org^$csp=script-src 'self' 'unsafe-eval' http: https: data: blob: mediastream: filesystem:
||example.org^$csp=font-src 'self' 'unsafe-eval' http: https: data: blob: mediastream: filesystem:
||example.org^
```

<a id="cookie-modifier"></a>
##### **`cookie`**

Модификатор `$cookie` полностью меняет поведение правила. Вместо того, чтобы блокировать запрос, этот модификатор позволяет нам блокировать или изменять заголовки `Cookie` или `Set-Cookie`.

> **Несколько правил, соответствующих одному запросу**. В случае, когда несколько правил `$cookie` соответствуют одному запросу, мы применим каждое из них по очереди.

### Синтаксис `$cookie`
Синтаксис правила зависит от того, собираемся ли мы заблокировать все cookie или удалить один cookie. Поведение правила можно изменить с помощью модификаторов `maxAge` и `sameSite`.

* `||example.org^$cookie=NAME;maxAge=3600;sameSite=lax` — каждый раз, когда AdGuard встречает cookie с именем `NAME` в запросе к `example.org`, он будет делать следующее:

  * Установит дату истечения срока хранения на текущее время плюс `3600` секунд
  * Позволяет cookie использовать [Same-Site](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#SameSite_cookies) "lax" стратегию.
* `||example.org^$cookie` — блокирует **все** cookie, установленные `example.org`; это эквивалентно установке `maxAge=0`
* `||example.org^$cookie=NAME` — блокирует единственную cookie с именем `NAME`
* `||example.org^$cookie=/regexp/` — блокирует все cookie, соответствующие заданному регулярному выражению `regexp`

> **Экранирование специальных символов:** в случае использования регулярных выражений необходимо экранировать запятую `,` и знак доллара `$`. Используйте для этого бекслеш `\`. Например, экранированная запятая выглядит так: `\,`.

Правила исключения (`@@`) не влияют на правила `$cookie`, только если это не правило исключение `$document`. Чтобы отключить правило `$cookie`, правило исключение также должно иметь модификатор `$cookie`. Вот как это работает:

* `@@||example.org^$cookie` — разблокирует все cookie, установленные `example.org`
* `@@||example.org^$cookie=NAME` — разблокирует одну cookie с именем `NAME`
* `@@||example.org^$cookie=/regexp/` — разблокирует все cookie, соответствующие заданному регулярному выражению `regexp`

> **Ограничения:** `$cookie` правила поддерживают ограниченный список модификаторов: `$domain`, `$~domain`, `$important`, `$third-party`, `$~third-party`.

### Примеры с `$cookie`
* `$cookie=__cfduid` — блокирует CloudFlare cookie везде
* `$cookie=/__utm[a-z]/` — блокирует Google Analytics cookie везде
* `||facebook.com^$third-party,cookie=c_user` — не позволяет Facebook'у отслеживать вас, даже если вы вошли в систему

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$cookie` не поддерживается в AdGuard Content Blocker, AdGuard для iOS и для Safari.

<a id="network-modifier"></a>
##### **`network`**

Правила этого типа позволяют полностью блокировать доступ к указанному удалённому адресу, то есть фактически работают как Брандмауэр.

1. `$network`-правила соответствуют **только IP-адресам**! Эти правила не получится использовать для блокирования или разблокирования доступа к домену.
2. Чтобы задать соответствие IPv6-адресу, вам надо использовать "сколлапсированный" синтаксис, например, используйте `[2001:4860:4860::8888]$network` вместо `[2001:4860:4860:0:0:0:0:8888]$network`.
3. Разблокирующее `$network`-правило заставит AdGuard направлять трафик в подходящую под соответствие конечную точку, например, тем самым можно прекратить всю последующую фильтрацию.
4. Если маска правила начинается и заканчивается символом `/`, то она считается регулярным выражением.

###### Примеры с `network`

* `174.129.166.49:3478^$network` — блокирует доступ к `174.129.166.49:3478` (но не к `174.129.166.49:34788`).
* `[2001:4860:4860::8888]:443^$network` — блокирует доступ к `[2001:4860:4860::8888]:443`.
* `174.129.166.49$network` — блокирует доступ к `174.129.166.49:*`.
* `@@174.129.166.49$network` — заставляет AdGuard направлять трафик в конечную точку. Никакие другие правила не будут применены.
* `/.+:3[0-9]{4}/$network` — блокирует доступ к диапазанону портов 30000 — 39999.
* `/8.8.8.(:?8|4)/$network` — блокирует доступ к `8.8.8.8` и `8.8.8.4`.

> **Совместимость с разными версиями AdGuard.** Только AdGuard для Windows, Mac, Android имеют технические возможности для поддержки правила с модификатором `$network`.

<a id="app-modifier"></a>
##### **`app`**

Этот модификатор ограничивает действие правила до конкретного приложения (или списка приложений). Это может быть не так критично на Windows или Mac, но на мобильных устройствах, где для корректной работы некоторые правила должны быть специфичны для конкретных приложений, данная функция крайне важна.

* Android — используйте имя пакета приложения (например, `org.example.app`).
* Windows — используйте имя процесса (например, `chrome.exe`).
* Mac — используйте bundle ID или имя процесса (например, `com.google.Chrome`).

>На Mac вы можете найти bundle ID или имя процесса интересующего вас приложения в деталях соответствующих запросов в Журнале фильтрации.

###### Примеры `app`

* `||baddomain.com^$app=org.example.app` — правило для блокирования запросов, подходящих под заданную маску и посылаемых Android-приложением `org.example.app`.
* `||baddomain.com^$app=org.example.app1|org.example.app2` — аналогичное правило, но оно работает как для приложения `org.example.app1`, так и для `org.example.app2`.

Если вы хотите, чтобы правило не применялось к заданному приложению, предваряйте имя приложения символом `~`.

###### Примеры `app` и `~`

* `||baddomain.com^$app=~org.example.app` — правило, блокирующее запросы, подходящие под заданную маску и посылаемые любым приложением кроме `org.example.app`.
* `||baddomain.com^$app=~org.example.app1|~org.example.app2` — аналогично, но в исключениях два приложения: `org.example.app1` и `org.example.app2`.

> **Совместимость с разными версиями AdGuard.** Только AdGuard для Windows, Mac, Android имеют технические возможности для поддержки правила с модификатором `$app`.

<a id="redirect-modifier"></a>
#### **`redirect`**

AdGuard может перенаправлять веб-запросы к локальному "ресурсу".

##### Синтаксис `redirect`

AdGuard использует [тот же синтаксис](https://github.com/gorhill/uBlock/wiki/Static-filter-syntax#redirect) правил фильтрации, что и uBlock Origin. Он совместим с модификатором `$rewrite=abp-resource` Adblock Plus.

> Значение модификатора `$redirect` должно быть именем ресурса, который будет использован для переадресации.

> Приоритет правил `$redirect` выше, чем у обычных базовых правил блокировки. Это означает, что, если есть базовое правило (даже с модификатором`$important`), `$redirect` будет иметь над ним преимущество. Если есть белый список (`@@`) с совпадающим URL, он также заблокирует переадресацию (только если правило `$redirect` также не отмечено как `$important`).

###### Отключение правил `$redirect`

* `@@||example.org^$redirect` отключит все правила `$redirect` для всех запросов, соответствующих `||example.org^`.
* `@@||example.org^$redirect=redirectName` отключит правила с `$redirect=redirectName` для всех запросов, соответствующих `||example.org^`.

##### Примеры `redirect`

```
||example.org/script.js$script,redirect=noopjs
```
Это правило перенаправляет все запросы к `example.org/script.js` к ресурсу `noopjs`.

```
||example.org/test.mp4$media,redirect=noopmp4-1s
```
Это правило перенаправляет все запросы `example.org/test.mp4` к ресурсу `noopmp4-1s`.

> Больше информации о редиректах и их использовании доступно [на GitHub](https://github.com/AdguardTeam/Scriptlets#redirect-resources).

> **Совместимость с другими модификаторами**
> Правила с `$redirect` совместимы с [базовыми модификаторами](#basic-rules-common-modifiers), [модификаторами с ограничением по типу контента](#content-type-modifiers), а также с модификаторами `$important` и `$app`. Правила, содержащие другие типы модификаторов, будут считаться некорректными и не будут применены.

> **Совместимость с разными версиями AdGuard.**
> Правила с модификатором `$redirect` не поддерживается в AdGuard Content Blocker, AdGuard для iOS и для Safari.
> Правила с `$redirect` приоритетами (например, `*$redirect=noopjs:42`) поддерживаются в AdGuard для Windows, Mac, и Android, **с CoreLibs версии 1.11 или выше**.

<a id="redirect-rule-modifier"></a>

#### **`redirect-rule`**
По сути, это тот же [`$redirect`](#redirect-modifier), поскольку у него тоже есть свойства "перенаправления" и логика у них практически аналогичная. Разница в том, что `$redirect-rule` применяется только в случае, если целевой запрос заблокирован другим базовым правилом.

> Отключение `$redirect-rule` работает точно так же, как для обычных правил `$redirect`. Даже более того, `@@||example.org^$redirect` будет отключать как правило `$redirect`, так и правило `$redirect-rule`.

Примеры:

```
||example.org/script.js
||example.org^$redirect-rule=noopjs
```

В этом случае только запросы к `example.org/script.js` будут перенаправлены. Все остальные запросы к `example.org` изменены не будут.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$redirect-rule` не поддерживается в AdGuard Content Blocker, AdGuard для iOS и для Safari.

<a id="denyallow-modifier"></a>

#### **`denyallow`**

Модификатор `$denyallow` позволяет избежать создания дополнительных правил, когда требуется отключить то или иное правило для определённого домена(-ов). Модификатор `$denyallow` соответствует только целевым доменам, но не доменам реферера.

Добавление этого модификатора в правило равносильно исключению доменов при помощи паттерна правила либо при помощи добавления дополнительных правил-исключений. Чтобы указать несколько доменов в одном правиле, используйте символ `|` в качестве разделителя.

Пожалуйста, обратите внимание, что правила с модификатором `$denyallow` имеют следующие ограничения:

* паттерн правила не может указывать на конкретный домен или домены (например, не может начинаться с `||`)
* домены, указанные в модификаторе `$denyallow`, не могут иметь отрицание (т.е. `$denyallow=~x.com`) или иметь `*` вместо домена верхнего уровня (`$denyallow=x.*`)

Правила, нарушающие эти ограничения, считаются недействительными.

**Пример:**

Данное правило:

```
*$script,domain=a.com|b.com,denyallow=x.com|y.com
```

эквивалентно такому правилу:

```
/^(?!.*(x.com|y.com)).*$/$script,domain=a.com|b.com
```

а также комбинации этих трёх правил:

```
*$script,domain=a.com|b.com
@@||x.com$script,domain=a.com|b.com
@@||y.com$script,domain=a.com|b.com
```

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$denyallow` не поддерживаются в AdGuard Content Blocker.

<a id="removeparam-modifier"></a>
#### **`removeparam`**

> Модификатор `$removeparam` является полным аналогом модификатора `$queryprune`. При этом модификатор `$queryprune` считается устаревшим, мы рекомендуем везде использовать только модификатор `$removeparam`.

Правила с модификатором `$removeparam` предназначены для того, чтобы убирать параметры из URL запросов. Такие правила применяются *только* к запросам типов `GET`, `HEAD`, и `OPTIONS`.

> Правила с модификатором `$removeparam`, не содержащие модификаторов типа контента, будут соответствовать только тем запросам, которые имеют тип контента `document`.

##### Синтаксис

###### Базовый синтаксис

* `$removeparam=param` — убирает параметр запроса с именем `param` из URL любого запроса. Например, запрос к `http://example.com/page?param=1&another=2` будет преобразован в `http://example.com/page?another=2`.

> Базовый синтаксис `$removeparam` поддерживается начиная с версии 1.7 [CoreLibs](https://adguard.com/ru/blog/introducing-corelibs.html) и версии 3.6 Браузерного расширения AdGuard.

###### Регулярные выражения

Вы также можете использовать регулярные выражения для соответствия параметрам запроса и/или их значениям:

* `$removeparam=/regexp/[options]` — убирает из URL-адреса любого запроса все параметры, соответствующие заданному регулярному выражению `regexp`. В отличие от базового синтаксиса, это означает *"убрать параметры запроса, нормализованные к строке `name=value`, которая соответствует регулярному выражению"*. Здесь `[options]` — это список опций регулярного выражения. На данный момент, единственная поддерживаемая опция — это `i`, делающая соответствие нечувствительным к регистру.

> Синтаксис `$removeparam` для регулярных выражений будет доступен начиная с версии 1.8 CoreLibs и версии 4.0. Браузерного расширения AdGuard, а пока используйте упрощенный вариант: `$removeparam=param`.

> **Экранирование специальных символов**: не забудьте экранировать специальные символы в регулярных выражениях, такие как `,`, `/` и `$`. Для этой цели используйте символ `\`. Например, экранированная запятая должна выглядеть так: `\,`.

> Обратите внимание, что правила с регулярными выражениями смотрят как на имя, так и на значение параметра. Чтобы минимизировать риск ошибки, рекомендуем начинать регулярное выражение с `/^`, если только вы не хотите специально работать с значениями параметров.

> Мы будем стараться обнаруживать и игнорировать неэкранированные символы `$` автоматически, используя следующее правило большого пальца:
> Это не является разделителем опций, если:
> Оно выглядит как `$/`,
> Существует другой символ слеша (`/`) слева от него,
> Существует другой неэкранированный символ `$` слева от этого символа слеша.

###### Убрать все параметры запроса

Укажите "голый" `$removeparam`, чтобы убрать все параметры запроса:

* `||example.org^$removeparam` — убирает все параметры запроса из URL, соответствующего `||example.org^`.

###### Инверсия

Используйте символ `~`, чтобы применить инверсию:

* `$removeparam=~param` — убирает все параметры запроса с именем, отличным от `param`
* `$removeparam=~/regexp/` — убирает все параметры запроса, которые не соответствуют заданному регулярному выражению `regexp`

###### Отмена `$removeparam`

Этот тип правил работает практически так же, как и в случае с модификаторами [`$csp`](#csp-modifier) и [`$redirect`](#redirect-modifier).

Используйте `@@`, чтобы отменить `$removeparam`:

* `@@||example.org^$removeparam` — отменяет все правила `$removeparam` для URL, соответствующих `||example.org^`.
* `@@||example.org^$removeparam=param` — отменяет правило с `$removeparam=param` для любого запроса, соответствующего `||example.org^`.
* `@@||example.org^$removeparam=/regexp/` — отменяет правило с `$removeparam=/regexp/` для любого запроса, соответствующего `||example.org^`.

> **Многочисленные правила, соответствующие одному запросу**
> В случае, когда несколько правил с `$removeparam` соответствуют одному запросу, они все будут применены по очереди.

##### Примеры

* `$removeparam=utm_source` — убирает параметр `utm_source` из всех запросов.

* `$removeparam=/utm_.*/` — убирает все параметры `utm_* query` из URL любого запроса, например, запрос `http://example.com/page?utm_source=test` будет трансформирован в `http://example.com/page`.

* `$removeparam=/^utm_source=campaign$/` — убирает параметр `utm_source` с значением `campaign`. Не затрагивает другие параметры `utm_source`.

Отмена одного правила `$removeparam` и замена его другим:

```
$removeparam=/^(gclid|yclid|fbclid)=/
@@||example.com^$removeparam=/^(gclid|yclid|fbclid)=/
||example.com^$removeparam=/^(yclid|fbclid)=/
```

При помощи этих правил из всех запросов убираются Click ID от Google, Yandex, и Facebook. Есть одно исключение: Google Click ID (gclid) не будет убран для запросов к example.com.

Отмена `$removeparam` для всех параметров:

```
$removeparam=/^(utm_source|utm_medium|utm_term)=/
$removeparam=/^(utm_content|utm_campaign|utm_referrer)=/
@@||example.com^$removeparam
```

С этими правилами некоторые [utm-параметры](https://ru.wikipedia.org/wiki/UTM-%D0%BC%D0%B5%D1%82%D0%BA%D0%B8) будут удалены из любого запроса, но в запросах к `example.com` не будут удаляться совсем. Например: `http://google.com/page?utm_source=s&utm_referrer=fb.com&utm_content=img` будет преобразован в `http://google.com/page`, но `http://example.com/page?utm_source=s&utm_referrer=fb.com&utm_content=img` вообще не будет затронут этим правилом блокировки.

> **Совместимость с другими модификаторами**
> Правила с `$removeparam` совместимы с [базовыми модификаторами](#basic-rules-common-modifiers), [модификаторами с ограничением по типу контента](#content-type-modifiers), а также с модификаторами `$important` и `$app`. Правила, содержащие другие типы модификаторов, будут считаться некорректными и не будут применены.

> **Обратите внимание**, что блокировку правил `$removeparam` также можно отключить с помощью правил исключений `$document` и `$urlblock`. Но базовые правила исключений без модификаторов не могут этого сделать. Например, `@@||example.com^` не отключит `$removeparam=p` для запросов к example.com, а вот `@@||example.com^$urlblock` — отключит.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$removeparam` поддерживаются в AdGuard для Windows, Mac, Android, а также браузерными расширениями AdGuard для Chrome, Firefox, Edge.

> **Ограничения.** Правила с модификатором `$removeparam` могут быть использованы **только в доверенных фильтрах** — то есть в **Пользовательских правилах** и всех фильтрах, разработанных командой AdGuard.

<a id="removeheader-modifier"></a>
#### **`$removeheader`**

Правила с модификатором `$removeheader` предназначены для того, чтобы убирать заголовки HTTP-запросов и ответов. Изначальная мотивация для создания этого типа правил заключалась в том, чтобы иметь возможность избавиться от заголовка `Refresh`, который часто используется для перенаправления пользователей на нежелаемую страницу. Однако, применение данного модификатора не ограничивается этим случаем.

Как и в случае с `$csp`, `$redirect`, `$removeparam` и `$cookie`, этот модификатор существует независимо, правила с ним не зависят от обычных базовых правил, то есть регулярные выражения или блокирующие правила никак на них не повлияют. По умолчанию, они работают только применительно к заголовкам ответов. Однако, вы также можете использовать модификатор `$removeheader`, чтобы убирать заголовки запросов.

##### Синтаксис

**Базовый синтаксис**

* `||example.org^$removeheader=header-name` — убирает заголовок **ответа** с названием `header-name`
* `||example.org^$removeheader=request:header-name` — убирает заголовок **запроса** с названием `header-name`

> Обратите внимание, что `$removeheader` нечувствителен к регистру, но мы настоятельно рекомендуем всегда использовать нижний регистр.

**Отрицание `$removeheader`**

Этот тип правил работает практически точно так же, как с модификаторами `$csp` и `$redirect`.

Используйте `@@`, чтобы добавить отрицание `$removeheader`:

* `@@||example.org^$removeheader` — отменяет **все** правила `$removeheader` для URL, соответствующих `||example.org^`.
* `@@||example.org^$removeheader=header` — отменяет все правила с `$removeheader=header` для любого запроса, соответствующего `||example.org^`.
* Правила с `$removeheader` также можно отключить, используя правила-исключения `$document` и `$urlblock`. Но базовые правила-исключения без модификаторов не смогут этого сделать. Например, `@@||example.com^` не отключит `$removeheader=p` для запросов к `example.com`, а `@@||example.com^$urlblock` отключит.

> **Множественные правила, соответствующие одному запросу**
> В случае, когда несколько правил с `$removeheader` соответствуют одному запросу, мы будем применять их все по очереди.

##### Ограничения

1. Пожалуйста, учтите, что этот тип правил может быть использован **только в доверенных фильтрах**. Эта категория включает в себя ваши Собственные правила и все фильтры, разработанные командой AdGuard.
2. Чтобы избежать потенциальных проблем с безопасностью, `$removeheader` не может убрать заголовки из списка ниже:

* `access-control-allow-origin`
* `access-control-allow-credentials`
* `access-control-allow-headers`
* `access-control-allow-methods`
* `access-control-expose-headers`
* `access-control-max-age`
* `access-control-request-headers`
* `access-control-request-method`
* `origin`
* `timing-allow-origin`
* `allow`
* `cross-origin-embedder-policy`
* `cross-origin-opener-policy`
* `cross-origin-resource-policy`
* `content-security-policy`
* `content-security-policy-report-only`
* `expect-ct`
* `feature-policy`
* `origin-isolation`
* `strict-transport-security`
* `upgrade-insecure-requests`
* `x-content-type-options`
* `x-download-options`
* `x-frame-options`
* `x-permitted-cross-domain-policies`
* `x-powered-by`
* `x-xss-protection`
* `public-key-pins`
* `public-key-pins-report-only`
* `sec-websocket-key`
* `sec-websocket-extensions`
* `sec-websocket-accept`
* `sec-websocket-protocol`
* `sec-websocket-version`
* `p3p`
* `sec-fetch-mode`
* `sec-fetch-dest`
* `sec-fetch-site`
* `sec-fetch-user`
* `referrer-policy`
* `content-type`
* `content-length`
* `accept`
* `accept-encoding`
* `host`
* `connection`
* `transfer-encoding`
* `upgrade`

3. Правила с `$removeheader` несовместимы с любыми другими модификаторами, кроме `$domain`, `$third-party`, `$app`, `$important`, `$match-case`, а также модификаторами, ограничивающими по типу контента (`$script`, `$stylesheet` и т.д.). Правила с `$removeheader`, имеющие другие модификаторы, считаются некорректными и будут игнорироваться.

##### Примеры

* `||example.org^$removeheader=refresh` — убирает заголовок `Refresh` из всех HTTP-ответов, возвращённых `example.org` и его поддоменами.
* `||example.org^$removeheader=request:x-client-data` — убирает заголовок `X-Client-Data` из всех HTTP-запросов.
* Данный блок правил убирает заголовки `Refresh` и `Location` из всех HTTP-ответов, возвращённых `example.org`, кроме запросов к `example.org/path/*`, для которого заголовки не будут убраны:

    ```
    ||example.org^$removeheader=refresh
    ||example.org^$removeheader=location
    @@||example.org/path/$removeheader
    ```

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$removeheader` поддерживаются в AdGuard для Windows, Mac, Android и Браузерном расширении AdGuard для Chrome, Firefox, Edge.

<a id="hls-modifier"></a>
#### **`$hls`**

Правила `$hls` модифицируют ответ на соответствующий правилу запрос. Они предназначены для удаления сегментов из [HLS-плейлистов (RFC 8216)](https://datatracker.ietf.org/doc/html/rfc8216).
Слово "сегмент" в данной документации подразумевает как "Media Segment", так и "playlist" (как часть "Master Playlist"): с точки зрения правил `$hls`, "Master Playlist" и "Media Playlist" неразличимы.

##### Синтаксис
* `||example.org^$hls=urlpattern` – удалить сегменты, URL которых соответствует паттерну `urlpattern`. Паттерн работает так же, как в базовых URL правилах, однако символы `/`, `$` и `,` в составе `urlpattern` необходимо экранировать с помощью `\`.
* `||example.org^$hls=/regexp/[options]` – удалить сегменты, URL которых, или, в зависимости от опций, один из тегов, соответствует регулярному выражению `regexp`. Опции `[options]` изменяют поведение правила как описано ниже.

Доступные значения `options`:
* `t` – вместо URL, тестировать теги сегмента на соответствие регулярному выражению. Сегмент с соответствующим тегом будет удален.
* `i` – сделать регулярное выражение нечувствительным к регистру символов.

Как и в случае с паттерном, символы `/`, `$` и `,` в `regexp` необходимо экранировать с помощью `\`.

##### Отключение правил `$hls`

Базовые правила-исключения не отключают правила `$hls`. Отключить их можно следующим образом:
* `@@||example.org^$hls` – отключить все правила `$hls` для ответов на запросы к соответствующим паттерну `||example.org^` URL.
* `@@||example.org^$hls=text` – отключить все правила `$hls`, у которых значение модификатора `hls` равно `text`, для ответов на запросы к соответствующим паттерну `||example.org^` URL.
* `$hls` также можно отключить с помощью правил-исключений с модификаторами `$document`, `$content` и `$urlblock`.

##### Ограничения
* Правила `$hls` разрешены только в доверенных фильтрах.
* Правила `$hls` не могут иметь других модификаторов, кроме `$domain`, `$third-party`, `$app`, `$important`, `$match-case` и `$xmlhttprequest`.
* Правила `$hls` применимы только к HLS-плейлистам, т.е. к тексту в кодировке UTF-8, начинающемуся со строки `#EXTM3U`. Никакие другие ответы не будут модифицированы правилами `$hls`.
* Правила `$hls` не будут применены к ответам размером больше 3 МБ.

##### Замечания
Если несколько правил `$hls` соответствуют одному и тому же запросу, их эффект суммируется.

##### Примеры
* `||example.org^$hls=\/videoplayback^?*&source=dclk_video_ads` — удалить все сегменты с соответствующим URL.
* `||example.org^$hls=/\/videoplayback\/?\?.*\&source=dclk_video_ads/i` — то же, но с помощью регулярного выражения.
* `||example.org^$hls=/#UPLYNK-SEGMENT:.*\,ad/t` — удалить все сегменты с соответствующим тегом.

##### О формате HLS-плейлистов

Из [спецификации](https://datatracker.ietf.org/doc/html/rfc8216) следует:
1. HLS-плейлист — это набор текстовых строк.
2. Строка может быть пустой, быть комментарием (начинается с `#`), тегом (начинается с `#` и имени тега) или URL.
3. URL-строка называется сегментом.
4. Тег может относиться к одному сегменту, т.е. к первой URL-строке, следующей после данного тега, ко всем последующим сегментами (пока не встретится тег с тем же именем) или ко всему плейлисту.

Замечания, касающиеся правил `$hls`:
1. Когда какой-либо сегмент удаляется, все теги, относящиеся только к этому сегменту, тоже удаляются.
2. Теги, относящиеся к нескольким сегментам, удаляются, если все эти сегменты были удалены.
3. Правила `$hls` распознают все теги, описанные в RFC, а также несколько распространенных нестандартных тегов. Все остальные строки, начинающиеся с `#`, считаются комментариями и остаются нетронутыми.
4. Правила `$hls` не могут удалить теги, относящиеся ко всему плейлисту. В данном случае могут быть использованы правила `$replace`.

Пример работы правил `$hls`:
<details>
<summary>Исходный ответ</summary>

```
#EXTM3U
#EXT-X-TARGETDURATION:10
#EXTINF,5
preroll.ts
#UPLYNK-SEGMENT:abc123,ad
#UPLYNK-KEY:aabb1122
#EXT-X-DISCONTINUITY
#EXTINF,10
01.ts
#EXTINF,10
02.ts
#UPLYNK-SEGMENT:abc123,segment
#UPLYNK-KEY:ccdd2233
#EXT-X-DISCONTINUITY
#EXTINF,10
01.ts
#EXTINF,10
02.ts
#EXT-X-ENDLIST
```
</details>

<details>
<summary>Примененные правила</summary>

```
||example.org^$hls=preroll
||example.org^$hls=/#UPLYNK-SEGMENT:.*\,ad/t
```
</details>

<details>
<summary>Модифицированный ответ</summary>

```
#EXTM3U
#EXT-X-TARGETDURATION:10
#UPLYNK-SEGMENT:abc123,segment
#UPLYNK-KEY:ccdd2233
#EXT-X-DISCONTINUITY
#EXTINF,10
01.ts
#EXTINF,10
02.ts
#EXT-X-ENDLIST
```
</details>

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$hls` поддерживаются в AdGuard для Windows, Mac, и Android, **с CoreLibs версии 1.10 или выше**.

<a id="jsonprune-modifier"></a>
#### **`$jsonprune`**

Правила `$jsonprune` модифицируют JSON-ответы на соответствующий правилу запрос. Они предназначены для удаления
элементов JSON-документа, выбираемых с помощью особого [JSONPath](https://goessner.net/articles/JsonPath/)
выражения (см. ниже). Эти правила не модифицируют ответы, не являющиеся валидным JSON-документом.

##### Синтаксис
* `||example.org^$jsonprune=expression` – удалить элементы, соответствующие особому JSONPath-выражению `expression` из ответа на запрос к URL, соответствующему `||example.org`.

Символы `$` и `,` в `expression` необходимо экранировать с помощью `\`.

Особые JSONPath-выражения отступают от оригинальной [спецификации](https://goessner.net/articles/JsonPath/) в следующем:
1. "Script expressions" не поддерживаются.
2. Поддерживаемые "filter expressions":
   2.1. `?(has <key>)` — `true` если текущий объект обладает свойством `<key>`.
   2.2. `?(key-eq <key> <value>)` — `true` если текущий объект обладает свойством `<key>`, равным `<value>`.
   2.3. `?(key-substr <key> <value>)` — `true` если текущий объект обладает свойством `<key>`, чье значение
                                         имеет тип "строка", и `<value>` является подстрокой этой строки.
4. Пробелы вне двойных или одинарных кавычек игнорируются.
5. Разрешено использовать как двойные, так и одинарные кавычки для строковых литералов.
6. Выражения, заканчивающиеся на `..`, не поддерживаются.
7. Разрешено указывать несколько `array slices` в квадратных скобках.

Существуют различные online-инструменты для проверки JSONPath-выражений:
https://jsonpath.herokuapp.com/
https://jsonpath.com/
Обратите внимание, что различные имплементации JSONPath обладают
уникальными особенностями и могут быть несовместимы друг с другом.

##### Отключение правил `$jsonprune`

Базовые правила-исключения не отключают правила `$jsonprune`. Отключить их можно следующим образом:
* `@@||example.org^$jsonprune` – отключить все правила `$jsonprune` для запросов к URL, соответствующим `||example.org^`.
* `@@||example.org^$jsonprune=text` – отключить все правила `$jsonprune`, у которых значение модификатора `jsonprune` равно `text`, для запросов к URL, соответствующим `||example.org^`.
* `$jsonprune` также можно отключить с помощью правил-исключений с модификаторами `$document`, `$content` и `$urlblock`.

##### Ограничения
* `$jsonprune` не могут иметь других модификаторов, кроме `$domain`, `$third-party`, `$app`, `$important`, `$match-case` и `$xmlhttprequest`.
* правила `$jsonprune` не будут применены к ответам размером больше 3 МБ.

##### Замечания
* Если несколько `$jsonprune` правил соответствуют одному запросу, их эффект суммируется.

##### Примеры
* `||example.org^$jsonprune=\$..[one\, "two three"]` — удалить все вхождения свойств "one" и "two three" в JSON-документ.
* `||example.org^$jsonprune=\$.a[?(has ad_origin)]` — удалить всех прямых потомков `a`, которые обладают свойством `ad_origin`.
* `||example.org^$jsonprune=\$.*.*[?(key-eq 'Some key' 'Some value')]` — удалить все элементы на уровне вложенности 3, обладающие свойством "Some key", равным "Some value".

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$jsonprune` поддерживаются в AdGuard для Windows, Mac и Android, **с CoreLibs версии 1.10 или выше**.

<a id="noop-modifier"></a>
#### **`noop`**

Модификатор `noop` не делает ничего и может быть использован исключительно в целях улучшения читабельности правил. Он состоит из последовательности символов нижнего подчёркивания (`_`) любой длины и может фигурировать в правиле столько раз, сколько требуется.

##### Примеры `noop`:

```
||example.com$_,removeparam=/^ss\\$/,_,image
||example.com$replace=/bad/good/,___,~third-party
```

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `noop` не поддерживаются в AdGuard Content Blocker.

<a id="empty-modifier"></a>
##### **`empty` (устаревший)**

> **Предупреждение об устаревании.** Этот модификатор считаются устаревшими. Вместо него теперь используется [модификатор `$redirect`](#redirect-modifier). Правила с `$empty` конвертируются в `$redirect=nooptext`.

Обычно заблокированный запрос выглядит для браузера как ошибка сервера. В случае применения модификатора `$empty`, AdGuard эмулирует пустой ответ сервера со статусом `200 OK`.

###### Примеры `empty`

* `||example.org^$empty` — возвращает пустой ответ для всех запросов к домену `example.org` и всех его поддоменов.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$empty` не поддерживаются в AdGuard Content Blocker, AdGuard для Safari и для iOS.

<a id="mp4-modifier"></a>
##### **`mp4` (устаревший)**

> **Предупреждение об устаревании.** Этот модификатор считаются устаревшими. Вместо него теперь используется [модификатор `$redirect`](#redirect-modifier). Правила с `$mp4` конвертируются в `$redirect=noopmp4-1s,media`.

В качестве ответа на заблокированный запрос AdGuard возвращает короткую видео-заглушку.

###### Примеры `mp4`

* `||example.com/videos/$mp4` — блокирует загрузку видео с адресов `||example.com/videos/*` и заменяет ответ на видео-заглушку.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$mp4` не поддерживаются в AdGuard Content Blocker, AdGuard для Safari и для iOS.

<a id="non-basic-rules"></a>
# Другие правила

Однако возможностей базовых правил может быть недостаточно, чтобы заблокировать рекламу. Иногда для этого требуется скрыть какой-нибудь элемент или изменить часть HTML-кода страницы, при этом ничего не сломав. Для этого предназначены правила, описанные в этом разделе.

<a id="wildcard-for-tld"></a>
## Поддержка wildcard для доменов верхнего уровня (TLD)

Вы можете использовать wildcard-символ для доменов верхнего уровня в паттернах косметических, html и js правил. Например, правило `example.*##.banner` будет соответствовать всем `example.TLD` доменам: `example.ru`, `example.com`, `example.net`, `example.org` и т.д.
При составлении базовых правил вы можете использовать *wildcard-символ для TLD* только вместе с модификатором `$domain`. Например, `||*/banners/*$image,domain=example.*`

> **Совместимость с разными версиями AdGuard.** Правила с wildcard для TLD поддерживается в AdGuard для Windows, Mac, Android, Safari, iOS и в Браузерном расширении AdGuard для Chrome, Firefox, Edge.

<a id="cosmetic-rules"></a>
## Косметические правила

> Для работы с косметическими правилами необходимы знания HTML и CSS. Так что если вы хотите научиться самостоятельно составлять такие правила, нужно получить хотя бы базовые навыки владения этими технологиями. Рекомендуем ознакомиться с [этой документацией](https://developer.mozilla.org/ru/docs/Web/Guide/CSS/Getting_started/What_is_CSS).

<a id="cosmetic-elemhide-rules"></a>
### Правила скрытия элементов

Правила скрытия элементов предназначены, как это следует из их названия, для скрытия элементов веб-страниц. По сути это аналогично применению стиля `{ display: none; }` к выбранному элементу.

> Правила скрытия элементов работают по-разному и их приоритет меняется [в зависимости от платформы](#cosmetic-rules-priority).

<a id="elemhide-syntax"></a>
#### Синтаксис правил скрытия

```
   rule = [domains] "##" selector
domains = [domain0, domain1[, ...[, domainN]]]
```

* **`selector`** — [CSS-селектор](https://developer.mozilla.org/ru/docs/Web/Guide/CSS/Getting_Started/Selectors), задающий элементы, которые должны быть скрыты.
* **`domains`** — ограничение на домены, на страницах которых будет применено правило.

Если вы хотите ограничить область действия одним или более доменами, просто перечислите их через запятую. Например: `example.org,example.com##selector`.

> Это правило будет работать также на всех поддоменах `example.org` и `example.com`.

Если вы хотите запретить действие правила на определенном домене, используйте символ `~` перед именем домена. Например: `~example.org##selector`.

Вы можете использовать оба подхода в одном правиле. Например, правило `example.org,~subdomain.example.org##domain` будет работать для домена `example.org` и всех его поддоменов, **кроме `subdomain.example.org`**.

> **Обратите внимание:** Правила скрытия не зависят друг от друга. Если в фильтре есть правило `example.org##selector`, и вы добавляете правило `~example.org##selector`, то оба этих правила будут применены независимо друг от друга.

<a id="elemhide-examples"></a>
#### Примеры правил скрытия

* `example.com##div.textad` — скроет элемент `div` с классом `textad` на домене `example.com` и всех его поддоменах.
* `example.com,example.org###adblock` — скроет элемент с атрибутом `id` равным `adblock` на доменах `example.com`, `example.org` и всех их поддоменах.
* `~example.com##.textad` — скроет элемент с классом `textad` на всех доменах, кроме `example.com` и всех его поддоменах.

> **Ограничения:** Safari не поддерживает одновременно разрешенные и запрещенные домены, поэтому правила вида `example.org,~foo.example.org##.textad` не работают в AdGuard для Safari.

<a id="elemhide-exceptions"></a>
#### Исключения для правил скрытия

Существует специальный тип правил, "выключающий" отдельные правила скрытия на определенных доменах. Синтаксис таких правил очень похож на обычные правила скрытия, но вместо маркера `##` используется `#@#`.

Например, пусть в фильтре есть следующее правило.
```
##.textad
```

Если вы хотите отключить это правило для домена `example.com`, можно воспользоваться правилом-исключением.
```
example.com#@#.textad
```

В некоторых случаях может потребоваться отключение всех запрещающих правил. Например, на время тестирования. Для этого следует воспользоваться правилом исключения без указания домена. Оно полностью отключит соответствующее косметическое правило на ВСЕХ доменах:

```
#@#.textad
```

Правило такого вида даст аналогичный результат:
```
*#@#.textad
```

Применять такие исключения рекомендуется только в случае, когда изменить само правило скрытия не представляется возможным. Во всех остальных случаях лучше изменить исходное правило, используя ограничение на домены.

<a id="cosmetic-css-rules"></a>
### Правила CSS-стилей

Иногда недостаточно просто скрыть какой-либо элемент, чтобы заблокировать рекламу. Например, блокировка рекламного элемента может просто сломать верстку сайта. Для таких случаев AdGuard позволяет использовать гораздо более гибкие правила, чем обычные правила скрытия. По сути, с помощью таких правил вы можете добавить на страницу любой CSS-стиль.

> **Ограничения.** Запрещено использование стилей, которые могут приводить к загрузке каких-либо ресурсов. По сути это означает, что запрещено использование атрибутов типа `<url>`.

> **Совместимость с разными версиями AdGuard.** Правила CSS-стилей не поддерживаются в AdGuard Content Blocker.

> Правила CSS-стилей работают по-разному и их приоритет меняется [в зависимости от платформы](#cosmetic-rules-priority).

<a id="cosmetic-css-rules-syntax"></a>
#### Синтаксис

```
   rule = [domains] "#$#" selector "{" style "}"
domains = [domain0, domain1[, ...[, domainN]]]
```

* **`selector`** — [CSS-селектор](https://developer.mozilla.org/ru/docs/Web/Guide/CSS/Getting_Started/Selectors), задающий элементы, которые должны быть скрыты.
* **`domains`** — ограничение на домены, на страницах которых будет применено правило. Строится по тем же правилам, что и в случае [правил скрытия элементов](#elemhide-syntax).
* **`style`** — CSS-стиль, который мы хотим применить к элементам, выбранным `selector`.

<a id="cosmetic-css-rules-examples"></a>
#### Примеры

```
example.com#$#body { background-color: #333!important; }
```

Это правило применит стиль `background-color: #333!important;` к элементу `body` для домена `example.com` и всех его поддоменов.

<a id="cosmetic-css-rules-exceptions"></a>
#### Исключения

По аналогии с правилами скрытия, существует специальный тип правил, отключающий действие выбранного правила CSS-стилей для определенных доменов. Синтаксис правил-исключений практически такой же, только маркер `#$#` заменяется на `#@$#`.

Например, пусть в фильтре есть следующее правило.
```
#$#.textad { visibility: hidden; }
```

Если вы хотите отключить это правило для домена `example.com`, можно воспользоваться правилом-исключением.
```
example.com#@$#.textad { visibility: hidden; }
```

Применять такие исключения рекомендуется только в случае, когда изменить само правило CSS-стиля не представляется возможным. Во всех остальных случаях лучше изменить исходное правило, используя ограничение на домены.

<a id="extended-css-selectors"></a>
### Расширенные CSS-селекторы

Возможностей CSS 3.0 не всегда хватает для блокировки рекламы. Чтобы решить эту проблему, AdGuard расширяет возможности CSS, добавляя поддержку новых псевдоэлементов. Для поддержки расширенных CSS-селекторов мы разработали модуль [с открытым кодом](https://github.com/AdguardTeam/ExtendedCss).

> **Область применения**. Расширенные селекторы можно применять в любом косметическом правиле, будь то [правила скрытия](#cosmetic-elemhide-rules) или [правила CSS-стилей](#cosmetic-css-rules).

> **Совместимость с разными версиями AdGuard.** Правила с расширенными CSS-селекторами не поддерживаются в AdGuard Content Blocker.

#### Синтаксис

Независимо от того, какие CSS-псевдоклассы вы используете в правилах, вы можете использовать специальные маркеры, чтобы эти правила обрабатывались движком "Extended CSS". Рекомендуется использовать эти маркеры для всех косметических расширенных CSS-правил, чтобы их было легче отличить.

Синтаксис расширенных CSS-правил:

* `#?#` — для скрытия элементов, `#@?#` — для исключений
* `#$?#` — для CSS-стилей, `#@$?#` — для исключений

Мы **крайне рекомендуем** использовать этим маркеры каждый раз, когда вы используете расширенный CSS-селектор.

##### Примеры

* `example.org#?#div:has(> a[target="_blank"][rel="nofollow"])` — это правило блокирует все элементы `div`, которые содержат дочерний элемент ссылку с атрибутами `[target="_blank"][rel="nofollow"]`. При этом правило будет работать только для домена `example.org` и всех его поддоменов.
* `example.com#$?#h3:contains(cookies) { display: none!important; }` — это правило устанавливает стиль  `display: none!important` для всех элементов `h3`, которые содержат слово `cookies`. При этом правило будет работать только для домена `example.com` и всех его поддоменов.
* `example.net#?#.banner:matches-css(width: 360px)` — это правило блокирует все элементы `.banner`, которые содержат стиль `width: 360px`. При этом правило будет работать только для домена `example.net` и всех его поддоменов.
* `example.net#@?#.banner:matches-css(width: 360px)` — это правило отменяет действие предыдущего правила.

> Обратите внимание, что вы можете использовать движок ExtCss для простых селекторов, применяя правило вида: `#?#div`.

> Подробнее [об отладке расширенных селекторов](#selectors-debugging-mode).


<a id="extended-css-has"></a>

##### Псевдокласс `:has()`

Рабочий черновик спецификации CSS 4.0 описывает [псевдокласс `:has`](https://www.w3.org/TR/selectors-4/#has-pseudo). К сожалению, пока что [не многие браузеры поддерживают его](https://developer.mozilla.org/ru/docs/Web/CSS/:has#browser_compatibility).

**Синтаксис**
```
:has(selector)
```

Обратно-совместимый синтаксис:
```
[-ext-has="selector"]
```

Поддерживаемые синонимы для лучшей совместимости: `:-abp-has`, `:if`.

Псевдокласс `:has()` выбирает те элементы, внутри которых есть элементы, подходящие под `selector`.

**Примеры**

Выделение всех элементов `div`, содержащих элемент с классом `banner`:

```html
<!-- HTML code -->
<div>Do not select this div</div>
<div>Select this div<span class="banner"></span></div>
```

Селектор:
```
div:has(.banner)
```

Обратно-совместимый синтаксис:
```
div[-ext-has=".banner"]
```

<a id="extended-css-if-not"></a>
##### Псевдокласс `:if-not()`

Этот псевдокласс фактически является сокращением от `:not(:has())`. Он поддерживается ExtendedCSS для лучшей совместимости с некоторыми фильтрами, но не рекомендуется к использованию в фильтрах AdGuard. Обосновывается это тем, что однажды браузеры будет добавлять встроенную поддержку `:has`, но этого никогда не случится для этого псевдокласса.

<a id="extended-css-contains"></a>
##### Псевдокласс `:contains()`

Принцип действия этого псевдокласса очень прост: он позволяет выбрать элементы, которые содержат внутри заданный текст, либо чьё содержимое соответствует указанному регулярному выражению. Пожалуйста, обратите внимание, что этот псевдокласс использует свойство элемента `textContent` для определения соответствия (а не `innerHTML`).

**Синтаксис**
```
// соответствие обычному тексту
:contains(text)

// соответствие регулярному выражению
:contains(/regexp/i)
```

Обратно-совместимый синтаксис:
```
// соответствие обычному тексту
[-ext-contains="text"]

// соответствие регулярному выражению
[-ext-contains="/regexp/"]
```

> Поддерживаемые синонимы для лучшей совместимости: `:-abp-contains`, `:has-text`.

**Примеры**

Выделение всех элементов `div`, содержащих текст `banner`:
```html
<!-- HTML code -->
<div>Do not select this div</div>
<div id="selected">Select this div (banner)</div>
<div>Do not select this div <div class="banner"></div></div>
```

Селектор:
```
// соответствие обычному тексту
div:contains(banner)

// соответствие регулярному выражению
div:contains(/this .* banner/)

// поддерживаются флаги регулярных выражений
div:contains(/this .* banner/gi)
```

Обратно-совместимый синтаксис:
```
// соответствие обычному тексту
div[-ext-contains="banner"]

// соответствие регулярному выражению
div[-ext-contains="/this .* banner/"]
```

> Обратите внимание, что в этом примере будет выбран только `div` с `id=selected`, так как следующий за ним элемент не содержит текст (`banner` является частью кода, а не текста).

<a id="extended-css-matches-css"></a>
##### Псевдокласс `:matches-css()`

Эти псевдоклассы позволяют выделять элемент по его текущему свойству стиля. Работа этого псевдокласса основана на использовании функции [`window.getComputedStyle`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle).

**Синтаксис**
```
/* element style matching */
selector:matches-css(property-name ":" pattern)

/* ::before pseudo-element style matching */
selector:matches-css-before(property-name ":" pattern)

/* ::after pseudo-element style matching */
selector:matches-css-after(property-name ":" pattern)
```

Обратно-совместимый синтаксис:
```
selector[-ext-matches-css="property-name ":" pattern"]
selector[-ext-matches-css-after="property-name ":" pattern"]
selector[-ext-matches-css-before="property-name ":" pattern"]
```

- `property-name` — название CSS свойства, которое будет проверено у элемента
- `pattern` —  паттерн значений, использующий то же простое соответствие по вайлдкардам как и базовые правила URL-фильтрации ИЛИ регулярное выражение. Для этого типа соответствия AdGuard не обращает внимание на регистр. В случае с регулярными выражениями, паттерн будет выглядеть как `/regexp/`.

> Для паттернов, не являющихся регулярными выражениями, символы `(`,`)`,`[`,`]` должны быть экранированы, потому что мы экранируем их в правилах фильтрации.

> Для регулярных выражений символы `"` и `\` должны быть экранированы, потому что мы вручную экранируем их в extended-css-selector.js.

**Примеры**

Выделение всех элементов `div`, содержащих псевдокласс `::before` с указанным содержимым:
```html
<!-- HTML code -->
<style type="text/css">
    #to-be-blocked::before {
        content: "Block me"
    }
</style>
<div id="to-be-blocked" class="banner"></div>
<div id="not-to-be-blocked" class="banner"></div>
```

Селектор:
```
// Обычное соответствие
div.banner:matches-css-before(content: block me)

// Регулярные выражения
div.banner:matches-css-before(content: /block me/)
```

Обратно-совместимый синтаксис:
```
// Обычное соответствие
div.banner[-ext-matches-css-before="content: block me"]

// Регулярные выражения
div.banner[-ext-matches-css-before="content: /block me/"]
```

<a id="extended-css-matches-attr"></a>
##### Псевдокласс `:matches-attr()`

Этот псевдокласс позволяет выделить элемент по его атрибутам, особенно если они рандомизированы и не постоянные.

**Синтаксис**
```
selector:matches-attr("name"[="value"])
```

- `name` — название атрибута ИЛИ регулярное выражение для названия атрибута
- `value` — необязательно, значение атрибута ИЛИ регулярное выражение для значения атрибута

> Для регулярных выражений символы `"` и `\` должны быть экранированы.

**Примеры**

```html
<!-- HTML code -->
<div id="targer1" class="matches-attr" hsd4jkf-link="ssdgsg-banner_240x400"></div>

<div id="targer2" class="has matches-attr">
  <div data-sdfghlhw="adbanner"></div>
</div>

<div id="targer3-host" class="matches-attr has contains">
  <div id="not-targer3" wsdfg-unit012="click">
    <span>socials</span>
  </div>
  <div id="targer3" hrewq-unit094="click">
    <span>ads</span>
  </div>
</div>

<div id="targer4" class="matches-attr upward">
  <div >
    <inner-afhhw class="nyf5tx3" nt4f5be90delay="1000"></inner-afhhw>
  </div>
</div>
```

```
// для div#targer1
div:matches-attr("/-link/")

// для div#targer2
div:has(> div:matches-attr("/data-/"="adbanner"))

// для div#targer3
div:matches-attr("/-unit/"="/click/"):has(> span:contains(ads))

// для div#targer4
*[class]:matches-attr("/.{5,}delay$/"="/^[0-9]*$/"):upward(2)
```

<a id="extended-css-matches-property"></a>
##### Псевдокласс `:matches-property()`

Этот псевдокласс позволяет выделить элемент по его свойствам.

**Синтаксис**
```
selector:matches-property("name"[="value"])
```

- `name` — название свойства ИЛИ регулярное выражение для названия свойства
- `value` — необязательно, значение свойства ИЛИ регулярное выражение для значения свойства

> Для регулярных выражений символы `"` и `\` должны быть экранированы.

> `name` поддерживает регулярные выражения для обозначения свойства в цепи, например, `prop./^unit[\\d]{4}$/.type`

**Примеры**

```javascript
divProperties = {
    id: 1,
    check: {
        track: true,
        unit_2ksdf1: true,
    },
    memoizedProps: {
        key: null,
        tag: 12,
        _owner: {
            effectTag: 1,
            src: 'ad.com',
        },
    },
};
```

```
// элемент с такими свойствами может быть выделен любым из таких правил:

div:matches-property("check.track")

div:matches-property("check./^unit_.{4,6}$/"))

div:matches-property("memoizedProps.key"="null")

div:matches-property("memoizedProps._owner.src"="/ad/")
```

> **Для разработчиков фильтров:** Чтобы проверить свойства элемента, нужно:
> 1. Исследовать нужный элемент на странице или выбрать его во вкладке `Элементы` в Инструментах разработчика браузера.
> 2. Выполнить `console.dir($0)` во вкладке `Console`.

<a id="extended-css-xpath"></a>
##### Псевдокласс `:xpath()`

Этот псевдокласс позволяет выбирать элементы согласно Xpath выражению.

> **Может быть только в конце выражения, за исключением использования [псевдокласса `:remove()`](#remove-pseudos).**

Этот псевдокласс отличается от других тем, что он может использоваться не только для фильтрации текущей выборки элементов, но и для расширения или создания новой. По этой причине `selector` является необязательным параметром. Например, выражение `:xpath()` может использоваться для выбора всех родительских элементов, что невозможно было бы сделать с помощью css-селекторов.

**Синтаксис**
```
[selector]:xpath(expression)
```

- `selector`- необязательно, CSS-селектор
- `expression` — XPath выражение

**Примеры**
```
// Фильтрация результатов выборки селектора
div:xpath(//*[@class="test-xpath-class"])
div:has-text(/test-xpath-content/):xpath(../../..)

// Использование xpath для выборки элементов
facebook.com##:xpath(//div[@id="stream_pagelet"]//div[starts-with(@id,"hyperfeed_story_id_")][.//h6//span/text()="People You May Know"])
```

<a id="extended-css-nth-ancestor"></a>
##### Псевдокласс `:nth-ancestor()`

Этот псевдокласс позволяет выбирать родительские элементы указанного n-поколения относительно элемента.

Это своего рода эквивалент `:xpath(..[/..]*)`.

> **Может быть только в конце выражения, за исключением использования [псевдокласса `:remove()`](#remove-pseudos).**

**Синтаксис**
```
selector:nth-ancestor(n)
```
- `selector` — CSS-селектор
- `n` — натуральное число от 1 до 255, количество поколений от выбранного элемента

**Примеры**
```
div.test:nth-ancestor(4)

div:has-text(/test/):nth-ancestor(2)
```

<a id="extended-css-upward"></a>
##### Псевдокласс `:upward()`

Этот псевдокласс позволяет выбирать ближайшие родительские элементы относительно указанного элемента.

> **Может быть только в конце выражения, за исключением использования [псевдокласса `:remove()`](#remove-pseudos).**

**Синтаксис**
```
/* параметр в виде селектора */
subjectSelector:upward(targetSelector)

/* параметр в виде числа */
subjectSelector:upward(n)
```
- `subjectSelector` — CSS-селектор — элемент, относительно которого будет искаться родительский элемент
- `targetSelector` — CSS-селектор — условный родительский элемент
- `n` — натуральное число от 1 до 255, количество поколений от выбранного элемента

**Примеры**
```
div.child:upward(div[id])
div:contains(test):upward(div[class^="parent-wrapper-")

div.test:upward(4)
div:has-text(/test/):upward(2)
```

<a id="remove-pseudos"></a>
##### Псевдокласс `:remove()` и псевдосвойство `remove`

Иногда необходимо именно удалить определенный элемент, а не просто скрыть его или применить какие-либо правила стиля. В таких случаях можно использовать псевдокласс `:remove()` или псевдосвойство `remove`.

> **Псевдокласс `:remove()` может быть только в конце выражения.**

**Синтаксис**
```
! псевдокласс
selector:remove()

! псевдосвойство
selector { remove: true; }
```
- `selector` — CSS-селектор

**Примеры**
```
div.inner:remove()
div:has(> div[ad-attr]):remove()
div:xpath(../..):remove()

div:contains(target text) { remove: true; }
div[class]:has(> a:not([id])) { remove: true; }
```

> Обратите внимание, если используется псевдокласс `:remove()` или псевдосвойство `remove`, то другие свойства этого стиля будут проигнорированы.

<a id="cosmetic-rules-priority"></a>
### Приоритет косметических правил

То, как применяются **правила скрытия элементов** и **правила CSS-стилей**, зависит от платформы.

**В AdGuard для Windows, Mac, Android** мы используем таблицу стилей, встроенную в страницу. Приоритет у косметических правил такой же, как и у любых других таблиц стилей CSS на сайтах. Но есть ограничение: [правила скрытия элементов](#elemhide-syntax) и [правила CSS-стилей](#cosmetic-css-rules) не могут обходить встроенные стили. В таких случаях рекомендуется использовать расширенные селекторы или HTML-фильтрацию.

**В Браузерном расширении AdGuard** используются так называемые "пользовательские таблицы стилей". Их приоритет выше, даже чем у встроенных стилей.

**Расширенные CSS-селекторы** используют для работы Javascript и добавляют встроенные стили сами, поэтому могут игнорировать любой стиль.

<a id="html-filtering-rules"></a>
## Правила фильтрации HTML

В большинстве случаев достаточно использовать базовые и косметические правила. Но иногда для фильтрации рекламы необходимо изменять HTML-код самой страницы до того, как он будет загружен браузером. Для того чтобы сделать это, применяются правила фильтрации HTML-контента. Они позволяют указать, какие HTML-элементы необходимо вырезать из страницы перед тем, как страница попадет в браузер.

> **Совместимость с разными версиями AdGuard.** Правила фильтрации HTML поддерживаются в AdGuard для Windows, Mac, Android и Браузерном расширеним AdGuard для Firefox. Они не работают в расширениях для других браузеров, потому что они не могут модифицировать содержимое страниц на сетевом уровне.

<a id="html-filtering-rules-syntax"></a>
### Синтаксис

```
      rule = [domains] "$$" tagName [attributes]
   domains = [domain0, domain1[, ...[, domainN]]]
attributes = "[" name0 = value0 "]" "[" name1 = value2 "]" ... "[" nameN = valueN "]"
```

* **`tagName`** — имя элемента в нижнем регистре, например, `div` или `script`.
* **`domains`** — ограничение на домены, на страницах которых будет применено правило. Строится по тем же правилам, что и в случае [правил скрытия элементов](#elemhide-syntax).
* **`attributes`** — список атрибутов, ограничивающих выбор элементов. `name` — название атрибута, `value` — подстрока, которая содержится в значении атрибута.

<a id="html-filtering-rules-examples"></a>
### Примеры

**HTML code**
```html
<script data-src="/banner.js"></script>
```

**Правило**
```
example.org$$script[data-src="banner"]
```

Это правило удалит из кода страниц все элементы `script` со значением `data-src`, содержащим подстроку `banner`. При этом правило будет работать только для домена `example.org` и всех его поддоменов.

<a id="html-filtering-rules-attributes"></a>
### Специальные атрибуты

Помимо обычных атрибутов, значение которых проверяется у каждого элемента, существует набор специальных атрибутов правила, которые изменяют способ работы правила. Ниже мы перечислим все эти атрибуты.

<a id="tag-content-attribute"></a>
##### `tag-content`

Пожалуй, наиболее часто используемый специальный атрибут. Он ограничивает выбор теми элементами, внутренний HTML код которых (innerHTML) содержит указанную подстроку.

> Используйте `""` для экранирования `"`, например:
> `$$script[tag-content="alert(""this is ad"")"]`

Например, рассмотрим такой HTML код:
```html
<script type="text/javascript">
    document.write('<div>banner text</div>" />');
</script>
```

Следующее правило удалит со страницы все элементы `script`, код которых содержит подстроку `banner`.
```
$$script[tag-content="banner"]
```

> **Вложенные элементы**. Если мы имеем дело с несколькими вложенными друг в друга элементами, каждый из которых подходит под одно и то же правило фильтрации HTML-контента, то удалены будут все эти элементы.

<a id="wildcard-attribute"></a>
##### `wildcard`

Этот специальный атрибут работает практически также как `tag-content` и позволяет проверить внутренний HTML-код элемента. Правило проверит, удовлетворяет ли HTML-код элемента заданному [шаблону поиска](https://ru.wikipedia.org/wiki/%D0%A8%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D0%BE%D0%B8%D1%81%D0%BA%D0%B0).

> Используйте `""` для экранирования `"`, например:
> `$$script[wildcard=""banner""]`

Возьмем для примера следующее правило:
`$$script[wildcard="*banner*text*"]`

Оно проверит, что код элемента содержит две последовательные подстроки `banner` и `text`.

<a id="max-length-attribute"></a>
##### `max-length`

Задает максимальную длину содержимого HTML-элемента. Если этот параметр задан, и длина содержимого превышает заданное значение — правило не применяется к элементу.

> **Значение по умолчанию**. Если этот параметр не задан, то `max-length` считается равным 8192.

Например, возьмем следующее правило:
```
$$div[tag-content="banner"][max-length="400"]
```

Это правило удалит все элементы `div`, код которых содержит подстроку `banner`, и длина которых не превышает `400` символов.

<a id="min-length-attribute"></a>
##### `min-length`

Задает минимальную длину содержимого HTML-элемента. Если этот параметр задан, и длина содержимого меньше заданного значения — правило не применяется к элементу.

Например, возьмем следующее правило:
```
$$div[tag-content="banner"][min-length="400"]
```

Это правило удалит все элементы `div`, код которых содержит подстроку `banner`, и длина которых превышает `400` символов.

<a id="html-filtering-rules-exceptions"></a>
### Исключения для правил фильтрации HTML

По аналогии с правилами скрытия, существует специальный тип правил, отключающий действие выбранного правила фильтрации HTML для определенных доменов. Синтаксис правил-исключений такой же, только маркер `$$` заменяется на `$@$`.

Например, пусть в фильтре есть следующее правило.
```
$$script[tag-content="banner"]
```

Если вы хотите отключить это правило для домена `example.com`, можно воспользоваться правилом-исключением.
```
example.com$@$script[tag-content="banner"]
```

В некоторых случаях может потребоваться отключение всех запрещающих правил. Например, на время тестирования. Для этого следует воспользоваться правилом исключения без указания домена.
```
$@$script[tag-content="banner"]
```

Применять такие исключения рекомендуется только в случае, когда изменить само правило скрытия не представляется возможным. Во всех остальных случаях лучше изменить исходное правило, используя ограничение на домены.

<a id="javascript-rules"></a>
## JavaScript-правила

AdGuard поддерживает специальный тип правил, позволяющий вставить любой javascript-код на страницы интернет-сайтов.

> **Ограничения.** JavaScript-правила могут быть использованы **только в доверенных фильтрах**. В эту категорию попадают ваши **Пользовательские правила**, и фильтры, которые создаются командой AdGuard.

> **Совместимость с разными версиями AdGuard.** Javascript-правила не поддерживаются в AdGuard Content Blocker.

Мы **крайне рекомендуем** использовать скриптлеты вместо Javascript-правил везде, где это возможно. JS-правила предназначены для помощи в процессе дебаггинга, но в качестве полноценного решения в долгосрочной перспективе следует использовать скриптлеты.

<a id="javascript-rules-syntax"></a>
### Синтаксис

```
rule = [domains]  "#%#" script
```

* **`domains`** — ограничение на домены, на страницах которых будет применено правило. Строится по тем же правилам, что и в случае [правил скрытия элементов](#elemhide-syntax).
* **`script`** — произвольный javascript-код **в одну строку**.

<a id="javascript-rules-examples"></a>
### Примеры

* `example.org#%#window.__gaq = undefined;` — выполняет код `window.__gaq = undefined;` на всех страницах сайта `example.org` и всех его поддоменах.

<a id="javascript-rules-exceptions"></a>
### Исключения

По аналогии с правилами скрытия, существует специальный тип правил, отключающий действие выбранного javascript правила для определенных доменов. Синтаксис правил-исключения такой же, только маркер `#%#` заменяется на `#@%#`.

Например, пусть в фильтре есть следующее правило.
```
#%#window.__gaq = undefined;
```

Если вы хотите отключить это правило для домена `example.com`, можно воспользоваться правилом-исключением.
```
example.com#@%#window.__gaq = undefined;
```

В некоторых случаях может потребоваться отключение всех запрещающих правил. Например, на время тестирования. Для этого следует воспользоваться правилом исключения без указания домена.
```
#@%#window.__gaq = undefined;
```

Применять такие исключения рекомендуется только в случае, когда изменить само правило скрытия не представляется возможным. Во всех остальных случаях лучше изменить исходное правило, используя ограничение на домены.

<a id="scriptlets"></a>
## Скриптлеты

Скриптлет — это функция JavaScript, которая предоставляет расширенные возможности для блокирования контента. Такие функции могут использоваться в декларативной манере в правилах фильтрации AdGuard.

> AdGuard поддерживает множество различных скриптлетов. Чтобы добиться совместимости между различными блокировщиками, мы также поддерживаем синтаксис uBO и ABP.

<a id="scriptlets-syntax"></a>
### Синтаксис
```
rule = [domains]  "#%#//scriptlet(" scriptletName arguments ")"
```

`scriptletName` (обязательно) — это имя скриптлета из библиотеки скриптлетов AdGuard
`arguments` (опционально) — это список аргументов в формате String (другие типы аргументов не поддерживаются)

<a id="scriptlets-examples"></a>
### Примеры

```
example.org#%#//scriptlet("abort-on-property-read", "alert")
```
Это правило применится на страницах домена example.org (и его поддоменов) и выполнит скриптлет "abort-on-property-read" с параметром "alert".

Подробнее [об отладке скриптлетов](#debug-scriptlets).

Больше информации о скриптлетах можно найти [на GitHub](https://github.com/AdguardTeam/Scriptlets#scriptlets).

> **Совместимость с разными версиями AdGuard.** Скриптлеты не поддерживаются в AdGuard Content Blocker.

<a id="non-basic-rules-modifiers"></a>

## Модификаторы для «не Базовых» правил

Поведение любого правила можно изменить, используя модификаторы, описанные ниже.

<a id="non-basic-rules-modifiers-syntax"></a>
### Синтаксис
```
rule = "[$" modifiers "]" [rule text]
modifiers = modifier0[, modifier1[, ...[, modifierN]]]
```

* `modifier` — группа модификаторов, [описанных ниже](https://kb.adguard.com/ru/general/how-to-create-your-own-ad-filters#basic-rules-modifiers).
* `rule text` — косметическое правило, которое нужно модифицировать.

Например: `[$domain=example.com,app=test_app]##selector`.

В значениях модификаторов следующие символы должны быть экранированы: `[`, `]`, `,` и `\` (если он не используется для экранирования). Используйте `\`, чтобы экранировать их. Например, экранированная скобка выглядит так: `\]`.

<a id="non-basic-rules-modifiers-app"></a>

### `app`

Модификатор `$app` ограничивает действие правила до конкретного приложения или списка приложений. Поведение и синтаксис модификатора полностью совпадают с соответствующим [модификатором `$app` для базовых правил](#app-modifier).

#### Примеры `app`:
* `[$app=org.example.app]example.com##.textad` — скрывает `div` и заменяет его на класс `textad` на `example.com` и всех поддоменах в запросах, посланных из `org.example.app` приложения Android.
* `[$app=~org.example.app1|~org.example.app2]example.com##.textad` — скрывает `div` и заменяет его на класс `textad` на `example.com` и всех поддоменах в запросах, посланных из любого приложения кроме `org.example.app1` и `org.example.app2`.
* `[$app=com.apple.Safari]example.org#%#//scriptlet('prevent-setInterval', 'check', '!300')` — это правило применит соответствующий скриптлет только в браузере Safari на Mac.
* `[$app=org.example.app]#@#.textad` — отключит все правила `##.textad` для всех доменов при использовании приложения `org.example.app`.

> **Совместимость с разными версиями AdGuard.** Этот тип правил поддерживается AdGuard для Windows, Mac и Android. **На данный момент только в девелоперских сборках.**

<a id="non-basic-rules-modifiers-domain"></a>

### `domain`

Модификатор `$domain` ограничивает область действия правила списком доменов и их поддоменов.
Поведение и синтаксис модификатора почти полностью совпадают с соответствующим [модификатором `$domain` для базовых правил](#domain-modifier).
Единственное отличие в том, что разделитель доменов `|` в регулярных выражениях экранировать не требуется.

#### Примеры `domain`:
* `[$domain=example.com]##.textad` — скрывает `div` и заменяет его на класс `textad` на `example.com` и всех поддоменах.
* `[$domain=example.com|example.org]###adblock` — скрывает элемент с атрибутом `id`, равным `adblock`, на `example.com`, `example.org` и всех поддоменах.
* `[$domain=~example.com]##.textad` — скрывает `div` и заменяет его на класс `textad` на всех доменах кроме `example.com` и его поддоменов.
* `[$domain=/(^|.+\.)example\.(com|org)\$/]##.textad` — скрывает элемент с атрибутом `id`, равным `adblock`, на `example.com`, `example.org` и всех поддоменах.

Существует 2 способа указать доменные ограничения для косметических правил:
    1) «классический»: обозначить ограничение на домены перед маской и атрибутами правила: `example.com##.textad`;
    2) с помощью модификаторов: обозначить ограничение на домены через модификатор `domain`: `[$domain=example.com]##.textad`.

Но правила, в которых используется смешанный стиль ограничения на домены, считаются некорректными. Так, например, правило `[$domain=example.org]example.com##.textad` будет отклонено.

<a id="non-basic-rules-modifiers-path"></a>

### `path`

Модификатор `$path` ограничивает действие правила определенным местоположением или страницей на сайтах.

#### Синтаксис
```
path=pattern
```

`pattern` — это маска пути, которой ограничено правило. Его синтаксис и поведение почти такие же, как в [шаблоне базовых правил](#basic-rules-syntax). Вы также можете использовать [специальные символы](#basic-rules-special-characters), за исключением `||`, поскольку в этом случае это не имеет никакого смысла (см. примеры ниже).

> Обратите внимание, что модификатор пути также соответствует параметрам запроса.

> Модификатор `path` поддерживает регулярные выражения [таким же образом](#regexp-support), что и базовые правила.

#### Примеры `path`:
* `[$path=page.html]##.textad` — скрывает `div` с классом `textad` на `/page.html`, `/page.html?<query>`, `/sub/page.html` или `/another_page.html`
* `[$path=/page.html]##.textad` — скрывает `div` с классом `textad` на `/page.html`, `/page.html?<query>`, `/sub/page.html` любого домена, но не на `/another_page.html`
* `[$path=|/page.html]##.textad` — скрывает `div` с классом `textad` на `/page.html` или `/page.html?<query>` любого домена, но не на `/sub/page.html`
* `[$path=/page.html|]##.textad` — скрывает `div` с классом `textad` на `/page.html` или `/sub/page.html` любого домена, но не на `/page.html?<query>`
* `[$path=/page*.html]example.com##.textad` — скрывает `div` с классом `textad` на `/page1.html`, `/page2.html` или любом другом пути, соответствующим `/page<...>.html`, на домене `example.com`
* `[$domain=example.com,path=/page.html]##.textad` — скрывает `div` с классом `textad` на `page.html` домена `example.com` и всех его поддоменах, но не на `another_page.html`
* `[$path=/\\/(sub1|sub2)\\/page\\.html/]##.textad` — скрывает `div` с классом `textad` как на `/sub1/page.html`, так и `/sub2/page.html` любого домена (обратите внимание на [экранированные символы](#non-basic-rules-modifiers-syntax))

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$path` поддерживаются в AdGuard для Windows, Mac, Android и Браузерном расширении AdGuard для Chrome, Firefox, Edge.

<a id="non-basic-rules-modifiers-path"></a>

### url

Модификатор `$url` ограничивает действие правила запросами, URL которых соответствует указанной маске.

#### Синтаксис

```
url = pattern
```

где `pattern` — маска адреса, синтаксис которой соответсвует [маске адреса (`pattern`) базовых правил](#basic-rules-syntax) за исключением того, что [некоторые символы](#non-basic-rules-modifiers-syntax) должны быть экранированны.
[Специальные символы](#basic-rules-special-characters) и [регулярные выражения](#regexp-support) также поддерживаются.

#### Примеры

* `[$url=||example.com/ads/*]##.textad` — скрывает `div` с классом `textad` в запросах, например, к `http://example.com/ads/banner.jpg` и `http://subdomain.example.com/ads/otherbanner.jpg`.
* `[$url=||example.org^]###adblock` — скрывает элемент с атрибутом `id` равным `adblock` в запросах к `example.org` и всем его поддоменам.
* `[$url=/\[a-z\]+\\.example\\.com^/]##.textad` — скрывает `div` с классом `textad` в запросах к доменам, соответствующих регулярному выражению `[a-z]+\.example\.com^`.

> **Совместимость с разными версиями AdGuard.** Правила с модификатором `$url` поддерживаются в AdGuard для Windows, Mac, и Android, **с CoreLibs версии 1.11 или выше**.

<a id="for_maintainers"></a>
## Информация для разработчиков фильтров

Если вы разрабатываете сторонний фильтр, известный AdGuard, вам может быть интересна информация, представленная в настоящем разделе. Пожалуйста, имейте в виду, что специальные комментарии hint будут применяться только к зарегистрированным фильтрам. Фильтр считается зарегистрированным и известным AdGuard, если он присутствует в [перечне известных фильтров](https://filters.adtidy.org/extension/chromium/filters.json).  Если вы желаете зарегистрировать свой фильтр, пожалуйста, направьте запрос в [репозиторий AdGuardFilters](https://github.com/AdguardTeam/AdguardFilters).

<a id="pre_processor"></a>
### Директивы пре-процессинга

Мы предоставляем несколько директив пре-процессинга, которые могут быть использованы разработчиками фильтров для улучшения совместимости с различными блокировщиками рекламы. Директивы могут
* [включать содержимое отдельного файла в фильтр](#include-directive)
* [применять правила в зависимости от типа блокировщика](#conditions-directive)
* [уточнять контент-блокер для применения правил в Safari](#safari-affinity-directive)

> Любая ошибка в пре-процессинговой директиве приведёт к невозможности обновить фильтр, как если бы URL фильтра был недоступен.

> Директивы пре-процессинга можно использовать в Пользовательских правилах в собственных (кастомных) фильтрах.

<a id="include-directive"></a>
#### Включение файла

Директива `!#include` позволяет включать в фильтр содержимое заданного файла. Она поддерживает только файлы из того же источника, чтобы удостовериться, что разработчик фильтров является владельцем указанного файла. Включённый файл также может содержать директивы пре-процессинга (даже другие `!#include` директивы). Блокировщики должны принимать во внимание случай рекурсивного использования `!#include` и внедрять защитный механизм.

**Синтаксис**
```
!#include file_path
```
- `file_path` — абсолютный (того же источника) или относительный путь к файлу

> Файлы должны находиться на том же домене, но могут быть расположены в другой директории.

> Если включённый файл не найден или недоступен, не будут работать обновления всего фильтра.

> Для локальных собственных фильтров ограничение на тот же источник не распространяется.

**Примеры**

URL фильтра: `https://example.org/path/filter.txt`
```
! Корректный (тот же источник):
!#include https://example.org/path/includedfile.txt
!
! Корректный (относительный путь):
!#include /includedfile.txt
!#include ../path2/includedfile.txt
!
! Некорректный (другой источник):
!#include https://domain.com/path/includedfile.txt
```

<a id="conditions-directive"></a>
#### Условия

Разработчики фильтров могут использовать условия, чтобы подставлять нужные правила, в зависимости от типа блокировщика. Когда блокировщик рекламы сталкивается с директивой `!#if`, за которой затем следует директива `!#endif`, он скомпилирует код внутри блока, только если выполнено указанное условие. Условие поддерживает базовые логические операторы.


> Директива с условием, начинающаяся с директивы `!#if`, должна явно прерываться директивой `!#endif`.

> Пробелы имеют значение. `!#if` — это корректная директива, в то время как `!# if` — нет.

**Синтаксис**
```
!#if (conditions)
rules_list
!#endif
```
- `!#if (conditions)` — начало блока
- `conditions` — точно так же, как и в случае с некоторыми популярными языками программирования, условия пре-процессинга основаны на константах, объявляемых блокировщиками. Разработчики блокировщиков самостоятельно определяют, какие именно константы объявлять. Возможные значения:
  - `adguard` — объявляется всегда; даёт разработчикам фильтров понять, что это один из продуктов AdGuard; должно быть достаточно в 95% случаев
  - специфичные для конкретных продуктов константы, которые нужны в редких случаях, когда правило должно работать (или не работать — тогда перед константой используйте `!`) только для конкретного продукта:
    - `adguard_app_windows` — AdGuard для Windows
    - `adguard_app_mac` — AdGuard для Mac
    - `adguard_app_android` — AdGuard для Android
    - `adguard_app_ios` — AdGuard для iOS
    - `adguard_ext_safari` — AdGuard для Safari
    - `adguard_ext_chromium` — Браузерное расширение AdGuard для Chrome (и браузеров на базе Chromium, например, новый Microsoft Edge)
    - `adguard_ext_firefox` — Браузерное расширение AdGuard для Firefox
    - `adguard_ext_edge` — Браузерное расширение AdGuard для Edge Legacy
    - `adguard_ext_opera` — Браузерное расширение AdGuard для Opera
    - `adguard_ext_android_cb` — AdGuard Content Blocker для мобильных браузеров Samsung и Yandex
    - `ext_ublock` — особый случай; эта константа объявляется, когда версия фильтра для ublock компилируется при помощи [FiltersRegistry](https://github.com/AdguardTeam/FiltersRegistry)
- `rules_list` — список правил
- `!#endif` — конец блока

**Примеры**
```
! для всех продуктов AdGuard, кроме AdGuard для Safari
!#if (adguard && !adguard_ext_safari)
||example.org^$third-party
domain.com##div.ad
!#endif
```

```
! директивы также можно совмещать
!#if (adguard_app_android)
!#include /androidspecific.txt
!#endif
```

<a id="safari-affinity-directive"></a>
#### Влияние на блокировщик контента Safari

В Safari существует строгое ограничение в 50 тысяч правил на один блокировщик контента. Но в AdGuard для Safari и в AdGuard для iOS максимальное число поддерживаемых правил увеличено до 300 тысяч. Это получилось благодаря условному их разделению на несколько блокировщиков контента. В целом, отдельные категории фильтров более-менее независимы, поэтому они поделены между блокировщиками контента следующим образом:
- AdGuard General — Блокировка рекламы, Языковые
- AdGuard Privacy — Антитрекинг
- AdGuard Social — Виджеты социальных сетей, Раздражители
- AdGuard Security — Безопасность
- AdGuard Other — Другие
- AdGuard Custom — Собственные

> `Пользовательские правила` и `Белый список` добавляются в каждый блокировщик контента.

Главным недостатком использования нескольких блокировщиков контента является то, что они не могут работать вместе и ссылаться в своих правилах фильтрации друг на друга. И из-за этого бывают весьма неожиданные проблемы. Чтобы избежать этого и заставить правила внутри блока (из одного блокировщика) применяться вместе с правилами из другого блокировщика контента, разработчики фильтров могут использовать директиву `!#safari_cb_affinity`.

**Синтаксис**
```
!#safari_cb_affinity(content_blockers)
rules_list
!#safari_cb_affinity
```
- `!#safari_cb_affinity(content_blockers)` — начало блока
- `content_blockers` — список блокировщиков контента, разделенные запятой. Возможные значения:
  - `general` — блокировщик контента AdGuard General
  - `privacy` — блокировщик контента AdGuard Privacy
  - `social` — блокировщик контента AdGuard Social
  - `security` — блокировщик контента AdGuard Security
  - `other` — блокировщик контента AdGuard Other
  - `custom` — блокировщик контента AdGuard Custom
  - `all` — особое ключевое слово, что значит применение правил ко **всем** блокировщикам контента
- `rules_list` — список правил
- `!#safari_cb_affinity` — конец блока

**Примеры**
```
! чтобы не-скрывать указанный элемент, который скрывается Базовым фильтром:
!#safari_cb_affinity(general)
example.org#@#.adBanner
!#safari_cb_affinity
```
```
! чтобы разблокировать запрос, который блокируется Фильтр счетчиков и систем аналитики:
!#safari_cb_affinity(privacy)
@@||example.org^
!#safari_cb_affinity
```

<a id="hints"></a>
### Hints

"Hint" — это специальный комментарий, инструкция для компилятора фильтров, работающего на стороне сервера (см. [FiltersRegistry](https://github.com/AdguardTeam/FiltersRegistry)).

<a id="hints_syntax"></a>
#### Синтаксис Hints

```
!+ HINT_NAME1(PARAMS) HINT_NAME2(PARAMS)
```

> Обратите внимание: вы можете использовать несколько комментариев hint.

<a id="not_optimized"></a>
#### NOT_OPTIMIZED hint

Для каждого фильтра AdGuard существуют две версии: полная и оптимизированная. Оптимизированная версия имеет намного меньший объем и не включает правила, которые не используются совсем или используются редко.

Частота использования правил определяется собранной [статистикой по рекламным фильтрам](https://kb.adguard.com/ru/general/filter-rules-statistics). Но оптимизация основана также на исходной конфигурации для каждого фильтра. Например, вот так это выглядит для Базового фильтра:

```
"filter": Базовый фильтр AdGuard,
"percent": 30,
"minPercent": 20,
"maxPercent": 40,
"strict": true
```
Где:

* **filter** — идентификатор фильтра
* **percent** — ожидаемый процент оптимизации `~= (количество правил в оптимизированном фильтре) / (количество правил в исходном фильтре) * 100`
* **minPercent** — нижняя граница значения `percent`
* **maxPercent** — верхняя граница значения `percent`
* **strict** — если `percent < minPercent` ИЛИ `percent > maxPercent` и включен режим strict, то компиляция фильтра должна завершиться неудачно, в противном случае должны использоваться оригинальные правила

>Иными словами, `percent` это "уровень сжатия". Например, для Базового фильтра он настроен на 40%. Это значит, что алгоритм оптимизации удалит 60% правил.

В итоге, вот так выглядят версии Базового фильтра для Браузерного расширения AdGuard для Chrome:
- полная: https://filters.adtidy.org/extension/chromium/filters/2.txt
- оптимизированная: https://filters.adtidy.org/extension/chromium/filters/2_optimized.txt

**Если вы хотите добавить правило, которое не должно удаляться при оптимизации, используйте хинт `NOT_OPTIMIZED`:**

```
!+ NOT_OPTIMIZED
||example.org^
```

**А такое правило не будет оптимизировано только для AdGuard для Android:**

```
!+ NOT_OPTIMIZED PLATFORM(android)
||example.org^

```

<a id="platform_not_platform"></a>

#### PLATFORM и NOT_PLATFORM hints

Записи этого типа позволяют указывать системную платформу, для которой применяется правило. Ниже представлен список используемых платформ:

* `windows` — например, Базовый фильтр для Windows — [https://filters.adtidy.org/windows/filters/2.txt](https://filters.adtidy.org/windows/filters/2.txt)

* `mac` — например, Базовый фильтр для Mac — [https://filters.adtidy.org/mac_v2/filters/2.txt](https://filters.adtidy.org/mac_v2/filters/2.txt)

* `android` — например, Базовый фильтр для Android — [https://filters.adtidy.org/android/filters/2.txt](https://filters.adtidy.org/android/filters/2.txt)

* `ios` — например, Базовый фильтр для iOS — [https://filters.adtidy.org/ios/filters/2.txt](https://filters.adtidy.org/ios/filters/2.txt)

* `ext_chromium` — например, Базовый фильтр для Браузерного расширения AdGuard для Chrome — [https://filters.adtidy.org/extension/chromium/filters/2.txt](https://filters.adtidy.org/extension/chromium/filters/2.txt)

* `ext_ff` — например, Базовый фильтр для Браузерного расширения AdGuard для Firefox — [https://filters.adtidy.org/extension/firefox/filters/2.txt](https://filters.adtidy.org/extension/firefox/filters/2.txt)

* `ext_edge` — например, Базовый фильтр для Браузерного расширения AdGuard для Edge — [https://filters.adtidy.org/extension/edge/filters/2.txt](https://filters.adtidy.org/extension/edge/filters/2.txt)

* `ext_opera` — например, Базовый фильтр для Браузерного расширения AdGuard для Opera — [https://filters.adtidy.org/extension/opera/filters/2.txt](https://filters.adtidy.org/extension/opera/filters/2.txt)

* `ext_ublock` — например, Базовый фильтр для uBlock Origin — [https://filters.adtidy.org/extension/ublock/filters/2.txt](https://filters.adtidy.org/extension/ublock/filters/2.txt)

* `ext_safari` — например, Базовый фильтр для AdGuard для Safari — [https://filters.adtidy.org/extension/safari/filters/2.txt](https://filters.adtidy.org/extension/safari/filters/2.txt)

* `ext_android_cb` — например, Базовый фильтр для AdGuard Content Blocker — [https://filters.adtidy.org/extension/android-content-blocker/filters/2.txt](https://filters.adtidy.org/extension/android-content-blocker/filters/2.txt)

Примеры:

Это правило будет действовать только в AdGuard для Windows, Mac, Android:

```
!+ PLATFORM(windows,mac,android)
||example.org^
```

Это правило будет действовать для всех платформ, кроме AdGuard для Safari, Android Content Blocker и AdGuard для iOS:

```
!+ NOT_PLATFORM(ext_safari, ext_android_cb, ios)
||example.org^
```

<a id="how-to-debug"></a>
## Как отлаживать правила фильтрации

Хоть самые простые правила фильтрации и возможно придумать "в голове", для чего-либо даже немного более сложного вам потребуются дополнительная помощь в отладке и итерировании. Для этой цели существуют различные инструменты. Вы можете использовать Инструменты разработчика в Chrome и их аналоги в других браузерах, но большинство продуктов AdGuard предоставляют и другой инструмент: Журнал фильтрации.

<a id="debug-filtering-log"></a>
### Журнал фильтрации

Журнал фильтрации — продвинутый инструмент, который полезен в основном для разработчиков фильтров. В нём отображаются все веб-запросы, проходящие через AdGuard, даётся исчерпывающая информация по каждому из них, предлагаются различные опции сортировки и другие полезные возможности.

В зависимости от используемого вами продукта AdGuard, Журнал фильтрации может находиться в различных пунктах меню.

* В **AdGuard для Windows** вы найдёте его во вкладке настроек *Антибаннер* или через меню трея;
* В **AdGuard для Mac** он располагается в разделе *Настройки > Дополнительно > Журнал запросов*;
* В **AdGuard для Android** это отдельный пункт бокового меню. Также в журнал фильтрации отдельного приложения или сайта можно перейти из Помощника.
* В **Браузерном расширении AdGuard** он находится во вкладке настроек *Дополнительно*, а также доступен после правого клика по иконке расширения. Только браузеры на основе Chromium и Firefox отображают **правила скрытия элементов** (включая CSS, ExtCSS) и **JS-правила и скриптлеты** в своих Журналах фильтрации.

> В **AdGuard для iOS** и в **AdGuard для Safari** Журнал фильтрации отсутствует из-за особенностей реализации блокировщиков контента в Safari. AdGuard сам не видит веб-запросы и поэтому не может отображать их.

<a id="selectors-debugging-mode"></a>
### Режим отладки селекторов

Иногда у вас может возникнуть необходимость проверить быстродействие того или иного селектора или таблицы стилей. Чтобы сделать это без непосредственного взаимодействия с JavaScript, вы можете использовать свойство стиля `debug`. Когда `ExtendedCSS` встречает это свойство, он включает режим отладки для конкретного селектора или для всех селекторов, в зависимости от значения `debug`. Откройте консоль браузера, находясь на веб-странице, чтобы посмотреть статистику по времени, затраченному на применение селектора(-ов). Режим отладки покажет следующую статистику для каждого из отлаживаемых селекторов:

`array`: время, которое ушло на применение селектора на странице, для каждого из случаев применения этого селектора (в миллисекундах)
`length`: общее количество раз, когда на странице был применён данный селектор
`mean`: среднее время, ушедшее на применение данного селектора на странице
`stddev`: стандартное отклонение
`squaredSum`: сумма квадратичных отклонений от среднего значения
`sum`: общее время, ушедшее на все применения данного селектора на текущей странице


#### Примеры

**Отладка конкретного селектора**

Когда значение свойства `debug` равно `true`, информация только по этому селектору будет отображена в консоли браузера.

```
#$?#.banner { display: none; debug: true; }
```

**Включение глобальной отладки**

Когда значение свойства `debug` равно `global`, в консоли будет отображаться информация по всем ExtendedCSS-селекторам, которые были применены на данной странице, для всех ExtendedCSS-правил из любого из включённых фильтров.

```
#$?#.banner { display: none; debug: global; }
```

<a id="testing-extended-selectors"></a>
#### Тестирование расширенных селекторов без AdGuard

Если у вас не установлен AdGuard, вы всё равно можете тестировать расширенные селекторы, но для этого вам потребуется сначала загрузить в текущую страницу ExtendedCSS. Чтобы сделать это, скопируйте и запустите следующий код в консоли браузера:

```
!function(E,x,t,C,s,s_){C=E.createElement(x),s=E.getElementsByTagName(x)[0],C.src=t,
C.onload=function(){alert('ExtCss loaded successfully')},s.parentNode.insertBefore(C,s)}
(document,'script','https://AdguardTeam.github.io/ExtendedCss/extended-css.min.js')
```

В качестве альтернативы вы можете установить пользовательский скрипт "ExtendedCssDebugger": https://github.com/AdguardTeam/Userscripts/blob/master/extendedCssDebugger/extended-css.debugger.user.js

Теперь вы можете использовать конструктор `ExtendedCss` в глобальном масштабе, а его метод `ExtendedCss.query` как `document.querySelectorAll`.
```
var selectorText = "div.block[-ext-has='.header:matches-css-after(content: Anzeige)']";

ExtendedCss.query(selectorText) // returns an array of Elements matching selectorText
```

<a id="debug-scriptlets"></a>
### Отладка скриптлетов

Если вы используете Браузерное расширение AdGuard и хотите отладить [скриптлет-правило](#scriptlets), вы можете получить дополнительную информацию, просто открыв Журнал фильтрации. В этом случае, скриптлеты переключатся в режим отладки и будут писать больше информации в браузерную консоль.

Следующие скриптлеты разработаны специально для отладки:

* [`debug-current-inline-script`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#debug-current-inline-script)
* [`debug-on-property-read`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#debug-on-property-read)
* [`debug-on-property-write`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#abort-on-property-write)
* [`log-addEventListener`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#log-addEventListener)
* [`log-on-stack-trace`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#log-on-stack-trace)
* [`log-eval`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#log-eval)
* [`log`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#log)

Следующие скриптлеты тоже могут быть использованы для отладки:

* [`json-prune`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#json-prune)
* [`prevent-fetch`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#prevent-fetch)
* [`prevent-requestAnimationFrame`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#prevent-requestanimationframe)
* [`prevent-setInterval`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#prevent-setinterval)
* [`prevent-setTimeout`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#prevent-settimeout)
* [`prevent-window-open`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#prevent-window-open) со специальным параметром `replacement`
* [`prevent-xhr`](https://github.com/AdguardTeam/Scriptlets/blob/master/wiki/about-scriptlets.md#prevent-xhr)


<a id="good-luck"></a>
## Удачи в составлении своих фильтров!

Желаем вам удачи в составлении собственных фильтров.

Если вы хотите спросить совета о том, как правильно составлять фильтры, на нашем форуме есть [специальный раздел](https://forum.adguard.com/index.php?forums/25/), посвященный написанию собственных правил.
