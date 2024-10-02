﻿# Abeblioteca

import tkinter as tk
from tkinter import messagebox
from PIL import Image, ImageTk

# Nova janela para adicionar livros
def nova_janela_add():
    add_window = tk.Toplevel()
    add_window.title("Cadastro de Livros")
    add_window.geometry("400x300")

    nome_var = tk.StringVar()
    editora_var = tk.StringVar()
    ano_var = tk.StringVar()

    def obter_texto():
        nome_var.set(entry_nome.get())
        editora_var.set(entry_editora.get())
        ano_var.set(entry_ano.get())

        if nome_var.get() and editora_var.get() and ano_var.get():
            lista.adicionar(nome_var.get(), editora_var.get(), ano_var.get())
            messagebox.showinfo("Sucesso", "Livro adicionado com sucesso!")
            add_window.destroy()  # Fecha a janela de adição
        else:
            messagebox.showwarning("Atenção", "Todos os campos devem ser preenchidos.")

    # Labels e entradas para os dados do livro
    label_nome = tk.Label(add_window, text="Nome do Livro:")
    label_nome.place(x=50, y=30)
    entry_nome = tk.Entry(add_window)
    entry_nome.place(x=150, y=30)

    label_editora = tk.Label(add_window, text="Editora:")
    label_editora.place(x=50, y=70)
    entry_editora = tk.Entry(add_window)
    entry_editora.place(x=150, y=70)

    label_ano = tk.Label(add_window, text="Ano:")
    label_ano.place(x=50, y=110)
    entry_ano = tk.Entry(add_window)
    entry_ano.place(x=150, y=110)

    # Criando um botão para obter o texto
    button = tk.Button(add_window, text="Enviar", command=obter_texto)
    button.place(x=150, y=150)

# LIVRO AQUI
class Livro:
    def __init__(self, nome, editora, ano):
        self.nome = nome
        self.editora = editora
        self.ano = ano

    def __str__(self):
        return f"{self.nome} - {self.editora} ({self.ano})"

# LISTA SEQUENCIAL IMPLEMENTADA
class ListaSequencial:
    def __init__(self, capacidade=3):
        self.capacidade = capacidade
        self.tamanho = 0
        self.elementos = [None] * capacidade

    def adicionar(self, nome, editora, ano):
        livro = Livro(nome, editora, ano)
        if self.tamanho == self.capacidade:
            self.redimensionar()
        self.elementos[self.tamanho] = livro
        self.tamanho += 1

    def redimensionar(self):
        nova_capacidade = self.capacidade * 2
        novos_elementos = [None] * nova_capacidade
        for i in range(self.tamanho):
            novos_elementos[i] = self.elementos[i]
        self.elementos = novos_elementos
        self.capacidade = nova_capacidade

    def buscar(self, nome):
        i = 0
        for livro in self.elementos:
            if livro is not None and livro.nome == nome:
                print(f"\n\nLivro na posicion: {i}\n\n")
                return livro
            i += 1
        print("Livro não encontrado.")
        return None

    def exibir(self):
        if self.tamanho == 0:
            print("Lista vazia")
        else:
            for i in range(self.tamanho):
                print(self.elementos[i], end="\n")
            print()
    def order (self):
        lista = [livro for livro in self.elementos if livro is not None]
        for j in range(len(lista), 0, -1):
            for i in range(j - 1):
                if lista[i].nome > lista[i + 1].nome:  # Ordena pelo nome do livro
                    x = lista[i]
                    lista[i] = lista[i + 1]
                    lista[i + 1] = x

        # Atualiza a lista original
        self.elementos[:len(lista)] = lista
        self.tamanho = len(lista)  # Atualiza o tamanho da lista
# Aqui a lista para usar
lista = ListaSequencial()

#------------------------------Interface----------------------------------------
# MEU PRINCIPAL DO ABEBLIOTECA
app = tk.Tk()
app.title("Menu")
app.geometry("573x322")
app.resizable(False, False)

imagem_fundo = Image.open(r"C:\Users\renan\OneDrive\Desktop\Programação\FACISA\ALGORITMOS DE APOIO À PESQUISA OPERACIONAL\Abeblioteca\Tela Inicial.png")
app.imagem_fundo = ImageTk.PhotoImage(imagem_fundo)

# GERAR O CANVA
canvas = tk.Canvas(app, width=573, height=322)
canvas.pack(fill="both", expand=True)
canvas.create_image(0, 0, image=app.imagem_fundo, anchor="nw")

# Função para detectar o clique nos falsos botões
def on_click(event):
    x, y = event.x, event.y  # Coordenadas do clique

    # Tamanho do botão
    W = 118
    H = 25

    # Define as coordenadas e dimensões das áreas de clique
    add_area = (427, 159, 427 + W, 159 + H)
    list_area = (427, 191, 427 + W, 191 + H)
    order_area = (427, 222, 427 + W, 222 + H)
    search_area = (427, 256, 427 + W, 256 + H)

    # Condiciona a área ao clique da função
    if add_area[0] <= x <= add_area[2] and add_area[1] <= y <= add_area[3]:
        nova_janela_add()  

    elif list_area[0] <= x <= list_area[2] and list_area[1] <= y <= list_area[3]:
        lista.exibir()

    elif order_area[0] <= x <= order_area[2] and order_area[1] <= y <= order_area[3]:
        lista.order()  # falta implementar

    elif search_area[0] <= x <= search_area[2] and search_area[1] <= y <= search_area[3]:
        
        livro_a_buscar = input("Nome do livro")
        lista.buscar(livro_a_buscar)  

# Vincula o evento de clique à tela
canvas.bind("<Button-1>", on_click)

# Inicia o loop principal da interface
app.mainloop()

print(lista)
