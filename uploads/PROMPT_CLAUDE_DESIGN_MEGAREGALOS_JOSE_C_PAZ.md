# Prompt para Claude Design — MegaRegalos José C. Paz

Quiero que trabajes sobre el proyecto adjunto **`Megaregalos Dashboard.zip`**. El dashboard actual ya está aprobado visualmente: su estética, composición, identidad, colores, tipografías, densidad, tablas, tarjetas, drawers, espaciados y navegación deben conservarse. Esto es una adaptación funcional de alcance, no un rediseño.

## Contexto y objetivo

MegaRegalos necesita una **demo funcional, presentable y vendible en unas horas** para una aplicación interna de empleados y dueño, inicialmente para **un único local: José C. Paz**. Se usa para operación diaria, pedidos, productos, inventario y clientes. No estamos construyendo todavía el e-commerce público ni una solución multisucursal.

Aunque hoy sea una demo, la base debe ser una arquitectura vendible: separar reglas de negocio de la UI, centralizar configuración, definir servicios/adapters reemplazables y dejar claro qué es mock/demo y qué se conectará luego a backend.

El resultado debe sentirse como el mismo dashboard que recibiste, pero coherente de punta a punta con:

1. un solo local: **MegaRegalos — José C. Paz**;
2. autenticación real/reutilizable con roles;
3. un flujo QR claramente marcado como **demo**;
4. todos los datos y estados funcionando de manera consistente en el prototipo.

## Regla principal: preservar el dashboard

- No cambies el lenguaje visual ni “modernices” el producto.
- No reemplaces componentes aprobados por otra librería visual.
- No cambies paleta, tipografía, radios, sombras, jerarquía, sidebar, header, tarjetas, tablas ni drawers salvo lo estrictamente necesario para incorporar login y eliminar multisucursal.
- No rehagas la arquitectura sin necesidad.
- Respeta `LEEME.md`: la fuente mantenible es `src/Dashboard MegaRegalos v0.0.3.dc.html`; `index.html` es una entrega generada y no debe convertirse en una segunda fuente divergente.
- Conserva los assets y datos que ya funcionan.
- Antes de editar, inspecciona `LEEME.md`, `PENDING_SHARED_CONTRACT.md`, la fuente de `src/` y `support.js`.

## Cambio 1 — Convertir todo a un solo local

El único local es:

- Nombre: `MegaRegalos — José C. Paz`
- ID interno estable sugerido: `jose-c-paz`
- Zona horaria: `America/Argentina/Buenos_Aires`
- Moneda: ARS

Elimina de la experiencia toda señal de multisucursal:

- quitar el selector de sucursal del header;
- quitar la opción “Todas las sucursales”;
- quitar el módulo de navegación “Sucursales”;
- quitar comparaciones y tarjetas “Pedidos por sucursal”;
- quitar filtros de sucursal en Inventario, Productos, Clientes y Pedidos;
- reemplazar “Sucursal habitual”, “Sucursal / dirección” y textos similares por campos apropiados para una sola tienda, por ejemplo “Entrega”, “Retiro en José C. Paz” o “Dirección de envío”;
- todos los pedidos, productos, clientes, movimientos y usuarios pertenecen al mismo local;
- no dejar IDs `b1...b5`, nombres de Tigre, Escobar, San Martín, Pilar u otras sucursales en mocks, filtros, mensajes de WhatsApp, métricas o drawers;
- mantener retiro en local y envío a domicilio como modalidades de entrega, pero retiro siempre corresponde a José C. Paz.

No simules una arquitectura multisucursal invisible. Simplifica el modelo y los mocks para una tienda, manteniendo IDs estables y una futura migración razonable.

## Cambio 2 — Login y permisos (demo lista para vender)

Agrega una pantalla de login con la misma identidad visual de MegaRegalos. Debe parecer parte del producto actual, no una plantilla genérica.

Usa como referencia primaria la autenticación de **Lidera**, disponible en los materiales adjuntos o en estas rutas de referencia:

- `lidera-app/`
- `lidera-ingresar.html`
- `lidera-accesos.html`
- `lidera-app/SUPABASE_SETUP.md`
- `lidera-app/TRASPASO_2026-08-18_recuperar-contrasena.md`

Usa Practigram sólo como referencia secundaria de UX si encuentras allí una implementación real de la aplicación:

- `rescate-dashboard-practigram/`
- `rescate-dashboard-practigram/login-manual.js`
- `rescate-dashboard-practigram/dashboard-practigram-rescate.zip`

No copies secretos, contraseñas reales, tokens, cookies, sesiones, archivos `.env`, contenido de HAR ni credenciales personales. No uses el HAR como fuente de código. Reutiliza patrones y componentes, no datos sensibles.

Requisitos del login:

- campos de email y contraseña;
- mostrar/ocultar contraseña;
- validaciones claras en español;
- estados de carga, error y sesión expirada;
- “Olvidé mi contraseña” con flujo de solicitud y restablecimiento;
- mantener sesión y cerrar sesión;
- rutas internas protegidas: nunca mostrar el dashboard antes de validar la sesión;
- el rol no se elige en el header: proviene del perfil autenticado;
- quitar el selector actual de “Actor simulado”.

Roles iniciales:

- `admin`: acceso total, usuarios, configuración, ajustes de stock, precios, altas/bajas, pedidos y reportes;
- `encargado`: operación del local, pedidos, inventario, productos y proveedores, sin administrar usuarios ni configuración sensible;
- `empleado`: consulta y operación diaria, escaneo QR demo, entradas/salidas permitidas y avance de pedidos según permisos; sin cambios sensibles.

