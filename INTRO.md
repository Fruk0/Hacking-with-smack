# Introducción al Curso

## Qué es el ethical hacking y para qué sirve

El ethical hacking es el proceso de atacar sistemas con permiso
para encontrar vulnerabilidades antes de que lo haga alguien sin permiso.

No es una disciplina defensiva. Es ofensiva — aplicada a favor del cliente.
Un pentester piensa, trabaja y ejecuta exactamente como un atacante.
La diferencia es el contrato, el scope, y el reporte al final.

Para qué sirve en el mundo real:

- Encontrar vulnerabilidades antes de que las explote alguien con malas intenciones
- Validar que los controles de seguridad funcionan bajo ataque real
- Cumplir requisitos regulatorios (PCI-DSS, ISO 27001, SOC 2)
- Dar al cliente evidencia concreta de qué arreglar y en qué orden
- Reducir el riesgo de un breach que puede costar millones

Lo que no es: buscar exploits en Google y correrlos contra un servidor.
Eso tiene nombre — y no es pentesting.

---

## Las fases reales de un pentest

Todo pentest profesional sigue este flujo, en este orden.
Saltarse fases no acelera el trabajo — lo arruina.

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   RECON      →    ACCESO    →    POST-EX    →    REPORTE        │
│   PASIVO          INICIAL                                       │
│     +                                                           │
│   RECON                                                         │
│   ACTIVO                                                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Reconocimiento pasivo**
Recolectar toda la información posible sobre el objetivo
sin enviarle un solo paquete. Fuentes públicas, DNS, OSINT.
El objetivo no sabe que existís.

**Reconocimiento activo**
Escanear, fingerprinting, enumeración de servicios.
Acá ya hay tráfico hacia el objetivo — requiere scope explícito.

**Acceso inicial**
Explotar vulnerabilidades para obtener un punto de entrada.
Puede ser técnico (CVE, misconfiguration) o humano (phishing).

**Post-explotación**
Desde el foothold: escalada de privilegios, movimiento lateral,
persistencia, exfiltración de evidencia de impacto.

**Reporte**
Documentar todo lo encontrado con evidencia, impacto de negocio,
y recomendaciones priorizadas. Sin esto, el pentest no existió.

---

## Los 9 pasos del proceso profesional

Esto es lo que diferencia un pentest de "hackear cosas":

```
 1. DEFINIR SCOPE        Qué está dentro, qué está fuera.
                         Sin scope definido no hay pentest — hay intrusión.

 2. REGLAS DE ENGAGEMENT Horarios permitidos, contactos de emergencia,
                         qué hacer si encontrás un breach real en curso.

 3. RECON PASIVO         Superficie del objetivo sin generar tráfico.
                         Whois, DNS, OSINT, subdominios, Shodan.

 4. RECON ACTIVO         Escaneo, enumeración, fingerprinting.
                         Nmap, Nessus, identificación de versiones.

 5. ANÁLISIS             Procesar lo recopilado. Identificar vectores
                         de ataque probables. Priorizar.

 6. EXPLOTACIÓN          Atacar las vulnerabilidades identificadas.
                         Documentar cada intento — exitoso o no.

 7. POST-EXPLOTACIÓN     Demostrar impacto real desde el acceso obtenido.
                         Escalada, movimiento lateral, exfiltración controlada.

 8. LIMPIEZA             Eliminar artefactos, backdoors y accesos creados.
                         El sistema queda en el estado en que estaba.

 9. REPORTE              Executive summary + hallazgos técnicos + evidencia
                         + impacto de negocio + recomendaciones priorizadas.
```

El reporte no es el paso final — es el producto.
Todo lo que hiciste antes existe para construir ese documento.

---

## Hacker, pentester, script kiddie

Tres palabras que la gente usa como si fueran intercambiables. No lo son.

**Hacker**
Alguien que entiende sistemas en profundidad y puede usarlos
de formas que sus creadores no anticiparon.
No implica ilegalidad. No implica malicia. Es una habilidad.

