# Notificación al finalizar una tarea en Claude Code

Este ejemplo permite mostrar una alerta cuando Claude Code termina una tarea usando el evento `Stop` de los hooks.

---

## macOS

Comando para probar directamente en la terminal:

```bash
osascript -e 'display alert "Claude Code" message "Claude terminó la tarea 🚀"'
```

Configuración del hook en macOS:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display alert \"Claude Code\" message \"Claude terminó la tarea 🚀\"'"
          }
        ]
      }
    ]
  }
}
```

---

## Windows

Equivalente en Windows usando PowerShell:

```powershell
powershell -Command "Add-Type -AssemblyName System.Windows.Forms; [System.Windows.Forms.MessageBox]::Show('Claude terminó la tarea 🚀','Claude Code')"
```

Configuración del hook en Windows:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "powershell -Command \"Add-Type -AssemblyName System.Windows.Forms; [System.Windows.Forms.MessageBox]::Show('Claude terminó la tarea 🚀','Claude Code')\""
          }
        ]
      }
    ]
  }
}
```

---

## Dónde configurar el hook

Puedes agregar esta configuración en el archivo de configuración de Claude Code:

```bash
.claude/settings.json
```

También puedes usar la configuración global del usuario:

```bash
~/.claude/settings.json
```

---

## Resultado esperado

Cuando Claude Code termine una tarea, se mostrará una alerta con el mensaje:

```text
Claude terminó la tarea 🚀
```
