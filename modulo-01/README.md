# Módulo 1 — Reconocimiento Pasivo

> *"Nos contratan para auditar Nexus Financial. No tocamos nada todavía. Solo miramos.
> Estamos por debajo del radar — solo fuentes abiertas."*

---

## 🧠 Mindset para esta fase

El recon pasivo no es "lo que hacés antes de lo importante". **Es lo importante.**
Un atacante que conoce tu infraestructura mejor que vos ya ganó — vos todavía no lo sabés.
En esta fase la pregunta no es *qué herramienta corro* sino *qué estoy tratando de entender* sobre el objetivo.
Si algo no devuelve resultados, es un dato: documentalo y seguí. → [MINDSET.md](../MINDSET.md)

---

## Qué vas a aprender

Al terminar este módulo vas a poder:

- Extraer información de infraestructura de cualquier empresa usando solo fuentes públicas
- Ejecutar consultas DNS completas y saber interpretar cada registro
- Intentar transferencias de zona y entender por qué son críticas cuando funcionan
- Enumerar subdominios pasivamente con subfinder, amass y dnsx
- Usar Shodan y Google Hacking para mapear superficie de ataque expuesta
- Encontrar emails, usuarios y documentos con theHarvester y ExifTool
- Encadenar todo esto en un pipeline con AI

---

## Clases

| Clase | Fecha | Tema |
|-------|-------|------|
| Clase 1 | Lunes 20 de Abril | Whois, DNS, Transferencia de zona, NIC Argentina |
| Clase 2 | Miércoles 22 de Abril | OSINT, Subdominios, Shodan, Google Hacking, AI Pipeline |

---

## Material

| Archivo | Descripción |
|---------|-------------|
| [comandos.md](./comandos.md) | Todas las herramientas del módulo — flag por flag |
| [cheatsheet.md](./cheatsheet.md) | Referencia rápida para tener abierta mientras practicás |
| [checklist.md](./checklist.md) | Guía paso a paso para practicar solo después de clase |
| [recursos.md](./recursos.md) | Links, tools, lecturas y certificaciones |
| [challenge.md](./challenge.md) | Enunciado del challenge — se activa al final de la Clase 2 |

---

## Posición en el pentest

```
[ RECON PASIVO ]  →  Recon Activo  →  Acceso  →  Post-Ex  →  Reporte
      ↑
   Módulo 1
   Estamos acá
```

En este módulo no enviamos tráfico al objetivo.
Todo lo que hacemos es consultarle a fuentes públicas — el objetivo no nos ve.

---

## Herramientas del módulo

| Herramienta | Clase | Qué hace |
|-------------|-------|----------|
| `whois` | 1 | Datos de registro de dominios e IPs |
| `dig` | 1 | Consultas DNS completas |
| `host` | 1 | Consultas DNS simples y rápidas |
| `subfinder` | 2 | Enumeración pasiva de subdominios |
| `amass` | 2 | OSINT + enumeración de subdominios |
| `dnsx` | 2 | Resolver subdominios a IPs en escala |
| Shodan | 2 | Servicios e infraestructura expuesta |
| Google Dorks | 2 | Archivos y servicios indexados sin querer |
| `theHarvester` | 2 | Emails y usuarios expuestos |
| `exiftool` | 2 | Metadatos de archivos públicos |

---

## Entorno necesario

Kali Linux con las siguientes herramientas instaladas.

Verificar antes de arrancar:

```bash
which dig whois host subfinder amass dnsx theHarvester exiftool
```

Instalar lo que falte:

```bash
# Herramientas del sistema
sudo apt update && sudo apt install -y dnsutils whois exiftool

# ProjectDiscovery toolkit (requiere Go instalado)
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest

# Amass
go install -v github.com/owasp-amass/amass/v4/...@master

# theHarvester
pip3 install theHarvester --break-system-packages
```

Verificar que Go está en el PATH:

```bash
export PATH=$PATH:$(go env GOPATH)/bin
# Agregar esta línea a ~/.zshrc o ~/.bashrc para que persista
```
