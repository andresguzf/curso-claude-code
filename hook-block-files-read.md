# Hook único para bloquear acceso a archivos sensibles (users.json)

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

  const toolArgs = JSON.parse(Buffer.concat(chunks).toString());

  // readPath is the path to the file that Claude is trying to read
  const readPath =
    toolArgs.tool_input?.file_path ||
    toolArgs.tool_input?.path ||
    "";

  // ensure Claude isn't trying to read the .env file
  if (readPath.includes('users.json')) {
    console.error("You cannot read the .env file");
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
