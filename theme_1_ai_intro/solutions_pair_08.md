> 📄 **Название файла:** `solutions_pair_08.md`  
> 📂 **Расположение (локальная папка преподавателя в `.gitignore`):** `teacher_notes/solutions_pair_08.md`

# 👨‍🏫 Методическая шпаргалка и ключи контроля — Пара 8 (Уроки 15–16)

**Привязка к учебному плану:** Модуль 1, Тема 2 «Основы Python: переменные, типы данных, ввод/вывод» ($5$-й финальный час практики $+$ $0.5$ ч самостоятельной работы $+$ $0.5$ ч контроля).

---

## 1. Подводные камни, крайние случаи (edge cases) и тайминг

* **Контроль времени на Уроке 16:** Строго раздели второй сорокаминутный урок пополам: $15$ минут ребята самостоятельно чинят `bug_hunt_fixed.py`, $5$ минут вы быстро сверяете все $6$ багов устно (чтобы никто не повторил их в контрольной!), затем $5$ минут на письменный Блок А (при выключенном интерпретаторе) и $15$ минут на кодинг Блока Б.
* **Частая путаница Мегабайтов и Мегабитов в контрольной:** В условии задачи мы специально дали готовую формулу $\text{ГБ} \times 1024 \times 8$, чтобы проверять знание Python, а не заучивание таблиц информатики. Продублируй эту формулу крупно на доске!

---

## 2. Эталонные решения Мини-проекта Урока 15 (`startup_calc.py`)

### Решение Мини-проекта — Уровень «База»

```python
# Решение уровня «База» (Пара 8, Урок 15): startup_calc.py
# 1. Сбор сырья
project = input("Введите название стартапа: ")
samples = int(input("Количество примеров в датасете: "))
ms_per_sample = int(input("Время обработки 1 примера (мс): "))
budget = float(input("Выделенный бюджет (руб.): ").replace(",", "."))
hour_price = float(input("Цена аренды сервера в час (руб.): ").replace(",", "."))

# 2. Математическое ядро
total_sec = (samples * ms_per_sample) // 1000
hours = total_sec // 3600
minutes = (total_sec % 3600) // 60
seconds = total_sec % 60

# Считаем стоимость с запасом +1 час на неполное время
total_cost = (hours + 1) * hour_price
is_enough = budget >= total_cost

blocks = max(0, min(10, int((total_cost / budget) * 10)))
bar = "█" * blocks + "░" * (10 - blocks)

# 3. Чистый отчет
print("\n==========================================")
print(f"ПАСПОРТ СТАРТАПА: {project}")
print("==========================================")
print(f"Точное время обучения:\t{hours:02d}:{minutes:02d}:{seconds:02d}")
print(f"Стоимость аренды:\t{total_cost:.2f} руб.")
print(f"Нагрузка на бюджет:\t[{bar}]")
print(f"Хватит ли бюджета:\t{is_enough}")
print("==========================================")
```

### Решение Мини-проекта — Уровень «Босс»

```python
# Решение уровня «Босс» (Пара 8, Урок 15): startup_calc.py
project = input("Введите название стартапа: ")
samples = int(input("Количество примеров в датасете: "))
ms_per_sample = int(input("Время обработки 1 примера (мс): "))
budget = float(input("Выделенный бюджет (руб.): ").replace(",", "."))
hour_price = float(input("Цена аренды сервера в час (руб.): ").replace(",", "."))

# 1. Расчет времени и точное округление оплачиваемых часов вверх (ceiling) без if
total_sec = (samples * ms_per_sample) // 1000
hours = total_sec // 3600
minutes = (total_sec % 3600) // 60
seconds = total_sec % 60

billable_hours = hours + ((minutes + seconds) > 0) * 1
total_cost = billable_hours * hour_price
is_enough = budget >= total_cost

# 2. Защита бюджета от 0 и построение 10-блочной шкалы
safe_budget = budget + (budget == 0) * 1.0
blocks = max(0, min(10, int((total_cost / safe_budget) * 10)))
bar = "█" * blocks + "░" * (10 - blocks)

# 3. Адаптивная двойная ASCII-рамка
header = f"ПАСПОРТ СТАРТАПА: {project}"
inner_w = max(42, len(header) + 4)

rows = [
    f"Точный тайминг:     {hours:02d}:{minutes:02d}:{seconds:02d}",
    f"Часов к оплате:     {billable_hours} ч.",
    f"Итоговая сумма:     {total_cost:.2f} руб.",
    f"Нагрузка бюджета:   [{bar}]",
    f"Статус одобрения:   {is_enough}",
]

print("\n╔" + "═" * inner_w + "╗")
print("║  " + header + " " * (inner_w - len(header) - 2) + "║")
print("╠" + "═" * inner_w + "╣")
for line in rows:
    print("║  " + line + " " * (inner_w - len(line) - 2) + "║")
print("╚" + "═" * inner_w + "╝")
```

