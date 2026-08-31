# MegaRegalos · Dashboard operativo v0.0.3

## Cómo abrir
Abrir `index.html` en el navegador. Es un archivo autónomo (sin build, sin rutas
locales, con assets embebidos). Es también el entry point para GitHub/Vercel.

## Estructura
```
MegaRegalos-Dashboard-v0.0.3/
├── index.html                  entrega desplegable (derivada de src/)
├── PENDING_SHARED_CONTRACT.md  PSC-001 … PSC-008
├── LEEME.md
├── assets/                     logo + imágenes de catálogo
└── src/
    ├── Dashboard MegaRegalos v0.0.3.dc.html   única fuente mantenible
    └── support.js
```
`index.html` no se edita: se regenera desde `src/`.

## Novedades v0.0.3
- **Productos**: listado con imagen, SKU, nombre, categoría, precio, stock total
  y estado (Disponible / Stock crítico / Sin stock). Búsqueda y filtro por categoría.
- **Inventario**: stock por producto y sucursal con físico, reservado y disponible.
  El **reservado se deriva de los pedidos abiertos reales** (pago pendiente, en
  preparación, empaquetado, listo para retiro), no de datos inventados. Filtro por
  sucursal y contador de filas críticas.
- **Clientes**: listado (pedidos, gasto total, sucursal habitual, última compra)
  y ficha lateral con historial de pedidos enlazado al drawer de pedido.
- Los clientes son un **selector derivado de `Order`**, no una entidad nueva
  (regla: no duplicar conceptos). El teléfono actúa como identificador operativo.
- B2B, Promociones, Sucursales y Configuración siguen como "Próximamente".

## Provisionalidades
El stock físico está simulado de forma determinística (aún no hay backend). El
umbral de stock crítico es 5 unidades. Ambos puntos, más los enums de estado,
pago, fulfillment, variantes e identidad de cliente, están registrados en
`PENDING_SHARED_CONTRACT.md` (PSC-001 a PSC-008) para revisar cuando llegue
`MEGAREGALOS_SHARED_DATA_CONTRACT_v1.0`.

## Pendiente
Supabase (datos semilla), modularización en dominio/servicios/adapters para el
puerto a React+TS, y los módulos B2B, Promociones, Reportes y Configuración.
