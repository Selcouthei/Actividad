# Prueba Frontend 525

Aplicación web frontend construida con **Angular 21** que implementa un sistema de gestión de usuarios con operaciones CRUD completas. Incluye un formulario de contacto con validaciones reactivas y una tabla de datos con búsqueda y ordenamiento .

---

## Características

- **Gestión de Usuarios (CRUD):** Crear, consultar, editar y eliminar usuarios con persistencia en `localStorage`.
- **Formulario de Contacto Reactivo:** Formulario tipado con `FormControl<T>` estricto, selector de fecha nativo via `MatDatepicker`, y selector de género sincronizado con el API.
- **Tabla de Datos interactiva:**
  - Búsqueda global en tiempo real (case-insensitive)
  - Ordenamiento por columna (case-insensitive, fechas cronológicas)
- **Tema globalizado:** Sistema de color Material 3 con paleta verde personalizada. Iconos y botones heredan el color primario desde `styles.scss` sin necesidad de definirlo en cada componente.
- **Diseño Responsivo:** Tarjetas con scroll interno limitadas a `85vh`, adaptables a móvil, tablet y escritorio.

---

## Tecnologías y Dependencias Principales

| Paquete | Versión | Rol |
|---|---|---|
| `@angular/core` | `^21.2.0` | Framework principal |
| `@angular/material` | `^21.2.7` | Componentes UI (Tabla, Formulario, Datepicker, etc.) |
| `@angular/cdk` | `^21.2.7` | Primitivos de Angular Material |
| `@angular/forms` | `^21.2.0` | Formularios reactivos tipados |
| `@angular/router` | `^21.2.0` | Ruteo con parámetros de consulta (`queryParams`) |
| `primeflex` | `^4.0.0` | Utilidades CSS de layout y espaciado |
| `rxjs` | `~7.8.0` | Programación reactiva y observables |
| `typescript` | `~5.9.2` | Lenguaje tipado |

---

## Requisitos Previos

- [Node.js](https://nodejs.org/) v20.x o superior
- [Angular CLI](https://angular.dev/tools/cli) v21.x

```bash
npm install -g @angular/cli
```

---

## Instalación

1. Clona el repositorio:
   ```bash
   git clone <URL_DEL_REPOSITORIO>
   cd prueba525
   ```

2. Instala las dependencias:
   ```bash
   npm install
   ```

---

## Scripts Disponibles

| Comando | Descripción |
|---|---|
| `npm start` | Inicia el servidor de desarrollo en `http://localhost:4200/` |
| `npm run build` | Compila el proyecto para producción (salida en `dist/`) |
| `npm run watch` | Compilación continua en modo desarrollo |

---

## Rutas de la Aplicación

| Ruta | Descripción |
|---|---|
| `/` | Formulario de contacto / creación de usuario |
| `/?edit=<id>` | Formulario en modo edición con datos precargados |
| `/table-data` | Tabla de usuarios con CRUD completo |

---

## Arquitectura del Proyecto

```
src/
├── app/
│   ├── core/
│   │   ├── interfaces/         # Tipos e interfaces TypeScript (UserData)
│   │   ├── pages/              # Páginas contenedor (FormPage, TableDataPage)
│   │   └── services/           # Servicios (UserHttpService, UserStorageService)
│   ├── features/
│   │   ├── contact-form/       # Componente de formulario reactivo
│   │   │   └── components/
│   │   │       ├── contact-form/   # Formulario principal con validaciones
│   │   │       └── social-links/   # Tarjeta lateral de información de contacto
│   │   └── table-data/         # Componente de tabla interactiva
│   │       └── components/
│   │           └── table-data/     # Tabla, búsqueda, ordenamiento y paginación
│   ├── app.routes.ts           # Definición de rutas lazy-loaded
│   └── app.scss                # Estilos globales del fondo animado
├── assets/                     # Recursos estáticos (imágenes, íconos)
└── styles.scss                 # Tema global de Angular Material (paleta verde M3)
```

---

## Generación de Código

```bash
# Crear un nuevo componente
ng generate component ruta/nombre-componente

# Crear un nuevo servicio
ng generate service ruta/nombre-servicio

# Ver todas las opciones disponibles
ng generate --help
```

---

## Diseño y Estrategias de Arquitectura

Este proyecto no es simplemente una pantalla con un formulario. Está construido siguiendo principios de ingeniería de software que lo hacen **mantenible, escalable y profesional**. A continuación se describen las decisiones de diseño más relevantes.

---

### Principio de Responsabilidad Única (SRP)

El pilar del proyecto es el **Principio de Responsabilidad Única** (*Single Responsibility Principle*), uno de los principios SOLID. Su regla es simple: **cada clase, servicio o componente debe tener una única razón para cambiar**.

En la práctica, esto se traduce en la siguiente separación de capas:

```
¿Quién sabe cómo hablar con el API?     → UserHttpService
¿Quién administra el estado local?      → UserStorageService
¿Quién orquesta la pantalla?            → FormPage / TableDataPage
¿Quién dibuja el formulario?            → ContactForm
¿Quién dibuja la tabla?                 → TableData
¿Quién muestra la info de contacto?     → SocialLinks
```

Cada pieza hace **una sola cosa bien**. Si mañana el API cambia su URL o estructura, solo se modifica `UserHttpService`. Si la lógica de almacenamiento local cambia, solo se toca `UserStorageService`. Ningún componente visual necesita saber de dónde vienen los datos.

---

### Capa de Servicios Desacoplada

La aplicación separa explícitamente **dos responsabilidades de datos** en dos servicios distintos:

| Servicio | Responsabilidad |
|---|---|
| `UserHttpService` | Consultar el API REST externo (`cincoveinticinco.com`) y mapear la respuesta al tipo `UserData` interno. |
| `UserStorageService` | Gestionar el ciclo de vida completo del CRUD en `localStorage`: leer, escribir, actualizar y eliminar usuarios de forma reactiva con `Observable`. |

Los componentes visuales **nunca hablan directamente con el API**. Siempre consumen `UserStorageService`, que actúa como la fuente de verdad local. Esto permite sustituir `localStorage` por una base de datos real en el futuro sin tocar ni una línea de código de los componentes.

---

### Formularios Reactivos Estrictamente Tipados

El formulario evita `FormBuilder` con tipado implícito, que en Angular infiere todo como `string`. En su lugar, cada campo se declara con su tipo explícito.


### Sistema de Temas Centralizado (Material Design 3)

Toda la identidad visual está definida en **un único punto de entrada** (`src/styles.scss`). Cambiar la paleta de colores de toda la app implica modificar una sola línea:

```scss
// Una línea controla el color de TODOS los botones, iconos, campos y cabeceras
primary: mat.$green-palette,
```

Los componentes individuales **no conocen ni hardcodean colores**. Consumen las variables de sistema que Angular Material calcula automáticamente (`--mat-sys-primary`, `--mat-sys-on-surface`, etc.), asegurando contraste, accesibilidad y coherencia visual en toda la interfaz.

---


