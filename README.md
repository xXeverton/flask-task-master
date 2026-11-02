# Task Master

Lista de tarefas (to-do list) feita com **Flask** e **SQLite**, desenvolvida acompanhando o tutorial
[Learn Flask for Python - Full Tutorial](https://www.youtube.com/watch?v=Z1RJmh_OqeA) do freeCodeCamp.

🔗 **Online:** https://xxeverton.pythonanywhere.com

## Funcionalidades

- Adicionar tarefas
- Listar tarefas com a data de criação
- Atualizar o texto de uma tarefa
- Deletar tarefas
- Mensagem quando a lista está vazia

## Tecnologias

- Python 3.11
- Flask 3.1
- Flask-SQLAlchemy 3.1 (ORM) com banco SQLite
- Jinja2 (templates) e CSS puro
- Deploy no PythonAnywhere (plano gratuito)

## Estrutura

```
Flask_tutorial/
├── app.py              # App Flask: modelo Todo e rotas
├── requirements.txt    # Dependências
├── static/
│   └── css/main.css    # Estilos
└── templates/
    ├── base.html       # Layout base (herdado pelas outras páginas)
    ├── index.html      # Lista de tarefas e formulário de criação
    └── update.html     # Formulário de edição
```

### Rotas

| Rota | Métodos | O que faz |
|---|---|---|
| `/` | GET | Lista as tarefas ordenadas pela data de criação |
| `/` | POST | Cria uma nova tarefa |
| `/delete/<id>` | GET | Remove a tarefa |
| `/update/<id>` | GET | Mostra o formulário de edição |
| `/update/<id>` | POST | Salva o novo texto da tarefa |

### Modelo

```python
class Todo(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.String(200), nullable=False)
    date_created = db.Column(db.DateTime, default=datetime.utcnow)
```

## Como rodar localmente

```bash
# 1. Criar e ativar o ambiente virtual (Windows)
python -m venv venv
venv\Scripts\activate

# 2. Instalar as dependências
pip install -r requirements.txt

# 3. Criar o banco (gera instance/test.db)
python -c "from app import app, db; app.app_context().push(); db.create_all()"

# 4. Rodar
python app.py
```

Acesse http://127.0.0.1:5000

## Deploy no PythonAnywhere

O vídeo usa o Heroku, que não tem mais plano gratuito. A alternativa usada foi o PythonAnywhere,
que mantém o arquivo do SQLite salvo em disco.

1. Criar uma conta **Beginner (free)**.
2. Enviar o projeto (sem `venv/`, `__pycache__/` e `instance/`) pela aba **Files** e descompactar
   no console Bash: `unzip Flask_tutorial.zip -d Flask_tutorial`.
3. Criar o ambiente virtual e instalar as dependências:
   ```bash
   mkvirtualenv --python=$(which python3.11) flaskenv
   pip install -r ~/Flask_tutorial/requirements.txt
   ```
4. Criar o banco: `cd ~/Flask_tutorial && python -c "from app import app, db; app.app_context().push(); db.create_all()"`.
5. Na aba **Web**: *Add a new web app* → **Manual configuration** → Python 3.11, e configurar:
   - **Source code:** `/home/<usuario>/Flask_tutorial`
   - **Virtualenv:** `/home/<usuario>/.virtualenvs/flaskenv`
   - **Static files:** `/static/` → `/home/<usuario>/Flask_tutorial/static`
6. Substituir todo o conteúdo do **WSGI configuration file** por:
   ```python
   import sys

   path = '/home/<usuario>/Flask_tutorial'
   if path not in sys.path:
       sys.path.append(path)

   from app import app as application
   ```
7. Clicar em **Reload**.

> No plano gratuito, é preciso entrar na aba Web uma vez por mês e clicar em
> **"Run until 1 month from today"** para o site não ser desativado.

## Problemas encontrados e o que aprendi

| Problema | Causa | Solução |
|---|---|---|
| `RuntimeError: Either 'SQLALCHEMY_DATABASE_URI' or 'SQLALCHEMY_BINDS' must be set` | Erro de digitação na chave (`SQALALCHEMY_...`) | O Flask só reconhece o nome exato `SQLALCHEMY_DATABASE_URI` |
| `Working outside of application context` ao criar o banco | No Flask-SQLAlchemy 3.x o `db.create_all()` precisa de um app context (o vídeo usa uma versão antiga) | `with app.app_context(): db.create_all()` |
| Página sem estilo (sem bordas, nada centralizado) | Faltavam as regras no `main.css`, não era erro no HTML | Adicionar o CSS e recarregar com **Ctrl+F5** por causa do cache |
| Botão "Add Task" quebrado | Aspas sem fechar: `value="Add Task>` | `value="Add Task"` |
| Erro 500 na rota `/update` | `render_template('update.html', task)`: variável passada sem nome | `render_template('update.html', task=task)` |
| `No module named '_posixsubprocess'` no PythonAnywhere | Ambiente virtual criado com um Python diferente do usado pelo sistema | Recriar com `mkvirtualenv --python=$(which python3.11) flaskenv` |
| Site mostrando "Hello, World!" padrão | Arquivo WSGI ainda com o código de exemplo | Trocar o conteúdo do WSGI e clicar em **Reload** |