**Pentester**
Un hacker que trabaja con autorización explícita de un cliente,
sigue una metodología documentada, y entrega un reporte al final.
Es una profesión con responsabilidades legales y éticas concretas.

**Script kiddie**
Alguien que corre herramientas sin entender qué hacen,
busca exploits por versión sin entender la vulnerabilidad,
y no documenta nada porque no sabe qué busca.
No es un insulto — es una etapa. El problema es quedarse ahí.

```
Script kiddie     →     Hacker     →     Pentester profesional
  ejecuta              entiende            entiende + autorización
                                           + metodología + reporte
```

Este curso te mueve del primero al tercero.

---

## Por qué el permiso y la ética no son opcionales

No es filosofía. Es la diferencia entre una carrera y un prontuario.

**El permiso lo es todo.**
Las mismas técnicas que vas a aprender en este curso,
ejecutadas contra un sistema sin autorización,
son delito en Argentina (Ley 26.388), en toda la región,
y en la mayoría de los países del mundo.
No importa la intención. No importa si "no hiciste daño".
Acceder a un sistema sin permiso es ilegal. Punto.

**El scope te protege a vos.**
Un scope bien definido y firmado es lo que te separa
de ser un consultor de seguridad de ser un imputado.
Antes de ejecutar cualquier técnica activa contra un objetivo real:
scope firmado, reglas de engagement claras, contacto de emergencia definido.

**La ética no es marketing.**
En este curso vas a aprender cosas que pueden hacer daño real.
Esa responsabilidad no desaparece cuando terminás la clase.
Si durante un engagement encontrás algo crítico que no estaba en el scope —
una base de datos expuesta, credenciales de usuarios reales, evidencia de un breach —
la respuesta profesional es notificar al cliente de inmediato, no explorar más.

**Divulgación responsable.**
Si encontrás una vulnerabilidad en un sistema que no es tuyo
y no tenés autorización para reportarla formalmente —
no la publiques, no la uses, no la vendas.
El proceso correcto es contactar al equipo de seguridad de la organización
a través de sus canales de responsible disclosure o bug bounty si existen.

---

## Cómo este curso se alinea con certificaciones reales

Todo lo que aprendés acá tiene aplicación directa en las certificaciones
que el mercado laboral de seguridad ofensiva reconoce.

### eJPT — eLearnSecurity Junior Penetration Tester
**El primer paso recomendado después de este curso.**

```
Módulo del curso     →     Contenido eJPT
─────────────────────────────────────────────────────
M1 — Recon pasivo    →     Information Gathering
M2 — Recon activo    →     Host & Network Enumeration
M3 — Acceso          →     Exploitation, Web Attacks
M4 — Reporte         →     Reporting
```

El eJPT es un examen práctico de 48 horas sobre una red real.
No hay opción múltiple. Tenés que entrar, enumerar, explotar, y reportar.
Todo lo que hacemos en este curso es práctica directa para ese examen.

### PNPT — Practical Network Penetration Tester (TCM Security)
Pentest externo + interno completo en entorno real.
Requiere reporte profesional para aprobar.
Este curso cubre la base metodológica completa que necesitás.

### OSCP — Offensive Security Certified Professional
El estándar de la industria para pentest técnico.
Examen de 24 horas, múltiples máquinas, reporte formal obligatorio.
Requiere base sólida en todo lo que cubre este curso más práctica extensa.

### Camino recomendado

```
Este curso  →  eJPT  →  PNPT  →  OSCP
              (validar)  (mercado)  (senior)
```

No hay atajos en este camino. Cada certificación construye sobre la anterior.
El eJPT primero — es la validación de que la base está sólida.

---

## Una cosa antes de arrancar

Todo lo que aprendés acá existe en internet.
Las herramientas son open source. Las técnicas están documentadas.
Los mismos comandos que vas a correr en clase
los está corriendo alguien en este momento contra sistemas sin permiso.

La diferencia entre ese alguien y vos es lo que hacés con el conocimiento.

Bienvenido al curso.
