# Mindset

No es un archivo motivacional. Es la diferencia entre alguien que ejecuta comandos
y alguien que entiende lo que está haciendo.

---

## Pentester vs script kiddie

Un script kiddie corre herramientas y espera que algo explote.
Un pentester entiende qué busca antes de abrir la terminal.

La diferencia no es técnica. Es de mentalidad.

```
Script kiddie                       Pentester profesional
────────────────────────────        ────────────────────────────────
Corre nmap contra todo              Define el scope antes de escanear
Busca exploits por versión          Entiende por qué esa versión es vulnerable
Espera que algo falle               Modela qué debería fallar y por qué
Omite lo que no entiende            Investiga lo que no entiende
No documenta                        Documenta desde el primer comando
Celebra cuando "entra"              Celebra cuando el reporte genera cambio real
```

El objetivo de un pentest no es entrar. Es demostrar el impacto
de lo que encontraste de forma que el cliente pueda actuar sobre eso.
Sin reporte, sin impacto. Sin impacto, sin valor profesional.

---

## Qué buscar en cada fase

Cada fase del pentest tiene una pregunta central.
Si no podés responderla al terminar la fase, no terminaste la fase.

```
FASE            PREGUNTA CENTRAL
──────────────────────────────────────────────────────────────────
Recon pasivo    ¿Qué superficie tiene expuesta este objetivo
                sin que sepa que lo estoy mirando?

Recon activo    ¿Qué servicios corren, en qué versiones,
                y cuáles tienen configuración débil?

Acceso          ¿Cuál es el camino de menor resistencia
                para obtener un foothold?

Post-explotación ¿Hasta dónde puedo llegar desde donde estoy
                 y qué impacto real tendría esto en el negocio?

Reporte         ¿Puede el cliente entender qué encontré,
                por qué importa, y qué hacer al respecto?
```

No avances de fase hasta poder responder la pregunta de la fase actual.
Apurar el recon para llegar al exploit es la forma más rápida
de perderte el 70% de la superficie de ataque.

---

## Cómo priorizar — el tiempo es finito

En un engagement real tenés un scope, un tiempo, y un cliente esperando.
No podés hacer todo. Necesitás decidir qué hace más daño primero.

**Regla de priorización:**

```
1. Lo que tiene mayor probabilidad de impacto crítico
2. Lo que es más fácil de explotar (menor esfuerzo, mayor resultado)
3. Lo que afecta más activos o más usuarios
4. Todo lo demás
```

En la práctica:

- Un subdominio de staging con framework desactualizado
  vale más que diez puertos abiertos con servicios actualizados.
- Un archivo `.env` expuesto con credenciales
  vale más que una hora de fuerza bruta.
- Un empleado con contraseña filtrada en HaveIBeenPwned
  vale más que buscar CVEs en el servidor web.

**Aprendé a parar.** Cuando algo no avanza después de varios intentos razonables,
anotalo, marcalo para revisitar, y seguí. El rabbit hole es el enemigo del tiempo.

---

## Cuando algo no funciona

Va a pasar. Siempre. La pregunta es qué hacés con eso.

**Lo que no hacés:**
- Abandonar y pasar a otra cosa sin entender por qué falló
- Correr la misma herramienta diez veces esperando un resultado diferente
- Asumir que si la herramienta no encontró nada, no hay nada

**Lo que hacés:**

```
1. Entender qué debería haber pasado y por qué no pasó
2. Verificar que el comando / la herramienta / el target están bien
3. Probar con una herramienta distinta que resuelve lo mismo
4. Documentar: "intenté X contra Y, resultado Z, posible causa W"
5. Si sigue sin avanzar: anotar y continuar con otra superficie
```

Un `Transfer failed.` en una transferencia de zona no es un fracaso —
es un dato. El servidor está bien configurado. Eso va al reporte.
Un Shodan que no devuelve resultados para una empresa
puede significar que usan IPs de terceros o CDN. También es un dato.

Los resultados negativos son resultados. Documentalos.

---

## Por qué documentar desde el primer comando

No desde que "encontraste algo interesante". Desde el primer comando.

**Razón 1 — el reporte se escribe con evidencia, no con memoria.**
Lo que no está documentado no existió. Si encontraste algo a las 22:00
y no lo anotaste, a las 10:00 del día siguiente vas a reconstruir
de memoria un hallazgo que tenías que presentar con precisión.

**Razón 2 — los falsos negativos también importan.**
El cliente a veces pregunta: "¿buscaron X?" Si tenés el log,
podés responder con evidencia. Si no, tenés que confiar en tu memoria
frente a alguien que pagó por una auditoría.

**Razón 3 — el ambiente cambia.**
Un servicio que estaba corriendo cuando hiciste el recon
puede no estar cuando vayas a explotarlo. Si no lo documentaste,
perdiste el dato.

**Razón 4 — tu reputación profesional.**
Un reporte bien documentado con evidencia clara y reproducible
es lo que diferencia un entregable profesional de un documento genérico.
Es lo que hace que el cliente te vuelva a contratar.

**Cómo documentar sin volverte loco:**

```bash
# Estructura mínima para cada engagement
mkdir recon_TARGET
cd recon_TARGET

# Un archivo de notas en texto plano — siempre abierto
touch notas.md

# Redirigir output de herramientas a archivos
whois TARGET.com > whois.txt
subfinder -d TARGET.com -silent -o subs.txt

# Capturas para hallazgos visuales (Shodan, Dorks, paneles)
# Screenshot + nombre descriptivo:
# shodan_apache_port80_TARGET.png
# google_dork_indexof_TARGET.png
```

No hace falta un sistema complejo. Hace falta consistencia.
El hábito de documentar todo desde el minuto uno
es lo que separa a los profesionales de los que "estaban haciendo algo
pero no saben bien qué".

---

## Una última cosa

Este curso te va a dar herramientas, técnicas y metodología.
Lo que no te puede dar es el criterio para usarlas bien.

El criterio se construye con práctica deliberada:
preguntándote por qué antes de ejecutar,
entendiendo qué pasó después de ejecutar,
y siendo honesto con vos mismo cuando no sabés algo.

No hay atajos para eso.

---

*Este archivo aplica a todos los módulos del curso.*
*Si algo de lo que dice acá te parece discutible, bien — discutilo en clase.*
