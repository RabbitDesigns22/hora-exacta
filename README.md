# hora-exacta

**Que tu Claude nunca se pierda ni en el tiempo ni en el lugar.**

Claude no tiene reloj. Si adivina la fecha, se equivoca de día, de semana o hasta de año, y eso sale caro: una publicación programada el día que no era, un reporte con la fecha mal, un "mañana" que ya pasó.

Con esta skill, **cada mensaje que le mandas a Claude llega ya con la fecha, la hora, la semana en curso y el lugar donde estás**, sin que tengas que decírselo ni que él lo pregunte.

La hicimos en [Rabbit Designs](https://rabbit-designs.com) para nuestro equipo de más de 15 chats de Claude, que trabajan con negocios de México, Perú, Costa Rica, Venezuela y Florida.

---

## Qué ve Claude en cada mensaje

```text
FECHA Y HORA REALES AHORA (no las adivines ni uses otra): CDMX domingo 1 de noviembre de 2026, 14:50 · Lima domingo 1 de noviembre de 2026, 15:50 · Costa Rica domingo 1 de noviembre de 2026, 14:50 · Caracas domingo 1 de noviembre de 2026, 16:50 · Florida domingo 1 de noviembre de 2026, 15:50 · UTC 2026-11-01 20:50. Dónde: Ubicación según la IP (aprox.): Puebla, Estado de Puebla, Mexico (zona America/Mexico_City). Reloj de esta computadora: UTC-6. Nota: … Semana en curso: del lunes 26 de octubre al domingo 1 de noviembre; la próxima empieza el lunes 2 de noviembre.
```

| Dato | De dónde sale |
|---|---|
| Fecha y hora en 5 zonas + UTC | El reloj de tu computadora. Sin internet. |
| Semana en curso (lunes a domingo) | Se calcula con la hora de la primera zona (CDMX). |
| Ciudad aproximada | Tu IP pública, con el servicio gratis [ipwho.is](https://ipwho.is). |
| Huso horario de tu computadora | Tu sistema. Si viajas y la computadora cambia de hora, lo sigue. |
| Nota (opcional) | Lo que tú escribas en `~/.claude/hora-exacta-lugar.txt`. |

---

## Instalar (2 pasos)

Necesitas [Claude Code](https://claude.com/claude-code) y Python 3.9 o más nuevo.

### 1. Instala la skill

```bash
npx skills add RabbitDesigns22/hora-exacta -g -y
```

Queda en `~/.claude/skills/hora-exacta/`. Con esto Claude ya puede correr el script cuando le preguntes la hora.

### 2. Agrega el hook (para que llegue en cada mensaje)

Abre `~/.claude/settings.json` y agrega esto dentro de `"hooks"`. Si ya tienes otros hooks en `UserPromptSubmit`, agrega solo el objeto `{ "type": "command", ... }` a su lista.

**Mac y Linux:**

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 ~/.claude/skills/hora-exacta/scripts/hora.py --corto",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

**Windows** (con PowerShell, para que los acentos salgan bien; cambia `TU_USUARIO`):

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "[Console]::OutputEncoding = [Text.Encoding]::UTF8; python 'C:\\Users\\TU_USUARIO\\.claude\\skills\\hora-exacta\\scripts\\hora.py' --corto",
            "shell": "powershell",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

Abre un chat nuevo y escribe cualquier cosa: Claude ya sabe qué día y qué hora es, y dónde estás.

### Probarlo a mano

```bash
python ~/.claude/skills/hora-exacta/scripts/hora.py
```

---

## Opciones

| Opción | Qué hace |
|---|---|
| *(sin nada)* | Una línea por zona. Para leerlo tú. |
| `--corto` | Todo en una sola línea. Es la que usa el hook. |
| `--sin-ip` | No consulta la IP: no sale nada a internet. |

**Tu nota de lugar (opcional).** Crea `~/.claude/hora-exacta-lugar.txt` con una línea, por ejemplo:

```text
Vivo y trabajo en Puebla, México. Mis clientes están en México y Perú.
```

Esa línea aparece en cada mensaje, después de "Nota:".

---

## Privacidad

- La hora y la semana salen de tu computadora. No usan internet.
- La ciudad sale de tu IP pública: el script le pregunta a ipwho.is **como mucho una vez cada 6 horas** y guarda la respuesta en `~/.claude/hora-exacta-ip.json`. No manda nada más.
- La ciudad es **aproximada**: a veces marca la ciudad de tu compañía de internet, y con VPN marca la de la VPN.
- Sin internet, usa la última ciudad guardada. Si no quieres que consulte nada, usa `--sin-ip`.

---

## Cómo funciona por dentro

- Un solo archivo, `skills/hora-exacta/scripts/hora.py`, sin paquetes extra.
- Usa `zoneinfo` de Python. En Windows sin la base de zonas (`tzdata`) usa desfases fijos: México, Perú, Costa Rica y Venezuela no cambian de hora, y el horario de verano de Florida (del 2.º domingo de marzo al 1.er domingo de noviembre) se calcula aparte.
- Si la consulta de IP tarda más de 2 segundos, se salta. El hook entero tarda menos de 1 segundo.

**¿Otras ciudades?** Agrégalas a la lista `ZONAS` al inicio de `hora.py`, con su nombre, su zona y su desfase fijo:

```python
ZONAS = [
    ("CDMX", "America/Mexico_City", -6),
    ("Bogotá", "America/Bogota", -5),
    ...
]
```

Si la ciudad cambia de horario en verano (Madrid, Nueva York…), instala la base de zonas con `pip install tzdata` para que la hora salga bien todo el año. La primera zona de la lista es la que manda para calcular la semana en curso.

---

Hecha por [Eduardo Suárez](https://github.com/RabbitDesigns22) · Rabbit Designs · Licencia MIT.
También te puede servir [one-pass](https://github.com/RabbitDesigns22/one-pass): que Claude haga las cosas bien a la primera.

## More skills from the same setup

These come from one real workflow: a small agency run with 15+ Claude Code sessions in parallel. Each skill fixes one problem that kept happening there.

| Skill | What it fixes |
|---|---|
| [one-pass](https://github.com/RabbitDesigns22/one-pass) | get it right the first time; stop paying tokens for rework |
| [hands-on](https://github.com/RabbitDesigns22/hands-on) | the agent does what it can and only asks you for codes, keys and payments |
| [easy-for-them](https://github.com/RabbitDesigns22/easy-for-them) | steps people actually follow: direct link, one action per line |
| [one-answer](https://github.com/RabbitDesigns22/one-answer) | one thought-through answer instead of plan, fix, new plan |
| [never-twice](https://github.com/RabbitDesigns22/never-twice) | a shared error log so no session repeats a known mistake |
| [report-now](https://github.com/RabbitDesigns22/report-now) | status checked live this minute, stamped with the time |
| [sort-me-out](https://github.com/RabbitDesigns22/sort-me-out) | one ordered list of what is urgent, waiting and forgotten |
| [in-the-loop](https://github.com/RabbitDesigns22/in-the-loop) | a live board so parallel sessions know what the others changed |
| [study-first](https://github.com/RabbitDesigns22/study-first) | reads everything in full and answers point by point, no invented details |
| [session-watchdog](https://github.com/RabbitDesigns22/session-watchdog) | wakes the sessions that got stuck mid-task, leaves the rest alone |
| [skill-harvest](https://github.com/RabbitDesigns22/skill-harvest) | reads your sessions and finds the skills you are missing |
| [small-budget-ads](https://github.com/RabbitDesigns22/small-budget-ads) | Meta Ads rules for 1-5 USD a day ad sets |
| [whatsapp-closer](https://github.com/RabbitDesigns22/whatsapp-closer) | evidence-ranked rules for a WhatsApp sales assistant that closes |
| [whatsapp-number-onboarding](https://github.com/RabbitDesigns22/whatsapp-number-onboarding) | connect a number to the WhatsApp Cloud API in the safe order |
| [proposal-pdf](https://github.com/RabbitDesigns22/proposal-pdf) | short PDF proposals with every price checked against its source |
| [published-vs-planned](https://github.com/RabbitDesigns22/published-vs-planned) | says "published" only for what it saw live on the public page, Facebook and Instagram checked apart |
| [source-every-fact](https://github.com/RabbitDesigns22/source-every-fact) | every price, phone and name on a post traced to its source, nothing added |

If one of them saved you a redo, a star helps other people find it.
