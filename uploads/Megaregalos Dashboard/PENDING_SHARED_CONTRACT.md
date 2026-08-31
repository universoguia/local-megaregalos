# PENDING SHARED CONTRACT — MEGAREGALOS

Registro de decisiones provisionales del Dashboard que deberán validarse contra
`MEGAREGALOS_SHARED_DATA_CONTRACT_v1.0` cuando exista.

No registrar acá bugs, tareas visuales ni ideas generales: sólo decisiones que
afecten la integración Ecommerce ↔ Dashboard ↔ Backend/Supabase.

Estado del dashboard al momento de este registro: **v0.0.2** (mocks centralizados,
motor de transiciones único, RBAC por rol/sucursal).

---

## PSC-001 — Enums de estado de pedido (`orderStatus`)

Estado: Pendiente

Área: Order

Dashboard necesita: una máquina de estados operable con caminos separados para
retiro en sucursal y envío.

Implementación provisional:
`pending → confirmed → preparing → packed → (pickup: ready_for_pickup → picked_up)
| (shipping: shipped → delivered)`, más `cancelled` como estado terminal.

Motivo: el ecommerce muestra su propio seguimiento al cliente y puede requerir
menos estados (o nombres distintos) de los que la operación interna necesita.

Debe validarse contra: Checkout ecommerce / seguimiento de pedido.

Impacto si cambia: Alto (afecta motor de transiciones, badges, KPIs, alertas).

Archivos afectados: capa de dominio del dashboard (`canTransition`,
`transitionOrder`), mocks de pedidos, badges y filtros de la tabla.

---

## PSC-002 — Separación `paymentStatus` / `orderStatus`

Estado: Pendiente

Área: Order / Payment

Dashboard necesita: bloquear la preparación de pedidos con transferencia no
acreditada, sin bloquear los pedidos en efectivo contra entrega.

Implementación provisional:
`paymentStatus: pending | paid | refunded | failed`,
`paymentMethod: cash | transfer | mercadopago (provisional)`.
La regla de bloqueo vive en una única función de permisos, no en la UI.

Motivo: el método de pago real dependerá de la integración de pasarela y de cómo
el ecommerce persista el pago (puede aparecer un objeto `Payment` propio).

Debe validarse contra: Checkout ecommerce / Mercado Pago / Backend.

Impacto si cambia: Medio.

Archivos afectados: dominio de pedidos, drawer, alertas, KPIs.

---

## PSC-003 — `fulfillmentType` (retiro vs envío)

Estado: Pendiente

Área: Order / Logística

Implementación provisional: `fulfillmentType: pickup | shipping`, con
`branchId` obligatorio en `pickup` y dirección de envío en `shipping`.

Motivo: el ecommerce puede introducir modalidades adicionales (envío express,
punto de retiro externo, retiro en depósito).

Debe validarse contra: Checkout ecommerce / logística.

Impacto si cambia: Medio-Alto.

---

## PSC-004 — Variantes de producto

Estado: Pendiente

Área: Product

Dashboard necesita: mostrar color / tamaño / presentación en listado, detalle e
inventario.

Implementación provisional: el producto expone una lista plana de variantes
`{ id, label, sku }` consumida sólo a través de un adapter de presentación. No se
asume `product.colors[]` ni matriz de opciones.

Motivo: el modelo definitivo de variantes lo determina el catálogo del ecommerce.

Debe validarse contra: Catálogo ecommerce.

Impacto si cambia: Medio (UI ya soporta N variantes; cambia el mapeo).

---

## PSC-005 — Stock por sucursal y reservas

Estado: Pendiente

Área: Inventory

Dashboard necesita: stock físico, reservado y disponible por producto y sucursal,
más umbrales de stock crítico.

Implementación provisional: `disponible = fisico - reservado`, umbral crítico
configurable por producto (default provisional: 5 unidades).

Motivo: la reserva real depende de cuándo el checkout del ecommerce reserva stock
(al agregar al carrito, al confirmar o al pagar).

Debe validarse contra: Checkout ecommerce / Backend (RLS y concurrencia).

Impacto si cambia: Alto.

---

## PSC-006 — Identidad de cliente

Estado: Pendiente

Área: Customer

Implementación provisional: `customer.name` como campo único de presentación,
teléfono como identificador operativo para WhatsApp.

Motivo: el ecommerce puede persistir `firstName` + `lastName`, y la identidad
puede pasar a depender de Supabase Auth (compras de invitado incluidas).

Debe validarse contra: Ecommerce / Supabase Auth.

Impacto si cambia: Bajo si se mantiene el adapter de clientes.

---

## PSC-007 — Promociones

Estado: Pendiente

Área: Promotion

Implementación provisional: sólo UX; ninguna regla de acumulación ni cálculo de
descuento se implementa en el dashboard. Los montos de pedido llegan ya
calculados desde el mock.

Motivo: el motor de promociones pertenece al checkout del ecommerce.

Debe validarse contra: Checkout ecommerce.

Impacto si cambia: Bajo mientras el dashboard no calcule descuentos.

---

## PSC-008 — Identificadores y dinero

Estado: Pendiente (bajo riesgo)

Área: Transversal

Implementación provisional: IDs string estables (`ORD-0001`, `BR-01`), dinero como
número entero en pesos, formateo sólo en presentación, timestamps ISO 8601.

Motivo: el backend puede usar UUID y/o centavos.

Debe validarse contra: Backend / Supabase.

Impacto si cambia: Bajo (aislado en adapters y formateadores).

---

## Al llegar el Shared Contract

Para cada PSC determinar: **compatible / requiere adaptación / obsoleto /
incorporado**. Marcar `RESOLVED` con la decisión definitiva y conservar el
historial.
