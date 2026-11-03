> 📄 **Название файла:** `solutions_pair_07.md`  
> 📂 **Расположение (локальная папка преподавателя в `.gitignore`):** `teacher_notes/solutions_pair_07.md`

# 👨‍🏫 Методическая шпаргалка и эталонные решения — Пара 7 (Уроки 13–14)

**Привязка к учебному плану:** Модуль 1, Тема 2 «Основы Python: переменные, типы данных, ввод/вывод» ($3$-й и $4$-й часы практики из $5$).

---

## 1. Подводные камни, крайние случаи (edge cases) и железо

* **Как удержать динамику на двух практических уроках подряд:** Не отпускай группу в молчаливое плавание на все $80$ минут! В начале Урока 13 и Урока 14 проводи бодрый $10$-минутный инженерный брифинг у доски.
* **Ловушка отрицательного счета в Задании 1.1:** Без тумблера `(requests > 100)` при вводе `40` запросов формула `(40 - 100) * 3` выдаст `-180` рублей. Разбери с базовыми учениками оба способа отсечки минуса: булево умножение и `max(0, requests - 100)`.
* **Крайний случай (edge case) с ANSI-цветами на Windows 10 (Задание 2.3):** Без строчки `os.system("")` стандартное окно консоли Windows 10 не включает режим обработки виртуального терминала (Virtual Terminal Processing) и печатает сырые коды `\033[91m`. Подскажи «Боссу» запускать скрипт именно в PowerShell или CMD.

---

## 2. Брифинги для доски (по $10$ мин в начале Уроков 13 и 14)

### Брифинг Урока 13: Булев тумблер
Выпиши на доске цепочку:

$$(120 > 100) \longrightarrow \text{True} \longrightarrow 1 \qquad\qquad (45 > 100) \longrightarrow \text{False} \longrightarrow 0$$

И формулу защиты знаменателя от нуля: $v_{\text{safe}} = v + (v == 0) \times 1$.

### Брифинг Урока 14: Анатомия шкалы и бортики Clamping
Нарисуй шкалу из $20$ клеток и запиши формулу перевода занятых гигабайт в закрашенные клетки с защитой границ:

$$\text{blocks} = \max\left(0, \min\left(20, \text{int}\left(\frac{\text{used}}{\text{total}} \times 20\right)\right)\right)$$

---

## 3. Эталонные решения Урока 13 (все уровни сложности)

### Задание 1.1 — Уровень «База» (`cloud_billing.py`)

```python
# Решение уровня «База» (Пара 7, Урок 13 — Задание 1.1): cloud_billing.py
requests = int(input("Введите количество запросов к ИИ за день: "))

# Если запросов <= 100, тумблер is_over равен False (0) и обнуляет счет
is_over = requests > 100
paid_requests = is_over * (requests - 100)
total_rub = paid_requests * 3

print(f"Платных запросов сверх лимита: {paid_requests} шт.")
print(f"Итого к оплате: {total_rub} руб.")
```

### Задание 1.2 — Уровень «База» (`xeon_threads.py`)

```python
# Решение уровня «База» (Пара 7, Урок 13 — Задание 1.2): xeon_threads.py
tasks = int(input("Введите общее количество задач для расчета: "))
threads = 12

per_thread = tasks // threads
tail = tasks % threads

is_perfect = (tail == 0)
penalty_sec = (tail > 0) * 5

print(f"На каждый из 12 потоков: {per_thread} задач (хвост: {tail} шт.)")
print(f"Идеальный баланс без остатка: {is_perfect}")
print(f"Штрафное время на доработку хвоста: {penalty_sec} сек.")
```

### Задание 1.3 — Уровень «Босс» (`cascade_tariff_boss.py`)

```python
# Решение уровня «Босс» (Пара 7, Урок 13 — Задание 1.3): cascade_tariff_boss.py
raw_gb = int(input("Введите арендуемый объем облака (ГБ): "))
volume_gb = max(0, raw_gb)
base_cost = volume_gb * 10.0

# Каскадное сложение булевых условий: 10% от 100 ГБ и еще +15% от 500 ГБ
discount_rate = (volume_gb >= 100) * 0.10 + (volume_gb >= 500) * 0.15
discount_sum = base_cost * discount_rate
final_cost = base_cost - discount_sum

print(f"Расчетный объем: {volume_gb} ГБ | Базовая стоимость: {base_cost:.2f} руб.")
print(f"Применена скидка ({discount_rate * 100:.0f}%): -{discount_sum:.2f} руб.")
print(f"Итого к оплате: {final_cost:.2f} руб.")
```