---

## 3. Эталонное решение разминки «Сапер кода» (`bug_hunt_fixed.py`)

```python
# Все 6 багов исправлены (Урок 16 — Самостоятельная работа):
dataset_name = input("Название датасета: ")
files_count = int(input("Количество файлов (например, 250): "))  # Баг 1: добавлен int()
file_size_mb = float("2.5")                                      # Баг 2: float() вместо int()

total_mb = files_count * file_size_mb
full_drives = int(total_mb // 100)                               # Баг 3: оператор // вместо /
remaining_mb = total_mb % 100                                    # Баг 4: оператор % для остатка

has_no_remainder = (remaining_mb == 0)                           # Баг 5: честное сравнение с 0
# Баг 6: исправлена склейка типов с помощью f-строки
print(f"Датасет {dataset_name} -> Полных флешек: {full_drives} | Остаток: {remaining_mb:.1f} МБ | Без остатка: {has_no_remainder}")
```

---

## 4. Ключи и эталонные решения Контрольной работы по Теме 2 ($10$ баллов)

### Ключи к Блоку А ($4$ балла — по $1$ баллу за каждый точный ответ):
1. `553` *(повторение строки `"5" * 2` дает `"55"`, плюс `"3"` от `15 // 4`)*.
2. `<class 'float'> 4` *(истинное деление `/` всегда дает `float`, а `int(4.99)` отсекает хвост до `4`)*.
3. `2` *(непустая строка `bool("0")` равна `True`, то есть `1`, плюс остаток `10 % 3 == 1`)*.
4. `2:5` *(целые минуты `2` и остаток секунд `5`, склеенные через `sep=":"`)*.

### Решение Блока Б — Уровень «База» (`weights_downloader.py`, $6$ баллов)

```python
# Контрольная работа по Теме 2 — Решение уровня «База»: weights_downloader.py
model = input("Название модели: ")
size_gb = float(input("Объем модели (ГБ): ").replace(",", "."))
speed_mbps = int(input("Скорость интернета (Мбит/с): "))

# Переводим гигабайты в мегабиты (1 ГБ = 1024 МБ, 1 МБ = 8 Мбит)
size_mbits = size_gb * 1024 * 8

total_sec = int(size_mbits // speed_mbps)
minutes = total_sec // 60
seconds = total_sec % 60

print(f'Модель "{model}" (объем {size_mbits:.2f} Мбит) скачается за {minutes} мин. {seconds} сек.')
```

### Решение Блока Б — Уровень «Босс» (`weights_downloader.py`, $6$ баллов)

```python
# Контрольная работа по Теме 2 — Решение уровня «Босс»: weights_downloader.py
model = input("Название модели: ")
size_gb = float(input("Объем модели (ГБ): ").replace(",", "."))
speed_mbps = int(input("Скорость интернета (Мбит/с): "))

size_mbits = size_gb * 1024 * 8
safe_speed = speed_mbps + (speed_mbps == 0) * 1

total_sec = int(size_mbits // safe_speed)
hours = total_sec // 3600
minutes = (total_sec % 3600) // 60
seconds = total_sec % 60

speed_blocks = max(0, min(10, int((speed_mbps / 1000) * 10)))
speed_bar = "█" * speed_blocks + "░" * (10 - speed_blocks)

print(f'Модель "{model}" (объем {size_mbits:.2f} Мбит) скачается за {hours:02d}:{minutes:02d}:{seconds:02d}.')
print(f"Канал 1 Гбит/с: [{speed_bar}] ({speed_mbps} Мбит/с)")
```

### Разбалловка Блока Б для быстрой проверки на уроке:
* **$2$ балла:** Корректный ввод и приведение типов `float` / `int` без падения на дробном объеме.
* **$2$ балла:** Правильный перевод в Мегабиты и применение `//` и `%` для времени.
* **$2$ балла:** Точное попадание в шаблон вывода с округлением `:.2f` в f-строке (и шкала с защитой от `0` для уровня «Босс»).