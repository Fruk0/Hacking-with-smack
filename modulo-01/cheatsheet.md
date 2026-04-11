# Cheatsheet — Módulo 1
## Reconocimiento Pasivo Completo

> Referencia rápida para tener abierta mientras practicás.
> Para explicaciones detalladas → [`comandos.md`](./comandos.md)

---

## Whois

```bash
whois DOMINIO.com                        # registro del dominio
whois IP.IP.IP.IP                        # a quién pertenece esa IP
whois -h whois.nic.ar DOMINIO.com.ar     # dominios .ar desde terminal
```
🌐 Dominios .ar → https://nic.ar/buscar

---

## DNS — dig

```bash
# Un vistazo a todo
dig DOMINIO.com ANY

# Por tipo — los que más importan
dig A     DOMINIO.com +short    # IP del servidor
dig MX    DOMINIO.com +short    # servidores de correo  ← proveedor de mail
dig NS    DOMINIO.com +short    # nameservers           ← anotar para AXFR
dig TXT   DOMINIO.com           # SPF, DKIM, SaaS internos

# Si el DNS local falla
dig @8.8.8.8 DOMINIO.com A
dig @1.1.1.1 DOMINIO.com MX

# Loop rápido — todos los tipos
for tipo in A MX NS TXT; do echo "=== $tipo ==="; dig $tipo DOMINIO.com +short; done
```

---

## DNS — host

```bash
host DOMINIO.com                # A + AAAA + MX de un vistazo
host -t MX  DOMINIO.com         # solo MX
host -t NS  DOMINIO.com         # solo NS
host IP.IP.IP.IP                # reverso: IP → dominio
```

---

## Transferencia de zona

```bash
# Contra objetivo real (debería fallar)
dig axfr @NAMESERVER DOMINIO.com

# Demo educativa — siempre funciona
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

---

## Subdomain enumeration

```bash
# Rápido — triage inicial
subfinder -d DOMINIO.com -silent
subfinder -d DOMINIO.com -silent -o subs.txt

# Completo — cobertura máxima
amass enum -passive -d DOMINIO.com -timeout 5 -o subs_amass.txt

# Combinar y deduplicar
cat subs.txt subs_amass.txt | sort -u > subs_total.txt

# Filtrar los más interesantes
cat subs_total.txt | grep -E "api|admin|dev|staging|vpn|mail|test|beta|internal"
```

---

## Resolver subdominios — dnsx

```bash
# Lista → IPs
cat subs.txt | dnsx -a -resp -silent

# Guardar solo los vivos
cat subs.txt | dnsx -a -resp -silent -o subs_vivos.txt

# Pipeline directo
subfinder -d DOMINIO.com -silent | dnsx -a -resp -silent
```

---

## Shodan — búsquedas clave

```
org:"Empresa"                              → toda la infraestructura
org:"Empresa" port:22                      → SSH expuesto
org:"Empresa" port:3389                    → RDP expuesto
org:"Empresa" product:"Apache"             → tecnología específica
hostname:DOMINIO.com                       → por hostname
ssl:"DOMINIO.com"                          → por certificado SSL
net:IP.IP.IP.IP/24                         → por rango de IPs
country:"AR" product:"Apache" port:80      → infra argentina
```
🌐 https://shodan.io

---

## Google Hacking

```
# Subdominios
site:*.DOMINIO.com -www

# Archivos
site:DOMINIO.com filetype:pdf
site:DOMINIO.com filetype:xlsx OR filetype:csv
site:DOMINIO.com ext:env OR ext:log OR ext:bak
site:DOMINIO.com ext:sql OR ext:config

# Accesos
site:DOMINIO.com inurl:login
site:DOMINIO.com inurl:admin
site:DOMINIO.com inurl:dashboard

# Directorios abiertos
intitle:"index of" site:DOMINIO.com

# Credenciales expuestas
site:DOMINIO.com "DB_PASSWORD" OR "API_KEY" OR "SECRET"
```

---

## theHarvester

```bash
theHarvester -d DOMINIO.com -b google,bing -l 100
# Agregar fuentes si google da poco:
theHarvester -d DOMINIO.com -b google,bing,yahoo,dnsdumpster -l 100 -f output
```

---

## ExifTool — metadatos

```bash
exiftool archivo.pdf                                    # todos los metadatos
exiftool archivo.pdf | grep -i "author\|creator\|path\|company\|user"
wget "URL-PDF" -O target.pdf && exiftool target.pdf     # descargar y analizar
```

**Qué buscar:** `Author` · `Creator` · `Company` · `FilePath` · `ModifyDate`

---

## Tipos de registros DNS

| Registro | Qué es | Por qué importa |
|----------|--------|-----------------|
| `A` | Dominio → IPv4 | Dónde vive el servidor |
| `MX` | Servidores de correo | Proveedor de mail |
| `NS` | Nameservers | Para zone transfer |
| `TXT` | Texto libre | SPF, DKIM, SaaS internos |
| `CNAME` | Alias | Servicios cloud, subdominios |
| `SOA` | Info del DNS primario | Admin, versión de zona |

---

## Proveedores de correo por MX

| Lo que ves | Proveedor |
|------------|-----------|
| `aspmx.l.google.com` | Google Workspace |
| `*.mail.protection.outlook.com` | Microsoft 365 |
| `mxa.mailgun.org` | Mailgun |
| `*.pphosted.com` | Proofpoint |
| `*.mimecast.com` | Mimecast |

---

## Subdominios que siempre priorizamos

| Patrón | Por qué importa |
|--------|-----------------|
| `api.` | Lógica de negocio, endpoints sin protección de UI |
| `dev.` / `test.` | Seguridad más laxa que producción |
| `staging.` | Copia de producción, a veces con datos reales |
| `admin.` | Panel de administración |
| `vpn.` | Acceso remoto |
| `internal.` | Algo interno que no debería ser público |
| `beta.` | Sin hardening completo |

---

## Flujo de recon — en orden

```bash
TARGET="DOMINIO.com"
mkdir recon_$TARGET && cd recon_$TARGET

# 1. Whois
whois $TARGET > whois.txt

# 2. DNS completo
for t in A MX NS TXT; do dig $t $TARGET +short; done > dns.txt

# 3. Zone transfer
NS=$(dig NS $TARGET +short | head -1)
dig axfr @$NS $TARGET > zone_transfer.txt

# 4. Subdominios
subfinder -d $TARGET -silent -o subs.txt
cat subs.txt | dnsx -a -resp -silent -o subs_vivos.txt

# 5. Emails
theHarvester -d $TARGET -b google,bing -l 100 -f harvester

# 6. Metadatos
# Google Dork: site:$TARGET filetype:pdf
# wget "URL" -O doc.pdf && exiftool doc.pdf
```

---

## Errores rápidos

| Error | Causa | Fix |
|-------|-------|-----|
| `dig` timeout | DNS local caído | `dig @8.8.8.8 ...` |
| `dig ANY` vacío | Servidor ignora ANY | Loop por tipo por separado |
| `whois` "No server known" | Extensión rara | `-h whois.nic.ar` |
| AXFR falla en zonetransfer.me | NS incorrecto | Usar `@nsztm1.digi.ninja` |
| `subfinder` sin resultados | Dominio nuevo | Agregar `-all -timeout 30` |
| `amass` no termina | Sin timeout | Agregar `-timeout 5` |
| Go tools no encontradas | PATH incompleto | `export PATH=$PATH:$(go env GOPATH)/bin` |

---

*Módulo 2 → nmap · fingerprinting · vulnerability scanning*
