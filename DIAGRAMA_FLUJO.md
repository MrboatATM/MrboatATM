# DIAGRAMA DE FLUJO - SISTEMA ERP BRADA SERVICES

## 1. FLUJO GENERAL DEL SISTEMA

```mermaid
graph TD
    A[Usuario Accede al Sistema] --> B{¿Autenticado?}
    B -->|No| C[Login Page]
    C --> D[Validar Credenciales]
    D -->|Válido| E[Dashboard Principal]
    D -->|Inválido| C
    B -->|Sí| E

    E --> F{Seleccionar Módulo}

    F -->|Fase 1| G[Gestión de Usuarios]
    F -->|Fase 2| H[Clientes y Contratos]
    F -->|Fase 3| I[Menús y Planificación]
    F -->|Fase 4| J[Inventario y Compras]

    G --> E
    H --> E
    I --> E
    J --> E
```

## 2. FASE 1 - FUNDACIÓN Y ACCESO

```mermaid
graph LR
    A[Sistema de Usuarios] --> B[CRUD Usuarios]
    A --> C[Control de Acceso]
    A --> D[Sistema de Logs]

    B --> E[(USERS Sheet)]
    C --> F[Validación Roles]
    D --> G[(LOGS Sheet)]

    F --> H{Rol?}
    H -->|Admin| I[Acceso Total]
    H -->|Usuario| J[Acceso Limitado]
```

## 3. FASE 2 - CLIENTES Y CONTRATOS

```mermaid
graph TD
    A[Módulo Clientes] --> B[Crear Cliente]
    A --> C[Listar Clientes]
    A --> D[Editar Cliente]
    A --> E[Eliminar Cliente]

    B --> F[(CLIENTES Sheet)]
    C --> F
    D --> F
    E --> F

    F --> G[Ver Contratos del Cliente]
    G --> H[Módulo Contratos]

    H --> I[Crear Contrato]
    H --> J[Gestionar Contrato]
    H --> K[Estados de Contrato]

    I --> L[(CONTRATOS Sheet)]
    J --> L
    K --> M[(ESTADOS_CONTRATO Sheet)]

    L --> N[Asignar Menús]
    N --> O[Módulo Planificación]
```

## 4. FASE 3 - MENÚS Y PLANIFICACIÓN

```mermaid
graph TD
    A[Módulo Menús] --> B[Crear Menú]
    A --> C[Gestionar Recetas]

    B --> D[(MENUS Sheet)]
    C --> E[(RECETAS Sheet)]
    C --> F[(INGREDIENTES_RECETA Sheet)]

    D --> G[Planificación]
    E --> G

    G --> H[Asignar Menú a Contrato]
    H --> I[Calcular Ingredientes]

    I --> J[(PLANIFICACION Sheet)]
    J --> K[Generar Lista de Compras]

    K --> L[Módulo Inventario]
```

## 5. FASE 4 - INVENTARIO Y COMPRAS (Flujo Completo)

```mermaid
graph TD
    START[Inicio Fase 4] --> A[Gestión de Proveedores]

    A --> B[Crear Proveedor]
    A --> C[Listar Proveedores]

    B --> D[(PROVEEDORES Sheet)]
    C --> D

    D --> E[Gestión de Productos]

    E --> F[Crear Producto]
    E --> G[Ajustar Stock]

    F --> H[(PRODUCTOS Sheet)]
    G --> H

    G --> I[Registrar Movimiento]
    I --> J[(MOVIMIENTOS_INVENTARIO Sheet)]

    H --> K{¿Stock Bajo?}
    K -->|Sí| L[Alerta Stock Bajo]
    K -->|No| M[Stock Normal]

    L --> N[Crear Orden de Compra]

    N --> O[Seleccionar Proveedor]
    O --> P[Agregar Productos al Carrito]
    P --> Q[Calcular Totales con IVA]

    Q --> R[(ORDENES_COMPRA Sheet)]
    R --> S[(DETALLE_ORDEN_COMPRA Sheet)]

    R --> T{Estado Orden}

    T -->|Pendiente| U[Aprobar Orden]
    U --> V[Orden Aprobada]

    V --> W[Recibir Orden]
    W --> X[Actualizar Stock]

    X --> H
    X --> I

    T -->|Cancelada| Y[Orden Cancelada]

    X --> Z[Dashboard Inventario]
    Z --> AA[Reportes]
    Z --> AB[Alertas]
    Z --> AC[Movimientos]
    Z --> AD[Top Productos]
```

