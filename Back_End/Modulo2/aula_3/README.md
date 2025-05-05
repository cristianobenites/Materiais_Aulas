# 📘 Subir o Projeto Node.js MVC com PostgreSQL

Este projeto demonstra a estrutura básica de uma aplicação Node.js utilizando o padrão MVC (Model-View-Controller) com conexão a um banco de dados PostgreSQL.

O objetivo é fornecer um exemplo simples e didático, ideal para iniciantes em backend e APIs REST.

---

## ✅ Como Executar

### 1. Clone o repositório

```bash
git clone https://github.com/afonsobrandaointeli/mvc-boilerplate.git
cd mvc-boilerplate
```

### 2. Instale as dependências

```bash
npm install
npm init -y
npm install express ejs
```

### 3. Configure o banco de dados PostgreSQL

Crie um banco chamado `alunosdb` e adicione as seguintes configurações em um arquivo `.env`:

```env
DB_USER=postgres
DB_HOST=localhost
DB_DATABASE=alunosdb
DB_PASSWORD=1234
DB_PORT=5432
```

---

## 🧱 Estrutura do Projeto

```text
mvc-boilerplate-main/
├── .env
├── .gitignore
├── jest.config.js
├── package-lock.json
├── package.json
├── readme.md
├── rest.http
├── server.js
├── assets/
│   └── favicon.ico
├── config/
│   └── db.js
├── controllers/
│   └── userController.js
├── documentos/
│   └── wad.md
├── models/
│   └── userModel.js
```

---

## 🧩 Explicação de Cada Parte

### `server.js` - Ponto de entrada do servidor (arquivo vazio).

Este arquivo inicia o servidor Express e define as rotas disponíveis.

---

### `config/db.js` - Conexão com o banco de dados PostgreSQL

Este módulo utiliza a biblioteca `pg` para conectar ao banco usando variáveis do `.env`.

```javascript
const { Pool } = require('pg');
require('dotenv').config();

const pool = new Pool({
  host: process.env.DB_HOST,
  port: process.env.DB_PORT,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_DATABASE,
  ssl: {
    rejectUnauthorized: false
  }
});

module.exports = pool;

```

---

### `models/userModel.js` - Modelagem e consultas no banco

Define funções para buscar e criar usuários diretamente no banco.

```javascript
const db = require('../config/db');

class User {
  static async getAll() {
    const result = await db.query('SELECT * FROM users');
    return result.rows;
  }

  static async getById(id) {
    const result = await db.query('SELECT * FROM users WHERE id = $1', [id]);
    return result.rows[0];
  }

  static async create(data) {
    const result = await db.query(
      'INSERT INTO users (name, email) VALUES ($1, $2) RETURNING *',
      [data.name, data.email]
    );
    return result.rows[0];
  }

  static async update(id, data) {
    const result = await db.query(
      'UPDATE users SET name = $1, email = $2 WHERE id = $3 RETURNING *',
      [data.name, data.email, id]
    );
    return result.rows[0];
  }

  static async delete(id) {
    const result = await db.query('DELETE FROM users WHERE id = $1 RETURNING *', [id]);
    return result.rowCount > 0;
  }
}

module.exports = User;
```

---

### `controllers/userController.js` - Lógica das requisições

Intermedia o que chega pela requisição e o que será feito com os dados.

```javascript
// controllers/userController.js

const userService = require('../services/userService');

const getAllUsers = async (req, res) => {
  try {
    const users = await userService.getAllUsers();
    res.status(200).json(users);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

const getUserById = async (req, res) => {
  try {
    const user = await userService.getUserById(req.params.id);
    if (user) {
      res.status(200).json(user);
    } else {
      res.status(404).json({ error: 'Usuário não encontrado' });
    }
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

const createUser = async (req, res) => {
  try {
    const { name, email } = req.body;
    const newUser = await userService.createUser(name, email);
    res.status(201).json(newUser);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

const updateUser = async (req, res) => {
  try {
    const { name, email } = req.body;
    const updatedUser = await userService.updateUser(req.params.id, name, email);
    if (updatedUser) {
      res.status(200).json(updatedUser);
    } else {
      res.status(404).json({ error: 'Usuário não encontrado' });
    }
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

const deleteUser = async (req, res) => {
  try {
    const deletedUser = await userService.deleteUser(req.params.id);
    if (deletedUser) {
      res.status(200).json(deletedUser);
    } else {
      res.status(404).json({ error: 'Usuário não encontrado' });
    }
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

module.exports = {
  getAllUsers,
  getUserById,
  createUser,
  updateUser,
  deleteUser
};
```

---

### `init.sql` - Criação da tabela no banco

Script SQL que pode ser executado diretamente no banco para criar a tabela `usuarios`.

```sql
Erro ao ler: [Errno 2] No such file or directory: 'mvc-boilerplate-main/init.sql'
```

---

### `runSQLScripts.js` - Executa o SQL automaticamente via Node

Roda o script `init.sql` automaticamente usando Node.js.

```javascript
-- init.sql

-- Criar extensão para suportar UUIDs, se ainda não estiver ativada
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Criar tabela de usuários com UUID como chave primária
CREATE TABLE IF NOT EXISTS users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL
);

-- Inserir 20 usuários com nomes e emails aleatórios
INSERT INTO users (name, email)
VALUES 
  ('Alice Smith', 'alice.smith@example.com'),
  ('Bob Johnson', 'bob.johnson@example.com'),
  ('Carol Williams', 'carol.williams@example.com'),
  ('David Jones', 'david.jones@example.com'),
  ('Emma Brown', 'emma.brown@example.com'),
  ('Frank Davis', 'frank.davis@example.com'),
  ('Grace Wilson', 'grace.wilson@example.com'),
  ('Henry Moore', 'henry.moore@example.com'),
  ('Isabella Taylor', 'isabella.taylor@example.com'),
  ('Jack Lee', 'jack.lee@example.com'),
  ('Kate Clark', 'kate.clark@example.com'),
  ('Liam Martinez', 'liam.martinez@example.com'),
  ('Mia Rodriguez', 'mia.rodriguez@example.com'),
  ('Noah Garcia', 'noah.garcia@example.com'),
  ('Olivia Hernandez', 'olivia.hernandez@example.com'),
  ('Patrick Martinez', 'patrick.martinez@example.com'),
  ('Quinn Lopez', 'quinn.lopez@example.com'),
  ('Rose Thompson', 'rose.thompson@example.com'),
  ('Samuel Perez', 'samuel.perez@example.com'),
  ('Tara Scott', 'tara.scott@example.com');
```

---

## 🧪 Teste com REST Client

Use o VSCode com a extensão REST Client para executar requisições via o arquivo `rest.http`.

---

## ▶️ Execução do Projeto

Após tudo configurado:

```bash
node server.js
```

Saída esperada:

```
Servidor rodando em http://localhost:3000
```

---

## 🧠 Observação Didática

- **Model** → A camada que acessa diretamente o banco de dados.
- **Controller** → Recebe a requisição, executa lógica e responde.
- **Config** → Guarda configurações de conexão.
- **View** → Não está implementada neste exemplo, mas poderia ser feita com EJS.
