# Capítulo IV: Product Implementation & Validation

## 4. Product Implementation & Validation

---

## 4.1. Software Configuration Management

### 4.1.1. Software Development Environment Configuration


### 4.1.2. Source Code Management


### 4.1.3. Source Code Style Guide & Conventions

Esta sección define las convenciones que sigue el equipo en el código del backend (Java 21 / Spring Boot) y de la aplicación móvil (Kotlin / Jetpack Compose). Se verifican automáticamente con Checkstyle en el CI del backend y con las pruebas unitarias de ambos repositorios.

#### Convenciones generales

| Aspecto | Convención | Dónde se aplica |
|---|---|---|
| Idioma del código | Identificadores (clases, métodos, variables, rutas) en inglés; comentarios, mensajes al usuario y documentación en español | Backend y app |
| Arquitectura | Domain-Driven Design por bounded context: `domain`, `application`, `infrastructure`, `interfaces` (REST y ACL) | Backend |
| Organización móvil | Un paquete por contexto con capas `data` (servicios Retrofit, repositorios, Room), `domain` (modelos) y `presentation` (pantalla + ViewModel) | App |
| Textos de pantalla | Nunca texto fijo en la pantalla: se usa `stringResource` con los archivos `res/values/strings.xml` (español) y `res/values-en/strings.xml` (inglés), para que al cambiar el idioma en Ajustes las secciones se traduzcan solas | App |
| Control de versiones | `main` (releases) y `develop` (integración); una rama por historia: `feature/USxx-descripcion` o `feature/TSxx-descripcion`; correcciones en `fix/...`; todo entra por Pull Request a `develop` | Ambos |
| Mensajes de commit | Conventional Commits en inglés: `feat(modulo): ...`, `fix(...)`, `test(...)`, `docs(...)`, `style(...)`, `chore(...)` | Ambos |
| Pruebas | Toda historia incluye pruebas: unitarias (dominio y servicios), de controlador (`@WebMvcTest`), de integración y de persistencia; en la app, pruebas de repositorio y de ViewModel | Ambos |

#### Backend (Java)

| Elemento | Regla | Ejemplo |
|---|---|---|
| Paquetes | minúsculas, `com.jamsell.gethics.<contexto>.<capa>` | `com.jamsell.gethics.livestock.domain.model.aggregates` |
| Clases | `UpperCamelCase` con sufijo según su rol: `...Controller`, `...CommandServiceImpl`, `...QueryServiceImpl`, `...Resource` (DTO REST), `...Assembler` (transformación), `...Command`, `...Query`, `...Exception` | `AnimalController`, `DeactivateAnimalCommand` |
| Métodos y variables | `lowerCamelCase` | `findByQrCode` |
| Constantes | `UPPER_SNAKE_CASE` | `DESCRIPTION_MAX_LENGTH` |
| DTO, comandos y consultas | `record` inmutables; las reglas de negocio viven en el agregado, no en el controlador | `SellAnimalCommand` |
| Separación CQRS | Un servicio de comandos (escribe) y uno de consultas (solo lee, `@Transactional(readOnly = true)`) por agregado | `AnimalCommandService` / `AnimalQueryService` |
| Comunicación entre contextos | Solo por fachadas ACL (`interfaces/acl`) y eventos de dominio; nunca por repositorios de otro contexto | `LivestockContextFacade`, `AnimalLeftActiveHerdEvent` |
| API REST | Rutas `/api/v1/<recurso>`, verbos HTTP semánticos, códigos 201/204/400/404/409 documentados con `@Operation` y `@ApiResponses` (Swagger en `/docs`) | `DELETE /api/v1/animals/{id}` |
| Errores | Excepciones de dominio traducidas a HTTP en un `@RestControllerAdvice` por contexto; cuerpo `{"message": "..."}` | `LivestockExceptionHandler` |
| Base de datos | Versionada con Flyway (`db/migration`); las migraciones nunca borran datos | `AlignVeterinaryAssignmentsMigration` |