Para esta entrega implementa un `DemoAuthService` funcional, con sesión persistida y usuarios demo claramente identificados. Define el contrato para poder reemplazarlo después por Supabase Auth sin rehacer las pantallas. Si el entorno permite Supabase, deja también preparado el adapter siguiendo el patrón de Lidera, con `role`, `display_name`, `active` y el local fijo `jose-c-paz`. Aplica autorización en la capa de dominio/servicio, no sólo ocultando botones.

Si Claude Design no puede conectar un backend, construye un adaptador `AuthService` con dos implementaciones intercambiables: `SupabaseAuthService` y `DemoAuthService`. El modo demo debe estar explícitamente rotulado y nunca mezclar credenciales demo con producción. Deja documentadas las variables de entorno necesarias sin incluir valores secretos.

## Cambio 3 — Sistema QR demo

El QR es una demostración funcional del flujo futuro; no debe fingir integración con cámara, impresora, stock real o POS si no existe.

Incorpóralo dentro de **Productos & Stock**, respetando el diseño actual, con estas capacidades:

- pestaña o acción “QR demo” integrada al módulo, no un producto visual separado;
- generar un QR demo por categoría y, opcionalmente, por producto/SKU;
- payload estable y no sensible, por ejemplo `megaregalos-demo://jose-c-paz/category/{slug}` o `.../product/{sku}`;
- ver QR, imprimir ficha y descargar imagen si el entorno lo permite;
- botón “Simular escaneo” que abra un modal/drawer y permita elegir un QR de prueba;
- al simular el escaneo, mostrar categoría/producto, SKU, stock actual y acciones permitidas: `Entrada`, `Salida` y `Ajuste`;
- pedir cantidad, motivo y confirmación antes de modificar el stock demo;
- registrar un historial local de movimientos demo con fecha, usuario, tipo, cantidad, motivo y stock anterior/nuevo;
- marcar el módulo y cada resultado con un badge visible `DEMO`;
- mostrar una aclaración discreta: “Escaneo y movimientos simulados; no impactan inventario real”.

No solicites permisos reales de cámara como requisito. Puedes ofrecer “Usar cámara” únicamente como acción deshabilitada o futura, explicando que no está conectada. El flujo principal debe poder probarse en desktop y móvil sin hardware.

## Inventario y operación

Mantén los módulos ya aprobados y hazlos coherentes con una tienda:

- Inicio operativo;
- Pedidos y su drawer;
- Productos & Stock;
- Inventario físico, reservado y disponible;
- Clientes;
- módulos “Próximamente” sólo si ya existen, excepto “Sucursales”, que debe desaparecer.

Agrega sin rediseñar:

- alta y edición simple de producto con foto, nombre, categoría, variante/presentación, SKU, precio y stock;
- entradas, salidas y ajustes de stock con validación para no quedar en negativo;
- historial de movimientos;
- proveedor asociado y cantidad sugerida a pedir, como demo si no hay backend;
- estados vacíos, errores, carga y confirmaciones consistentes con el dashboard.

No implementes e-commerce público, checkout de cliente, multiempresa ni multisucursal en esta etapa. Deja interfaces claras para conectar esas capacidades después sin rehacer la UI.

## Datos demo y consistencia

- Usa exclusivamente nombres, direcciones y mensajes compatibles con José C. Paz.
- Conserva el catálogo y las imágenes actuales siempre que sea posible.
- Etiqueta datos simulados cuando corresponda.
- El stock reservado debe seguir derivándose de pedidos abiertos; no dupliques conceptos.
- Toda mutación de stock debe pasar por una única función/servicio.
- Los mensajes de retiro deben mencionar MegaRegalos José C. Paz y nunca otra sucursal.
- Actualiza `PENDING_SHARED_CONTRACT.md` para reflejar tienda única, autenticación/roles y QR demo.

## Responsive y accesibilidad

- Conserva el comportamiento desktop actual.
- En móvil, sidebar, login, tablas, drawers y modal QR deben seguir siendo utilizables.
- Mantén navegación por teclado, foco visible, labels, contraste y mensajes accesibles.
- No uses texto diminuto ni controles táctiles demasiado pequeños.

## Forma de trabajo

1. Primero inspecciona el proyecto y resume en pocas líneas qué conservarás y qué archivos tocarás.
2. Identifica todo rastro de multisucursal antes de editar.
3. Implementa los cambios en la fuente mantenible.
4. Regenera la entrega final siguiendo el mecanismo existente.
5. Prueba todos los flujos principales y corrige errores reales.
6. Entrega un resumen de cambios, decisiones, archivos modificados y limitaciones demo.

No te detengas a pedir confirmación por decisiones menores. Si falta una dirección exacta del local, usa “José C. Paz, Buenos Aires” como placeholder claramente marcado y centralízalo en configuración para reemplazarlo después.

## Criterios de aceptación

El trabajo queda aprobado sólo si:

- visualmente sigue siendo el dashboard original;
- no existe ningún selector, filtro, métrica, mock o navegación multisucursal;
- todo el sistema opera sobre José C. Paz;
- login, logout, protección de rutas, recuperación y roles están resueltos o correctamente adaptados mediante `AuthService`;
- ya no se puede cambiar el rol desde el header;
- el QR demo se puede generar, visualizar y “escanear” de forma simulada;
- una entrada/salida demo actualiza stock e historial de manera consistente;
- no hay secretos ni credenciales reales en el código;
- la demo puede mostrarse y venderse en unas horas, pero sus servicios/adapters permiten reemplazar mocks por backend sin rehacer la interfaz;
- no se rompe Inicio, Pedidos, Productos & Stock, Inventario ni Clientes;
- la entrega final abre y funciona según `LEEME.md`.
