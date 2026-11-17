# Respuesta y trazabilidad

## Alcance y criterios

Las respuestas se basan en las 24 amenazas de la [tabla STRIDE](tabla%20de%20amenazas%20stride.md). Las cinco amenazas trazadas son las que la [matriz de priorización](priorizacion%20matriz%20impacto%20probabilidad.md) identifica como prioritarias: T01, T03, T13, T21 y T23.

Para las referencias se toma OWASP ASVS 4.0.3 como base. Se indican las áreas y objetivos de verificación pertinentes, expresados aquí de forma resumida; deben contrastarse con el texto oficial de esa versión al convertirlos en criterios formales de aceptación.

## Respuesta y mitigación por amenaza

| ID | Respuesta | Mitigación o decisión | Estado propuesto |
| --- | --- | --- | --- |
| T01 | Mitigar | Verificar criptográficamente la firma del proveedor; validar timestamp y evento; aplicar idempotencia y confirmar el pago con la pasarela antes de cambiar la inscripción a pagada. | Pendiente de diseño e implementación |
| T02 | Mitigar | Validar `state` y `redirect_uri` contra valores registrados; aceptar solo el proveedor autorizado y validar el código con el proveedor antes de crear la sesión. | Pendiente de diseño e implementación |
| T03 | Mitigar | Generar tokens de entrada impredecibles y no basados en IDs secuenciales; asociarlos a la inscripción y comprobar en servidor su firma, estado y uso. | Pendiente de diseño e implementación |
| T04 | Mitigar | Evitar exponer tokens en equipos compartidos; reducir su vigencia, permitir revocación y rotación, y proteger la sesión con almacenamiento seguro. | Pendiente de diseño e implementación |
| T05 | Mitigar | Verificar firma y timestamp, rechazar eventos repetidos mediante idempotencia y contrastar monto e inscripción con la pasarela. | Pendiente de diseño e implementación |
| T06 | Mitigar | Consumir la entrada con una operación atómica en la base de datos para que dos escaneos concurrentes no puedan aceptarla ambos. | Pendiente de diseño e implementación |
| T07 | Mitigar | Calcular precio, descuentos y estado exclusivamente en la API; ignorar valores de pago enviados por el cliente y validar los datos contra el evento. | Pendiente de diseño e implementación |
| T08 | Mitigar | Permitir tipos de archivo estrictamente necesarios, validar el contenido, servirlos como descarga o desde un origen aislado y evitar ejecutar SVG/HTML activo. | Pendiente de diseño e implementación |
| T09 | Mitigar | Fijar y revisar versiones de dependencias, analizar componentes vulnerables, proteger credenciales y permisos de publicación del CDN y verificar la integridad del artefacto desplegado. | Pendiente de diseño e implementación |
| T10 | Mitigar | Registrar cambios administrativos con actor autenticado, acción, objetivo, fecha y resultado; proteger los registros contra alteración no autorizada. | Pendiente de diseño e implementación |
| T11 | Mitigar | Registrar cada exportación con organizador, evento, fecha y resultado, evitando guardar el contenido completo del CSV en el registro. | Pendiente de diseño e implementación |
| T12 | Mitigar | Registrar la identidad del operador, dispositivo, entrada y hora de cada decisión de validación; restringir el acceso a esos registros. | Pendiente de diseño e implementación |
| T13 | Mitigar | Mantener el bucket privado; comprobar autorización antes de entregar el CSV y usar enlaces firmados de vida corta cuando se requiera descarga. Cifrar los datos almacenados. | Pendiente de diseño e implementación |
| T14 | Mitigar | Comprobar en cada solicitud que la inscripción pertenece al usuario autenticado antes de devolver datos o el QR; no confiar en el ID proporcionado por el cliente. | Pendiente de diseño e implementación |
| T15 | Mitigar | No guardar JWT en `localStorage`; preferir cookies `HttpOnly`, `Secure` y `SameSite`, junto con protección contra CSRF y XSS según el flujo. | Pendiente de diseño e implementación |
| T16 | Mitigar | Usar consultas parametrizadas, limitar la cuenta de base de datos por privilegio y validar los parámetros de búsqueda. | Pendiente de diseño e implementación |
| T17 | Mitigar | Usar tokens de restablecimiento aleatorios, de un solo uso y con expiración breve; invalidarlos después del cambio de contraseña. | Pendiente de diseño e implementación |
| T18 | Mitigar | Limitar frecuencia y concurrencia de validaciones, aplicar límites por cuenta/dispositivo y dimensionar la API para el ingreso esperado. | Pendiente de diseño e implementación |
| T19 | Mitigar | Aplicar límites de inscripción por cuenta y evento, controles contra automatización y protección de cupos en servidor. | Pendiente de diseño e implementación |
| T20 | Aceptar (propuesta) | Aceptar temporalmente el riesgo residual de saturación del almacenamiento: el impacto previsto se limita principalmente a disponibilidad de archivos y costos, sin comprometer directamente pagos o datos personales. Revisar la decisión si aumenta el volumen de archivos o afecta funciones esenciales. | Aceptación propuesta; requiere aprobación del responsable |
| T21 | Mitigar | Aplicar autorización RBAC en la API para cada operación administrativa; ocultar opciones en la interfaz no cuenta como control de acceso. | Pendiente de diseño e implementación |
| T22 | Mitigar | Verificar en servidor que el organizador autenticado tiene permiso sobre el evento para toda modificación, consulta y exportación. | Pendiente de diseño e implementación |
| T23 | Mitigar | Permitir solo algoritmos de firma explícitos y seguros; rechazar tokens sin firma, validar firma, emisor, audiencia y expiración, y proteger/rotar las claves. | Pendiente de diseño e implementación |
| T24 | Mitigar | Asignar roles únicamente en el servidor; rechazar o ignorar `role` recibido en el registro público y probar que no se pueda elevar privilegios modificando la solicitud. | Pendiente de diseño e implementación |

