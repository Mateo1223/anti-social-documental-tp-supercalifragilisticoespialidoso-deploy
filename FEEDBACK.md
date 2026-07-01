# Feedback del Trabajo Práctico (TP2 — MongoDB)

## Integrantes

A partir de los commits del repositorio:

- **Mateo Agustín Sánchez**
- **Thomas Barenghi**
- **GERACE MANRIQUEZ, CESAR BOSCO**
> Trabajo repartido entre los integrantes. 👏

---

## Resumen General

¡Excelente trabajo! 🎉 Es una de las entregas más completas y profesionales. Arquitectura en capas con **repositorios + servicios** y controladores finos (la única responsabilidad que pide el enunciado), modelado documental coherente, validación de `ObjectId`, la **regla de los comentarios antiguos aplicada y configurable**, y varios extras muy por encima de lo pedido: upload de imágenes a **Cloudinary** (con `sharp`), autenticación, tests con Vitest, ESLint y un `MODELADO.md` que explica las decisiones de diseño.

Hay un único punto de fondo a corregir: hoy el post **exige** al menos una imagen, cuando el enunciado las define como opcionales.

### Estado por criterio

| Criterio        | Estado | Comentario breve |
|-----------------|:------:|------------------|
| Arquitectura    |   ✅   | Capas + repositorios + servicios; controladores de única responsabilidad. |
| Modelado        |   ✅   | Híbrido coherente (imágenes embebidas, resto referenciado); `nickName`/`email` únicos. |
| Validaciones    |   ✅   | Joi + validación de `ObjectId` que carga el recurso. |
| Middlewares     |   ✅   | `validatePostId`/`validateFollow`/etc. genéricos y por entidad. |
| API REST        |   ⚠️   | CRUD + relaciones; la creación exige imagen (Obs. 1). |
| Configuración   |   ✅   | `MONGODB_URI` (Atlas), `PORT`, `COMMENT_MONTHS` por `.env`. |
| Documentación   |   ✅   | Swagger, Postman, `MODELADO.md`, tests. |

---

## Fortalezas

### 1. Arquitectura en capas con repositorios y servicios 🏗️
**Ubicación:** `src/controllers/`, `src/services/`, `src/repositories/`

El flujo `controller → service → repository → model` está muy bien aplicado: los controladores orquestan, los servicios tienen la lógica de negocio, y los repositorios concentran el acceso a Mongoose. Es exactamente la separación que valora el enunciado. 👌

### 2. Regla de comentarios antiguos aplicada y configurable ⏳
**Ubicación:** `src/controllers/posts.controller.js` (`getCommentCutoff`), `src/repositories/posts.repository.js` (`findByIdWithRelations`)

Al traer el detalle de un post, los comentarios se filtran por antigüedad en la consulta:

```js
const comments = await Comment.find({ postId: id, createdAt: { $gte: commentCutoff } })...
```

con `cutoff` calculado desde `COMMENT_MONTHS`. Se aplica donde se muestran los comentarios y el umbral es configurable. 🎯

### 3. Validación de `ObjectId` que carga el recurso ♻️
**Ubicación:** `src/middlewares/validatePostId.middleware.js`

`validatePostId` valida el formato del id, busca el post y lo deja en `req.post` para que el controlador lo reutilice (sin volver a consultar). Mismo patrón en `validateFollow`, `validateUserId`, etc.

### 4. Bonus y prácticas por encima de lo pedido 🌟
**Ubicación:** `src/services/cloudinary.service.js`, `src/services/sharp.service.js`, `src/services/auth.service.js`, `tests/`

- **Upload de imágenes a Cloudinary** (con procesamiento `sharp`) — más que el bonus de “guardar en una carpeta”.
- **Seguidores** (`validateFollow` + follow/unfollow).
- Además: autenticación con `bcrypt`, **tests** con Vitest, ESLint y `MODELADO.md`. Todo esto excede el alcance de la materia y suma como plus.

### 5. Modelado documental coherente 🗃️
**Ubicación:** `src/models/`, `MODELADO.md`

Imágenes embebidas en el post, comentarios y tags referenciados, `nickName`/`email` únicos. Y documentaron el porqué de cada decisión en `MODELADO.md`. 👏

---

## Observaciones

### 1. La creación de post exige al menos una imagen (deberían ser opcionales)

**Estado:** ⚠️  **Severidad:** 🟠 Importante
**Ubicación:** `src/models/post.js`, `src/services/posts.service.js` (`create`)

**Descripción:**
Tanto el schema como el servicio obligan a que el post tenga imágenes:

```js
// modelo
images: { type: [postImageSchema], validate: v => v.length > 0, required: true }
// servicio
if (!file && !data.images) throw new Error('La publicación debe tener al menos una imagen')
```

El enunciado, en cambio, dice que la descripción es obligatoria pero las imágenes son **opcionales** (“cero o más imágenes”).

**Impacto:**
No se puede crear un post **solo con descripción**, que es el caso principal del MVP.

**Recomendación:**
Quitar la obligatoriedad de imágenes: hacer `images` opcional en el schema (sin el `validate` de longitud ni `required`) y, en el servicio, permitir crear el post cuando no se envía imagen.

---

### A futuro (fuera del alcance de la materia)

Solo informativo: incorporaron autenticación (registro/login con `bcrypt`). No se evalúa en esta entrega, pero es un buen plus; si lo siguen, recordá no exponer el `password` en las respuestas.

---

## Conclusión

Es una entrega sobresaliente: arquitectura con repositorios y servicios, controladores de única responsabilidad, la regla de negocio resuelta y configurable, upload a Cloudinary, seguidores, tests y una documentación excelente (incluido `MODELADO.md`). 🌟

El único punto de fondo es **hacer opcionales las imágenes** del post; es un cambio chico y bien localizado. ¡Felicitaciones por el nivel del trabajo! 🚀
