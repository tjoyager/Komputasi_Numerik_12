## Algoritma Metode Regula Falsi

import numpy as np
import matplotlib.pyplot as plt
import tkinter as tk
from tkinter import ttk, messagebox

def regula_falsi():
    try:
        fungsi_str = input_fungsi.get()
        x1 = float(input_x1.get())
        x2 = float(input_x2.get())
        n = int(input_iterasi.get())

        def f(x):
            return eval(fungsi_str, {"x": x, "np": np})

        if f(x1) * f(x2) > 0:
            messagebox.showerror("Error", "Tidak ada perubahan tanda!")
            return

        x1_awal = x1
        x2_awal = x2

        if f(x1) == f(x2):
            messagebox.showerror("Error", "Pembagian nol (f(x1) = f(x2))")
            return

        for row in tree.get_children():
            tree.delete(row)

        xr_old = None

        for i in range(1, n+1):
            xr = x2 - (f(x2) * (x1 - x2)) / (f(x1) - f(x2))
            fx = f(xr)

            if xr_old is None:
                error = "-"
            else:
                error = abs((xr - xr_old) / xr) * 100

            tree.insert("", "end", values=(
                i,
                f"{xr:.6f}",
                f"{fx:.6f}",
                error if error == "-" else f"{error:.6f}%"
            ))

            if f(x1) * fx < 0:
                x2 = xr
            else:
                x1 = xr

            xr_old = xr

        label_hasil.config(text=f"Akar ≈ {xr:.6f}")

        x_plot = np.linspace(x1_awal - 1, x2_awal + 1, 200)
        y_plot = [f(i) for i in x_plot]

        plt.figure(figsize=(6,4))
        plt.plot(x_plot, y_plot, label="f(x)")
        plt.axhline(0)
        plt.scatter(xr, f(xr), color='red', label="Akar")
        plt.title("Grafik Regula Falsi")
        plt.legend()
        plt.grid()
        plt.show()

    except:
        messagebox.showerror("Error", "Input tidak valid!")

root = tk.Tk()
root.title("Metode Regula Falsi")
root.geometry("750x500")

root.grid_columnconfigure(0, weight=1)
root.grid_columnconfigure(1, weight=3)
root.grid_rowconfigure(5, weight=1)

style = ttk.Style()
style.configure("Treeview", font=("Consolas", 9), rowheight=22)
style.configure("Treeview.Heading", font=("Consolas", 10, "bold"))

tk.Label(root, text="f(x):").grid(row=0, column=0, sticky="w", padx=5)
input_fungsi = tk.Entry(root)
input_fungsi.grid(row=0, column=1, sticky="ew", padx=5, pady=2)
input_fungsi.insert(0, "(1 - 0.6*x)/x")

tk.Label(root, text="x1:").grid(row=1, column=0, sticky="w", padx=5)
input_x1 = tk.Entry(root)
input_x1.grid(row=1, column=1, sticky="ew", padx=5, pady=2)
input_x1.insert(0, "1")

tk.Label(root, text="x2:").grid(row=2, column=0, sticky="w", padx=5)
input_x2 = tk.Entry(root)
input_x2.grid(row=2, column=1, sticky="ew", padx=5, pady=2)
input_x2.insert(0, "2")

tk.Label(root, text="Iterasi:").grid(row=3, column=0, sticky="w", padx=5)
input_iterasi = tk.Entry(root)
input_iterasi.grid(row=3, column=1, sticky="ew", padx=5, pady=2)
input_iterasi.insert(0, "5")

tk.Button(root, text="Hitung", command=regula_falsi)\
    .grid(row=4, column=0, columnspan=2, pady=10)

columns = ("Iterasi", "xr", "f(xr)", "Error")
tree = ttk.Treeview(root, columns=columns, show="headings", height=8)

tree.heading("Iterasi", text="Iterasi")
tree.heading("xr", text="xr")
tree.heading("f(xr)", text="f(xr)")
tree.heading("Error", text="Error")

tree.column("Iterasi", anchor="center", width=70)
tree.column("xr", anchor="center", width=130)
tree.column("f(xr)", anchor="center", width=130)
tree.column("Error", anchor="center", width=130)

tree.grid(row=5, column=0, columnspan=2, sticky="nsew", padx=10, pady=5)

scrollbar = ttk.Scrollbar(root, orient="vertical", command=tree.yview)
tree.configure(yscroll=scrollbar.set)
scrollbar.grid(row=5, column=2, sticky="ns")

label_hasil = tk.Label(root, text="Akar ≈ -", font=("Arial", 12, "bold"))
label_hasil.grid(row=6, column=0, columnspan=2, pady=10)

root.mainloop()