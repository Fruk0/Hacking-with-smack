# Comandos — Módulo 1
## Reconocimiento Pasivo Completo

> Todos los comandos de este módulo son **pasivos**.
> No enviamos tráfico al objetivo — consultamos fuentes públicas.
> El objetivo no sabe que existimos.

---

## Índice

**Clase 1**
1. [Whois](#1-whois)
2. [DNS con dig](#2-dns-con-dig)
3. [DNS con host](#3-dns-con-host)
4. [Transferencia de zona](#4-transferencia-de-zona)
5. [NIC Argentina — dominios .ar](#5-nic-argentina--dominios-ar)

**Clase 2**

6. [Subdomain enumeration — subfinder](#6-subdomain-enumeration--subfinder)
7. [Subdomain enumeration — amass](#7-subdomain-enumeration--amass)
8. [Resolución de subdominios — dnsx](#8-resolución-de-subdominios--dnsx)
9. [Shodan](#9-shodan)
10. [Google Hacking](#10-google-hacking)
11. [theHarvester](#11-theharvester)
12. [ExifTool — metadatos](#12-exiftool--metadatos)
13. [Flujo completo encadenado](#13-flujo-completo-encadenado)
14. [Errores comunes](#14-errores-comunes)

---

## 1. Whois

Whois expone los datos de registro de un dominio o bloque de IPs.
Cuando alguien registra un dominio, esa información queda en una base de datos pública.

```bash
whois tesla.com
# Consulta básica de dominio
# Qué buscar: registrar, nameservers, fechas, emails de contacto

whois mercadolibre.com
# Empresa LATAM — a veces datos más visibles

whois 104.215.148.63
# Whois de una IP — a quién pertenece el rango
# Muestra: empresa dueña, país, ASN, rango de IPs

whois -h whois.arin.net 104.215.148.63
# -h = especificar servidor whois manualmente
# ARIN   = IPs de América del Norte
# LACNIC = IPs de América Latina y Caribe (lacnic.net)
# RIPE   = IPs de Europa
# APNIC  = IPs de Asia-Pacífico
```

**Qué anotar del output:**

```
Registrar           → quién administra el dominio
                      MarkMonitor = corporativo serio
                      GoDaddy/Namecheap = registro personal o empresa chica

Creation Date       → antigüedad del dominio
                      dominio viejo = más infraestructura legacy

Name Server         → nameservers autoritativos
                      ANOTARLOS — se usan para zone transfer

Registrant Email    → a veces redactado por GDPR
                      si aparece: email corporativo directo, muy valioso
```

---

## 2. DNS con dig

`dig` (Domain Information Groper) es la herramienta principal para consultas DNS.
Viene instalada en Kali como parte del paquete `dnsutils`.

### Tipos de registros

```
A      → Dominio → IPv4. Dónde vive el servidor.
AAAA   → Dominio → IPv6.
MX     → Servidores de correo.
NS     → Nameservers autoritativos del dominio.
TXT    → Texto libre: SPF, DKIM, verificaciones de SaaS.
CNAME  → Alias que apunta a otro nombre de dominio.
SOA    → Start of Authority: info del DNS primario y admin.
PTR    → Reverso: IP → dominio.
```

### Consultas básicas

```bash
dig tesla.com ANY
# ANY = todos los registros disponibles
# Algunos servidores modernos ignoran ANY — ver solución en errores comunes

dig A tesla.com +short
# +short = solo el resultado, sin cabeceras ni metadata
# Devuelve: una o varias IPs
# Varias IPs = balanceo de carga o CDN

dig MX tesla.com +short
# Servidores de correo con su prioridad
# Número menor = mayor prioridad
# Ejemplo: 10 mxa.mailgun.org. → usa Mailgun

dig NS tesla.com +short
# Nameservers autoritativos
# Estos son los que usamos para intentar zone transfer

dig TXT tesla.com
# Sin +short para ver el contenido completo
# Qué buscar: SPF, DKIM, verificaciones de Google/Microsoft/Atlassian
```

### Leer registros TXT — guía rápida

```
"v=spf1 include:mailgun.org ~all"
→ Usa Mailgun para enviar correo

"v=spf1 include:_spf.google.com ~all"
→ Usa Google Workspace

"MS=msXXXXXXXX"
→ Tiene Microsoft 365

"atlassian-domain-verification=XXXX"
→ Usa Confluence o Jira (Atlassian Cloud)

"google-site-verification=XXXX"
→ Tiene Google Search Console o Google Ads
```

### Consultar contra un servidor específico

```bash
dig @8.8.8.8 tesla.com A
# @8.8.8.8 = usar Google DNS como resolver
# Útil cuando el DNS local da resultados incorrectos

dig @1.1.1.1 tesla.com MX
# Cloudflare DNS como alternativa

dig @ns1.markmonitor.com tesla.com NS
# Preguntar directamente al nameserver autoritativo
# Necesario para zone transfer
```

### Loop para consultar todos los tipos

```bash
for tipo in A AAAA MX NS TXT SOA; do
  echo "=== $tipo ==="
  dig $tipo tesla.com +short
done
# Útil cuando ANY no devuelve resultados completos
```

### Leer el output completo de dig

```
;; QUESTION SECTION:     → lo que preguntamos
;; ANSWER SECTION:       → la respuesta — acá está la data
;; AUTHORITY SECTION:    → qué servidor es autoritativo
;; ADDITIONAL SECTION:   → info extra (IPs de nameservers)

;; Query time: 45 msec   → latencia al servidor DNS
;; SERVER: 8.8.8.8       → qué servidor respondió
```

---

## 3. DNS con host

`host` es más simple que `dig`. Útil para resultados limpios y rápidos.

```bash
host tesla.com
# Muestra A, AAAA y MX en una sola línea cada uno

host -t MX mercadolibre.com
# -t = tipo de registro específico
# Equivalente a: dig MX mercadolibre.com +short

host -t NS personal.com.ar
# Nameservers de un dominio .ar

host 104.215.148.63
# Resolución inversa: IP → nombre de dominio
# Equivalente a: dig -x 104.215.148.63
```

**¿Cuándo usar cada uno?**

```
dig   → análisis completo, TTL, flags, output scripteable
host  → resultado rápido, demo limpia, scripting simple
```

---

## 4. Transferencia de zona

Una transferencia de zona (AXFR) sincroniza todos los registros DNS
de un servidor primario a uno secundario.

Si el servidor está **mal configurado**, acepta el pedido de cualquiera
y entrega el mapa completo de toda la infraestructura.

```bash
# Contra un objetivo real — va a fallar si está bien configurado
dig axfr @ns1.markmonitor.com tesla.com
# axfr = full zone transfer request
# @ns1.markmonitor.com = nameserver al que preguntamos (del dig NS anterior)
# RESULTADO ESPERADO: Transfer failed. → bien configurado

# Demo educativa — zonetransfer.me
# Dominio creado específicamente para practicar esto en clase
dig axfr @nsztm1.digi.ninja zonetransfer.me
# Siempre usar este nameserver exacto para zonetransfer.me
```

**Output esperado de zonetransfer.me:**

```
zonetransfer.me.          7200  IN  A      5.196.105.14
email.zonetransfer.me.    7200  IN  A      74.125.206.26
office.zonetransfer.me.   7200  IN  A      4.23.39.254
vpn.zonetransfer.me.     14400  IN  A      174.36.59.154
staging.zonetransfer.me.  7200  IN  CNAME  www.zonetransfer.me.
```

**Por qué es crítico:**

```
Sin zone transfer:   subfinder + amass + fuerza bruta  →  horas
Con zone transfer:   un solo comando                   →  mapa completo
```

Siempre intentar **antes** de pasar a enumeración de subdominios.

---

## 5. NIC Argentina — dominios .ar

Los dominios `.ar` no están en registrars internacionales.
Los administra NIC Argentina. El whois clásico puede ser incompleto.

**Fuente canónica:** https://nic.ar/buscar

```bash
# Las consultas DNS funcionan igual que con cualquier dominio
dig NS personal.com.ar +short
dig MX personal.com.ar +short
dig A  personal.com.ar +short
dig TXT personal.com.ar

host -t MX personal.com.ar

# Whois desde terminal — puede funcionar o no según el dominio
whois personal.com.ar
whois -h whois.nic.ar personal.com.ar
# -h whois.nic.ar = apuntar directamente al servidor de NIC Argentina
```

---

## 6. Subdomain enumeration — subfinder

subfinder consulta fuentes pasivas en paralelo: certificados SSL,
DNS pasivo, APIs de threat intelligence. No toca el objetivo.

```bash
subfinder -d tesla.com -silent
# -d = dominio objetivo
# -silent = sin banner, solo resultados

subfinder -d tesla.com -silent -o subs_tesla.txt
# -o = guardar output en archivo
# Siempre guardar — se usa con dnsx después

subfinder -d tesla.com -silent | wc -l
# Contar cuántos subdominios encontró

subfinder -d tesla.com -silent | grep -E "api|admin|dev|staging|vpn|mail|test|beta|internal"
# Filtrar los subdominios más interesantes para un atacante
# Estos son los que priorizamos para investigar

subfinder -d tesla.com -all -silent
# -all = usar todas las fuentes disponibles
# Más lento pero más completo

subfinder -d tesla.com -silent -t 50
# -t = threads (paralelismo)
# Default: 10. Aumentar para objetivos grandes.
```

**Patrones de subdominios que siempre priorizamos:**

```
api.           → lógica de negocio, endpoints sin protección de UI
dev. / test.   → seguridad más laxa que producción
staging.       → copia de producción, a veces con datos reales
admin.         → panel de administración
vpn.           → acceso remoto
mail. / smtp.  → infraestructura de correo
internal.      → algo interno que no debería ser público
beta.          → versión nueva sin hardening completo
```

---

## 7. Subdomain enumeration — amass

amass es más completo que subfinder pero más lento.
Incluye datos de ASN, Whois, certificados y DNS pasivo.

```bash
amass enum -passive -d tesla.com
# -passive = solo fuentes pasivas, sin fuerza bruta
# Más lento que subfinder — puede tardar varios minutos

amass enum -passive -d tesla.com -o subs_amass_tesla.txt
# Guardar output

amass enum -passive -d mercadolibre.com -timeout 5
# -timeout = minutos máximos de ejecución
# Útil para no esperar indefinidamente

# Combinar resultados de subfinder y amass
cat subs_tesla.txt subs_amass_tesla.txt | sort -u > subs_tesla_total.txt
# sort -u = ordenar y eliminar duplicados
# Siempre combinar — cada herramienta encuentra cosas distintas
```

**subfinder vs amass:**

```
subfinder   → rápido, bueno para triage inicial
amass       → más completo, para cobertura máxima
En pentest real: correr los dos y combinar resultados
```

---

## 8. Resolución de subdominios — dnsx

dnsx toma una lista de subdominios y resuelve cuáles están vivos.

```bash
cat subs_tesla.txt | dnsx -a -resp -silent
# -a      = solo registros A (IPv4)
# -resp   = mostrar la IP en el output
# -silent = sin banner

cat subs_tesla.txt | dnsx -a -resp -silent -o subs_tesla_vivos.txt
# Guardar solo los que resuelven

cat subs_tesla.txt | dnsx -a -resp -silent | grep "104\."
# Filtrar subdominios que apuntan a un rango IP específico
# Útil para agrupar por infraestructura

# También puede resolver directamente sin lista
echo "api.tesla.com" | dnsx -a -resp -silent

# Con múltiples tipos de registro
cat subs_tesla.txt | dnsx -a -aaaa -cname -resp -silent
# -aaaa  = incluir IPv6
# -cname = incluir registros CNAME (apunta a otro dominio)
#          un CNAME a un servicio cloud puede indicar tecnología usada
```

**Pipeline completo subfinder → dnsx:**

```bash
subfinder -d tesla.com -silent | dnsx -a -resp -silent
# Enumerar + resolver en un solo comando encadenado
```

---

## 9. Shodan

Shodan indexa dispositivos y servicios expuestos en internet.
Guarda: puerto, servicio, versión, certificado SSL, banner.

### Búsquedas desde la web — shodan.io

```
# Por organización
org:"Tesla Motors"
org:"MercadoLibre"
org:"Telecom Argentina"

# Por organización + puerto específico
org:"Tesla Motors" port:22
org:"MercadoLibre" port:443
org:"Telecom Argentina" port:3389

# Por organización + producto/tecnología
org:"Tesla Motors" product:"Apache"
org:"Telecom Argentina" product:"nginx"

# Por hostname
hostname:tesla.com
hostname:.personal.com.ar

# Por rango de IPs (CIDR)
net:104.215.0.0/16

# Por país
org:"Tesla Motors" country:"US"
country:"AR" product:"Apache" port:80

# Por certificado SSL — útil para encontrar subdominios
ssl:"tesla.com"
ssl.cert.subject.cn:"*.tesla.com"
```

**Filtros útiles para combinar:**

```
port:       → puerto específico (22, 80, 443, 3389, 8080, 8443)
product:    → software detectado (Apache, nginx, IIS, OpenSSH)
version:    → versión específica — útil para buscar software desactualizado
os:         → sistema operativo detectado
country:    → país (AR, US, BR, MX)
city:       → ciudad
org:        → organización/empresa dueña de las IPs
hostname:   → nombre de host en el certificado o banner
net:        → rango de IPs en notación CIDR
ssl:        → texto en el certificado SSL
http.title: → título de la página HTTP
```

### CLI de Shodan (requiere API key)

```bash
shodan search 'org:"Tesla Motors"'
# Requiere: shodan init TU_API_KEY

shodan host 104.215.148.63
# Detalle completo de una IP: puertos, servicios, historial

shodan count 'org:"Tesla Motors"'
# Solo contar resultados sin listarlos
```

---

## 10. Google Hacking

Los operadores de Google permiten búsquedas quirúrgicas
para encontrar información que no debería estar indexada.

### Operadores principales

```
site:       → solo resultados de este dominio
filetype:   → extensión de archivo específica
ext:        → igual que filetype:
inurl:      → la URL contiene esta cadena
intitle:    → el título de la página contiene esto
intext:     → el cuerpo de la página contiene esto
-           → excluir término de los resultados
OR          → uno u otro término
*           → wildcard
```

### Dorks por categoría

**Mapear subdominios:**
```
site:*.tesla.com -www
site:*.mercadolibre.com -www -developers
```

**Archivos expuestos:**
```
site:tesla.com filetype:pdf
site:tesla.com filetype:xlsx OR filetype:csv
site:tesla.com filetype:doc OR filetype:docx
site:tesla.com ext:env OR ext:log OR ext:bak
site:tesla.com ext:sql OR ext:db
```

**Paneles y accesos:**
```
site:tesla.com inurl:login
site:tesla.com inurl:admin
site:tesla.com inurl:portal
site:tesla.com inurl:dashboard
site:tesla.com intitle:"Login" OR intitle:"Sign in"
```

**Directorios abiertos:**
```
intitle:"index of" site:tesla.com
intitle:"index of /" site:tesla.com
```

**Archivos de configuración y credenciales:**
```
site:tesla.com ext:env
site:tesla.com ext:config
site:tesla.com "DB_PASSWORD" OR "API_KEY" OR "SECRET"
```

**Tecnologías usadas:**
```
site:tesla.com "powered by"
site:tesla.com intext:"WordPress" OR intext:"Drupal"
```

**Documentos con metadatos ricos:**
```
site:tesla.com filetype:pdf inurl:report
site:tesla.com filetype:pdf inurl:investor
site:tesla.com filetype:pdf inurl:manual
```

> **Regla de oro:** Si encontrás algo que parece sensible en una empresa real,
> documentarlo y reportarlo. No descargarlo, no usarlo, no publicarlo sin coordinar.

---

## 11. theHarvester

theHarvester busca emails, usuarios y subdominios
a través de motores de búsqueda y fuentes públicas.

```bash
theHarvester -d tesla.com -b google,bing -l 100
# -d = dominio objetivo
# -b = fuentes separadas por coma
# -l = límite de resultados por fuente

theHarvester -d mercadolibre.com -b google,bing,yahoo -l 50
# yahoo a veces devuelve resultados que google/bing no tienen

theHarvester -d tesla.com -b google -l 100 -f output_tesla
# -f = guardar output (genera output_tesla.xml y output_tesla.json)

theHarvester -d tesla.com -b all -l 200
# -b all = todas las fuentes disponibles
# Más lento pero máxima cobertura
```

**Fuentes disponibles:**

```
google      → Google Search
bing        → Bing Search
yahoo       → Yahoo Search
linkedin    → LinkedIn (perfiles de empleados)
hunter      → Hunter.io (emails verificados — requiere API key)
dnsdumpster → DNS recon pasivo
baidu       → Buscador chino
```

**Qué hacer con los emails encontrados:**

```
1. Identificar el formato: nombre.apellido@ / n.apellido@ / nombre@
2. Inferir emails de empleados que no aparecen públicamente
3. Documentar para el reporte: "la empresa tiene X emails expuestos"
4. En fase de acceso: vector de spear phishing (fuera del scope de M1)
```

---

## 12. ExifTool — metadatos

Los archivos tienen metadatos que revelan información interna:
nombre del autor, software usado, rutas del sistema, fechas.

```bash
# Analizar un archivo ya descargado
exiftool target.pdf
# Sin flags = todos los metadatos disponibles

exiftool target.pdf | grep -i "author\|creator\|company\|path\|user"
# Filtrar los campos más reveladores

# Descargar y analizar en un paso
wget "https://URL-DEL-PDF" -O target.pdf && exiftool target.pdf

# Analizar múltiples archivos
exiftool *.pdf
exiftool *.docx

# Output en formato JSON (útil para scripting)
exiftool -json target.pdf

# Solo campos específicos
exiftool -Author -Creator -Company target.pdf
```

**Campos más valiosos:**

```
Author          → nombre del empleado que creó el documento
                  → posible usuario de Active Directory / email

Creator         → software usado internamente
                  → versión de Office, sistema operativo inferible

Company         → nombre real de la empresa o subsidiaria

FilePath        → ruta interna del sistema donde fue guardado
                  C:\Users\jsmith\Documents\...
                  → usuario de Windows, estructura de carpetas

CreateDate      → cuándo se creó
ModifyDate      → cuándo se modificó — a veces revela zona horaria del sistema

Producer        → pipeline de generación (Adobe, LibreOffice, etc.)
```

**Cómo encontrar PDFs para analizar:**

```bash
# 1. Google Dork
# site:tesla.com filetype:pdf

# 2. Descargar el PDF que aparece
wget "URL-ENCONTRADA" -O target.pdf

# 3. Analizar
exiftool target.pdf
```

---

## 13. Flujo completo encadenado

Orden de ejecución para un recon pasivo completo.
Ejecutar en este orden — cada paso alimenta al siguiente.

```bash
# ══════════════════════════════════════════════════
# SETUP — crear directorio de trabajo
# ══════════════════════════════════════════════════
mkdir recon_TARGET && cd recon_TARGET
TARGET="tesla.com"   # cambiar por el dominio objetivo

# ══════════════════════════════════════════════════
# PASO 1 — WHOIS
# ══════════════════════════════════════════════════
whois $TARGET > whois_dominio.txt
# Anotar los nameservers para usar en el paso 3

# ══════════════════════════════════════════════════
# PASO 2 — DNS COMPLETO
# ══════════════════════════════════════════════════
for tipo in A AAAA MX NS TXT SOA; do
  echo "=== $tipo ===" >> dns_completo.txt
  dig $tipo $TARGET +short >> dns_completo.txt
done

# Guardar la IP principal para whois de IP
IP=$(dig A $TARGET +short | head -1)
whois $IP > whois_ip.txt

# ══════════════════════════════════════════════════
# PASO 3 — ZONE TRANSFER
# ══════════════════════════════════════════════════
# Reemplazar NAMESERVER con el NS encontrado en paso 2
NAMESERVER=$(dig NS $TARGET +short | head -1)
dig axfr @$NAMESERVER $TARGET > zone_transfer.txt
# Si devuelve "Transfer failed." → bien configurado, continuar

# ══════════════════════════════════════════════════
# PASO 4 — SUBDOMINIOS
# ══════════════════════════════════════════════════
subfinder -d $TARGET -silent -o subs_subfinder.txt
amass enum -passive -d $TARGET -o subs_amass.txt
cat subs_subfinder.txt subs_amass.txt | sort -u > subs_total.txt
echo "[+] Subdominios únicos encontrados: $(wc -l < subs_total.txt)"

# ══════════════════════════════════════════════════
# PASO 5 — RESOLVER A IPs
# ══════════════════════════════════════════════════
cat subs_total.txt | dnsx -a -resp -silent -o subs_vivos.txt
echo "[+] Subdominios activos: $(wc -l < subs_vivos.txt)"

# ══════════════════════════════════════════════════
# PASO 6 — EMAILS Y USUARIOS
# ══════════════════════════════════════════════════
theHarvester -d $TARGET -b google,bing,yahoo -l 100 -f harvester_output

# ══════════════════════════════════════════════════
# PASO 7 — METADATOS
# ══════════════════════════════════════════════════
# 1. Buscar en Google: site:$TARGET filetype:pdf
# 2. Descargar el PDF más interesante
# wget "URL" -O doc_target.pdf
# 3. Analizar
# exiftool doc_target.pdf > metadatos.txt

# ══════════════════════════════════════════════════
# RESUMEN FINAL
# ══════════════════════════════════════════════════
echo ""
echo "══════════════════════════════════"
echo "RECON COMPLETADO — $TARGET"
echo "══════════════════════════════════"
echo "Subdominios totales  : $(wc -l < subs_total.txt)"
echo "Subdominios activos  : $(wc -l < subs_vivos.txt)"
echo "Archivos generados   : $(ls *.txt | wc -l)"
echo "══════════════════════════════════"
ls -la
```

---

## 14. Errores comunes

### dig no responde / timeout

```bash
# Causa: resolver DNS local caído o filtrando
# Fix: usar un DNS público
dig @8.8.8.8 tesla.com A
dig @1.1.1.1 tesla.com MX
```

### dig ANY devuelve vacío o SERVFAIL

```bash
# Causa: el servidor ignora consultas ANY (comportamiento moderno)
# Fix: consultar por tipo por separado
for tipo in A MX NS TXT; do echo "=== $tipo ==="; dig $tipo tesla.com +short; done
```

### whois da "No whois server is known"

```bash
# Causa: extensión de dominio poco conocida
# Fix: especificar el servidor manualmente
whois -h whois.nic.ar dominio.com.ar
```

### Zone transfer falla en zonetransfer.me

```bash
# Causa: estás usando el nameserver incorrecto
# Fix: usar siempre este exacto
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

### subfinder no encuentra nada

```bash
# Causa: el dominio es muy nuevo o tiene poca presencia pública
# Fix: agregar fuentes adicionales y aumentar tiempo de espera
subfinder -d TARGET.com -all -silent -timeout 30
```

### amass corre indefinidamente

```bash
# Causa: sin timeout, amass puede correr por horas
# Fix: siempre usar -timeout
amass enum -passive -d TARGET.com -timeout 5
# -timeout = minutos máximos
```

### theHarvester no encuentra emails

```bash
# Causa: Google limita scraping, resultados variables
# Fix: agregar más fuentes y reintentar
theHarvester -d TARGET.com -b bing,yahoo,dnsdumpster -l 100
```

### dnsx no resuelve nada

```bash
# Causa: lista de subdominios vacía o formato incorrecto
# Fix: verificar que el archivo de entrada tiene un subdominio por línea
cat subs_tesla.txt | head -5   # verificar formato
echo "api.tesla.com" | dnsx -a -resp -silent   # probar con uno solo
```

### Go tools no encontradas después de instalar

```bash
# Causa: GOPATH/bin no está en el PATH
# Fix:
export PATH=$PATH:$(go env GOPATH)/bin
# Para que persista, agregar al final de ~/.zshrc o ~/.bashrc:
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> ~/.zshrc
source ~/.zshrc
```

---

*Módulo 2 → recon activo: nmap, fingerprinting, vulnerability scanning*