## 6. FLUJO DE ORDEN DE COMPRA (Detallado)

```mermaid
stateDiagram-v2
    [*] --> Crear_Orden

    Crear_Orden --> Seleccionar_Proveedor
    Seleccionar_Proveedor --> Agregar_Productos

    Agregar_Productos --> Agregar_Productos: Agregar más productos
    Agregar_Productos --> Calcular_Totales

    Calcular_Totales --> Pendiente: Guardar Orden

    Pendiente --> Aprobada: Aprobar
    Pendiente --> Cancelada: Cancelar

    Aprobada --> Recibida: Recibir Productos
    Aprobada --> Cancelada: Cancelar

    Recibida --> Actualizar_Inventario
    Actualizar_Inventario --> Registrar_Movimientos

    Registrar_Movimientos --> [*]
    Cancelada --> [*]
```

## 7. FLUJO DE MOVIMIENTOS DE INVENTARIO

```mermaid
graph LR
    A[Evento Dispara Movimiento] --> B{Tipo Movimiento}

    B -->|Entrada| C[Compra Recibida]
    B -->|Salida| D[Uso en Producción]
    B -->|Ajuste| E[Ajuste Manual]

    C --> F[Calcular Nuevo Stock]
    D --> F
    E --> F

    F --> G[Stock Anterior]
    F --> H[Stock Nuevo]

    G --> I[Registrar en MOVIMIENTOS_INVENTARIO]
    H --> I

    I --> J[Actualizar PRODUCTOS.Stock_Actual]

    J --> K{Stock <= Mínimo?}
    K -->|Sí| L[Generar Alerta]
    K -->|No| M[Fin]

    L --> N[Mostrar en Dashboard]
    N --> M
```

## 8. ARQUITECTURA DE DATOS

```mermaid
erDiagram
    USERS ||--o{ LOGS : genera
    CLIENTES ||--o{ CONTRATOS : tiene
    CONTRATOS ||--o{ PLANIFICACION : incluye
    MENUS ||--o{ RECETAS : contiene
    MENUS ||--o{ PLANIFICACION : asigna
    RECETAS ||--o{ INGREDIENTES_RECETA : requiere

    PROVEEDORES ||--o{ PRODUCTOS : suministra
    PROVEEDORES ||--o{ ORDENES_COMPRA : recibe
    PRODUCTOS ||--o{ DETALLE_ORDEN_COMPRA : incluye
    PRODUCTOS ||--o{ MOVIMIENTOS_INVENTARIO : registra
    ORDENES_COMPRA ||--o{ DETALLE_ORDEN_COMPRA : contiene

    CATEGORIAS_PRODUCTO ||--o{ PRODUCTOS : clasifica
    UNIDADES_MEDIDA ||--o{ PRODUCTOS : mide
```

## 9. FLUJO DE NAVEGACIÓN DEL USUARIO

```mermaid
graph TD
    A[Login] --> B[Dashboard]

    B --> C[Clientes]
    B --> D[Contratos]
    B --> E[Menús]
    B --> F[Planificación]
    B --> G[Proveedores]
    B --> H[Productos]
    B --> I[Órdenes de Compra]
    B --> J[Inventario]

    C --> C1[Crear/Editar/Ver]
    D --> D1[Crear/Editar/Ver]
    E --> E1[Crear Menú]
    E1 --> E2[Agregar Recetas]
    E2 --> E3[Agregar Ingredientes]

    F --> F1[Asignar Menú a Contrato]
    F1 --> F2[Calcular Ingredientes]

    G --> G1[CRUD Proveedores]
    H --> H1[CRUD Productos]
    H1 --> H2[Ajustar Stock]

    I --> I1[Crear Orden]
    I1 --> I2[Carrito de Compras]
    I2 --> I3[Aprobar]
    I3 --> I4[Recibir]

    J --> J1[Reportes]
    J --> J2[Movimientos]
    J --> J3[Alertas]
    J --> J4[Top Productos]
```

## 10. FLUJO DE CÁLCULO DE INGREDIENTES (Planificación)

```mermaid
graph TD
    A[Seleccionar Contrato] --> B[Ver Cantidad Comensales]
    B --> C[Seleccionar Menú]
    C --> D[Obtener Recetas del Menú]

    D --> E{Para cada Receta}
    E --> F[Obtener Ingredientes]
    F --> G[Cantidad Base Ingrediente]

    G --> H[Calcular: Cantidad × Comensales / Porciones]
    H --> I[Sumar Ingredientes Repetidos]

    I --> J[Generar Lista Consolidada]
    J --> K[Mostrar Total Requerido]

    K --> L[Verificar Stock Disponible]
    L --> M{¿Stock Suficiente?}

    M -->|No| N[Lista de Faltantes]
    M -->|Sí| O[Listo para Producción]

    N --> P[Sugerencia Orden Compra]
```