Reglas de Checkstyle (`checkstyle.xml`, falla el build si se incumplen): `UnusedImports`, `RedundantImport`, `EmptyStatement`, `EqualsHashCode`, `MissingSwitchDefault`, `TypeName`, `MethodName`, `MemberName`, `ConstantName`, `LocalVariableName`, `PackageName` y `NewlineAtEndOfFile`.

#### Aplicación móvil (Kotlin)

| Elemento | Regla | Ejemplo |
|---|---|---|
| Pantallas | Una función `@Composable` por pantalla (`...Screen`) con su `...ViewModel`; el estado se expone como `State` de solo lectura | `AnimalDetailScreen` / `AnimalDetailViewModel` |
| Acceso a datos | `...Service` (Retrofit) → `...Repository` (devuelve `Resource<T>`) → Room como caché sin conexión | `LivestockRepository` |
| Idioma de la interfaz | `stringResource` / `pluralStringResource`; cada texto nuevo se agrega en español e inglés | `animal_deactivate_title` |
| Nombres | Clases `UpperCamelCase`, funciones y propiedades `lowerCamelCase`, constantes `UPPER_SNAKE_CASE`, recursos `snake_case` con prefijo del módulo | `livestock_search_hint` |
| Pruebas | Servicios falsos (`FakeService`, `FakeDao`) y pruebas de ViewModel con corrutinas | `AnimalDetailDeactivationTest` |

### 4.1.4. Software Deployment Configuration


---

## 4.2. Landing Page & Mobile Application Implementation

### 4.2.1. Sprint n

#### 4.2.1.1. Sprint Planning n


#### 4.2.1.2. Aspect Leaders and Collaborators


#### 4.2.1.3. Sprint Backlog n

El Sprint Backlog toma las historias de usuario (US) y las historias técnicas (TS) del tablero de Trello del equipo. Los títulos están escritos en verbo, como pide la guía de la asignatura; los criterios de aceptación completos de cada historia están en la sección 2.4 del Capítulo II.

##### Sprint 1 – Base funcional (autenticación, animales, granjas, sanidad, finanzas, veterinario, reportes)

| ID | Historia (en verbo) | Épica | Story Points | Responsable |
|---|---|---|---|---|
| TS01 | Configurar repositorio, arquitectura y entornos | EPIC-11 | 3 | Equipo |
| US-01 | Registrar un usuario | EPIC-02 | 3 | Equipo |
| US-02 | Iniciar sesión | EPIC-02 | 2 | Equipo |
| US-03 | Recuperar la contraseña | EPIC-02 | 3 | Equipo |
| US-04 | Editar el perfil | EPIC-02 | 2 | Equipo |
| TS06 | Autorizar el acceso por rol (ganadero / veterinario) | EPIC-02 | 3 | Equipo |
| TS05 | Manejar errores, logs y validaciones de forma global | EPIC-11 | 3 | Equipo |
| US-24 | Contratar planes de suscripción in-app | EPIC-10 | 6 | Equipo |
| TS02 | Definir el modelo de datos y las migraciones de base de datos | EPIC-11 | 5 | Equipo |
| US-05 | Registrar un animal | EPIC-03 | 5 | Equipo |
| US-06 | Listar y buscar animales | EPIC-03 | 5 | Equipo |
| US-08 | Dar de baja un animal | EPIC-03 | 2 | Equipo |
| US-09 | Registrar una granja | EPIC-04 | 3 | Equipo |
| US-10 | Asociar animales a una granja | EPIC-04 | 3 | Equipo |
| TS04 | Documentar la API (OpenAPI/Swagger) | EPIC-11 | 2 | Equipo |
| US-11 | Registrar un evento sanitario | EPIC-05 | 5 | Equipo |
| US-12 | Consultar el calendario sanitario | EPIC-05 | 5 | Equipo |
| US-13 | Recibir recordatorios de vacunación (push) | EPIC-05 | 5 | Equipo |
| US-14 | Consultar el historial clínico por animal | EPIC-05 | 5 | Equipo |
| US-21 | Recibir alertas automáticas por tendencias | EPIC-08 | 5 | Equipo |
| US-15 | Registrar ingresos y egresos | EPIC-06 | 5 | Equipo |
| US-17 | Consultar los clientes asignados (veterinario) | EPIC-07 | 5 | Equipo |
| US-18 | Consultar los pacientes de un cliente (veterinario) | EPIC-07 | 5 | Equipo |
| US-19 | Registrar la atención veterinaria | EPIC-07 | 5 | Equipo |
| TS03 | Configurar CI/CD y desplegar en la nube | EPIC-11 | 5 | Equipo |
| US-22 | Recibir notificaciones push generales | EPIC-09 | 3 | Equipo |
| US-20 | Consultar reportes y estadísticas del ganado | EPIC-08 | 8 | Equipo |
| US-23 | Integrar dispositivos IoT | EPIC-09 | 8 | Equipo |
| US-16 | Consultar el balance económico del ganado | EPIC-06 | 3 | Equipo |
| US-07 | Editar un animal | EPIC-03 | 3 | Equipo |

