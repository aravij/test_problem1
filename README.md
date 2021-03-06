# Тестовое задание

## Решение

 1. Переберём все пароли вида [a-zA-Z0-9]{3}.
 2. От каждого такого пароля вычислим MD5 хэш.
 3. Полученные 16 байт будем использовать для расшифровки зашифрованного текста с помощью алгоритма 3DES(EDE2/DED2) в режиме CBC. Для режима сцепки блоков (CBC) используется первые 8 байт в файле в качестве изначального значения (initial value). Алгоритм расшифровки на первой и третей фазе использует одинаковые ключи (DED**2**).
 4. От расшифрованного сообщения вычисляется SHA256 и сравнивается с последними 32 байтами файла.
 5. Если сравнение прошло успешно - выводим пароль на печать.

В следствии коллизий хеш функции SHA256 может быть выведено несколько паролей. Единственный, в этом случае, способ подбора единственно правильного пароля - это проанализировать расшифрованный сообщения в ручном режиме (опция -p).

## Код
Решение задачи имплементировано в виде программы на C++14. Сборка проекта осуществляется с помощью утилиты CMake и может быть осуществлена следующим образом в терминале под Линуксом:

```shell
mkdir build
cd build
cmake ..
make
```
## Запуск программы
Исполняемый файл `test_problem`  после сборки находиться в папке `build/src` и поддерживает следующие ключи:

`test_problem [-h|--help] | [-p|--print-decrypted] CIPHERFILE`

Здесь CIPHERFILE - файл для расшифровки, а ключ `[-p|--print-decrypted]` позволяет просмотреть так же расшифрованный текст сообщения. Ключ `[-h|--help]` - стандартный ключ для справки по программе.

## Зависимости

Программа использует библиотеки Boost (filesystem, program_options, range, iterator, unit_testing_framework ...) и LibGCrypt. Так же при поддержке компилятором стандарта OpenMP, в программе автоматически будет включена поддержка многопоточности.

## Структура проекта
  * **cmake** - папка с дополнительными скриптами и модулями CMake.
    * **Modules** - модули для поиска пакетов в системе (LibGCrypt) и дополнительные функции.
    * **Scripts** - скрипты для запуска в виде `cmake - P`.
  * **data** - файлы для расшифровки.
  * **src** - код программы.
    * **cryptography** - обёртка над библиотекой LibGCryp.
    * **io** - чтение и парсинг файла.
    * **util** - различные вспомогательные конструкции, например декартова степень диапазона позволяет перебрать все сочетания, определённой длинны, с повторениями из некоторого диапазона.
  * **test** - тесты программы.
    * **unit** - модульное тестирование.
    * **functional** - функциональное тестирование.

## Тестирование
В папке test представлены некоторые тесты программы: модульное тестирование и функциональное.

  * Модульное тестирование охватывает чтение и парсинг файла (`io/parse_file.cpp`) и декартову степень диапазона (`util/cartesian_power_range.cpp`). Написано при помощи Boost.Test.
  * Функциональное тестирование написано на чистом CMake и проверяет, что при запуске на файле test.bin программа выводит только строчку abc и не выдаёт никаких ошибок.

## Многопоточность
Если компилятор поддерживает стандарт OpenMP, то в программе будет включена многопоточность. При расшифровке  файла `target.bin` время исполнения падает с 16 секунд до 8 на 4ёх ядрах.
