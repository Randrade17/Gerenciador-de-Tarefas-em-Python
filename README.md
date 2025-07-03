# Gerenciador de tarefas em Python

Aplicação de linha de comando que permite criar, listar, atualizar e excluir tarefas, com dados salvos em um arquivo JSON. O projeto demonstra conhecimentos intermediários em Python, incluindo orientação a objetos, manipulação de arquivos e interação via terminal.

Funcionalidades
Adicionar tarefas com título e categoria

Listar todas ou apenas tarefas pendentes

Atualizar status (pendente/concluída)

Excluir tarefas por ID

Salvar dados automaticamente no JSON


import json
import os
from datetime import datetime

DATA_FILE = "tasks.json"

class TaskManager:
    def __init__(self):
        self.tasks = []
        self.load_tasks()

    def load_tasks(self):
        if os.path.exists(DATA_FILE):
            with open(DATA_FILE, "r", encoding="utf-8") as f:
                self.tasks = json.load(f)
        else:
            self.tasks = []

    def save_tasks(self):
        with open(DATA_FILE, "w", encoding="utf-8") as f:
            json.dump(self.tasks, f, indent=4, ensure_ascii=False)

    def add_task(self, title, category):
        task = {
            "id": len(self.tasks) + 1,
            "title": title,
            "category": category,
            "status": "pendente",
            "created_at": datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        }
        self.tasks.append(task)
        self.save_tasks()
        print("✅ Tarefa adicionada com sucesso.")

    def list_tasks(self, filter_status=None):
        if not self.tasks:
            print("Nenhuma tarefa cadastrada.")
            return

        for task in self.tasks:
            if filter_status and task["status"] != filter_status:
                continue
            print(f"""
ID: {task["id"]}
Título: {task["title"]}
Categoria: {task["category"]}
Status: {task["status"]}
Criada em: {task["created_at"]}
""")

    def update_task_status(self, task_id, new_status):
        for task in self.tasks:
            if task["id"] == task_id:
                task["status"] = new_status
                self.save_tasks()
                print("✅ Status atualizado.")
                return
        print("❌ Tarefa não encontrada.")

    def delete_task(self, task_id):
        for i, task in enumerate(self.tasks):
            if task["id"] == task_id:
                del self.tasks[i]
                self.save_tasks()
                print("✅ Tarefa removida.")
                return
        print("❌ Tarefa não encontrada.")

def main():
    manager = TaskManager()

    while True:
        print("""
===== GERENCIADOR DE TAREFAS =====
1. Adicionar tarefa
2. Listar todas as tarefas
3. Listar tarefas pendentes
4. Atualizar status da tarefa
5. Deletar tarefa
6. Sair
""")
        choice = input("Escolha uma opção: ")

        if choice == "1":
            title = input("Título da tarefa: ")
            category = input("Categoria: ")
            manager.add_task(title, category)
        elif choice == "2":
            manager.list_tasks()
        elif choice == "3":
            manager.list_tasks(filter_status="pendente")
        elif choice == "4":
            try:
                task_id = int(input("ID da tarefa: "))
                new_status = input("Novo status (pendente/concluída): ").strip().lower()
                manager.update_task_status(task_id, new_status)
            except ValueError:
                print("❌ ID inválido.")
        elif choice == "5":
            try:
                task_id = int(input("ID da tarefa: "))
                manager.delete_task(task_id)
            except ValueError:
                print("❌ ID inválido.")
        elif choice == "6":
            print("Saindo...")
            break
        else:
            print("❌ Opção inválida.")

if __name__ == "__main__":
    main()
