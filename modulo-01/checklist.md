# Checklist — Módulo 1
## Reconocimiento Pasivo

---

## Objetivo de la fase

Construir el mapa de superficie del objetivo usando solo fuentes públicas.
Al terminar, tenés que saber qué tiene expuesto, dónde vive, y por dónde entrarías.

---

## Qué buscás

No técnicas. Información que tiene valor real como atacante:

- Nameservers y registrar — indican madurez de seguridad del dominio
- Proveedor de correo — vector de phishing, configuración SPF/DKIM
- Servicios SaaS internos — revelados por registros TXT (Atlassian, Microsoft, Google)
- Subdominios olvidados — dev, staging, test, internal, beta
- IPs y rangos de red — para cruzar con Shodan
- ASN de la organización — superficie completa de infraestructura propia
- Emails corporativos — formato, empleados expuestos, vectores de ataque
- Documentos públicos con metadatos — usuario de sistema, rutas internas, software
- Servicios con versiones expuestas — especialmente en subdominios secundarios
- Paneles de administración indexados por Google
- Archivos que no deberían ser públicos — `.env`, `.log`, `.bak`, `.sql`

---

## Checklist de ejecución

Ejecutar en este orden. Cada paso alimenta al siguiente.

```bash
TARGET="dominio-objetivo.com"
mkdir recon_$TARGET && cd recon_$TARGET
```

- [ ] **Whois del dominio** — `whois $TARGET` → anotar registrar, nameservers, fechas, emails si aparecen
- [ ] **Whois inverso de la IP** — `whois $(dig A $TARGET +short | head -1)` → a quién pertenece el servidor, ASN, rango
- [ ] **DNS completo** — `dig $TARGET ANY` o loop por tipo → registrar todos los registros A, MX, NS, TXT
- [ ] **Leer los TXT** — identificar servicios SaaS por los `include:` del SPF y las verificaciones
- [ ] **Anotar los nameservers** — del `dig NS $TARGET +short` → los necesitás para el siguiente paso
- [ ] **Intentar zone transfer** — `dig axfr @NAMESERVER $TARGET` → si funciona, es crítico; si falla, es un dato
- [ ] **Enumerar subdominios** — `subfinder -d $TARGET -silent -o subs.txt` → guardar output siempre
- [ ] **Ampliar con amass** — `amass enum -passive -d $TARGET -timeout 5 -o subs_amass.txt` → combinar con subfinder
- [ ] **Filtrar subdominios interesantes** — `cat subs.txt | grep -E "api|admin|dev|staging|vpn|test|internal|beta"` → priorizarlos
- [ ] **Resolver a IPs** — `cat subs.txt | dnsx -a -resp -silent -o subs_vivos.txt` → quedarse con los activos
- [ ] **Buscar en Shodan** — `org:"Empresa"` y `hostname:$TARGET` → puertos abiertos, versiones, tecnologías
- [ ] **Google Dorks** — ejecutar al menos: `site:*.$TARGET -www`, `site:$TARGET ext:env OR ext:log OR ext:bak`, `intitle:"index of" site:$TARGET`
- [ ] **Emails y usuarios** — `theHarvester -d $TARGET -b google,bing,yahoo -l 100` → formato de email, empleados expuestos
- [ ] **Descargar un doc público** — Google Dork: `site:$TARGET filetype:pdf` → `wget URL -O doc.pdf && exiftool doc.pdf`
- [ ] **Documentar hallazgos** — para cada ítem valioso: herramienta usada + output + por qué importa + captura

---

## Señales de que encontraste algo valioso

Cuando aparece alguno de estos, **detenerse y documentar antes de seguir**:

- 🔴 Zone transfer exitosa — tenés el mapa completo de infraestructura
- 🔴 Archivo `.env`, `.bak`, `.sql`, o `.config` indexado por Google
- 🔴 `FilePath` o `Author` en metadatos de un documento — usuario interno expuesto
- 🟠 Subdominio con patrón `dev.`, `staging.`, `test.`, `internal.`, `beta.` — seguridad más laxa
- 🟠 Panel de administración (`/admin`, `/dashboard`, `/portal`) accesible sin auth
- 🟠 Servicio con versión desactualizada visible en Shodan
- 🟠 Directorio abierto (`intitle:"index of"`) con archivos listados
- 🟡 Email de empleado técnico (sysadmin, devops, IT) — vector de spear phishing
- 🟡 Registro TXT con herramientas internas (Atlassian, Okta, Salesforce)
- 🟡 CNAME apuntando a servicio cloud no reclamado — potencial subdomain takeover

---

## Errores comunes que hacen perder tiempo

**1. Pasar directo a subfinder sin hacer DNS primero.**
El DNS te dice el proveedor de mail, los SaaS internos, y los nameservers para zone transfer.
Si lo saltás, arrancás ciego.

**2. No guardar el output de cada herramienta.**
Lo que no está en un archivo no existió. Redirigí siempre con `-o` o `>`.
Reconstruir de memoria lo que corriste hace dos horas es tiempo perdido.

**3. Atascarse cuando algo no devuelve resultados.**
`Transfer failed.` es un dato. Shodan sin resultados es un dato. Anotalo y seguí.
El rabbit hole de "por qué no funciona esta herramienta" consume tiempo que vale más en otra superficie.

**4. Enumerar sin priorizar.**
Subfinder puede devolverte 300 subdominios. No investigás los 300 — filtrás los interesantes
y arrancás por ahí. Un `dev.` olvidado vale más que cincuenta instancias de CDN.

**5. No anotar por qué algo es relevante en el momento.**
Un subdominio interesante que no tiene nota sobre por qué lo marcaste
va a parecer ruido cuando lo revises al día siguiente. Una línea de contexto alcanza.

---

## Mindset de esta fase

El recon pasivo no termina cuando corriste todas las herramientas — termina cuando podés
responder: *¿qué tiene expuesto este objetivo y por dónde entraría un atacante?*
Si no podés responderlo, falta información o falta análisis.

Los datos no son hallazgos. Un subdominio es un dato. Un subdominio de staging
con un framework desactualizado accesible sin autenticación es un hallazgo.
La diferencia está en el análisis, no en la cantidad de comandos que corriste.

Documentá ahora. El reporte se escribe con evidencia, no con memoria.
