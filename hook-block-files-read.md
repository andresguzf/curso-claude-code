# Hook único para bloquear acceso a archivos sensibles (users.json)

Este script intercepta tanto `Read` como `Grep` usando un solo hook evitando el acceso al archivo users.json:
```json
[
  {
    "name": "Juan",
    "lastname": "Pérez",
    "email": "juan.perez@example.com",
    "password": "$2b$10$wH8KzQ1Yv1c9bq8y5ZQ7VuZqZ1Qx7pF7W2yJc6s8G4L5r8Vj2k3aK",
    "created_at": "2026-05-01T10:15:30Z",
    "updated_at": "2026-05-01T10:15:30Z"
  },
  {
    "name": "María",
    "lastname": "González",
    "email": "maria.gonzalez@example.com",
    "password": "$2b$10$YkP3Fz9dL6h2s8TqR4xVFeZy7m8N1k2Qw3E5r6T7y8U9i0O1p2aB",
    "created_at": "2026-05-01T11:20:10Z",
    "updated_at": "2026-05-01T11:20:10Z"
  },
  {
    "name": "Carlos",
    "lastname": "Ramírez",
    "email": "carlos.ramirez@example.com",
    "password": "$2b$10$Lk9J8H7G6F5D4S3A2P1OqWzXyVbNmCkLjHgFdSaQwErTyUiOpAsD",
    "created_at": "2026-05-01T12:05:45Z",
    "updated_at": "2026-05-01T12:05:45Z"
  },
  {
    "name": "Ana",
    "lastname": "Torres",
    "email": "ana.torres@example.com",
    "password": "$2b$10$QwErTyUiOpAsDfGhJkLzXcVbNm1234567890poiUYTREWqASDFG",
    "created_at": "2026-05-01T13:30:22Z",
    "updated_at": "2026-05-01T13:30:22Z"
  },
  {
    "name": "Luis",
    "lastname": "Martínez",
    "email": "luis.martinez@example.com",
    "password": "$2b$10$ZxCvBnMqWeRtYuIoPaSdFgHjKlQwErTyUiOpAsDfGhJkLzXcVb",
    "created_at": "2026-05-01T14:10:05Z",
    "updated_at": "2026-05-01T14:10:05Z"
  },
  {
    "name": "Sofía",
    "lastname": "López",
    "email": "sofia.lopez@example.com",
    "password": "$2b$10$AsDfGhJkLzXcVbNmQwErTyUiOpZxCvBnMqWeRtYuIoPaSdFgHj",
    "created_at": "2026-05-01T15:45:18Z",
    "updated_at": "2026-05-01T15:45:18Z"
  },
  {
    "name": "Diego",
    "lastname": "Fernández",
    "email": "diego.fernandez@example.com",
    "password": "$2b$10$MnBvCxZlKjHgFdSaQwErTyUiOpAsDfGhJkLzXcVbNmQwErTyU",
    "created_at": "2026-05-01T16:25:50Z",
    "updated_at": "2026-05-01T16:25:50Z"
  },
  {
    "name": "Valentina",
    "lastname": "Rojas",
    "email": "valentina.rojas@example.com",
    "password": "$2b$10$Q1W2E3R4T5Y6U7I8O9P0AsDfGhJkLzXcVbNmQwErTyUiOpAsD",
    "created_at": "2026-05-01T17:05:33Z",
    "updated_at": "2026-05-01T17:05:33Z"
  },
  {
    "name": "Pedro",
    "lastname": "Castillo",
    "email": "pedro.castillo@example.com",
    "password": "$2b$10$Z9X8C7V6B5N4M3Q2W1E0RtyUiOpAsDfGhJkLzXcVbNmQwErTyU",
    "created_at": "2026-05-01T18:40:12Z",
    "updated_at": "2026-05-01T18:40:12Z"
  },
  {
    "name": "Camila",
    "lastname": "Herrera",
    "email": "camila.herrera@example.com",
    "password": "$2b$10$TyUiOpAsDfGhJkLzXcVbNmQwErTyUiOpAsDfGhJkLzXcVbNmQw",
    "created_at": "2026-05-01T19:55:00Z",
    "updated_at": "2026-05-01T19:55:00Z"
  }
]
```

---

## 1. Script único

Crear archivo:

```bash
hooks/block_files_hook.js
```

Contenido:

```js
async function main() {
  const chunks = [];

  for await (const chunk of process.stdin) {
    chunks.push(chunk);
  }

  const input = JSON.parse(Buffer.concat(chunks).toString());

  // Block Read/Grep tool calls (PreToolUse event)
  const readPath =
    input.tool_input?.file_path ||
    input.tool_input?.path ||
    "";

  if (readPath.includes("users.json")) {
    console.error("You cannot read the users.json file");
    process.exit(2);
  }
}

main();

```

---

## 2. Configuración en Claude Code

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Read|Grep",
        "hooks": [
          {
            "type": "command",
            "command": "node ./hooks/block_files_hook.js"
          }
        ]
      }
    ]
  }
}
```

---

## 3. Qué bloquea exactamente

Este hook evita:

- Leer `users.json`
- Buscar (`grep`) dentro de `users.json`

---

Con esto ya tienes un **firewall básico de archivos sensibles dentro de Claude Code** 🔥
