# Recursos — Módulo 1
## Reconocimiento Pasivo

> Todo lo que necesitás para profundizar más allá de las clases.
> Marcado con 🆓 si es gratuito, 💰 si tiene costo, 🔑 si requiere registro.

---

## Índice

1. [Herramientas usadas en clase](#1-herramientas-usadas-en-clase)
2. [Consultas y bases de datos públicas](#2-consultas-y-bases-de-datos-públicas)
3. [Motores de búsqueda de infraestructura](#3-motores-de-búsqueda-de-infraestructura)
4. [Práctica y laboratorios](#4-práctica-y-laboratorios)
5. [Lectura recomendada](#5-lectura-recomendada)
6. [Videos y canales](#6-videos-y-canales)
7. [Certificaciones relevantes](#7-certificaciones-relevantes)

---

## 1. Herramientas usadas en clase

### DNS

| Herramienta | Qué hace | Link |
|-------------|----------|------|
| `dig` | Consultas DNS completas | Viene en Kali — paquete `dnsutils` |
| `host` | Consultas DNS simples | Viene en Kali — paquete `dnsutils` |
| `whois` | Datos de registro de dominios e IPs | Viene en Kali |
| `dnsx` | Resolver subdominios en escala | https://github.com/projectdiscovery/dnsx |

### Subdomain Enumeration

| Herramienta | Qué hace | Link |
|-------------|----------|------|
| `subfinder` | Enumeración pasiva de subdominios | https://github.com/projectdiscovery/subfinder |
| `amass` | OSINT + enumeración de subdominios | https://github.com/owasp-amass/amass |

### OSINT

| Herramienta | Qué hace | Link |
|-------------|----------|------|
| `theHarvester` | Emails, usuarios, subdominios | https://github.com/laramies/theHarvester |
| `exiftool` | Metadatos de archivos | https://exiftool.org |

---

## 2. Consultas y bases de datos públicas

### Whois y registros de dominio

| Recurso | Descripción | Link |
|---------|-------------|------|
| NIC Argentina 🆓 | Registro oficial de dominios `.ar` | https://nic.ar/buscar |
| ICANN Lookup 🆓 | Whois centralizado para dominios globales | https://lookup.icann.org |
| who.is 🆓 | Whois web con historial | https://who.is |
| DomainTools 🔑💰 | Whois histórico + reverse whois | https://whois.domaintools.com |

### DNS

| Recurso | Descripción | Link |
|---------|-------------|------|
| MXToolbox 🆓 | DNS lookup, MX check, blacklist check | https://mxtoolbox.com |
| dnsdumpster 🆓 | DNS recon + mapa visual de infraestructura | https://dnsdumpster.com |
| ViewDNS 🆓 | Reverse IP, DNS history, Whois | https://viewdns.info |
| SecurityTrails 🔑 | DNS histórico + subdominios | https://securitytrails.com |
| PassiveTotal / RiskIQ 🔑💰 | DNS pasivo enriquecido | https://community.riskiq.com |

### Certificados SSL (fuente de subdominios)

| Recurso | Descripción | Link |
|---------|-------------|------|
| crt.sh 🆓 | Certificados SSL/TLS públicos — mina de subdominios | https://crt.sh |
| Certificate Transparency 🆓 | Logs oficiales de certificados | https://certificate.transparency.dev |

### IP y ASN

| Recurso | Descripción | Link |
|---------|-------------|------|
| bgp.he.net 🆓 | ASN lookup, rangos IP, relaciones BGP | https://bgp.he.net |
| ipinfo.io 🆓 | IP → ASN, organización, geolocalización | https://ipinfo.io |
| ARIN 🆓 | Registro de IPs América del Norte | https://search.arin.net |
| LACNIC 🆓 | Registro de IPs América Latina y Caribe | https://www.lacnic.net |

---

## 3. Motores de búsqueda de infraestructura

| Recurso | Descripción | Link |
|---------|-------------|------|
| Shodan 🔑🆓/💰 | Dispositivos y servicios expuestos en internet | https://shodan.io |
| Censys 🔑🆓/💰 | Escaneo de internet, certificados, servicios | https://search.censys.io |
| Fofa 🔑🆓/💰 | Alternativa china a Shodan, muy completa | https://fofa.info |
| GreyNoise 🔑 | Contexto sobre IPs escaneando internet | https://viz.greynoise.io |
| Onyphe 🔑 | OSINT de infraestructura + inteligencia de amenazas | https://www.onyphe.io |
| ZoomEye 🔑 | Alternativa a Shodan, foco en China y APAC | https://www.zoomeye.org |
| BinaryEdge 🔑💰 | Escaneo de internet + threat intelligence | https://app.binaryedge.io |

> **Para empezar:** Shodan con cuenta gratuita alcanza para el curso.
> Censys tiene un tier académico — si tenés mail universitario, pedirlo.

---

## 4. Práctica y laboratorios

### Targets diseñados para practicar

| Recurso | Qué practicar | Link |
|---------|---------------|------|
| zonetransfer.me 🆓 | Transferencia de zona AXFR — el que usamos en clase | https://digi.ninja/projects/zonetransfer.php |
| HackTheBox 🔑🆓/💰 | Máquinas y challenges con componente de recon | https://hackthebox.com |
| TryHackMe 🔑🆓/💰 | Rooms guiados de OSINT y recon | https://tryhackme.com |
| OWASP WebGoat 🆓 | Aplicación vulnerable para práctica local | https://owasp.org/www-project-webgoat |

### Rooms específicos de OSINT en TryHackMe

| Room | Tema |
|------|------|
| OhSINT | OSINT básico — excelente para empezar |
| Google Dorking | Google Hacking paso a paso |
| Passive Reconnaissance | Recon pasivo completo |
| Shodan.io | Uso de Shodan desde cero |

---

## 5. Lectura recomendada

### Metodologías y frameworks

| Recurso | Descripción | Link |
|---------|-------------|------|
| PTES — Penetration Testing Execution Standard 🆓 | Estándar de facto para pentest profesional | http://www.pentest-standard.org |
| OWASP Testing Guide 🆓 | Guía de testing de aplicaciones web | https://owasp.org/www-project-web-security-testing-guide |
| MITRE ATT&CK — Reconnaissance 🆓 | Tácticas de reconocimiento documentadas por MITRE | https://attack.mitre.org/tactics/TA0043 |

### Documentación de herramientas

| Recurso | Link |
|---------|------|
| Subfinder docs | https://docs.projectdiscovery.io/tools/subfinder |
| Amass docs | https://github.com/owasp-amass/amass/blob/master/doc/user_guide.md |
| theHarvester wiki | https://github.com/laramies/theHarvester/wiki |
| ExifTool docs | https://exiftool.org/exiftool_pod.html |

### Artículos y writeups

| Recurso | Descripción | Link |
|---------|-------------|------|
| The Art of Subdomain Enumeration 🆓 | Comparativa de técnicas de enumeración | https://blog.sweepatic.com/art-of-subdomain-enumeration |
| DNS Reconnaissance 🆓 | Recon DNS en profundidad | https://resources.infosecinstitute.com/topics/general-security/dns-reconnaissance |
| Shodan Manual 🆓 | Manual completo de Shodan | https://leanpub.com/shodan |

---

## 6. Videos y canales

| Canal / Video | Plataforma | Por qué vale la pena |
|---------------|------------|----------------------|
| IppSec | YouTube | Writeups de HTB — siempre arranca con recon real |
| TCM Security | YouTube | Cursos de pentest prácticos, muy accesibles |
| NahamSec | YouTube | Bug Bounty y recon — casos reales |
| John Hammond | YouTube | CTFs + malware + recon |
| HackerOne Hacker101 | YouTube | Bug bounty desde cero |
| STÖK | YouTube | Bug bounty con mucho foco en recon y OSINT |

---

## 7. Certificaciones relevantes

Lo que podés lograr después de este curso:

| Certificación | Emisor | Nivel | Recon en el examen |
|---------------|--------|-------|--------------------|
| eJPT | eLearnSecurity / INE | Principiante | Sí — recon básico |
| PNPT | TCM Security | Intermedio | Sí — recon completo |
| OSCP | Offensive Security | Intermedio-Avanzado | Sí — fundamental |
| CEH | EC-Council | Teórico | Sí — teórico |

> **Recomendación del curso:** eJPT primero para validar lo aprendido,
> después PNPT o OSCP según el camino que elijas.

---

*¿Encontraste un recurso útil que no está acá? Abrí un issue en el repo.*
