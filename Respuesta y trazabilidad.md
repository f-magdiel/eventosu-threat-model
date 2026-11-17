# Respuesta y trazabilidad

## Alcance y criterios

Las respuestas se basan en las 24 amenazas de la [tabla STRIDE](tabla%20de%20amenazas%20stride.md). Las cinco amenazas trazadas son las que la [matriz de priorización](priorizacion%20matriz%20impacto%20probabilidad.md) identifica como prioritarias: T01, T03, T13, T21 y T23.

Para la trazabilidad se usa OWASP ASVS 4.0.3. Se indican los identificadores concretos y su aplicación al escenario; las pruebas propuestas verifican los controles diseñados para EventosU.

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
| T20 | Aceptar riesgo residual | Establecer límites de tamaño y cuotas de almacenamiento por organizador, y alertas de consumo. Se acepta el riesgo residual de interrupción de archivos o costos dentro de esos límites; revisar la decisión si afecta funciones esenciales o supera el presupuesto aprobado. | Aceptado en este modelo; controles pendientes de diseño e implementación |
| T21 | Mitigar | Aplicar autorización RBAC en la API para cada operación administrativa; ocultar opciones en la interfaz no cuenta como control de acceso. | Pendiente de diseño e implementación |
| T22 | Mitigar | Verificar en servidor que el organizador autenticado tiene permiso sobre el evento para toda modificación, consulta y exportación. | Pendiente de diseño e implementación |
| T23 | Mitigar | Permitir solo algoritmos de firma explícitos y seguros; rechazar tokens sin firma, validar firma, emisor, audiencia y expiración, y proteger/rotar las claves. | Pendiente de diseño e implementación |
| T24 | Mitigar | Asignar roles únicamente en el servidor; rechazar o ignorar `role` recibido en el registro público y probar que no se pueda elevar privilegios modificando la solicitud. | Pendiente de diseño e implementación |

## Trazabilidad de las cinco amenazas prioritarias

Las tareas y los controles siguientes son propuestas para el futuro desarrollo. Los controles se describen como pruebas planificadas, no como capacidades existentes en EventosU.

| ID | Requisito OWASP ASVS 4.0.3 (área y objetivo aplicable) | Historia de usuario o tarea | Control de verificación propuesto |
| --- | --- | --- | --- |
| T01 | **V13.2.2**: validar el esquema JSON antes de aceptar la entrada. Este requisito no especifica la firma del webhook; verificar la firma del proveedor e idempotencia son controles adicionales necesarios para este escenario. | Como API, quiero autenticar y validar cada notificación de pago, evitando aceptar eventos alterados o repetidos. | **Prueba manual e integración:** enviar webhook sin firma, con firma inválida, timestamp vencido, monto alterado y evento repetido; todos deben rechazarse o procesarse una sola vez. |
| T03 | **V11.1.1**: procesar el flujo de negocio del mismo usuario en orden secuencial y sin saltar pasos; **V4.1.3**: limitar las funciones y recursos a usuarios con autorización específica. | Como personal de puerta, quiero que la API valide que el QR corresponde a una inscripción pagada y no utilizada antes de permitir el ingreso. | **Prueba manual:** probar QR inventado, alterado, de inscripción no pagada y ya utilizado. **Prueba de concurrencia:** enviar dos validaciones simultáneas del mismo QR y comprobar que solo una se acepte. |
| T13 | **V4.1.3**: limitar el acceso a archivos y recursos según autorización; **V8.3.5**: auditar el acceso a datos sensibles cuando corresponda; **V8.3.7**: cifrar la información privada que requiera cifrado. | Como organizador, quiero descargar el CSV únicamente para los eventos que tengo autorizados, sin que el archivo quede accesible públicamente. | **DAST y prueba manual:** solicitar el archivo sin sesión, con un usuario no autorizado y con un enlace vencido; el acceso debe denegarse. **IaC:** comprobar que la configuración del bucket no permite acceso público. |
| T21 | **V4.1.1**: aplicar el control de acceso en una capa de servicio confiable, aunque también exista control en el cliente; **V4.1.3**: aplicar privilegio mínimo. | Como administrador, quiero que cada endpoint administrativo confirme el rol autorizado, incluso si se invoca directamente sin usar la interfaz. | **DAST:** invocar endpoints administrativos con sesión de estudiante, sin sesión y con rol inválido; todos deben denegarse y no modificar datos. |
| T23 | **V3.5.3**: proteger los tokens de sesión sin estado mediante firmas digitales y contramedidas contra alteración, repetición y sustitución de claves. | Como API, quiero aceptar únicamente JWT firmados con algoritmos y claves configurados, y validar emisor, audiencia y expiración antes de autorizar solicitudes. | **Prueba manual e integración:** probar JWT con `alg=none`, firma incorrecta, clave no autorizada, emisor/audiencia incorrectos y token vencido; ninguno debe obtener rol ni acceso privilegiado. |

## Amenaza aceptada: T20

La aceptación de T20 es una **decisión de modelado adoptada para este ejercicio**, no un hecho descrito por el caso ni una aprobación institucional real. Se asigna al rol **Administrador** la responsabilidad de revisar el riesgo y sus límites porque gestiona usuarios, roles y reportes en el sistema descrito. La aceptación cubre únicamente el riesgo residual de disponibilidad de archivos y costos una vez que se definan límites de tamaño, cuotas por organizador y alertas de consumo. El Administrador debe revisar la decisión si esos límites se superan o si se afectan funciones esenciales.

En la tabla STRIDE, T20 queda con respuesta “Aceptar riesgo residual” y conserva controles como condiciones de esa aceptación.

## Observaciones de consistencia

- T01 se clasifica como **Impacto Alto / Probabilidad Alta / Riesgo Crítico** en la priorización y en la tabla STRIDE, dado que el escenario describe un endpoint expuesto cuya validación de firma puede ser débil o inexistente.
- Las amenazas T01, T03, T13, T21 y T23 se tratan como prioritarias según la matriz existente. Las mitigaciones no significan que el riesgo ya esté reducido; la valoración residual se deberá realizar cuando los controles se diseñen y verifiquen.
- La asignación del Administrador como responsable de T20 es una decisión explícita de este modelo; no afirma que el caso le otorgue autoridad institucional formal para aceptar riesgos.