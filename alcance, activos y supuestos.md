# 1. Alcance, activos y supuestos

## 1.1. Descripción general del sistema

EventosU es una plataforma web ficticia diseñada para la publicación y gestión de eventos universitarios, como congresos, talleres y conciertos. Su propósito es permitir que los usuarios consulten eventos disponibles, realicen inscripciones, efectúen pagos y obtengan entradas digitales mediante códigos QR.

La plataforma contempla diferentes tipos de usuarios: visitantes, estudiantes o público general, organizadores, personal de puerta y administradores. Cada uno desempeña funciones específicas relacionadas con la consulta de eventos, gestión de inscripciones, validación de entradas o administración del sistema.

Desde el punto de vista tecnológico, EventosU contempla una aplicación web desarrollada en React, una API en Node.js, una base de datos PostgreSQL y almacenamiento de archivos mediante un bucket de objetos. Además, utiliza servicios externos para autenticación institucional, procesamiento de pagos y envío de correos electrónicos.

Debido a que EventosU se encuentra en fase de diseño y todavía no dispone de código implementado, el análisis de amenazas se realizará sobre su arquitectura propuesta, los flujos de información y las interacciones entre sus componentes.

## 1.2. Alcance del modelado de amenazas

El modelado de amenazas tiene como objetivo identificar posibles riesgos de seguridad relacionados con la confidencialidad, integridad y disponibilidad de la información, así como con la autenticación, autorización y funcionamiento de los procesos principales de EventosU.

### Componentes incluidos en el alcance

1. **Aplicación web:** interfaz desarrollada en React y distribuida mediante CDN, utilizada para consultar eventos, iniciar sesión, inscribirse y descargar entradas.

2. **API:** servicio desarrollado en Node.js y ejecutado en un contenedor en la nube, responsable de procesar solicitudes, aplicar reglas de negocio y comunicarse con otros componentes.

3. **Base de datos PostgreSQL:** almacenamiento de información relacionada con usuarios, eventos, inscripciones y pagos.

4. **Almacenamiento de archivos:** bucket de objetos utilizado para almacenar afiches de eventos y archivos CSV exportados.

5. **Panel de administración:** funcionalidad disponible mediante la ruta `/admin`, utilizada para administrar usuarios, roles y consultar reportes.

6. **Aplicación de puerta:** aplicación móvil utilizada por el personal autorizado para consultar la API y validar códigos QR.

7. **Integración con Microsoft Entra ID:** mecanismo de autenticación institucional mediante OAuth 2.0.

8. **Integración con la pasarela de pagos:** comunicación con el proveedor externo para procesar pagos y recibir confirmaciones mediante webhooks.

9. **Integración con el servicio de correo:** mecanismo utilizado para enviar entradas digitales y mensajes relacionados con el restablecimiento de contraseñas.

### Procesos incluidos en el análisis

- Registro e inicio de sesión de usuarios.
- Autenticación mediante cuentas institucionales.
- Inscripción a eventos.
- Procesamiento y confirmación de pagos.
- Generación, entrega y validación de entradas QR.
- Publicación y administración de eventos.
- Carga de afiches.
- Consulta y exportación de listas de inscritos.
- Administración de usuarios y roles.

### Elementos fuera del alcance

Se excluye del análisis detallado la implementación interna de los servicios administrados por terceros, como Microsoft Entra ID, la pasarela de pagos y el proveedor de correo electrónico.

Sin embargo, las comunicaciones, intercambios de información y límites de confianza entre dichos servicios y EventosU sí forman parte del modelado de amenazas.

También se excluyen las pruebas de penetración contra sistemas reales y el análisis de vulnerabilidades sobre código fuente, debido a que EventosU todavía se encuentra en fase de diseño.

## 1.3. Identificación y valoración de activos

Los activos representan datos, recursos o funcionalidades cuyo compromiso podría afectar la seguridad, operación o confiabilidad de EventosU.

