# Gerador-de-senha
Meu primeiro repositório no GitHub

import random
from tkinter import *
from tkinter import messagebox
import back
import csv
from ttkbootstrap import *


class window:
    #lista de caracteres recrutados na função generate
    digits = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"]

    lc = ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k",
          "m", "n", "o", "p", "q",
          "r", "s", "t", "u", "v", "w", "x", "y", "z"]

    uc = ["A", "B", "C", "D", "E", "F", "G", "H",
          "I", "J", "K", "M", "N", "O", "p", "Q",
          "R", "S", "T", "U", "V", "W", "X", "Y", "Z"]

    sym = ["@", "#", "$", "%", "=", ":", "?", ".", "/", "|",
           '~', '>', '*', '<']

#criando o layout usando o tkinter
    def __init__(self, root, geo, title) -> None:
        self.root = root
        self.root.title(title)
        self.root.geometry(geo)
        self.root.resizable(width=False, height=False)

        Label(self.root, text="Site/Softwere:").grid(row=0, column=0, padx=8, pady=8)

        Label(self.root, text="Usuário:").grid(row=1, column=0, padx=8, pady=8)
        Label(self.root, text="Senha:").grid(row=5, column=0, padx=8, pady=8)

        self.pa = StringVar()
        self.user_id = StringVar()
        self.site = StringVar()

        ttk.Entry(self.root, width=30, textvariable=self.site).grid(row=0, column=1, padx=0, pady=8)
        ttk.Entry(self.root, width=30, textvariable=self.user_id).grid(row=1, column=1, padx=0, pady=8)
        ttk.Entry(self.root, width=30, textvariable=self.pa).grid(row=6, column=1, padx=0, pady=8)


        self.length = StringVar()

        e = ttk.Combobox(self.root, width=20, values=['4', '8', '12', '16', '20', '24'],
                         textvariable=self.length)
        e.grid(row=4, column=1, pady=10, padx=10)
        e['state'] = 'readonly'
        self.length.set("quantidade de dígitos")

        ttk.Button(self.root, text="GERAR SENHA", padding=5,
                   style="success.Outline.TButton", width=20,
                   command=self.generate).grid(row=5, column=1)

        ttk.Button(self.root, text="SALVAR DADOS", style="success.TButton",
                   width=20, padding=5, command=self.save).grid(row=8, column=2, padx=10, pady=10)

        ttk.Button(self.root, text="DELETAR", width=20, style='danger.TButton',
                   padding=5, command=self.erase).grid(row=8, column=1)

        ttk.Button(self.root, text="MOSTRAR TUDO", width=20, padding=5,
                   command=self.view).grid(row=8, column=0)

        # construção da grade onde sera mostrado os dados salvos
        self.tree = ttk.Treeview(self.root, height=5)
        self.tree['columns'] = ('site', 'user', 'pas')
        self.tree.column('#0', width=0, stretch=NO)
        self.tree.column('site', width=160, anchor=W)
        self.tree.column('user', width=140, anchor=W)
        self.tree.column('pas', width=180, anchor=W)
        self.tree.heading('#0', text='')
        self.tree.heading('site', text="nome do site")
        self.tree.heading('user', text="login do usuário")
        self.tree.heading('pas', text="senha")
        self.tree.grid(row=7, column=0, columnspan=3, pady=10, padx=10)

    def refresh(self):
        # essa função sempre será chamada quando houver mudanças na grade
        self.table()
        self.view()

    def table(self):
    #função resposável por mostrar as alterações
     for r in self.tree.get_children():
         self.tree.delete(r)



    def view(self):
        # wiew será chamada quando for solicitado o banco de dados
        if back.check() is False:
            messagebox.showerror("Attention Amigo!", "não há arquivo salvo")
        else:
            for row in back.show():
                self.tree.insert(parent="", text="", index="end",
                                 values=(row[0], row[1], row[2]))

    def erase(self):
        #erase removerá a tupla ou linha do banco de dados selecionada
        selected = self.tree.focus()
        value = self.tree.item(selected, 'value')
        back.Del(value[2])
        self.refresh()

    def update(self):
       # vai atualiazar de acordo com as novas informações dadas pelo usuário
        selected = self.tree.focus()
        value = self.tree.item(selected, 'value')
        back.edit(self.site.get(), self.user_id.get(), self.pa.get())
        self.refresh()

    def save(self):
        #função responsavel por salvar todas as informações no banco de dados
        back.enter(self.site.get(), self.user_id.get(), self.pa.get())
        self.tree.insert(parent='', index='end', text='',
                         values=(self.site.get(), self.user_id.get(), self.pa.get()))

    def generate(self):
        #função responsavel por gerar a senha aleatoria
        if self.length.get() == "Limite de caracteres":
            messagebox.showerror('Attention!', "You forgot to SELECT")
        else:
            a = ''
            for x in range(int(int(self.length.get()) / 4)):
                a0 = random.choice(self.uc)
                a1 = random.choice(self.lc)
                a2 = random.choice(self.sym)
                a3 = random.choice(self.digits)
                a = a0 + a1 + a2 + a3 + a
                self.pa.set(a)

    def export(self):
        #função q exporta do banco de dados csv, salvando os dados tal qual uma planilha
        pop = Toplevel(self.root)
        pop.geometry('300x100')
        self.v = StringVar()
        Label(pop, text='Save File Name as').pack()
        ttk.Entry(pop, textvariable=self.v).pack()
        ttk.Button(pop, text='Save', width=18,
                   command=lambda: exp(self.v.get())).pack(pady=5)

        def exp(x):
            with open(x + '.csv', 'w', newline='') as f:
                chompa = csv.writer(f, dialect='excel')
                for r in back.show():
                    chompa.writerow(r)
            messagebox.showinfo("File Saved", "Saved as " + x + ".csv")


#innformação da janela de exibição
if __name__ == '__main__':
    win = Style(theme="darkly").master
    name = "Gerador de Senhas"
    dimension = "508x400"

    app = window(win, dimension, name)
    win.mainloop()
