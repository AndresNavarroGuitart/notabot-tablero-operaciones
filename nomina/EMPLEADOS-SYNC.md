# Sync de Nómina con Google Forms/Sheets — PLAN A FUTURO (no activar todavía)

## Objetivo

Que cuando un compañero complete el Google Form de alta de empleado (datos
personales, dirección, contacto de emergencia, datos bancarios, etc.), esa
información entre directo a la ficha de Nómina del tablero, sin tipearla a mano.

## Por qué está pausado hoy

El tablero, tal como está armado, **no es apto para tener datos reales de
personas**:

- Es un sitio estático público, servido por GitHub Pages, **sin login**
  (hallazgo **A-01** del análisis ISO 27001).
- Persiste todo en `localStorage` del navegador, **sin cifrar** — y la Nómina
  guarda DNI, dirección completa, teléfono, contacto de emergencia y, en
  Administración, **CBU/cuenta bancaria y remuneración** (hallazgo **A-02**).
- Cualquiera con el link ve el código fuente y, si hubiese datos reales
  cargados, los vería también (no hay backend que los oculte).

Conectar el Form real de RRHH a esto —aunque sea "solo para uso interno"—
significaría publicar datos personales y bancarios de compañeros reales en un
sitio público. Por eso se frena acá, con el mismo criterio que ya se aplicó al
sync de Notion (`proyectos/SYNC.md`) y a los nombres de contacto de clientes en
`proyectos-data.js`.

## Qué tiene que pasar antes de activarlo

1. **Destino con autenticación real** (dejar de servir esto desde GitHub Pages
   público). Opciones: hosting privado con login (Vercel/Netlify + auth),
   intranet interna, o algo con SSO de Google Workspace de Not a Bot.
2. **Persistencia del lado del servidor**, no `localStorage` del navegador —
   localStorage no cifra nada y no es un lugar seguro para PII ni datos
   bancarios, ni siquiera "mientras tanto".
3. **Acceso de lectura al Google Sheet** de respuestas del Form:
   - Service account de Google Cloud con el Sheet compartido (Sheets API,
     solo lectura), o
   - OAuth de la cuenta de quien es dueño del Form.
4. **Aviso/consentimiento a los empleados** de que sus datos se procesan en
   esta herramienta — esto aplica aparte de ISO 27001, por protección de datos
   personales en general.
5. Definir el comportamiento:
   - ¿Alta nueva vs. edición de un empleado que ya existe? ¿Cómo se matchea
     (por mail, por DNI)?
   - ¿Sync automático (cron, como el de Proyectos) o manual a demanda
     (botón "Importar desde el Form")?
   - ¿Qué campos del Form se traen automático y cuáles sigue completando RRHH
     a mano en el tablero (ej. legajo, centro de costo)?

## Boceto técnico (para cuando se habilite)

Mismo patrón que ya existe para Proyectos (`proyectos/sync-proyectos.mjs`, que
lee de Notion): un script Node que

1. Lee el Sheet vía **Google Sheets API** (paquete `googleapis`).
2. Mapea columnas del Form → campos del objeto empleado (tabla abajo, a
   completar con las columnas reales del Form el día que se retome esto).
3. Crea o actualiza el registro correspondiente **en el backend/API privado**
   del paso 1 — nunca en un archivo `.js` commiteado a este repo público,
   como se hace hoy con `empleados-demo.js`.

## Mapeo de campos (pendiente de completar con el Form real)

| Columna del Form | Campo en la ficha de Nómina |
|---|---|
| _(pendiente)_ | `nombre` |
| _(pendiente)_ | `apellido` |
| _(pendiente)_ | `documento` |
| _(pendiente)_ | `mail` |
| _(pendiente)_ | `telefono` |
| _(pendiente)_ | `direccionCompleta` |
| _(pendiente)_ | `contactoAltNombre` / `contactoAltVinculo` / `contactoAltTelefono` |
| _(pendiente)_ | `cliente` / `proyecto` / `rol` / `dedicacion` |
| _(pendiente)_ | `administracion.banco` / `.cuenta` / `.alias` / `.remuneracion` |

## Mientras tanto

La Nómina sigue funcionando con `empleados-demo.js` (datos 100% ficticios).
No cargar altas reales a mano tampoco, por la misma razón de fondo: el sitio
público no es un lugar seguro para eso todavía.
