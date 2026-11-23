# Registro de Ventas en Solana con PDAs

**Participante para el certificado:** Yair Carbajal López
**Correo del registro:** yaircarbajallopez@gmail.com
**Repositorio:** https://github.com/yaircarbajallopez-code/registro-ventas-solana

## Ruta elegida

**Proyecto guiado con una mejora funcional** (WayLearn · Solana Engineering Track: Advanced).

**Versión de partida:** repositorios `WayLearnLatam/Registro-Ventas-Solana-App` (frontend) y `WayLearnLatam/Registro-Ventas-Solana-PDA` (programa), versión resuelta de las sesiones 1 a 5.

## Propósito

Aplicación para un negocio pequeño: un **Registro** del negocio, sus **Clientes** y las **Ventas** de cada cliente. Cada entidad vive en su propia cuenta PDA en Solana Devnet:

- `registro` ← `["registro", nombre_negocio, owner]`
- `cliente` ← `["cliente", registro, id_cliente]`
- `venta` ← `["venta", cliente, sku]`

Stack: Rust + Anchor (programa), TypeScript + React + Vite (frontend), cliente generado con Codama a partir del IDL, wallet Solflare.

## Despliegue en Devnet

- **Program ID:** `7dPZZDLYYy8uNb5mthSwwXrVaTDVUuLpJvi2JqFcYTb6`
- **Explorer:** https://explorer.solana.com/address/7dPZZDLYYy8uNb5mthSwwXrVaTDVUuLpJvi2JqFcYTb6?cluster=devnet

## Aporte propio

Sobre la base del curso agregué:

1. **Baja de cliente (`baja_cliente`)**, con su operación en el cliente TypeScript y una pestaña nueva en la interfaz. La regla del programa solo la permite si el cliente no tiene ventas activas; en ese caso cierra la cuenta del cliente (el rent vuelve al owner) y retira su referencia del registro. Si tiene ventas, la rechaza con el error `ClienteConVentasActivas` (6002).
2. **Validación de monto en Rust:** `registrar_venta` y `cambiar_monto` rechazan un monto igual a 0 con el error `MontoInvalido` (6003). Esta regla está en el programa, no solo en el formulario.

Cambios en el código: `programa/src/lib.rs`, `src/solana/acciones.ts` y `src/components/Registro.tsx`.

## Cómo ejecutarlo

Requiere Node.js 22 y una wallet de navegador (Solflare) en **Devnet** con SOL de prueba.

```
npm install
npm run cliente
npm run dev
```

Abre `http://localhost:5173` en el navegador donde tienes tu wallet. También funciona en GitHub Codespaces (puerto 5173).

## Cómo probarlo

Con el negocio `Cafe WayLearn D1` y la wallet conectada:

1. Crear registro.
2. Crear cliente `Ana`.
3. Registrar venta: cliente 1, SKU `CAF01`, descripción `Cafe americano`, importe 50.
4. Baja de cliente 1 con la venta activa: el programa la rechaza (`ClienteConVentasActivas`).
5. Devolución: cliente 1, SKU `CAF01`.
6. Baja de cliente 1: se confirma y la lista queda sin clientes.

## Pruebas y evidencias

| Prueba | Resultado | Evidencia |
|---|---|---|
| Crear registro | Confirmada | [Transacción](https://explorer.solana.com/tx/2sZLoXteDgPYWYmNjJkj1nH4Fz9XNxXoUEe5937guP3P1gkH1QewvGBtuE4HDruTazSfiNTcgcKgGqNinK6T96i8?cluster=devnet) · `docs/01-registro.png` |
| Crear cliente | Confirmada | [Transacción](https://explorer.solana.com/tx/5F2LBYhM2vfqLZthQUZkxytvby4aPnGoRcxaMpc68DKfZsCYcHqFCBPCN3ZMLWvBbAqceJMT5UiGRHkb3beSVmda?cluster=devnet) |
| Registrar venta | Confirmada | [Transacción](https://explorer.solana.com/tx/3oNCyqDE6TVFA8eGBtYqrid5Utd5WCDKyNH8tTJswX5ch8KBL3Fwn6soAuYm7XfR5w6rkQhnA7orfshjt84yacZQ?cluster=devnet) · `docs/02-venta.png` |
| Baja de cliente con ventas activas | Rechazada por el programa en la simulación de la wallet (no se envía transacción) | `docs/03-baja_rechazada.png` |
| Devolución | Confirmada | [Transacción](https://explorer.solana.com/tx/9VzTHU3V86iFQ8e4fXfzMVjtVYoxuAqEs5ihaD73Q4QVbRoa1UX86Tw6d5rDT5P5DQwMkWzMFgCjAe2mN1EvRBG?cluster=devnet) |
| Baja de cliente sin ventas | Confirmada | [Transacción](https://explorer.solana.com/tx/3VMGhJufdRdoDnMhAQmAXAAZDMtfzWtWFX6r4gPrYspCEKhfkg2DbaTg8uctCxzGaiub5rKPYTN9SrNBdBQ8SkN3?cluster=devnet) · `docs/04-baja_exitosa.png` |

Nota sobre el monto 0: el formulario impide enviar 0, y además el programa lo rechaza con `MontoInvalido` (6003).