| ID | Activo | Valor para el negocio |
|---|---|---|
| A01 | Cuentas y credenciales de usuarios | Permiten identificar a los usuarios y proteger el acceso a sus cuentas. |
| A02 | Tokens de sesión JWT | Permiten mantener sesiones autenticadas y acceder a funcionalidades protegidas. |
| A03 | Información personal de inscritos | Contiene nombres, correos, carnés y teléfonos, cuya exposición afectaría la privacidad de los usuarios. |
| A04 | Registros de inscripciones y pagos | Permiten comprobar la inscripción y el estado de pago de los participantes. |
| A05 | Entradas digitales y códigos QR | Permiten verificar el derecho de acceso de los asistentes a los eventos. |
| A06 | Catálogo y configuración de eventos | Contienen información necesaria para publicar, organizar y administrar las actividades universitarias. |
| A07 | Roles y permisos de acceso | Permiten restringir las operaciones según las responsabilidades de cada usuario. |
| A08 | Archivos CSV de inscritos | Contienen información de los participantes utilizada para la gestión de eventos. |
| A09 | Disponibilidad de la API y validación de entradas | Permite mantener operativos los procesos de inscripción, pago y control de acceso. |
| A10 | Afiches y archivos publicados | Representan contenido utilizado para comunicar y promocionar los eventos. |

## 1.4. Supuestos del modelado

Debido a que la descripción de EventosU no especifica todos los detalles técnicos y de seguridad, se establecen los siguientes supuestos de trabajo. Estos no representan controles confirmados, sino condiciones propuestas para desarrollar un modelo consistente.

| ID | Supuesto | Justificación |
|---|---|---|
| S01 | Las comunicaciones entre clientes, API y servicios externos utilizan HTTPS. | Establecer el protocolo esperado para los flujos de información. |
| S02 | La API es responsable de verificar la identidad y los permisos de los usuarios antes de ejecutar operaciones protegidas. | Definir dónde se evaluarán las amenazas de autenticación y autorización. |
| S03 | Cada entrada QR está asociada a una inscripción específica. | Permitir el análisis de duplicación, falsificación y reutilización de entradas. |
| S04 | La API recibe y procesa las notificaciones de pago mediante un endpoint de webhook. | Delimitar el flujo de confirmación de pagos descrito en el caso. |
| S05 | Los archivos CSV exportados contienen los datos de los inscritos indicados en el enunciado. | Evaluar posibles exposiciones de información personal. |
| S06 | Los organizadores únicamente deben administrar los eventos para los que tienen autorización. | Establecer un criterio de autorización para analizar posibles accesos indebidos. |
| S07 | La validación de una entrada QR requiere consultar su estado mediante la API. | Evaluar la integridad y disponibilidad del proceso de control de acceso. |
| S08 | El almacenamiento y la consulta de registros de auditoría no están definidos en el caso y deberán considerarse como una decisión pendiente de diseño. | Permitir el análisis de amenazas relacionadas con trazabilidad y repudio sin asumir controles inexistentes. |

## 1.5. Consideraciones iniciales de seguridad

A partir de los componentes y procesos descritos, se identifican tres interacciones especialmente relevantes para las siguientes etapas del modelado:

**Confirmación de pagos mediante webhook:** una notificación de pago falsificada o manipulada podría provocar que el sistema registre una inscripción como pagada sin que exista una transacción válida.

**Validación de entradas mediante QR:** una entrada falsificada, duplicada o reutilizada podría permitir el acceso no autorizado a un evento si no existen controles adecuados.

**Exportación de información de inscritos:** una autorización incorrecta o una exposición accidental de archivos CSV podría comprometer información personal de los participantes.

Estas situaciones constituyen escenarios preliminares que serán desarrollados y clasificados durante el análisis STRIDE. No representan vulnerabilidades confirmadas.

## 1.6. Conclusión de la delimitación

La definición del alcance, los activos y los supuestos establece la base del modelado de amenazas de EventosU. Esta delimitación permite identificar los componentes relevantes, comprender el valor de la información que procesan y establecer criterios consistentes para analizar posibles riesgos.

En las siguientes actividades se utilizará esta información para construir los diagramas de flujo de datos de nivel 0 y nivel 1, identificar límites de confianza y desarrollar escenarios de amenaza mediante la metodología STRIDE.
