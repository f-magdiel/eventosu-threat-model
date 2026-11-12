# 1. Alcance, activos y supuestos

## 1.1. Descripción general del sistema

EventosU es una plataforma web ficticia diseñada para la publicación y gestión de eventos universitarios, como congresos, talleres y conciertos. Permite a los usuarios consultar eventos, realizar inscripciones, efectuar pagos y obtener entradas digitales mediante códigos QR.

El sistema contempla cinco roles: visitante, estudiante o público general, organizador, personal de puerta y administrador. Cada uno dispone de funciones relacionadas con la consulta de eventos, gestión de inscripciones, validación de entradas o administración del sistema.

La arquitectura propuesta incluye una aplicación web React distribuida mediante CDN, una API Node.js ejecutada en un contenedor en la nube, una base de datos PostgreSQL administrada y un bucket de almacenamiento de objetos. También incorpora servicios externos para autenticación institucional, procesamiento de pagos y envío de correos electrónicos.

Debido a que EventosU se encuentra en fase de diseño y todavía no dispone de código implementado, el modelado de amenazas se realizará sobre su arquitectura propuesta, los flujos de información y las interacciones entre componentes.

## 1.2. Alcance del modelado de amenazas

El objetivo es identificar posibles riesgos relacionados con la confidencialidad, integridad y disponibilidad de la información, así como con la autenticación, autorización y funcionamiento de los procesos principales de EventosU.

### Componentes incluidos

1. **Aplicación web:** interfaz React distribuida mediante CDN para consultar eventos, iniciar sesión, inscribirse y descargar entradas.

2. **API:** servicio Node.js responsable de procesar solicitudes, ejecutar operaciones del negocio y comunicarse con los demás componentes.

3. **Base de datos PostgreSQL:** almacena usuarios, eventos, inscripciones y registros de pagos, sin almacenar información de tarjetas bancarias.

4. **Almacenamiento de archivos:** bucket utilizado para almacenar afiches de eventos y archivos CSV exportados.

5. **Panel de administración:** funcionalidad disponible mediante la ruta `/admin`, utilizada para gestionar usuarios, roles y reportes.

6. **Aplicación de puerta:** aplicación móvil utilizada para validar entradas QR mediante consultas a la API.

7. **Integración con Microsoft Entra ID:** autenticación institucional mediante OAuth 2.0.

8. **Integración con pasarela de pagos:** procesamiento externo de pagos y confirmación mediante webhook hacia la API.

9. **Integración con servicio de correo:** envío de entradas QR y mensajes de restablecimiento de contraseña.

### Procesos incluidos

- Registro e inicio de sesión de usuarios.
- Autenticación mediante cuentas institucionales.
- Inscripción a eventos.
- Procesamiento y confirmación de pagos.
- Entrega y validación de entradas QR.
- Publicación y administración de eventos.
- Carga de afiches.
- Consulta y exportación de listas de inscritos.
- Administración de usuarios y roles.

### Elementos fuera del alcance

Se excluye el análisis de la implementación interna de Microsoft Entra ID, la pasarela de pagos y el proveedor externo de correo electrónico.

**Decisión aprobada:** las integraciones con estos servicios sí se incluyen en el análisis, especialmente sus intercambios de información y límites de confianza.

También se excluyen las pruebas de penetración contra sistemas reales y el análisis de vulnerabilidades sobre código fuente, ya que EventosU todavía no ha sido implementado.

## 1.3. Identificación y valoración de activos

Los activos son datos, recursos y funcionalidades cuyo compromiso podría afectar la seguridad, operación o confiabilidad de EventosU.

