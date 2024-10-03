import tkinter as tk
from tkinter import messagebox
from PIL import Image, ImageTk

# Classe para o livro
class Book:
    def __init__(self, title, author, year):
        self.title = title
        self.author = author
        self.year = year

    def __str__(self):
        return f"{self.title} - {self.author} ({self.year})"

# Estrutura de um nó da árvore binária de busca
class Node:
    def __init__(self, book):
        self.book = book
        self.left = None
        self.right = None

    def __str__(self):
        return str(self.book)

# Classe da biblioteca, utilizando árvore binária de busca
class Library:
    def __init__(self):
        self.root = None

    def insert(self, book):
        if self.root is None:
            self.root = Node(book)
        else:
            self._insert(self.root, book)

    def _insert(self, node, book):
        if book.title < node.book.title:
            if node.left is None:
                node.left = Node(book)
            else:
                self._insert(node.left, book)
        elif book.title > node.book.title:
            if node.right is None:
                node.right = Node(book)
            else:
                self._insert(node.right, book)
        else:
            print(f"Livro '{book.title}' já está na biblioteca.")

    def search_by_title(self, title):
        return self._search_by_title(self.root, title)
    
    #Buscando com a arvore:

    def _search_by_title(self, node, title):
        if node is None:
            return None
        if node.book.title == title:
            return node.book
        elif title < node.book.title:
            return self._search_by_title(node.left, title)
        else:
            return self._search_by_title(node.right, title)
        
    #Indicacao com mesmo autor
    def search_books_by_author(self, author):
        result = []
        self._search_books_by_author(self.root, author, result)
        return result

    #Buscando autor com arvore 
    def _search_books_by_author(self, node, author, result):
        if node is None:
            return
        if node.book.author == author:
            result.append(node.book)
        if node.left:
            self._search_books_by_author(node.left, author, result)
        if node.right:
            self._search_books_by_author(node.right, author, result)

    def simetric_traversal(self, node=None):
        if node is None:
            node = self.root
        if node.left:
            self.simetric_traversal(node.left)
        print(node)
        if node.right:
            self.simetric_traversal(node.right)

# Função para abrir nova janela e adicionar livros
def nova_janela_add():
    add_window = tk.Toplevel()
    add_window.title("Cadastro de Livros")
    add_window.geometry("400x300")

    nome_var = tk.StringVar()
    autor_var = tk.StringVar()
    ano_var = tk.StringVar()

    def obter_texto():
        nome_var.set(entry_nome.get())
        autor_var.set(entry_autor.get())
        ano_var.set(entry_ano.get())

        if nome_var.get() and autor_var.get() and ano_var.get():
            livro = Book(nome_var.get(), autor_var.get(), ano_var.get())
            biblioteca.insert(livro)
            messagebox.showinfo("Sucesso", "Livro adicionado com sucesso!")
            add_window.destroy()  # Fecha a janela de adição
        else:
            messagebox.showwarning("Atenção", "Todos os campos devem ser preenchidos.")

    # Labels e entradas para os dados do livro
    label_nome = tk.Label(add_window, text="Nome do Livro:")
    label_nome.place(x=50, y=30)
    entry_nome = tk.Entry(add_window)
    entry_nome.place(x=150, y=30)

    label_autor = tk.Label(add_window, text="Autor:")
    label_autor.place(x=50, y=70)
    entry_autor = tk.Entry(add_window)
    entry_autor.place(x=150, y=70)

    label_ano = tk.Label(add_window, text="Ano:")
    label_ano.place(x=50, y=110)
    entry_ano = tk.Entry(add_window)
    entry_ano.place(x=150, y=110)

    # Criando um botão para obter o texto
    button = tk.Button(add_window, text="Enviar", command=obter_texto)
    button.place(x=150, y=150)

# Função para abrir nova janela de busca de livros por nome
def nova_janela_buscar():
    buscar_window = tk.Toplevel()
    buscar_window.title("Buscar Livro por Nome")
    buscar_window.geometry("400x300")

    nome_var = tk.StringVar()

    def buscar_por_nome():
        nome_var.set(entry_nome.get())

        if nome_var.get():
            livro_encontrado = biblioteca.search_by_title(nome_var.get())
            if livro_encontrado:
                livros_mesmo_autor = biblioteca.search_books_by_author(livro_encontrado.author)
                resultado = "\n".join([str(livro) for livro in livros_mesmo_autor])
                messagebox.showinfo("Livros do mesmo autor", resultado)
            else:
                messagebox.showinfo("Livro não encontrado", "O livro solicitado não foi encontrado.")
            buscar_window.destroy()
        else:
            messagebox.showwarning("Atenção", "O campo nome deve ser preenchido.")

    # Labels e entrada para buscar o livro
    label_nome = tk.Label(buscar_window, text="Nome do Livro:")
    label_nome.place(x=50, y=30)
    entry_nome = tk.Entry(buscar_window)
    entry_nome.place(x=150, y=30)

    # Criando um botão para buscar
    button = tk.Button(buscar_window, text="Buscar", command=buscar_por_nome)
    button.place(x=150, y=70)

# Função para exibir todos os livros
def exibir_livros():
    print("\nLivros na biblioteca (em ordem alfabética):")
    biblioteca.simetric_traversal()

# Criar a instância da biblioteca (árvore binária de busca)
biblioteca = Library()

# Interface gráfica com Tkinter
app = tk.Tk()
app.title("Menu")
app.geometry("573x322")
app.resizable(False, False)

# Adiciona uma imagem de fundo
imagem_fundo = Image.open(r"C:\Users\renan\OneDrive\Desktop\Programação\FACISA\ALGORITMOS DE APOIO À PESQUISA OPERACIONAL\Abeblioteca\Tela Inicial.png")
app.imagem_fundo = ImageTk.PhotoImage(imagem_fundo)

# Criar o canvas para imagem de fundo
canvas = tk.Canvas(app, width=573, height=322)
canvas.pack(fill="both", expand=True)
canvas.create_image(0, 0, image=app.imagem_fundo, anchor="nw")

# Função para detectar cliques nos botões da interface
def on_click(event):
    x, y = event.x, event.y  # Coordenadas do clique

    # Dimensões dos "botões"
    W = 118
    H = 25

    # Define as coordenadas das áreas clicáveis
    add_area = (427, 159, 427 + W, 159 + H)
    list_area = (427, 191, 427 + W, 191 + H)
    search_area = (427, 256, 427 + W, 256 + H)

    # Verifica onde o clique ocorreu e executa a ação correspondente
    if add_area[0] <= x <= add_area[2] and add_area[1] <= y <= add_area[3]:
        nova_janela_add()  # Adicionar livro
    elif list_area[0] <= x <= list_area[2] and list_area[1] <= y <= list_area[3]:
        exibir_livros()  # Exibir todos os livros
    elif search_area[0] <= x <= search_area[2] and search_area[1] <= y <= search_area[3]:
        nova_janela_buscar()  # Buscar livro por nome

# Vincula o evento de clique ao canvas
canvas.bind("<Button-1>", on_click)

# Inicia o loop principal da interface gráfica
app.mainloop()
