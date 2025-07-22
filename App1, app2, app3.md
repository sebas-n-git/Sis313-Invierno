# Apps Node.js (app1, app2, app3): Instalación, Configuración y CRUD

## **1. Instalación de Node.js y npm**

En cada VM de aplicación (app1, app2, app3):

```bash
sudo apt update
sudo apt install curl -y
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node -v    # Para verificar la instalación
npm -v     # Para verificar la instalación
```

---

## **2. Instalación de Dependencias**

En el directorio del proyecto, inicializa npm e instala dependencias:

```bash
npm init -y
npm install express mysql2
```

---

## **3. Configuración de la Aplicación**

Crea el archivo `index.js` y personalízalo según la instancia (distintivo, color, puerto):

```javascript
const express = require('express');
const mysql = require('mysql2');
const os = require('os');
const app = express();
app.use(express.json());

// Personalización por app
const APP_NAME = process.env.APP_NAME || "App 1";
const APP_COLOR = process.env.APP_COLOR || "#00bfff"; // Cambia por app
const APP_PORT = process.env.APP_PORT || 3001;        // Cambia por app

const db = mysql.createConnection({
    host: '192.168.43.105',    // IP de MariaDB
    user: 'usuario',
    password: 'contraseña',
    database: 'compras'
});

db.connect((err) => {
    if (err) {
        console.error('Error al conectar con la base de datos:', err);
    } else {
        console.log('Conectado a MariaDB');
    }
});

app.get('/', (req, res) => {
    res.send(`
      <div style="font-family:sans-serif;">
        <h1 style="color:${APP_COLOR};">${APP_NAME}</h1>
        <p>IP de instancia: ${req.socket.localAddress} / Host: ${os.hostname()}</p>
        <a href="/items">Ver Items</a>
      </div>
    `);
});

// CRUD básico sobre "items"
app.get('/items', (req, res) => {
    db.query('SELECT * FROM items', (err, rows) => {
        if (err) return res.status(500).json({ error: err });
        res.json(rows);
    });
});

app.post('/items', (req, res) => {
    const { nombre } = req.body;
    db.query('INSERT INTO items (nombre) VALUES (?)', [nombre], (err, result) => {
        if (err) return res.status(500).json({ error: err });
        res.json({ id: result.insertId, nombre });
    });
});

app.put('/items/:id', (req, res) => {
    const { nombre } = req.body;
    db.query('UPDATE items SET nombre=? WHERE id=?', [nombre, req.params.id], (err) => {
        if (err) return res.status(500).json({ error: err });
        res.json({ id: req.params.id, nombre });
    });
});

app.delete('/items/:id', (req, res) => {
    db.query('DELETE FROM items WHERE id=?', [req.params.id], (err) => {
        if (err) return res.status(500).json({ error: err });
        res.json({ deleted: req.params.id });
    });
});

app.listen(APP_PORT, () => {
    console.log(`${APP_NAME} corriendo en puerto ${APP_PORT}`);
});
```

**Personaliza cada app:**
- app1: `APP_NAME="App 1"`, `APP_COLOR="#00bfff"`, `APP_PORT=3001`
- app2: `APP_NAME="App 2"`, `APP_COLOR="#32cd32"`, `APP_PORT=3002`
- app3: `APP_NAME="App 3"`, `APP_COLOR="#ff4500"`, `APP_PORT=3003`

---

## **4. Inicio de la Aplicación**

En cada VM, inicia la app con sus variables de entorno:

```bash
# app1
APP_NAME="App 1" APP_COLOR="#00bfff" APP_PORT=3001 node index.js

# app2
APP_NAME="App 2" APP_COLOR="#32cd32" APP_PORT=3002 node index.js

# app3
APP_NAME="App 3" APP_COLOR="#ff4500" APP_PORT=3003 node index.js
```

Verifica que en la consola aparezca:
```
App 1 corriendo en puerto 3001
App 2 corriendo en puerto 3002
App 3 corriendo en puerto 3003
```

---

## **5. Conexión a la Base de Datos**

Asegúrate de que la base de datos y la tabla existen:

```sql
CREATE DATABASE compras;
USE compras;
CREATE TABLE items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255) NOT NULL
);
```

La base debe aceptar conexiones remotas desde las apps.

---

## **6. CRUD Básico: Comandos y Pruebas**

**Crear un item:**
```bash
curl -X POST -H "Content-Type: application/json" -d '{"nombre":"Leche"}' http://192.168.43.101:3001/items
```

**Listar items:**
```bash
curl http://192.168.43.101:3001/items
```

**Actualizar un item:**
```bash
curl -X PUT -H "Content-Type: application/json" -d '{"nombre":"Yogurt"}' http://192.168.43.101:3001/items/1
```

**Eliminar un item:**
```bash
curl -X DELETE http://192.168.43.101:3001/items/1
```

Repite cambiando la IP y el puerto para app2 y app3.

---

**Accede por navegador a cada app para ver:**
- El nombre y color distintivo de la app
- IP y hostname de la instancia
- Acceso al CRUD funcional conectado a la base MariaDB

---
