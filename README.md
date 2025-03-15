=### Interface ✨
#

![image (1)](https://github.com/user-attachments/assets/1f805594-39b4-4f33-af5b-f46e7165c5d5)
![image (2)](https://github.com/user-attachments/assets/9866fefe-31b2-402c-8fc8-62d31b808b0a)
![image (3)](https://github.com/user-attachments/assets/6123c02e-d4f6-45f5-bf74-45cffa2d1f7f)
![image (4)](https://github.com/user-attachments/assets/098209ac-48e3-4686-8993-b94028503fc5)

#

### Código do projeto 👩🏽‍💻 

"""
import sqlite3
import customtkinter as ctk
from customtkinter import *
from tkinter import messagebox, ttk

conn = sqlite3.connect('produtos.db')
cursor = conn.cursor()
cursor.execute('''CREATE TABLE IF NOT EXISTS produtos (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    nome TEXT NOT NULL,
                    descricao TEXT,
                    valor REAL NOT NULL,
                    disponivel TEXT NOT NULL)''')

root = ctk.CTk()
root.title("Cadastro de Produtos")
root.geometry("470x400")
root.iconbitmap("icon.ico")
root.resizable(width=False, height=False)
set_appearance_mode("light")


janela_cadastro = ctk.CTkFrame(root)
janela_cadastro.pack(fill="both", expand=True)


def cadastrar_produto(nome, descricao, valor, disponivel):
    if not nome or not valor:
        messagebox.showwarning("Erro", "Nome e valor são obrigatórios!")
        return

    cursor.execute('''
        INSERT INTO produtos (nome, descricao, valor, disponivel)
        VALUES (?, ?, ?, ?)
    ''', (nome, descricao, valor, disponivel))
    conn.commit()
    messagebox.showinfo("Sucesso", "Produto cadastrado com sucesso! :)")
    abrir_listagem()


def listar_produtos():
    for row in tree.get_children():
        tree.delete(row)

    cursor.execute('SELECT nome, valor FROM produtos ORDER BY valor ASC')
    produtos = cursor.fetchall()
    for produto in produtos:
        valor_formatado = f'R${produto[1]:,.2f}'.replace(",","X").replace('.', ',').replace('X', '.')
        tree.insert("", "end", values=(produto[0], valor_formatado))


def apagar_lista():
    if messagebox.askyesno("Confirmação", 
                           "Você tem certeza que deseja limpar todos os produtos?"):
        cursor.execute('DELETE FROM produtos')
        conn.commit()
        listar_produtos()
        messagebox.showinfo("Sucesso", 
                            "Todos os produtos foram removidos.")


def abrir_listagem():
    janela_listagem.pack(fill="both", expand=True)
    janela_cadastro.pack_forget()
    listar_produtos()


def abrir_cadastro():
    janela_cadastro.pack(fill="both", expand=True)
    janela_listagem.pack_forget()


def visualizar_lista():
    try:
        cursor.execute('SELECT nome, valor FROM produtos')
        produtos = cursor.fetchall()
        if not produtos:
            messagebox.showinfo("Lista Vazia", 
                                "Não há itens na lista.")
        else:
            itens = "\n".join([f"Produto: {p[0]}  "
                               f"  Valor: R${p[1]:,.2f}".replace(',', 'X').replace('.', ',').replace('X', '.') 
                               for p in produtos])
            messagebox.showinfo("Itens da Lista", itens)
    except Exception as e:
        messagebox.showerror("Erro", 
                             f"Ocorreu um erro: {str(e)}")



def salvar_e_listar():
    nome = entry_nome.get()
    descricao = entry_descricao.get()
    valor = entry_valor.get().replace('R$', '').replace('.', '').replace(',', '.')
    disponivel = var_disponivel.get()

    if not valor:
        messagebox.showwarning("Erro", "Valor deve ser um número!")
        return
    cadastrar_produto(nome, descricao, float(valor), disponivel)
    entry_nome.delete(0, ctk.END)
    entry_descricao.delete(0, ctk.END)
    entry_valor.delete(0, ctk.END)
    var_disponivel.set("Sim")
    listar_produtos()




ctk.CTkLabel(janela_cadastro, 
text="Nome do Produto", 
text_color="black", font=("Ariel", 12, "bold")).grid(row=1, column=0,
                          sticky="s", padx=0, pady=0)


entry_nome = ctk.CTkEntry(janela_cadastro, 
                          width=300, height=35, 
                          fg_color="#bcbcbc", 
                          corner_radius=7, border_color="black")
entry_nome.grid(row=4, column=0, 
                padx=85, pady=0, 
                sticky="nsew")




ctk.CTkLabel(janela_cadastro, 
text="Descrição do Produto", font=("Ariel", 12, "bold")).grid(row=6, 
                                  column=0, sticky="s", padx=0, pady=0)

entry_descricao = ctk.CTkEntry(janela_cadastro, width=250, 
                               height=60, 
                               fg_color="#bcbcbc",corner_radius=7, 
                               border_color="black" )
entry_descricao.grid(row=7,column=0, 
                padx=85, pady=0, 
                sticky="nsew")




ctk.CTkLabel(janela_cadastro, 
text="Valor do Produto", font=("Ariel", 12, "bold")).grid(row=8,column=0, sticky="s", padx=0, pady=0)
entry_valor = ctk.CTkEntry(janela_cadastro, width=250, 
                               height=35, 
                               fg_color="#bcbcbc",corner_radius=7, 
                               border_color="black", )
entry_valor.grid(row=9, column=0, padx=5, pady=5)




ctk.CTkLabel(janela_cadastro, 
text="Disponível para Venda", font=("Ariel", 12, "bold")).grid(row=11, column=0, 
sticky="s", padx=0, pady=0)
var_disponivel = ctk.StringVar(value="Sim")
opcoes_disponivel = (["Sim", "Não"])

ctk.CTkOptionMenu(janela_cadastro, variable=var_disponivel, 
values=opcoes_disponivel, fg_color="#ffffff", 
text_color="black", corner_radius=32, dropdown_hover_color="#d9d2e9", 
dropdown_text_color="#000000", dropdown_fg_color="#bcbcbc", anchor="sw", button_color="#444444", button_hover_color="#000000", font=("Ariel", 14,), ).grid(row=12, column=0, padx=0, pady=0, sticky="s")


ctk.CTkButton(janela_cadastro, 
text="Salvar", command=salvar_e_listar, width=200,
fg_color="#bcbcbc", text_color="#000000", height=30, border_color="#000000", corner_radius=32, hover_color="#d9d2e9", border_width=2).grid(column=0, padx=0, pady=6, sticky="nswe")


janela_listagem = ctk.CTkFrame(root, corner_radius=35)

tree = ttk.Treeview(janela_listagem, 
columns=("Produto", "Valor"), show="headings")
tree.heading("Produto", text="Produto", anchor="center")
tree.heading("Valor", text="Valor", anchor="center")
tree.column("Produto", width=10, anchor="center")
tree.column("Valor", width=10, anchor="center")
tree.pack(padx=25, pady=25, fill="both", expand=True, anchor="center")



ctk.CTkButton(janela_listagem, text="Cadastrar Novo Produto", command=abrir_cadastro, fg_color="#bcbcbc", text_color="#000000",
              corner_radius=32, hover_color="#d9d2e9", border_width=2).pack(pady=4)

ctk.CTkButton(janela_listagem, text="Limpar Lista", command=apagar_lista,fg_color="#660000",text_color="#e9e9e9", border_color="#000000", corner_radius=32, hover_color="#990000", border_width=2, font=("Ariel", 12, "bold")).pack(pady=3)

ctk.CTkButton(janela_cadastro, text="Visualizar Lista", command=visualizar_lista, width=250, height=30, border_width=2, 
              border_color="#000000", fg_color="#351c75", text_color="#e6dff5",
              corner_radius=32,hover_color="#20124d", font=("Ariel", 12, "bold")).grid(row=15, column=0, stick="nswe", padx=0, pady=0)

ctk.CTkButton(janela_listagem, text="Visualizar Lista", command=visualizar_lista,border_color="#000000", 
              fg_color="#351c75", text_color="#e6dff5",
              corner_radius=32,hover_color="#20124d", font=("Ariel", 12, "bold")).pack(pady=10)


janela_listagem.pack_forget()
abrir_cadastro()


def on_closing():
    conn.close()
    root.destroy()

root.protocol("WM_DELETE_WINDOW", on_closing)
root.mainloop()
"""
