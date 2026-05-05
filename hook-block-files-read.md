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
