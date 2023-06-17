# longtext
A black hole for plain text: an interface between you and one long ass txt with everything you ever type (kinda)

I really don't know what I'm doing still. I'm almost sure it basically just runs the following python code: 

```
import tkinter as tk
from tkinter import messagebox, filedialog
from datetime import datetime
import os
import re
from tkinter import constants as tkc


class TextEditor:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("Text Editor")
        self.text = tk.Text(self.root, font=('Monaco', 12))
        self.text.pack(fill=tk.BOTH, expand=True)
        self.filename = "long.txt"
        self.add_button = tk.Button(self.root, text="Add", command=self.add_text)
        self.add_button.pack(side="left")
        self.add_and_replace_button = tk.Button(self.root, text="Add and Replace", command=self.confirm_add_and_replace)
        self.add_and_replace_button.pack(side="left")
        self.stop_button = tk.Button(self.root, text="Stop", command=self.root.quit)
        self.stop_button.pack(side="left")
        self.save_label = tk.Label(self.root, text="")
        self.save_label.pack(side="left")
        self.text.bind('<Control-v>', self.paste_from_clipboard)

    def paste_from_clipboard(self, event=None):
        clipboard_text = self.root.clipboard_get()
        self.text.insert(tkc.INSERT, clipboard_text)


    def add_text(self):
        current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        content = self.text.get(1.0, tk.END).strip()
        with open(self.filename, "a") as file:
            file.write(f'\n{current_time}\n{content}\n')
        self.update_save_label()

    def confirm_add_and_replace(self):
        confirmation = messagebox.askyesno("Confirmation", "Are you sure you want to replace the last text added?")
        if confirmation:
            self.add_and_replace_text()

    def add_and_replace_text(self):
        current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        content = self.text.get(1.0, tk.END).strip()

        with open(self.filename, "r") as file:
            lines = file.readlines()

        # Assuming timestamp takes 2 lines (1 line for actual timestamp and 1 line blank)
        timestamp_pattern = re.compile(r"\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}")

        # Find the last timestamp and remove it along with the following content
        for i in range(len(lines) - 1, -1, -1):
            if timestamp_pattern.match(lines[i].strip()):
                del lines[i:i+3]  # remove timestamp, blank line, and the following line of content
                break

        with open(self.filename, "w") as file:
            file.writelines(lines)

        # Add new content
        self.add_text()

    def update_save_label(self):
        file_info = os.stat(self.filename)
        file_size_MB = file_info.st_size / (1024 * 1024)
        current_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        self.save_label.config(text=f"Last saved: {current_time}, File: {self.filename}, Size: {file_size_MB:.2f} MB")

    def open_file(self):
        self.filename = filedialog.askopenfilename(defaultextension=".txt",
                                                   filetypes=[("Text Files", "*.txt"), ("All Files", "*.*")])
        if self.filename:
            with open(self.filename, "r") as file:
                content = file.read()
                self.text.delete(1.0, tk.END)
                self.text.insert(tk.END, content)
            self.update_save_label()

    def save_file_as(self):
        self.filename = filedialog.asksaveasfilename(defaultextension=".txt",filetypes=[("Text Files", "*.txt"), ("All Files", "*.*")])
        if self.filename:
            self.add_text()

    def run(self):
        menu_bar = tk.Menu(self.root)
        file_menu = tk.Menu(menu_bar, tearoff=0)
        file_menu.add_command(label="Open", command=self.open_file)
        file_menu.add_command(label="Save As", command=self.save_file_as)
        file_menu.add_command(label="Stop", command=self.root.quit)
        menu_bar.add_cascade(label="File", menu=file_menu)
        self.root.config(menu=menu_bar)
        self.root.mainloop()

editor = TextEditor()
editor.run()
```
It feels vaguely illegal to put code here, sorry 🙃