**Total Sprint 1:** 30 historias, 125 story points.

##### Sprint 2 – Funcionalidades complementarias, integración y calidad

| ID | Historia (en verbo) | Épica | Story Points | Responsable |
|---|---|---|---|---|
| TS09 | Versionar la base de datos con Flyway | EPIC-11 | 3 | Luis |
| TS08 | Proteger los endpoints con JWT | EPIC-11 | 3 | Mauricio |
| TS07 | Integrar veterinario con animales y sanidad | EPIC-11 | 5 | Luis, Mateo y Juanjo |
| US-37 | Consultar las lecturas de un dispositivo | EPIC-09 | 3 | Abby |
| US-39 | Exportar el reporte del ganado | EPIC-08 | 3 | Abby |
| US-38 | Cerrar sesión | EPIC-02 | 1 | Mauricio |
| US-36 | Consultar los animales sin conexión | EPIC-12 | 3 | Mateo |
| US-35 | Registrar eventos sanitarios sin conexión | EPIC-12 | 8 | Juanjo |
| US-34 | Desvincular a un cliente asignado | EPIC-07 | 2 | Luis |
| US-33 | Cancelar un evento sanitario programado | EPIC-05 | 3 | Juanjo |
| US-32 | Editar una granja | EPIC-04 | 2 | Mateo |
| US-31 | Anular un movimiento financiero | EPIC-06 | 3 | Luis |
| US-30 | Registrar la venta de un animal | EPIC-03 | 3 | Mateo |
| US-29 | Identificar un animal con código QR | EPIC-03 | 5 | Mateo |

**Total Sprint 2:** 14 historias, 47 story points.

##### Tareas de Mateo Salazar (Animales, Swagger y backlog)

| Historia | Alcance entregado en el backend | Alcance entregado en la app |
|---|---|---|
| US-08 Dar de baja un animal | `DELETE /api/v1/animals/{id}`: baja lógica, conserva el historial, cancela los eventos sanitarios programados y bloquea editar o mover un animal que ya no está activo | Botón "Dar de baja" en la ficha con diálogo de confirmación y opción de cancelar; el animal sale de la lista activa |
| TS04 Documentar la API (OpenAPI/Swagger) | Todos los controladores anotados con `@Operation` y `@ApiResponses`; documentación en `/docs` y prueba automática que falla si un endpoint queda sin documentar | — |
| TS07 Integrar veterinario con animales y sanidad | Fachadas `LivestockContextFacade` y `SanitaryContextFacade`; pacientes reales con resumen clínico y atención reflejada en el historial del ganadero | — |
| US-29 Identificar un animal con código QR | `GET /api/v1/animals/qr/{code}` | Pendiente de pantalla (escáner) |
| US-30 Registrar la venta de un animal | `POST /api/v1/animals/{id}/sale` con ingreso en Finanzas | Pendiente de pantalla |
| US-32 Editar una granja | `PUT /api/v1/farms/{id}` con control de nombres duplicados | Pendiente de pantalla |

