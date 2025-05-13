# ✅ Checklist Back-End - To-do List com Node.js + PostgreSQL

---

## 📦 1. Preparação do Projeto
- [ ] Criar uma pasta para o projeto
- [ ] Rodar `npm init -y` para iniciar o package.json
- [ ] Instalar dependências principais:
  - `npm install express pg`
- [ ] Instalar dependência de desenvolvimento:
  - `npm install --save-dev nodemon`

---

## 🗂️ 2. Estrutura de Pastas
- [ ] Criar as pastas:
  - controller/
  - database/
  - models/
  - routes/
  - views/
  - public/
- [ ] Criar os arquivos principais:
  - `index.js`
  - `routes/routes.js`
  - `controller/TaskController.js`
  - `database/db.js`
  - `views/index.ejs`
  - `public/Style.css`
  - `public/Script.js`

---

## 🧩 3. Banco de Dados
- [ ] Criar banco no PostgreSQL (ex: `todolist`)
- [ ] Criar tabela:
  ```sql
  CREATE TABLE tasks (
    id SERIAL PRIMARY KEY,
    task_name TEXT NOT NULL
  );
---
  ## 4. Arquivo de Conexão (database/db.js)
 ```js
const { Pool } = require('pg');

const pool = new Pool({
  user: 'seu_usuario',
  host: 'localhost',
  database: 'todolist',
  password: 'sua_senha',
  port: 5432
});

module.exports = pool;
```
  ## 5. Controller de Tarefas (controller/TaskController.js)
```js
const pool = require('../database/db');

const getTasks = (req, res) => {
  pool.query('SELECT * FROM tasks', (err, result) => {
    if (err) return res.status(500).json({ message: 'Erro no banco de dados' });
    res.status(200).json(result.rows);
  });
};

const createTask = (req, res) => {
  const { task_name } = req.body;
  pool.query('INSERT INTO tasks (task_name) VALUES ($1) RETURNING *', [task_name], (err, result) => {
    if (err) return res.status(500).json({ message: 'Erro ao inserir tarefa' });
    res.status(201).json(result.rows[0]);
  });
};

module.exports = { getTasks, createTask };


```

  ## 6. Rotas(routes/routes.js)
```js
const express = require('express');
const router = express.Router();
const TaskController = require('../controller/TaskController');

router.get('/tarefas', TaskController.getTasks);
router.post('/tarefas', TaskController.createTask);

router.get('/', (req, res) => {
  res.render('index');
});

module.exports = router;

```
 ## 7.Servidor Principal(index.js)
 ```js
const express = require('express');
const app = express();
const path = require('path');
const routes = require('./routes/routes');

app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

app.use(routes);

app.listen(3000, () => {
  console.log('Servidor rodando em http://localhost:3000');
});

```
## ✅ 8. Testar Back-end

### ▶️ Rodar o servidor
```bash
npx nodemon index.js
Abra o navegador e vá para:
http://localhost:3000


```
##9. Frond-end básico(views/index.ejs)
```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>To Do List</title>
</head>
<body>
  <h1>Adicionar Tarefa</h1>
  <form id="form-tarefa">
    <input type="text" id="nome" placeholder="Nome da tarefa" required>
    <button type="submit">Adicionar</button>
  </form>

  <script>
    document.getElementById('form-tarefa').addEventListener('submit', async (e) => {
      e.preventDefault();
      const nome = document.getElementById('nome').value;

      const response = await fetch('/tarefas', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ task_name: nome })
      });

      const data = await response.json();
      console.log('Tarefa adicionada:', data);
    });
  </script>
</body>
</html>


```
