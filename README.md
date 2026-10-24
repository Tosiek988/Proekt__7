# Здесь нет интерфейса: только хранение привычек,
# отметки «сделано сегодня» и подсчёт серии дней.
#
# Привычка — это словарь:
#   {"name": "Зарядка", "days": ["2026-10-24", "2026-10-25"]}
# где days — даты, когда привычка была выполнена.
import json
from datetime import date, timedelta
from pathlib import Path
FILE = Path(__file__).with_name("habits.json")  # файл лежит рядом с программой
def load_habits():
# Читает привычки из файла. Если файла ещё нет — возвращает пустой список
    try:
        with open(FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError:
        return []
def save_habits(habits):
# Записывает привычки в файл.
    with open(FILE, "w", encoding="utf-8") as f:
        json.dump(habits, f, ensure_ascii=False, indent=2)
def add_habit(name):
# Добавляет привычку. Возвращает False, если такая уже есть.
    habits = load_habits()
    for habit in habits:
        if habit["name"].lower() == name.lower():
            return False
    habits.append({"name": name, "days": []})
    save_habits(habits)
    return True
def mark_today(index):
# Отмечает привычку выполненной сегодня. index — номер с нуля.
    habits = load_habits()
    today = date.today().isoformat()  # например "2026-10-25"
    if today not in habits[index]["days"]:
        habits[index]["days"].append(today)
    save_habits(habits)
def done_today(habit):
# Выполнена ли привычка сегодня.
    return date.today().isoformat() in habit["days"]
def streak(habit):
# Сколько дней подряд привычка выполнялась, считая с сегодня.
    days = set(habit["days"])
    count = 0
    day = date.today()
    while day.isoformat() in days:
        count += 1
        day -= timedelta(days=1)  # шагаем на день назад
    return count
# ---------- проверка: запустите файл, чтобы увидеть результат ----------
if __name__ == "__main__":
    if not load_habits():
        add_habit("Зарядка")
        add_habit("Чтение 20 минут")
    mark_today(0)  # отмечаем первую привычку на сегодня
    for number, habit in enumerate(load_habits(), start=1):
        status = "сделано" if done_today(habit) else "не сделано"
        print(number, "|", habit["name"], "| сегодня:", status, "| серия:", streak(habit), "дн.")
