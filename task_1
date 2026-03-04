import tkinter as tk
from tkinter import ttk, messagebox
import requests
from datetime import datetime


websites = [
    "https://github.com/",
    "https://www.binance.com/en",
    "https://tomtit.tomsk.ru/",
    "https://jsonplaceholder.typicode.com/",
    "https://moodle.tomtit-tomsk.ru/"
]

CURRENCY_URL = "https://www.cbr-xml-daily.ru/daily_json.js"


def get_currency_data():
    try:
        r = requests.get(CURRENCY_URL, timeout=10)
        r.raise_for_status()
        data = r.json()
        return data.get("Valute", {})
    except Exception as e:
        print("Ошибка загрузки курсов:", e)
        return {}

def update_currency_tree(tree):
    for item in tree.get_children():
        tree.delete(item)

    valute = get_currency_data()
    if not valute:
        messagebox.showwarning("Внимание", "Не удалось загрузить курсы валют")
        return

    popular = ["USD", "EUR", "CNY", "TRY", "KZT", "BYN", "UAH", "GBP", "JPY", "CHF"]

    for code in popular:
        if code in valute:
            item = valute[code]
            tree.insert("", "end", values=(
                code,
                item.get("Name", "—"),
                f"{item.get('Value', 0):.4f} ₽"
            ))

    for code in sorted(valute.keys()):
        if code not in popular:
            item = valute[code]
            tree.insert("", "end", values=(
                code,
                item.get("Name", "—"),
                f"{item.get('Value', 0):.4f} ₽"
            ))

def check_websites(text_widget):
    text_widget.delete(1.0, tk.END)
    text_widget.insert(tk.END, "Проверка запущена...\n\n")
    text_widget.update()

    for url in websites:
        try:
            r = requests.get(url, timeout=10)
            code = r.status_code

            if code == 200:
                status = "доступен"
                tag = "green"
            elif code == 403:
                status = "вход запрещён"
                tag = "orange"
            elif code == 404:
                status = "не найден"
                tag = "red"
            elif code >= 500:
                status = "ошибка сервера"
                tag = "red"
            else:
                status = f"код {code}"
                tag = "gray"

            line = f"{url:<50} → {status} ({code})\n"
            text_widget.insert(tk.END, line)
            text_widget.tag_add(tag, "end-1l linestart", "end")

        except requests.Timeout:
            line = f"{url:<50} → тайм-аут (нет ответа)\n"
            text_widget.insert(tk.END, line)
            text_widget.tag_add("red", "end-1l linestart", "end")
        except Exception:
            line = f"{url:<50} → не удалось подключиться\n"
            text_widget.insert(tk.END, line)
            text_widget.tag_add("red", "end-1l linestart", "end")

        text_widget.see(tk.END)
        text_widget.update()

    text_widget.insert(tk.END, "\nПроверка завершена\n")


root = tk.Tk()
root.title("Задания практической")
root.geometry("960x540")
root.resizable(False, False)

text_tags = {
    "green":  {"foreground": "darkgreen"},
    "orange": {"foreground": "darkorange"},
    "red":    {"foreground": "red"},
    "gray":   {"foreground": "gray50"}
}

notebook = ttk.Notebook(root)
notebook.pack(fill="both", expand=True, padx=10, pady=10)

style = ttk.Style()
style.configure("TNotebook.Tab", padding=[10, 5], font=('Helvetica', 11))

tab1 = ttk.Frame(notebook)
notebook.add(tab1, text=" Проверка сайтов ")

ttk.Label(tab1, text="Проверка доступности выбранных сайтов", font=('Helvetica', 12)).pack(pady=12)

text_area = tk.Text(tab1, height=22, width=90, font=('Consolas', 10))
text_area.pack(padx=10, pady=5, fill="both", expand=True)

for tag, config in text_tags.items():
    text_area.tag_configure(tag, **config)

ttk.Button(
    tab1,
    text="Запустить проверку",
    command=lambda: check_websites(text_area),
    width=25
).pack(pady=12)

tab2 = ttk.Frame(notebook)
notebook.add(tab2, text=" Курсы ЦБ РФ ")

ttk.Label(tab2, text="Курсы валют Банка России", font=('Helvetica', 12)).pack(pady=12)

frame_tree = ttk.Frame(tab2)
frame_tree.pack(fill="both", expand=True, padx=10, pady=5)

tree = ttk.Treeview(frame_tree, columns=("code", "name", "rate"), show="headings", height=18)
tree.heading("code", text="Код")
tree.heading("name", text="Валюта")
tree.heading("rate", text="Курс ₽")
tree.column("code", width=80, anchor="center")
tree.column("name", width=380, anchor="w")
tree.column("rate", width=160, anchor="e")

scrollbar = ttk.Scrollbar(frame_tree, orient="vertical")
scrollbar.pack(side="right", fill="y")

tree.configure(yscrollcommand=scrollbar.set)
scrollbar.configure(command=tree.yview)

tree.pack(side="left", fill="both", expand=True)

ttk.Button(
    tab2,
    text="Обновить курсы",
    command=lambda: update_currency_tree(tree),
    width=25
).pack(pady=12)

update_currency_tree(tree)

if __name__ == "__main__":
    root.mainloop()
