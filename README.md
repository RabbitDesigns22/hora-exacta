# hora-exacta

Skill para que Claude sepa siempre la fecha y la hora reales, en vez de adivinarlas.

Somos Rabbit Designs, una agencia que trabaja con negocios de México, Perú, Costa Rica, Venezuela y Florida. Nuestros chats de Claude programan publicaciones, escriben reportes y arman semanas de trabajo. Cuando adivinaban la fecha se equivocaban de día. Esta skill lo arregla.

## Instalar

```bash
npx skills add RabbitDesigns22/hora-exacta -g -y
```

Después agrega el hook de `skills/hora-exacta/SKILL.md` para que cada mensaje llegue con la fecha y la hora.

## Probar

```bash
python skills/hora-exacta/scripts/hora.py
```

Licencia MIT.
