# Vuln Scanner — Plugin Marketplace para Claude Code

Marketplace de un solo plugin: una Agent Skill de [Claude Code](https://claude.com/claude-code)
que escanea URLs en busca de vulnerabilidades web (XSS, SQLi, CSRF, cabeceras de
seguridad, open redirect, exposicion de informacion) usando el backend de
[Web Vulnerability Scanner](https://vulnerabilidad-web.sytes.net).

## Instalar (para compañeros de clase / cualquiera)

Dentro de una sesion de Claude Code:

```
/plugin marketplace add mary010-sky/skill-vuln-scanner
/plugin install vuln-scanner-plugin@vuln-scanner-marketplace
```

Reinicia o corre `/reload-plugins` si no aparece de inmediato.

## Configurar tu propia API key (una sola vez)

El plugin no trae ninguna credencial incluida — cada quien usa su propia cuenta:

1. Genera una API key desde `https://vulnerabilidad-web.sytes.net` (dashboard, seccion
   API Keys) o con `POST /api/api-keys` autenticado con tu usuario.
2. Expórtala como variable de entorno antes de usar la skill:
   ```bash
   export WVS_API_KEY="wvs_tu_key_aqui"
   ```
   O crea un archivo `.env.local` junto al `SKILL.md` del plugin instalado (dentro de
   `~/.claude/plugins/cache/...`) con la linea `WVS_API_KEY=wvs_tu_key_aqui`.

## Usar

Pide a Claude en lenguaje natural, por ejemplo:

```
Escanea https://ejemplo.com en busca de vulnerabilidades
```

Claude detecta la skill por su descripcion y la invoca sola. El reporte incluye
severidad, modulo, ubicacion exacta (URL/parametro) y evidencia cuando el backend la
reporta (por ejemplo el payload reflejado en un XSS).

**Solo escanea sitios que tengas autorizacion para auditar.**

## Estructura de este repo

```
.claude-plugin/marketplace.json          Catalogo del marketplace
plugins/vuln-scanner-plugin/
  .claude-plugin/plugin.json             Metadata del plugin
  skills/vuln-scanner/
    SKILL.md                             Instrucciones que usa Claude
    scripts/scan.py                      Script que llama a la API del scanner
```

## Actualizar el plugin (para el mantenedor)

Sube cambios a este repo y sube la version en `plugin.json`. Quienes ya lo instalaron
reciben la actualizacion al correr `/plugin marketplace update`.