## Trazabilidad de las cinco amenazas prioritarias

Las tareas y los controles siguientes son propuestas para el futuro desarrollo. Los controles se describen como pruebas planificadas, no como capacidades existentes en EventosU.

| ID | Requisito OWASP ASVS 4.0.3 (área y objetivo aplicable) | Historia de usuario o tarea | Control de verificación propuesto |
| --- | --- | --- | --- |
| T01 | V13 API y servicios web / V11 Lógica de negocio: verificar autenticidad e integridad de las solicitudes entrantes y que el estado de pago solo cambie tras una confirmación válida. | Como API, quiero autenticar y validar cada notificación de pago, evitando aceptar eventos alterados o repetidos. | **Prueba manual e integración:** enviar webhook sin firma, con firma inválida, timestamp vencido, monto alterado y evento repetido; todos deben rechazarse o procesarse una sola vez. |
| T03 | V4 Control de acceso / V11 Lógica de negocio: verificar que la autorización y el estado de la operación se comprueban en servidor y que una entrada no pueda reutilizarse. | Como personal de puerta, quiero que la API valide que el QR corresponde a una inscripción pagada y no utilizada antes de permitir el ingreso. | **Prueba manual:** probar QR inventado, alterado, de inscripción no pagada y ya utilizado. **Prueba de concurrencia:** enviar dos validaciones simultáneas del mismo QR y comprobar que solo una se acepte. |
| T13 | V4 Control de acceso / V8 Protección de datos: verificar autorización para acceder a datos sensibles y protección de dichos datos frente a divulgación no autorizada. | Como organizador, quiero descargar el CSV únicamente para los eventos que tengo autorizados, sin que el archivo quede accesible públicamente. | **DAST y prueba manual:** solicitar el archivo sin sesión, con un usuario no autorizado y con un enlace vencido; el acceso debe denegarse. **IaC, si se adopta:** comprobar que la configuración del bucket no permite acceso público. |
| T21 | V4 Control de acceso, especialmente control de acceso a nivel de operación: verificar que cada función protegida realiza una comprobación de autorización en el servidor. | Como administrador, quiero que cada endpoint administrativo confirme el rol autorizado, incluso si se invoca directamente sin usar la interfaz. | **DAST:** invocar endpoints administrativos con sesión de estudiante, sin sesión y con rol inválido; todos deben denegarse y no modificar datos. |
| T23 | V2 Autenticación / V3 Gestión de sesiones: verificar la validación criptográfica y de las propiedades de los tokens de sesión, rechazando tokens inválidos o no firmados. | Como API, quiero aceptar únicamente JWT firmados con algoritmos y claves configurados, y validar emisor, audiencia y expiración antes de autorizar solicitudes. | **Prueba manual e integración:** probar JWT con `alg=none`, firma incorrecta, clave no autorizada, emisor/audiencia incorrectos y token vencido; ninguno debe obtener rol ni acceso privilegiado. |

## Amenaza aceptada: T20

La aceptación de T20 es una **decisión propuesta para este ejercicio**, no un hecho descrito por el caso ni una aceptación ya aprobada por la universidad. El rol de **Administrador** se propone como responsable de revisar y aprobar la aceptación por ser el rol del caso que gestiona el sistema; el enunciado no confirma que tenga autoridad formal para aceptar riesgos. La aceptación es temporal y debe revisarse si el almacenamiento afecta la disponibilidad del servicio o genera costos relevantes. Si no se aprueba esta asignación, la universidad deberá designar al responsable autorizado antes de cerrar el análisis.

La tabla STRIDE original clasificaba T20 como “Mitigar”. Este documento propone cambiar su tratamiento a “Aceptar” para cumplir la actividad; la decisión requiere aprobación y debe sincronizarse con el inventario de amenazas si se adopta.

## Observaciones de consistencia

- La priorización califica T01 como **Alto / Alta / Crítico**, mientras que la tabla STRIDE la califica como **Impacto Alto / Probabilidad Media / Riesgo Alto**. La selección de las cinco amenazas de esta sección sigue la matriz de priorización, pero deben reconciliarse ambas calificaciones antes de entregar.
- Las amenazas T01, T03, T13, T21 y T23 se tratan como prioritarias según la matriz existente. Las mitigaciones no significan que el riesgo ya esté reducido; la valoración residual se deberá realizar cuando los controles se diseñen y verifiquen.
- La aceptación propuesta para T20 no elimina la necesidad de documentar quién tiene autoridad para aprobarla en la organización real.