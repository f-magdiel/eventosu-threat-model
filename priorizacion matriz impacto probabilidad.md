# Priorización: matriz de impacto × probabilidad

## Criterios usados

### Impacto
- Bajo: afecta solo a un usuario o a un flujo aislado y no compromete datos sensibles ni procesos clave.
- Medio: afecta a varios usuarios, expone información no crítica o interrumpe parcialmente un servicio importante.
- Alto: compromete pagos, autenticación, acceso administrativo, validación de entrada o datos personales sensibles.

### Probabilidad
- Baja: requiere condiciones muy específicas, varias etapas complejas o acceso privilegiado.
- Media: es viable para un atacante con conocimiento básico o con acceso a un flujo expuesto.
- Alta: el flujo es directamente explotable y la validación es débil o inexistente.

## Matriz 3×3

| Impacto \ Probabilidad | Baja | Media | Alta |
| --- | --- | --- | --- |
| Bajo | Bajo | Bajo | Medio |
| Medio | Bajo | Medio | Alto |
| Alto | Medio | Alto | Crítico |

## Justificación de cada calificación

- Impacto bajo: “El efecto se limita a un usuario o a un flujo menor y no pone en riesgo la operación del sistema.”
- Impacto medio: “El efecto afecta a varios usuarios o compromete información parcialmente sensible, pero no destruye la funcionalidad principal.”
- Impacto alto: “El efecto compromete pagos, autenticación, acceso administrativo o datos personales sensibles, por lo que impacta la confianza del servicio.”
- Probabilidad baja: “La explotación requiere condiciones muy específicas o varios pasos difíciles de coordinar.”
- Probabilidad media: “La explotación es posible con conocimientos razonables o con manipulación directa de un flujo expuesto.”
- Probabilidad alta: “El flujo permite una explotación directa, sin protección suficiente, o con validación débil.”
- Riesgo medio: “Combina daño relevante con una explotación posible, pero no representa un fracaso inmediato del negocio.”
- Riesgo alto: “Combina impacto importante con probabilidad real de explotación y requiere atención urgente.”
- Riesgo crítico: “Combina impacto alto con probabilidad alta, afectando directamente pagos, autenticación, acceso o integridad del sistema.”

## Aplicación a EventosU

Las amenazas con mayor prioridad son aquellas que afectan directamente la autenticación, el pago, el QR, la exportación de datos personales o el acceso administrativo, porque impactan la operación del negocio y la confianza del servicio.

## Amenazas prioritarias

| ID | Amenaza | Impacto | Probabilidad | Riesgo |
| --- | --- | --- | --- | --- |
| T01 | Webhook de pago falsificado | Alto | Alta | Crítico |
| T07 | Precio o estado alterado en la inscripción | Alto | Alta | Crítico |
| T03 | QR falsificado o predecible | Alto | Media | Alto |
| T13 | CSV de inscritos accesible por URL pública | Alto | Media | Alto |
| T21 | Control de acceso de /admin solo en el frontend | Alto | Media | Alto |

T23 también conserva riesgo Alto, pero no se incluye entre estas cinco: para explotarlo se requiere además una configuración criptográfica débil o la aceptación de JWT sin firma. Entre los riesgos Altos empatados se priorizan T03, T13 y T21 por cubrir los flujos obligatorios del QR, la exportación CSV y el acceso administrativo.

## Justificación individual de las calificaciones

Las justificaciones siguientes se basan en los escenarios de la tabla STRIDE y explican el impacto y la probabilidad asignados. El nivel de riesgo se obtiene aplicando la matriz a esos dos valores.

