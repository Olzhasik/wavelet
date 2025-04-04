import stegano
import os
from tkinter import *
from tkinter import filedialog, messagebox
from tkinter.scrolledtext import ScrolledText

ZERO_WIDTH_SPACE = '\u200B'
ZERO_WIDTH_JOINER = '\u200D'

def binary_to_zwc(binary_str):
    return ''.join(ZERO_WIDTH_SPACE if b == '0' else ZERO_WIDTH_JOINER for b in binary_str)

def zwc_to_binary(zwc_str):
    return ''.join('0' if c == ZERO_WIDTH_SPACE else '1' for c in zwc_str)

def embed_watermark(text, watermark):
    binary = ''.join(format(ord(c), '08b') for c in watermark)
    zwc = binary_to_zwc(binary)
    return text + zwc

def extract_watermark(text):
    zwc_part = ''.join(c for c in text if c in (ZERO_WIDTH_SPACE, ZERO_WIDTH_JOINER))
    binary = zwc_to_binary(zwc_part)
    chars = [chr(int(binary[i:i+8], 2)) for i in range(0, len(binary), 8)]
    return ''.join(chars)

def open_file(entry):
    filepath = filedialog.askopenfilename(filetypes=[("Text files", "*.txt")])
    if filepath:
        entry.delete(0, END)
        entry.insert(0, filepath)

def save_file(entry):
    filepath = filedialog.asksaveasfilename(defaultextension=".txt",
                                             filetypes=[("Text files", "*.txt")])
    if filepath:
        entry.delete(0, END)
        entry.insert(0, filepath)

def embed():
    try:
        with open(input_file.get(), 'r', encoding='utf-8') as f:
            content = f.read()
        watermarked = embed_watermark(content, watermark_text.get())
        with open(output_file.get(), 'w', encoding='utf-8') as f:
            f.write(watermarked)
        messagebox.showinfo("Сәтті!", "Су таңба енгізілді.")
    except Exception as e:
        messagebox.showerror("Қате", str(e))

def extract():
    try:
        with open(input_file.get(), 'r', encoding='utf-8') as f:
            content = f.read()
        wm = extract_watermark(content)
        messagebox.showinfo("Су таңба табылды", wm if wm else "Табылған су таңба жоқ.")
    except Exception as e:
        messagebox.showerror("Қате", str(e))

def compare():
    try:
        with open(input_file.get(), 'r', encoding='utf-8') as f1, \
             open(output_file.get(), 'r', encoding='utf-8') as f2:
            text1 = f1.read()
            text2 = f2.read()
        text1_clean = ''.join(c for c in text1 if c not in (ZERO_WIDTH_SPACE, ZERO_WIDTH_JOINER))
        differences.delete(1.0, END)
        for i, (a, b) in enumerate(zip(text1_clean, text2)):
            if a != b:
                differences.insert(END, f"→ {i}: {a} ≠ {b}\n")
        if len(text1_clean) != len(text2):
            differences.insert(END, f"→ Ұзындығы әртүрлі: {len(text1_clean)} ≠ {len(text2)}\n")
    except Exception as e:
        messagebox.showerror("Қате", str(e))

root = Tk()
root.title("Мәтіндік файлға су таңба енгізу / шығару")
root.geometry("600x600")

Label(root, text="Кіріс файл").pack()
input_file = Entry(root, width=60)
input_file.pack()
Button(root, text="Таңдау", command=lambda: open_file(input_file)).pack()

Label(root, text="Шығыс файл").pack()
output_file = Entry(root, width=60)
output_file.pack()
Button(root, text="Сақтау ретінде таңдау", command=lambda: save_file(output_file)).pack()

Label(root, text="Су таңба мәтіні").pack()
watermark_text = Entry(root, width=40)
watermark_text.pack()

Button(root, text="Су таңбаны енгізу", command=embed).pack(pady=5)
Button(root, text="Су таңбаны шығару", command=extract).pack(pady=5)
Button(root, text="Айырмашылықты көрсету", command=compare).pack(pady=5)

Label(root, text="Айырмашылықтар").pack()
differences = ScrolledText(root, height=15)
differences.pack(fill=BOTH, expand=True)

root.mainloop()