---

## 4. Эталонные решения Урока 14 (все уровни сложности)

### Задание 2.1 — Уровень «База» (`pc_dashboard.py`)

```python
# Решение уровня «База» (Пара 7, Урок 14 — Задание 2.1): pc_dashboard.py
ram_used = float(input("Занято RAM (из 16 ГБ): ").replace(",", "."))
vram_used = float(input("Занято VRAM (из 8 ГБ): ").replace(",", "."))

ram_blocks = max(0, min(20, int((ram_used / 16.0) * 20)))
ram_bar = "█" * ram_blocks + "░" * (20 - ram_blocks)

vram_blocks = max(0, min(20, int((vram_used / 8.0) * 20)))
vram_bar = "█" * vram_blocks + "░" * (20 - vram_blocks)

print("\n--- МОНИТОР ПАМЯТИ РАБОЧЕЙ СТАНЦИИ ---")
print(f"RAM  (16 ГБ): [{ram_bar}] {ram_blocks * 5}%")
print(f"VRAM ( 8 ГБ): [{vram_bar}] {vram_blocks * 5}%")
```

### Задание 2.2 — Уровень «База» (`model_benchmark.py`)

```python
# Решение уровня «База» (Пара 7, Урок 14 — Задание 2.2): model_benchmark.py
name_1 = input("Название первой модели: ")
acc_1 = float(input(f"Точность {name_1} (0-100%): ").replace(",", "."))

name_2 = input("Название второй модели: ")
acc_2 = float(input(f"Точность {name_2} (0-100%): ").replace(",", "."))

b1 = max(0, min(20, int(acc_1 / 5)))
b2 = max(0, min(20, int(acc_2 / 5)))

bar_1 = "█" * b1 + "░" * (20 - b1)
bar_2 = "█" * b2 + "░" * (20 - b2)
gap = abs(acc_1 - acc_2)

print("\n--- СРАВНИТЕЛЬНЫЙ БЕНЧМАРК ---")
print(f"{name_1}:\t[{bar_1}] {acc_1:.1f}%")
print(f"{name_2}:\t[{bar_2}] {acc_2:.1f}%")
print(f"Разрыв в точности по модулю: {gap:.1f}%")
```

### Задание 2.3 — Уровень «Босс» (`ansi_telemetry_boss.py`)

```python
# Решение уровня «Босс» (Пара 7, Урок 14 — Задание 2.3): ansi_telemetry_boss.py
import os

# Включаем обработку ANSI-последовательностей в консоли Windows 10
os.system("")

GREEN = "\033[92m"
YELLOW = "\033[93m"
RED = "\033[91m"
RESET = "\033[0m"


def render_sensor_line(label: str, raw_pct: float) -> str:
    """Формирует цветную шкалу датчика с тремя зонами нагрузки без использования if."""
    pct = max(0.0, min(100.0, raw_pct))
    blocks = round(pct / 5.0)
    bar = "█" * blocks + "░" * (20 - blocks)

    # Взаимоисключающие булевы маски трех диапазонов
    is_norm = pct < 60.0
    is_warn = (pct >= 60.0) and (pct < 85.0)
    is_crit = pct >= 85.0

    color = is_norm * GREEN + is_warn * YELLOW + is_crit * RED
    tag = is_norm * "НОРМА" + is_warn * "НАГРУЗКА" + is_crit * "ПЕРЕГРЕВ!"

    return f"{label:<12} {color}[{bar}] {pct:5.1f}% ({tag}){RESET}"


if __name__ == "__main__":
    cpu = float(input("Загрузка CPU Xeon (0-100%): ").replace(",", "."))
    ram = float(input("Загрузка RAM (0-100%): ").replace(",", "."))
    gpu = float(input("Загрузка GPU RX 570 (0-100%): ").replace(",", "."))

    print("\n=== ЦВЕТНАЯ ПАНЕЛЬ ТЕЛЕМЕТРИИ СЕРВЕРА ===")
    print(render_sensor_line("CPU Xeon:", cpu))
    print(render_sensor_line("RAM 16GB:", ram))
    print(render_sensor_line("GPU RX570:", gpu))
```