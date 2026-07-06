---
name: vuln-scanner
description: Escanea una URL en busca de vulnerabilidades web (XSS, SQLi, CSRF, cabeceras faltantes, open redirect, exposicion de informacion, LFI/path traversal, SSRF, command injection, metodos HTTP peligrosos) usando el backend Web Vulnerability Scanner. Usar cuando el usuario pida escanear, auditar o analizar la seguridad de un sitio/URL, o pregunte por vulnerabilidades de una pagina web.
---

# Vuln Scanner

Lanza un escaneo de seguridad contra una URL objetivo usando la API de integraciones del
Web Vulnerability Scanner (`/api/integrations/scans`) y devuelve un reporte legible.

## Requisitos previos

Cada usuario necesita su propia API key (no viene incluida en el plugin, por seguridad).

- `WVS_API_KEY` (obligatoria): genera una desde el dashboard del scanner en
  `https://vulnerabilidad-web.sytes.net`, seccion API Keys, o con
  `POST /api/api-keys` autenticado con tu usuario.
- `WVS_API_URL` (opcional): por defecto usa el backend de produccion
  `https://vulnerabilidad-web.sytes.net/backend`. Solo cambiala si usas tu propio
  backend.

El script busca `WVS_API_KEY`/`WVS_API_URL` primero en variables de entorno ya
exportadas, y si no existen, en un archivo `.env.local` ubicado en la carpeta padre
de `scripts/` (junto a este mismo `SKILL.md`). Crea ese archivo la primera vez:

```
WVS_API_KEY=wvs_tu_key_aqui
```

Si `WVS_API_KEY` no esta definida en ningun lado, pide al usuario que la configure
antes de continuar (no existe ningun flujo de login automatico en esta skill).

## Uso

Este SKILL.md tiene un script hermano en `scripts/scan.py`, ubicado siempre en la misma
carpeta que este archivo. Como este plugin puede quedar instalado en ubicaciones
distintas segun el usuario (por ejemplo dentro de la cache de plugins de Claude Code),
sigue estos pasos para ejecutarlo sin fallar:

1. Si sabes con certeza la ruta de la carpeta donde se cargo este `SKILL.md`, usa esa
   ruta directamente.
2. Si no la conoces, busca el archivo con la herramienta Glob usando el patron
   `**/vuln-scanner/scripts/scan.py` (o revisa `~/.claude/plugins/cache/` si el patron
   no encuentra nada).
3. Ejecuta el script con el interprete de Python disponible en el sistema. Prueba
   primero `python3`; si el comando falla porque no existe (comun en Windows, donde
   normalmente solo esta disponible `python`), reintenta con `python`:

```bash
python3 <ruta-encontrada>/scripts/scan.py <target_url> [--depth N] [--modules m1,m2,...] [--timeout N]
# si falla por no existir python3:
python <ruta-encontrada>/scripts/scan.py <target_url> [--depth N] [--modules m1,m2,...] [--timeout N]
```

- `target_url` (obligatorio): URL completa a escanear, ej. `https://ejemplo.com`.
- `--depth`: profundidad de rastreo, 0-3 (default 1).
- `--modules`: lista separada por comas de modulos a ejecutar. Por defecto:
  `xss,sqli,headers,csrf,open_redirect,info_disclosure,lfi,command_injection,ssrf,http_methods`.
- `--timeout`: timeout por request del scanner en segundos, 3-30 (default 10).

El script crea el escaneo, hace polling hasta que termina (`completed` o `failed`) y
finalmente imprime en stdout un reporte en Markdown con severidad, ubicacion, evidencia
(cuando aplica) y recomendacion de cada vulnerabilidad encontrada. Ese texto se puede
mostrar al usuario tal cual o resumirse.

## Notas

- Un escaneo puede tardar desde varios segundos hasta un par de minutos segun la
  profundidad y los modulos seleccionados; el script hace polling cada 3s sin limite de
  tiempo, informar al usuario que puede tomar un momento.
- Si el backend responde 401/403, la API key es invalida o fue revocada: pedir al
  usuario que genere una nueva.
- Solo se debe escanear URLs que el usuario tenga autorizacion para auditar.
