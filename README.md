# Form reader and evaluator 
4 семестр, 1 проект по Питону в МФТИ

## Установка
По мимо зависимостей в `requirements.txt`, дополнительно нужно установить `tensorflow`. Его установка зависит от платформы, [см. официальный сайт](https://www.tensorflow.org/install).

## Руководство пользователя

Всё взаимодействие с программой разбивается на пять этапов:
- `create` &mdash; создаёт-шаблон для заполнения информации о тесте
- `generate` &mdash; генерирует бланки по описанию заданий и кодов
- `restore` &mdash; восстанавливает бланки, подготавливает их к распознаванию
- `recognize` &mdash; распознаёт ответы для каждого кода участника
- `grade` &mdash; оценивает ответы по критериям
  

### Команда `create`
Для создания комплекта, например, в корневой папке 
```
py main.py -m create -s <set_name>
```
При запуске из другой директории нужно указывать путь до `main.py`.

При запуске будет создана директория `sets/<set_name>/`. Все необходимые файлы нужно либо класть в эту директорию, либо они будут появляться в этой директории.

При создании комплекта в директории будет лежать частично заполненный файл `description.json` для демонстрации и удобства дальнейшего заполнения.

#### Формат `description.json`
```json
{
    "Codes": {
        "<префикс>": <кол-во кодов>,
        ...
    },
   "Sections": [
        {
            "Questions": [
                {
                    "ans": "ABCDE",
                    "type": <"SORT" или "MATCH">
                },
                ...
            ]
        },
        ...
    ]
}
```
- `"Codes"` &mdash; хранит для каждого префикса количество кодов. Коды генерируются в формата `<префикс><номер>`, начиная с номера `00` под нужное количество.
- `"Sections"` &mdash; хранит массив секций, в каждой секции массив задачи
  - `"Questions"` &mdash; хранит массив задач, в каждой задаче два поля: 
    - `"ans"` &mdash; ответ, обязательно заглавные латинские буквы. Перед оцениваем, при необходимости, можно поменять ответ (главное, не изменять его длину)
    - `"type"` &mdash; тип задачи, пока поддерживается только два: 
      - `SORT` &mdash; расставить в правильном порядке
      - `MATCH` &mdash; поставить в каждую позицию нужную букву
  
### Команда `generate`
Когда файл `description.json` заполнен, можно запускать команду
```
py main.py -m generate -s <set_name>
```

После запуска появится файл `blanks.pdf` со всеми бланками и технический файл `generator_data.json` с нужной для распознавания информацией.

### Команда `restore`
Для запуска нужно положить в директорию комплекта файл со сканами под названием `scans.pdf` и написать в консоли
```
py main.py -m restore -s <set_name>
```
После этого все сканы будут разобраны по кодам и сохранены в отдельную папку. Про все файлы, при восстановлении которых возникла ошибка, будет выведена информация.

### Команда `recognize`
После восстановления всех сканов для распознавания нужно выполнить команду
```
py main.py -m recognize -s <set_name>
```

После запуска в директории появится табличка с ответами `recognized.csv` и файл с технической информацией о распознавании `recognized.json`.

### Команда `grade`
При желании на этом этапе можно изменить содержимое `recognized.csv` или вообще пропустить все этапы до этого и просто предоставить заполненный файл такого же формата :)

Настройки оценщика можно менять в `set_manager.py`.

Для запуска оценщика 
```
py main.py -m grade -s <set_name>
```
После запуска в директории появится файл `results.csv` с баллами по каждой задаче.

<!-- [Пример.](https://github.com/NoblFriend/python_proj_1/blob/master/demo/exmaple_blank.png) -->

<!-- Для каждого тестирования создается отдельная папка где будет храниться вся нужная информация (описание заданий, сканы).
Программа запускаеться из корня этой папки. Здесь в планах располагать критерии и описание заданий сразу на бланках, чтобы развести генерацию бланков и проверку и не быть зависимым от потери данных.

## Структура проекта -->

## Тестирование `grade`
Используется `coverage` ([см. документацию](https://coverage.readthedocs.io/en/7.2.5/source.html))

Для запуска тестов из директории `app` нужно ввести команду 
```shell
coverage run -m unittest 
```
Для просмотра информации о покрытии в консоли 
```shell
coverage report
```
Для интерактивного отчёта (покрытие, какие строки не были покрыты и т.д.) 
```shell
coverage hmtl
open htmlcov/index.html
```
При создании отчёта для его хранения `coverage` создаёт файл, для удаления которого можно использовать 
```
coverage erase
```