| ID | Justificación del impacto | Justificación de la probabilidad |
| --- | --- | --- |
| T01 | Alto: una confirmación falsa convierte una inscripción no pagada en una entrada válida y afecta directamente los ingresos. | Alta: si no se verifica la firma, basta enviar un POST al webhook conocido para intentar cambiar el estado. |
| T02 | Alto: el acceso a la cuenta de la víctima permite suplantar su identidad y usar sus funciones. | Media: requiere manipular el flujo OAuth y que la víctima complete el inicio de sesión bajo esas condiciones. |
| T03 | Alto: un QR ajeno o falsificado permite el ingreso no autorizado a un evento. | Media: se necesita descubrir el formato del QR y generar un identificador que la API acepte. |
| T04 | Alto: reutilizar el JWT de un organizador da acceso a una cuenta con funciones de gestión. | Media: primero se debe obtener el token desde un equipo compartido, XSS o extensión comprometida. |
| T05 | Alto: un webhook repetido o alterado puede hacer que una inscripción aparezca pagada sin confirmación válida. | Media: requiere capturar un webhook legítimo y que falten controles de firma, timestamp o idempotencia. |
| T06 | Alto: aceptar dos escaneos del mismo QR permite que una entrada se use más de una vez. | Media: depende de presentar copias del mismo QR con sincronización entre escaneos y de que la actualización no sea atómica. |
| T07 | Alto: modificar el precio o estado puede permitir una inscripción sin pagar. | Alta: el escenario describe una solicitud manipulable que la API acepta sin recalcular el precio de forma confiable. |
| T08 | Medio: el script afecta a quienes consultan el afiche, sin comprometer por sí mismo pagos o administración. | Media: se requiere que se permita subir contenido activo y que el bucket lo sirva ejecutable. |
| T09 | Alto: código malicioso distribuido por la SPA puede alcanzar a los usuarios que cargan la aplicación. | Media: el atacante necesita comprometer una dependencia o la cuenta del CDN antes de alterar el código servido. |
| T10 | Medio: sin auditoría no se puede atribuir un cambio administrativo, aunque el escenario no implica por sí mismo exposición masiva o interrupción. | Media: requiere una acción administrativa y que no exista un registro que permita atribuirla. |
| T11 | Medio: la falta de autoría dificulta investigar una exportación o filtración de datos, pero no causa por sí sola la filtración. | Media: depende de que se realice una exportación y no haya registro de quién la hizo. |
| T12 | Medio: una validación indebida afecta el control de acceso al evento y no queda atribuida al operador. | Media: requiere una acción deliberada del operador y ausencia de registros de escaneo. |
| T13 | Alto: la descarga expone en conjunto nombres, correos, carnés y teléfonos de inscritos. | Media: el atacante debe obtener o descubrir una URL pública o predecible del archivo. |
| T14 | Alto: consultar la inscripción ajena expone datos personales y el QR de otra persona. | Media: depende de que la API no compruebe la propiedad del registro y de que se manipule su identificador. |
| T15 | Alto: el robo del JWT permite tomar la sesión de la víctima y acceder a sus datos y funciones. | Media: se necesita primero XSS o una dependencia comprometida para leer el token del almacenamiento del navegador. |
| T16 | Alto: una inyección exitosa podría exponer datos de usuarios y registros internos. | Media: depende de que la búsqueda use una consulta vulnerable y de que los permisos de base de datos permitan acceder a esos registros. |
| T17 | Alto: usar el enlace de restablecimiento permite cambiar la contraseña y tomar la cuenta. | Media: requiere acceso al buzón o a registros del proveedor y que el token sea largo y reutilizable. |
| T18 | Medio: la saturación impide validar entradas durante el evento, afectando ese proceso operativo. | Alta: una ráfaga automatizada puede saturar la API si no existen límites de frecuencia o concurrencia. |
| T19 | Medio: agotar los cupos impide que otros asistentes se inscriban en el evento afectado. | Alta: la automatización permite generar muchas inscripciones si no hay límites o controles anti-bot. |
| T20 | Medio: llenar el bucket afecta archivos y costos, sin comprometer directamente pagos ni cuentas administrativas. | Media: requiere cargas grandes o repetidas y que no haya cuotas o límites efectivos. |
| T21 | Alto: obtener privilegios administrativos permite operar sobre usuarios, roles y reportes. | Media: requiere invocar un endpoint administrativo y que la API no aplique autorización, aunque el escenario plantea esa falta de control. |
| T22 | Alto: modificar o exportar datos de otro evento compromete la gestión y privacidad de sus inscritos. | Media: requiere alterar el identificador del evento y que falle la comprobación de propiedad del organizador. |
| T23 | Alto: un JWT forjado con rol admin permite acceso privilegiado a la API. | Media: depende de una clave débil o de que la API acepte tokens sin firma, condiciones de configuración específicas. |
| T24 | Alto: autoasignarse el rol admin concede privilegios que el registro público no debería otorgar. | Media: requiere que el endpoint de registro acepte el campo `role` del cliente sin validarlo en servidor. |

### Revisión de consistencia

T07 tiene Impacto Alto y Probabilidad Alta; por tanto, su riesgo es **Crítico** en esta matriz y en la tabla STRIDE. La selección de las cinco amenazas trazadas incluye ahora T01, T07, T03, T13 y T21.

## Observación final

La priorización se realiza conforme al criterio de que un riesgo crítico requiere atención inmediata, ya que compromete la integridad del negocio, la seguridad de acceso y la protección de datos personales.