| ID | Activo | Valor para el negocio |
|---|---|---|
| A01 | Cuentas y credenciales | Protegen la identidad de los usuarios y el acceso a sus cuentas. |
| A02 | Tokens JWT | Permiten mantener sesiones autenticadas y acceder a funcionalidades protegidas. |
| A03 | Datos personales de inscritos | Contienen nombres, correos, carnés y teléfonos, cuya exposición comprometería la privacidad. |
| A04 | Inscripciones y registros de pagos | Permiten comprobar las inscripciones y confirmar los pagos realizados. |
| A05 | Entradas y códigos QR | Permiten verificar el derecho de acceso a los eventos. |
| A06 | Catálogo de eventos | Contiene información necesaria para publicar y administrar actividades. |
| A07 | Roles y permisos | Restringen operaciones según las responsabilidades de cada usuario. |
| A08 | Archivos CSV de inscritos | Contienen información personal utilizada para gestionar participantes. |
| A09 | Disponibilidad de la API | Permite mantener operativos los procesos de inscripción, pago y validación de entradas. |
| A10 | Afiches y archivos publicados | Permiten comunicar y promocionar los eventos universitarios. |

## 1.4. Supuestos del modelado

Los siguientes supuestos se establecen para cubrir aspectos técnicos que el enunciado no define completamente. No deben interpretarse como controles de seguridad implementados o verificados.

| ID | Supuesto | Justificación |
|---|---|---|
| S01 | Las comunicaciones entre clientes, API y servicios externos utilizan HTTPS. | Definir el protocolo esperado para los flujos de información. |
| S02 | La API es responsable de verificar identidad y permisos antes de ejecutar operaciones protegidas. | Identificar el componente donde se analizarán los controles de autenticación y autorización. |
| S03 | Cada código QR está asociado a una inscripción individual. | Analizar posibles escenarios de falsificación, duplicación y reutilización de entradas. |
| S04 | La API recibe las notificaciones de pago mediante un endpoint de webhook. | Representar el flujo de confirmación de pagos descrito en el caso. |
| S05 | Los CSV exportados contienen nombre, correo, carné y teléfono de los inscritos. | Evaluar posibles exposiciones de información personal. |
| S06 | Cada organizador debe tener autorización sobre los eventos que administra. | Definir el criterio esperado de autorización para operaciones sobre eventos. |
| S07 | La validación de entradas QR requiere consultar su estado mediante la API. | Analizar la integridad y disponibilidad del control de acceso a eventos. |
| S08 | El almacenamiento y consulta de registros de auditoría no están definidos. | Identificar una decisión pendiente relacionada con trazabilidad y repudio. |

### Decisiones de diseño aprobadas

**D01. Servicios externos:** se analizarán las integraciones con los proveedores externos, sin evaluar sus implementaciones internas.

**D02. Entradas QR:** cada QR estará asociado a una inscripción individual. No se asumirá que los códigos están firmados, cifrados o protegidos contra reutilización, ya que estos mecanismos deberán evaluarse durante el análisis de amenazas.

**D03. Autorización de organizadores:** cada organizador deberá tener autorización sobre los eventos que administra. El mecanismo específico para aplicar esta restricción todavía no está definido y será considerado durante el análisis STRIDE.

## 1.5. Consideraciones iniciales de seguridad

Se identifican tres interacciones especialmente relevantes para las siguientes etapas del modelado.

**Confirmación de pagos mediante webhook:** una notificación falsificada o manipulada podría provocar que el sistema registre una inscripción como pagada sin que exista una transacción válida.

**Validación de entradas QR:** una entrada falsificada, duplicada o reutilizada podría permitir el acceso no autorizado a un evento si no existen controles adecuados.

**Exportación de información de inscritos:** una autorización incorrecta o una exposición accidental de archivos CSV podría comprometer información personal de los participantes.

Estas situaciones representan escenarios preliminares de amenaza y no vulnerabilidades confirmadas.

## 1.6. Conclusión

La definición del alcance, los activos y los supuestos establece la base para el modelado de amenazas de EventosU.

Esta delimitación permite identificar los componentes relevantes, comprender el valor de la información procesada y establecer criterios consistentes para analizar posibles riesgos de seguridad.

En las siguientes actividades se utilizará esta información para construir los diagramas de flujo de datos de nivel 0 y nivel 1, identificar límites de confianza y desarrollar escenarios de amenaza mediante la metodología STRIDE.
