#php #extensions #internationalization
Расширение **intl** (Internationalization) в PHP — это мощный инструмент, который предоставляет доступ ко многим возможностям для работы с международными форматами данных. Оно основано на библиотеке ICU (International Components for Unicode) и позволяет работать с различными аспектами локализации: числовые форматы, валюты, даты, время и даже преобразование текста.

---

#### Что можно делать с помощью расширения intl?

1. **Пропись чисел**
   Используется класс `NumberFormatter` с форматом `NumberFormatter::SPELLOUT`, который может преобразовывать числа в слова на нужном языке.

```php
$number = 12345;
$formatter = new NumberFormatter('ru_RU', NumberFormatter::SPELLOUT);
echo $formatter->format($number);
// вывод: двенадцать тысяч триста сорок пять
```

`NumberFormatter` может также обрабатывать форматирование чисел в валюту, процентные значения и прочее.

2. **Форматирование валют**
   Подходит для вывода чисел в формате валюты с учетом локали.

```php
$formatter = new NumberFormatter('ru_RU', NumberFormatter::CURRENCY);
echo $formatter->formatCurrency(12345.67, 'RUB');
// вывод: 12 345,67 руб.
```

3. **Работа с датами и временем**
   Используя `IntlDateFormatter`, можно легко форматировать даты в соответствии с локалями.

```php
$formatter = new IntlDateFormatter('ru_RU', IntlDateFormatter::LONG, IntlDateFormatter::SHORT);
echo $formatter->format(new DateTime());
// пример вывода: 20 октября 2023 г., 12:34
```

4. **Транслитерация текста**
   Класс `Transliterator` помогает преобразовывать текст, например, из кириллицы в латиницу.

```php
$transliterator = Transliterator::create('Russian-Latin/BGN');
echo $transliterator->transliterate('Привет, мир!');
// вывод: Privet, mir!
```

5. **Работа с разными календарями**
   Поддерживаются нестандартные календари (например, исламский, еврейский).

```php
$formatter = new IntlDateFormatter('ar_EG@calendar=islamic', IntlDateFormatter::FULL, IntlDateFormatter::FULL);
echo $formatter->format(new DateTime());
// пример вывода на арабском языке
```

6. **Сортировка текста с учетом локали**
   С помощью класса `Collator` можно проводить сортировку строк, учитывая их локализацию.

```php
$collator = new Collator('ru_RU');
$array = ['яблоко', 'груша', 'апельсин'];
$collator->sort($array);
print_r($array);
// результат: ['апельсин', 'груша', 'яблоко']
```

7. **Проверка и обработка локализованных данных**
   Можно работать с международными телефонными кодами, индексами и прочими данными.

#### Почему intl полезен?

Вместо написания собственных функций и "велосипедов", `intl` предоставляет готовые решения, которые:
- Работают с учетом локалей.
- Соответствуют международным стандартам.
- Просты в использовании.

---

#### Установка расширения Intl

Если у вас не установлено это расширение, его можно добавить через менеджер пакетов:

***В Linux***
```shell script
sudo apt-get install php-intl
```

***В Windows***
Для Windows оно обычно поставляется со сборкой PHP, нужно лишь активировать в файле `php.ini`:
```ini
;extension=intl
```
После чего раскомментировать строку и перезапустить сервер.