# hshsj
jajsja
import tkinter as tk
from tkinter import messagebox
import random
import json
import os

QUOTES_FILE = "quotes.json"
HISTORY_FILE = "history.json"

# Загрузка цитат
def load_quotes():
    if not os.path.exists(QUOTES_FILE):
        return []
    with open(QUOTES_FILE, "r", encoding="utf-8") as f:
        return json.load(f)

# Загрузка истории
def load_history():
    if not os.path.exists(HISTORY_FILE):
        return []
    with open(HISTORY_FILE, "r", encoding="utf-8") as f:
        return json.load(f)

# Сохранение истории
def save_history():
    with open(HISTORY_FILE, "w", encoding="utf-8") as f:
        json.dump(history, f, ensure_ascii=False, indent=4)

quotes = load_quotes()
history = load_history()

# Генерация цитаты
def generate_quote():
    author_filter = author_entry.get().strip()
    theme_filter = theme_entry.get().strip()

    filtered = quotes

    if author_filter:
        filtered = [q for q in filtered if author_filter.lower() in q["author"].lower()]

    if theme_filter:
        filtered = [q for q in filtered if theme_filter.lower() in q["theme"].lower()]

    if not filtered:
        messagebox.showwarning("Нет данных", "Цитаты не найдены")
        return

    quote = random.choice(filtered)
    text = f'{quote["text"]}\n— {quote["author"]} ({quote["theme"]})'

    quote_label.config(text=text)

    history.append(quote)
    history_listbox.insert(tk.END, text)
    save_history()

# Добавление новой цитаты
def add_quote():
    text = text_entry.get().strip()
    author = author_add_entry.get().strip()
    theme = theme_add_entry.get().strip()

    if not text or not author or not theme:
        messagebox.showerror("Ошибка", "Все поля должны быть заполнены!")
        return

    new_quote = {
        "text": text,
        "author": author,
        "theme": theme
    }

    quotes.append(new_quote)

    with open(QUOTES_FILE, "w", encoding="utf-8") as f:
        json.dump(quotes, f, ensure_ascii=False, indent=4)

    messagebox.showinfo("Успех", "Цитата добавлена!")

# GUI
root = tk.Tk()
root.title("Random Quote Generator")

# Отображение цитаты
quote_label = tk.Label(root, text="Нажмите кнопку для генерации цитаты", wraplength=400)
quote_label.pack(pady=10)

# Фильтры
tk.Label(root, text="Фильтр по автору:").pack()
author_entry = tk.Entry(root)
author_entry.pack()

tk.Label(root, text="Фильтр по теме:").pack()
theme_entry = tk.Entry(root)
theme_entry.pack()

# Кнопка генерации
generate_btn = tk.Button(root, text="Сгенерировать цитату", command=generate_quote)
generate_btn.pack(pady=10)

# История
tk.Label(root, text="История:").pack()
history_listbox = tk.Listbox(root, width=60, height=10)
history_listbox.pack()

# Добавление цитаты
tk.Label(root, text="Добавить новую цитату").pack()

text_entry = tk.Entry(root)
text_entry.pack()
text_entry.insert(0, "Текст")

author_add_entry = tk.Entry(root)
author_add_entry.pack()
author_add_entry.insert(0, "Автор")

theme_add_entry = tk.Entry(root)
theme_add_entry.pack()
theme_add_entry.insert(0, "Тема")

add_btn = tk.Button(root, text="Добавить", command=add_quote)
add_btn.pack(pady=5)

# Загрузка истории в GUI
for q in history:
    history_listbox.insert(tk.END, f'{q["text"]} — {q["author"]}')

root.mainloop()