#### 4.2.1.4. Development Evidence for Sprint Review


#### 4.2.1.5. Testing Suite Evidence for Sprint Review


#### 4.2.1.6. Execution Evidence for Sprint Review


#### 4.2.1.7. Services Documentation Evidence for Sprint Review

La API REST de Gethics está documentada con OpenAPI 3 (springdoc) y se publica con Swagger UI en `/docs` (JSON en `/v3/api-docs`). Una prueba automática (`OpenApiDocumentationTest`) falla si algún endpoint queda sin resumen, etiqueta o respuestas documentadas. A continuación se listan los **46 endpoints** vigentes, agrupados por módulo, con los códigos HTTP que puede devolver cada uno.

Nota: los endpoints `/api/v1/**` aún no exigen token JWT (historia TS08, a cargo de Mauricio); hasta entonces el dueño o el veterinario se indican como parámetro o en el cuerpo de la petición.

##### Auth

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| POST | `/auth/reset-password` | **Restablecer la contrasena.** US-03: define una nueva contrasena con el token recibido por correo. | 200, 400 |
| POST | `/auth/register` | **Registrar un usuario.** US-01: crea una cuenta con rol GANADERO o VETERINARIO. La contrasena se guarda cifrada. | 201, 400, 409 |
| POST | `/auth/login` | **Iniciar sesion.** US-02: valida las credenciales y devuelve un token JWT para usar como Bearer en el resto de endpoints. | 200, 400, 401 |
| POST | `/auth/forgot-password` | **Solicitar recuperacion de contrasena.** US-03: envia al correo un enlace de restablecimiento con vigencia limitada. | 200, 400, 404 |

##### Users

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| GET | `/users/me` | **Consultar mi perfil.** US-04: datos del usuario autenticado. | 200, 401, 404 |
| PUT | `/users/me` | **Editar mi perfil.** US-04: actualiza nombre y telefono del usuario autenticado. | 200, 400, 401 |
| GET | `/users/me/photo` | **Obtener mi foto de perfil.** Devuelve los bytes de la imagen con su content-type. | 200, 401, 404 |
| PUT | `/users/me/photo` | **Actualizar mi foto de perfil.** US-04: sube la imagen (multipart, parte file; maximo 2 MB). | 200, 400, 401 |

