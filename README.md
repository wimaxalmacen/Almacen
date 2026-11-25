# QR-Wimax · Gestión de almacén con códigos QR

Sistema web para controlar el inventario del almacén de **Importaciones Wimax E.I.R.L.** con códigos QR. Funciona desde el celular o la computadora, se instala como app (PWA) y guarda los datos en Google Sheets.

## Qué hace

| Módulo | Para qué sirve |
|---|---|
| **Inicio** | Pedidos por atender, entregados hoy, tiempo promedio de abastecimiento frente a la meta y productos por reponer. |
| **Inventario** | Búsqueda por modelo, color, código o ubicación, con filtros por familia y estado. Cada producto tiene su ficha con stock, ubicación, precio y kardex. |
| **Escanear** | Lee los QR con la cámara o desde una foto. Tiene tres modos: **Consultar** (abre la ficha), **Conteo físico** (compara con el sistema y calcula la exactitud) y **Entrada** (mercadería del proveedor). |
| **Nuevo pedido** | El vendedor arma el pedido y lo envía al almacén. Se clasifica solo como Unitario, Mediano o Grande. |
| **Pedidos** | Lista de alistamiento ordenada por ubicación. Al marcar **Entregado** se descuenta el stock y se registra el tiempo total. |
| **Reportes** *(supervisor)* | Indicadores frente a la línea base, reposición sugerida, clasificación ABC, productos sin movimiento y registros. Todo se exporta a Excel (CSV). |
| **Etiquetas QR** *(supervisor)* | Genera e imprime etiquetas. Al escanear una con cualquier celular se abre la ficha del producto. |
| **Alertas por correo** | Avisos automáticos de productos por reponer y pedidos atrasados, más un resumen diario y otro semanal (ver abajo). |

### Indicadores del proyecto

| Indicador | Antes del sistema | Meta |
|---|---|---|
| Tiempo de abastecimiento | 24 min | 17 min |
| Exactitud del inventario | 83,33 % | 95 % |
| Ventas perdidas por falta de stock | 56 al mes | 0 |

## Cómo está hecho

```
Celular / PC ──► index.html (GitHub Pages) ──► Apps Script (API) ──► Google Sheets
                                                    ▲
                     Alertas.gs (correos automáticos) ┘
```

| Archivo | Contenido |
|---|---|
| `index.html` | La aplicación completa: diseño, animaciones y lógica. |
| `manifest.json`, `sw.js`, `icon-*.png` | Permiten instalarla como app en el celular y que abra rápido. |
| `Alertas.gs` | Correos automáticos. Va en el proyecto de Apps Script de la hoja. |
| `Code.gs` *(en Apps Script)* | API que valida la clave y lee/escribe la hoja `Sistema_QR_Wimax`. |

- **Seguridad:** la clave no está en la página; la valida Apps Script en cada consulta. Hay dos roles: **Almacén** y **Supervisor**.
- **Sin instalar nada:** usa solo HTML, CSS y JavaScript. Las librerías de QR (`html5-qrcode` para leer y `qrcode-generator` para crear) se cargan solo cuando se necesitan.
- **Animaciones:** se apagan solas si el celular tiene activado "reducir movimiento".

## Uso

1. Abra la página en el celular y toque **Agregar a pantalla de inicio** para instalarla.
2. Ingrese con su nombre y su clave.
3. Para escanear, dé permiso de cámara al navegador. La cámara solo funciona si la página se abre con `https://`.

## Alertas automáticas por correo (`Alertas.gs`)

| Aviso | Frecuencia |
|---|---|
| Productos que pasan a **REPONER** (un aviso por producto, sin repetir) | Cada hora |
| Pedidos que pasan los **17 min** sin entregarse | Cada 10 min |
| Resumen del día anterior | Diario, 7 a. m. |
| Resumen de la semana | Lunes, 7 a. m. |

**Instalación (una sola vez):**

1. En la hoja de Google: **Extensiones → Apps Script**. Agregue un archivo nuevo llamado `Alertas` y pegue el contenido de `Alertas.gs`.
2. En **Configuración del proyecto → Propiedades del script**, agregue:
   - `ALERTAS_PIN`: la clave de un usuario supervisor.
   - `ALERTAS_CORREOS`: los correos que recibirán los avisos, separados por coma.
3. Elija la función `instalar` y toque **▶ Ejecutar**. Acepte los permisos.
4. Para probar: ejecute `probarTodo`. Para apagar las alertas: ejecute `desinstalar`.

## Publicar cambios

Con **GitHub Pages** activado (Settings → Pages → rama `main`), cualquier cambio en `index.html` aparece en la página en uno o dos minutos. Si el celular sigue mostrando la versión anterior, cierre la app y vuelva a abrirla.