## 11. FLUJO DE AUTENTICACIÓN Y AUTORIZACIÓN

```mermaid
sequenceDiagram
    participant U as Usuario
    participant L as Login.html
    participant A as Auth.js
    participant S as USERS Sheet
    participant D as Dashboard

    U->>L: Ingresa credenciales
    L->>A: loginUser(username, password)
    A->>S: Buscar usuario
    S-->>A: Datos usuario

    alt Usuario encontrado y activo
        A->>A: Verificar contraseña
        alt Contraseña correcta
            A->>S: Actualizar último acceso
            A-->>L: {success: true, user: {...}}
            L->>D: Redirigir a Dashboard
        else Contraseña incorrecta
            A-->>L: {success: false, message: "..."}
            L->>U: Mostrar error
        end
    else Usuario no encontrado
        A-->>L: {success: false, message: "..."}
        L->>U: Mostrar error
    end
```

## 12. FLUJO DE CREACIÓN DE PRODUCTO CON STOCK INICIAL

```mermaid
sequenceDiagram
    participant U as Usuario
    participant P as ProductosPage
    participant PJS as Productos.js
    participant I as Inventario.js
    participant PS as PRODUCTOS Sheet
    participant MS as MOVIMIENTOS Sheet

    U->>P: Llena formulario producto
    U->>P: Ingresa stock inicial = 100
    P->>PJS: createProducto({nombre, stockActual: 100, ...})

    PJS->>PJS: Validar datos
    PJS->>PJS: Generar ID y código
    PJS->>PS: Insertar producto

    alt Stock inicial > 0
        PJS->>I: registrarMovimientoInventario({...})
        I->>I: Generar ID movimiento
        I->>MS: Insertar movimiento (Entrada, 100, Stock Inicial)
        MS-->>I: OK
        I-->>PJS: {success: true}
    end

    PJS-->>P: {success: true, id: X, codigo: "PROD-0001"}
    P->>U: Mostrar "Producto creado exitosamente"
    P->>P: Recargar lista productos
```

## 13. RESUMEN DE HOJAS Y SU PROPÓSITO

```mermaid
mindmap
  root((BRADA SERVICES
    ERP))
    FASE 1
      USERS
        Autenticación
        Roles
      LOGS
        Auditoría
      CONFIGURACION
        Parámetros
    FASE 2
      CLIENTES
        Empresas
        Escuelas
      CONTRATOS
        Servicios
        Estados
      TIPOS_CLIENTE
      ESTADOS_CONTRATO
    FASE 3
      MENUS
        Planificación
      RECETAS
        Ingredientes
      INGREDIENTES_RECETA
      PLANIFICACION
        Asignación
      CATEGORIAS_MENU
    FASE 4
      PROVEEDORES
        Suministros
      PRODUCTOS
        Catálogo
        Stock
      CATEGORIAS_PRODUCTO
      ORDENES_COMPRA
        Workflow
      DETALLE_ORDEN_COMPRA
      MOVIMIENTOS_INVENTARIO
        Trazabilidad
      UNIDADES_MEDIDA
```

---

## NOTAS DE IMPLEMENTACIÓN

### Flujo de Estados de Orden de Compra:
1. **Pendiente** → Orden creada, esperando aprobación
2. **Aprobada** → Orden confirmada, lista para recibir
3. **Recibida** → Productos recibidos, inventario actualizado
4. **Cancelada** → Orden anulada (puede cancelarse en Pendiente o Aprobada)

### Tipos de Movimientos de Inventario:
- **Entrada**: Compras, devoluciones, ajustes positivos
- **Salida**: Uso en producción, ventas, ajustes negativos
- **Ajuste**: Correcciones manuales de inventario

### Cálculo de Totales en Orden de Compra:
```
Subtotal = Σ(Cantidad × Precio Unitario)
Impuesto = Subtotal × 18% (IVA)
Total = Subtotal + Impuesto
```

### Fórmula de Cálculo de Ingredientes:
```
Cantidad Requerida = (Cantidad Base × Comensales) / Porciones Receta
```

---

**Generado para:** Brada Services ERP System
**Versión:** Fase 4 Completa
**Fecha:** 2025-01-25