##### Livestock

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| PUT | `/api/v1/farms/{id}` | **Editar una granja.** US-32: actualiza nombre, ubicacion y tamano (tamano ausente = sin registrar). El nombre no puede repetirse entre las granjas del mismo dueno. | 200, 400, 404, 409 |
| GET | `/api/v1/animals/{id}` | **Obtener un animal.** Devuelve el animal sin importar su estado, para precargar el detalle o la edicion. | 200, 404 |
| PUT | `/api/v1/animals/{id}` | **Editar un animal.** Actualiza nombre, raza, sexo, fecha de nacimiento, peso, foto y, opcionalmente, la granja. No cambia el arete ni el QR. Solo se puede editar un animal ACTIVE. | 200, 400, 404, 409 |
| DELETE | `/api/v1/animals/{id}` | **Dar de baja un animal (baja logica).** Cambia el estado a INACTIVE sin borrar el registro ni su historial sanitario, y cancela sus eventos sanitarios programados. Responde 409 si el animal ya no esta ACTIVE. | 204, 404, 409 |
| PUT | `/api/v1/animals/{animalId}/farm` | **Asignar o mover un animal de granja.** Idempotente: repetir la misma granja no cambia nada. Cada cambio queda en el historial. | 200, 404 |
| GET | `/api/v1/farms` | **Listar granjas del dueno.** Devuelve las granjas del dueno ordenadas por nombre. | 200, 400 |
| POST | `/api/v1/farms` | **Registrar una granja.** Crea una granja para el dueno indicado. | 201, 400, 409 |
| GET | `/api/v1/animals` | **Listar y buscar animales.** Busca por arete, nombre o raza. Por defecto solo devuelve animales ACTIVE; usa status para ver otros estados. | 200, 400 |
| POST | `/api/v1/animals` | **Registrar un animal.** Crea un animal ACTIVE con arete unico y genera su codigo QR. | 201, 400, 404, 409 |
| POST | `/api/v1/animals/{id}/sale` | **Registrar la venta de un animal.** US-30: marca el animal como SOLD con su precio y fecha, conserva su historial, cancela sus eventos sanitarios programados y registra un ingreso (categoria 'Venta de ganado') en Finance. El dueno sale de la granja del animal; si no tiene granja, hay que enviar ownerId. | 201, 400, 404, 409 |
| GET | `/api/v1/farms/{farmId}/animals` | **Listar animales de una granja.** Por defecto solo animales ACTIVE; usa status para ver otros estados. | 200, 400, 404 |
| GET | `/api/v1/animals/{animalId}/farm-history` | **Historial de granjas del animal.** Cambios de granja del mas antiguo al mas reciente. | 200, 404 |
| GET | `/api/v1/animals/qr/{code}` | **Buscar un animal por su codigo QR.** US-29: devuelve el animal cuyo QR tiene ese codigo (sin distinguir mayusculas ni espacios), sin importar su estado. | 200, 404 |

##### Sanitary

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| POST | `/api/v1/animals/{animalId}/sanitary-events` | **Registrar un evento sanitario.** US-11: registra un evento ya ocurrido (vacuna, tratamiento, enfermedad, chequeo u otro). Con clientRequestId es idempotente (US-35): el primer envio responde 201 y un reintento identico 200. | 200, 201, 400, 409 |
| POST | `/api/v1/animals/{animalId}/sanitary-events/{eventId}/complete` | **Completar un evento programado.** Registra como aplicado el mismo evento programado (SCHEDULED a COMPLETED) sin crear uno nuevo. | 200, 400, 404, 409 |
| POST | `/api/v1/animals/{animalId}/sanitary-events/{eventId}/cancel` | **Cancelar un evento programado.** US-33: cambia el evento de SCHEDULED a CANCELLED sin borrarlo; queda en el historial clinico y sale del calendario y de los recordatorios. | 200, 404, 409 |
| POST | `/api/v1/animals/{animalId}/sanitary-events/scheduled` | **Programar un evento sanitario.** US-12/US-13: programa un evento futuro; genera recordatorios de vacunacion. Con clientRequestId es idempotente (US-35). | 200, 201, 400, 409 |
| GET | `/api/v1/sanitary-calendar` | **Calendario sanitario del mes.** US-12: eventos sanitarios programados del mes indicado. | 200, 400 |
| GET | `/api/v1/animals/{animalId}/clinical-history` | **Consultar el historial clinico de un animal.** US-14: todos los eventos sanitarios del animal en orden cronologico (incluye programados y cancelados). Se conserva aunque el animal haya sido dado de baja (US-08). Sin eventos devuelve 200 con un mensaje de sin registros. | 200, 400 |

