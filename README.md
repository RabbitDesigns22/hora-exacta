# hora-exacta

Skill para que Claude sepa siempre la fecha y la hora reales, en vez de adivinarlas.

Somos Rabbit Designs, una agencia que trabaja con negocios de México, Perú, Costa Rica, Venezuela y Florida. Nuestros chats de Claude programan publicaciones, escriben reportes y arman semanas de trabajo. Cuando adivinaban la fecha se equivocaban de día. Esta skill lo arregla.

## Instalar

```bash
npx skills add RabbitDesigns22/hora-exacta -g -y
```

Después agrega el hook de `skills/hora-exacta/SKILL.md` para que cada mensaje llegue con la fecha y la hora.

## Dónde estás (automático)

Cada mensaje trae también dónde estás, para que Claude no se pierda ni en el tiempo ni en el lugar:

- **Ciudad aproximada según tu IP** (servicio gratis [ipwho.is](https://ipwho.is), sin clave). Se consulta como mucho cada 6 horas y se guarda en `~/.claude/hora-exacta-ip.json`. Sin internet usa lo último guardado. Con VPN dirá la ciudad de la VPN. Para no consultarla, usa `--sin-ip`.
- **El huso horario de tu computadora**, sin internet. Si viajas y tu computadora cambia de hora, lo sigue.
- **Una nota tuya** (opcional) en `~/.claude/hora-exacta-lugar.txt`, en una línea.

También sale siempre la semana en curso (de lunes a domingo).

## Probar

```bash
python skills/hora-exacta/scripts/hora.py
```

Licencia MIT.
