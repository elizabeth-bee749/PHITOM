# PHITOM
APLICACIONES DE AGENDAS
import tkinter as tk
from tkinter import ttk, messagebox
import csv
import os

# ---------------- Funciones ----------------
def agregar_evento():
    fecha = entry_fecha.get()
    hora = entry_hora.get()
    tarea = entry_tarea.get()

    if fecha and hora and tarea:
        tree.insert("", "end", values=(fecha, hora, tarea))
        guardar_eventos()
        entry_fecha.delete(0, tk.END)
        entry_hora.delete(0, tk.END)
        entry_tarea.delete(0, tk.END)
    else:
        messagebox.showwarning("Advertencia", "Por favor, complete todos los campos.")

def eliminar_evento():
    seleccion = tree.selection()
    if seleccion:
        for item in seleccion:
            tree.delete(item)
        guardar_eventos()
    else:
        messagebox.showwarning("Advertencia", "Seleccione un evento para eliminar.")

def guardar_eventos():
    with open("agenda.csv", "w", newline="", encoding="utf-8") as archivo:
        writer = csv.writer(archivo)
        for item in tree.get_children():
            writer.writerow(tree.item(item)["values"])

def cargar_eventos():
    if os.path.exists("agenda.csv"):
        with open("agenda.csv", "r", newline="", encoding="utf-8") as archivo:
            reader = csv.reader(archivo)
            for row in reader:
                if row:
                    tree.insert("", "end", values=row)

def buscar_eventos():
    termino = entry_buscar.get().lower()
    for item in tree.get_children():
        tree.delete(item)
    
    if os.path.exists("agenda.csv"):
        with open("agenda.csv", "r", newline="", encoding="utf-8") as archivo:
            reader = csv.reader(archivo)
            for row in reader:
                if termino in row[0].lower() or termino in row[2].lower():
                    tree.insert("", "end", values=row)

def mostrar_todos():
    entry_buscar.delete(0, tk.END)
    for item in tree.get_children():
        tree.delete(item)
    cargar_eventos()

# ---------------- Ventana Principal ----------------
ventana = tk.Tk()
ventana.title("Agenda Personal")
ventana.geometry("650x450")
ventana.config(bg="#F5F5F5")

# Frame superior (entradas y botones)
frame_superior = tk.Frame(ventana, bg="#F5F5F5")
frame_superior.pack(pady=10)

tk.Label(frame_superior, text="Fecha (dd/mm/aaaa):", bg="#F5F5F5").grid(row=0, column=0, padx=5, pady=5)
entry_fecha = tk.Entry(frame_superior)
entry_fecha.grid(row=0, column=1, padx=5, pady=5)

tk.Label(frame_superior, text="Hora (hh:mm):", bg="#F5F5F5").grid(row=0, column=2, padx=5, pady=5)
entry_hora = tk.Entry(frame_superior)
entry_hora.grid(row=0, column=3, padx=5, pady=5)

tk.Label(frame_superior, text="Tarea:", bg="#F5F5F5").grid(row=1, column=0, padx=5, pady=5)
entry_tarea = tk.Entry(frame_superior, width=40)
entry_tarea.grid(row=1, column=1, columnspan=3, padx=5, pady=5)

btn_agregar = tk.Button(frame_superior, text="Agregar Evento", command=agregar_evento, bg="#4CAF50", fg="white")
btn_agregar.grid(row=2, column=1, pady=10)

btn_eliminar = tk.Button(frame_superior, text="Eliminar Evento", command=eliminar_evento, bg="#F44336", fg="white")
btn_eliminar.grid(row=2, column=2, pady=10)

# ---------------- Frame de búsqueda ----------------
frame_buscar = tk.Frame(ventana, bg="#E8E8E8", relief="ridge", bd=2)
frame_buscar.pack(pady=5, fill="x", padx=10)

tk.Label(frame_buscar, text="Buscar (fecha o palabra clave):", bg="#E8E8E8").pack(side="left", padx=5)
entry_buscar = tk.Entry(frame_buscar, width=30)
entry_buscar.pack(side="left", padx=5)

btn_buscar = tk.Button(frame_buscar, text="Buscar", command=buscar_eventos, bg="#2196F3", fg="white")
btn_buscar.pack(side="left", padx=5)

btn_mostrar = tk.Button(frame_buscar, text="Mostrar Todos", command=mostrar_todos, bg="#9C27B0", fg="white")
btn_mostrar.pack(side="left", padx=5)

# ---------------- Tabla (Treeview) ----------------
frame_tabla = tk.Frame(ventana)
frame_tabla.pack(fill="both", expand=True, padx=10, pady=10)

tree = ttk.Treeview(frame_tabla, columns=("Fecha", "Hora", "Tarea"), show="headings", height=10)
tree.heading("Fecha", text="Fecha")
tree.heading("Hora", text="Hora")
tree.heading("Tarea", text="Tarea")

tree.column("Fecha", width=100, anchor="center")
tree.column("Hora", width=80, anchor="center")
tree.column("Tarea", width=350, anchor="w")

tree.pack(fill="both", expand=True)

# ---------------- Cargar datos guardados ----------------
cargar_eventos()

# ---------------- Iniciar la aplicación ----------------
ventana.mainloop()