##### Veterinary

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| POST | `/api/v1/vet/patients/{patientId}/care` | **Registrar una atencion veterinaria.** US-19 / TS07: valida que el paciente exista, este activo y sea de un cliente asignado al veterinario; guarda la atencion, la agrega al historial clinico del animal (visible para el ganadero) y, si trae proximo control futuro, lo programa como CHECKUP en el calendario sanitario. Es idempotente por clientRequestId: 201 la primera vez y 200 en un reintento identico. | 200, 201, 400, 403, 404, 409, 503 |
| POST | `/api/v1/vet/care/sync` | **Sincronizar atenciones veterinarias en lote.** US-19 / US-35: procesa hasta 100 atenciones registradas sin conexion. Cada item devuelve su resultado (CREATED, DUPLICATE, CONFLICT, REJECTED por regla de negocio, o FAILED si el historial no se pudo actualizar y hay que reintentar); un item fallido no detiene al resto. | 200, 400 |
| POST | `/api/v1/vet/assignments` | **Asignar un cliente a un veterinario.** US-17: vincula un ganadero (cliente) con un veterinario. | 201, 400, 409 |
| GET | `/api/v1/vet/clients` | **Listar los clientes asignados a un veterinario.** US-17: clientes con asignacion activa. Sin clientes devuelve 200 con un mensaje. | 200, 400 |
| GET | `/api/v1/vet/clients/{clientId}/animals` | **Consultar los pacientes de un cliente.** US-18 / TS07: animales activos del cliente (datos reales del modulo de ganaderia) con el resumen de su historial clinico. Los animales dados de baja no aparecen. El cliente debe estar asignado al veterinario. | 200, 403 |

##### Veterinary Assignments

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| DELETE | `/api/v1/vet/assignments/{id}` | **Desvincular un cliente del veterinario.** US-34: desvincula al cliente (la asignacion pasa a INACTIVE); el cliente deja de aparecer en la lista de clientes activos del veterinario. No borra animales ni su historial clinico. | 204, 404 |

##### Finance

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| GET | `/api/v1/finances` | **Balance economico del ganadero.** Devuelve ingresos, egresos, saldo y movimientos activos. Sin from/to usa todo el historial; con from y to filtra el periodo. Los movimientos anulados no cuentan. Si no hay movimientos devuelve 200 con un mensaje. | 200, 400 |
| POST | `/api/v1/finances` | **Registrar un ingreso o egreso.** Crea un movimiento financiero del ganadero (INCOME o EXPENSE) con monto, fecha y categoria. | 201, 400 |
| POST | `/api/v1/finances/{id}/void` | **Anular un movimiento financiero.** US-31: marca el movimiento como anulado sin borrarlo; deja de sumar en el balance. | 204, 400 |

##### IoT

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| POST | `/api/v1/devices/{code}/readings` | **Registrar una lectura del sensor.** Ingesta HTTP de una lectura (metrica y valor) para un dispositivo ya vinculado, identificado por su codigo. | 201, 400, 404 |
| POST | `/api/v1/devices/link` | **Vincular un dispositivo IoT.** US-23: vincula un dispositivo compatible a un ganadero mediante su codigo unico. | 201, 400, 409 |
| GET | `/api/v1/devices` | **Listar los dispositivos de un ganadero.** Devuelve los dispositivos del ganadero con su estado actual (CONNECTED o DISCONNECTED). | 200, 400 |
| GET | `/api/v1/devices/{id}/readings` | **Consultar las lecturas de un dispositivo.** US-37: lecturas paginadas, de la mas reciente a la mas antigua. Sin lecturas devuelve 200 con content vacio. | 200, 400, 404 |

##### Analytics

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| GET | `/api/v1/reports` | **Reporte y estadisticas del ganado.** US-20: indicadores de salud, productividad y finanzas del ganadero en el rango from-to (formato yyyy-MM-dd). | 200, 400, 404 |
| GET | `/api/v1/reports/export` | **Exportar el reporte del ganado en CSV.** US-39: descarga el mismo reporte del periodo como archivo CSV (text/csv, UTF-8) con Content-Disposition attachment. | 200, 400, 404 |
| GET | `/api/v1/alerts` | **Listar las alertas de tendencia del ganadero.** US-21: alertas automaticas generadas por tendencias, de la mas reciente a la mas antigua. Un ownerId sin alertas devuelve la lista vacia. | 200, 400 |

##### Notifications

