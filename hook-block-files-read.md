# Hook único para bloquear acceso a archivos sensibles (.env)

Este script intercepta tanto `Read` como `Grep` usando un solo hook.

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

  const toolInput = input.tool_input || {};

  // Unificamos todos los posibles campos donde podría venir la ruta
  const values = [
    toolInput.file_path,
    toolInput.path,
    toolInput.glob,
    toolInput.pattern
  ]
    .filter(Boolean)
    .join(" ")
    .toLowerCase();

  const blocked = [
    ".env",
    ".env.local",
    ".env.development",
    ".env.production",
    "users.json"
  ];

  const isBlocked = blocked.some((file) =>
    values.includes(file.toLowerCase())
  );

  if (isBlocked) {
    console.error("🚫 Acceso bloqueado: archivos .env están protegidos");
    process.exit(2);
  }

  process.exit(0);
}

main().catch((err) => {
  console.error(err.message);
  process.exit(2);
});
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

- Leer `.env`
- Buscar (`grep`) dentro de `.env`
- Cualquier variante:
  - `.env.local`
  - `.env.production`
  - `.env.*`

---

## 4. Mejora opcional (más estricto 🔒)

Si quieres bloquear cualquier archivo que CONTENGA `.env` en el path:

```js
const isBlocked = values.includes(".env");
```

---

## 5. Tip

Puedes extender esto fácil:

```js
const blocked = [
  ".env",
  "secrets.json",
  "private.key",
  "id_rsa"
];
```

---

Con esto ya tienes un **firewall básico de archivos sensibles dentro de Claude Code** 🔥