| Método | Ruta | Descripción | Códigos HTTP |
|---|---|---|---|
| GET | `/api/v1/users/{userId}/notification-preference` | **Consultar la preferencia de notificaciones push.** Indica si el usuario tiene habilitadas las notificaciones push. | 200, 400 |
| PUT | `/api/v1/users/{userId}/notification-preference` | **Actualizar la preferencia de notificaciones push.** Activa o desactiva las notificaciones push del usuario. | 200, 400 |
| POST | `/api/v1/notifications` | **Enviar una notificacion push.** US-22: envia una notificacion al usuario si tiene las push habilitadas; si no, responde 201 con el resultado SKIPPED_DISABLED; si el proveedor falla, DELIVERY_FAILED. | 201, 400 |


#### 4.2.1.8. Software Deployment Evidence for Sprint Review


#### 4.2.1.9. Team Collaboration Insights during Sprint


---

## 4.3. Validation Interviews

### 4.3.1. Diseño de Entrevistas


### 4.3.2. Registro de Entrevistas

> **Pendiente de completar por Mateo Salazar:** esta sección debe registrar las entrevistas de validación realizadas por él. No se inventan datos: cada campo se llena con lo ocurrido en la entrevista.

| Campo | Detalle |
|---|---|
| Entrevistado (nombre y perfil: ganadero / veterinario) | _completar_ |
| Fecha y duración | _completar_ |
| Medio (presencial / videollamada) | _completar_ |
| Entrevistador | Mateo Salazar |
| Enlace a la grabación o evidencia | _completar_ |

**Resumen de la entrevista** (5 a 8 líneas): _completar_

**Hallazgos principales**

| # | Hallazgo | Pantalla o historia relacionada |
|---|---|---|
| 1 | _completar_ | _completar_ |
| 2 | _completar_ | _completar_ |
| 3 | _completar_ | _completar_ |

### 4.3.3. Evaluaciones según heurísticas

> **Pendiente de completar por Mateo Salazar** con lo observado en su entrevista. Se evalúa cada heurística de Nielsen sobre las pantallas de Animales (lista, registro, detalle, edición y baja).

| # | Heurística de Nielsen | Evaluación (1 = incumple, 5 = cumple) | Observación y mejora propuesta |
|---|---|---|---|
| 1 | Visibilidad del estado del sistema | _completar_ | _completar_ |
| 2 | Coincidencia entre el sistema y el mundo real | _completar_ | _completar_ |
| 3 | Control y libertad del usuario (por ejemplo, cancelar la baja) | _completar_ | _completar_ |
| 4 | Consistencia y estándares | _completar_ | _completar_ |
| 5 | Prevención de errores (confirmación antes de dar de baja) | _completar_ | _completar_ |
| 6 | Reconocer en lugar de recordar | _completar_ | _completar_ |
| 7 | Flexibilidad y eficiencia de uso | _completar_ | _completar_ |
| 8 | Diseño estético y minimalista | _completar_ | _completar_ |
| 9 | Ayudar a reconocer, diagnosticar y recuperarse de errores | _completar_ | _completar_ |
| 10 | Ayuda y documentación | _completar_ | _completar_ |

**Conclusión:** _completar_

---

# Conclusiones

## Conclusiones y recomendaciones


---

# Video App Validation


# Video About the Product


# Video About the Team

> **Pendiente:** insertar el enlace del video "About the Team" cuando esté publicado (por ejemplo, YouTube no listado) y completar los datos del equipo.

| Dato | Contenido |
|---|---|
| Enlace del video | _completar_ |
| Duración | _completar_ |
| Equipo | JamSell: Abigail Raymundo, Mauricio Castillo, Mateo Salazar, Luis Pillaca y Juanjo Meza |
| Contenido sugerido | Presentación de cada integrante, rol en el proyecto, aporte principal (Mateo: Animales, Swagger y backlog) y aprendizaje del curso |

---

# Glosario


# Bibliografía


# Anexos
