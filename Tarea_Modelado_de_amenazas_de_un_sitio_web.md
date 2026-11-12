## Tarea: Modelado de amenazas de un sitio web

## Objetivo y competencias

El estudiante aplica un proceso repetible de modelado de amenazas a un sitio web en fase de diseño y convierte cada amenaza importante en un requisito verificable.

## Al terminar, la pareja podrá:

- Representar un sistema con un DFD y sus límites de confianza.

- Identificar amenazas con STRIDE por elemento y por flujo.

- Priorizar con una matriz de impacto × probabilidad y justificar cada calificación.

- Elegir una respuesta (mitigar, eliminar, transferir, aceptar) para cada amenaza.

- Trazar las amenazas críticas a requisitos OWASP ASVS y a un control del pipeline (SAST, SCA, DAST o IaC).

## Caso: EventosU

EventosU es un sitio web ficticio donde la universidad publica congresos, talleres y conciertos; los usuarios se inscriben, pagan y reciben un código QR de acceso. Todavía está en diseño: no hay código, solo esta descripción.

## Usuarios

| Rol | Qué hace |
| --- | --- |
| Visitante | Consulta el catálogo público de eventos |
| Estudiante o público | Se registra, inicia sesión, se inscribe, paga y descarga su entrada QR |
| Organizador | Crea eventos, sube el afiche, ve la lista de inscritos y exporta un CSV |
| Personal de Puerta | Escanea el QR con una app móvil para validar la entrada |
| Administrador | Gestiona usuarios y roles, revisa reportes |

## Componentes

| Componente | Tecnología | Notas |
| --- | --- | --- |
| Sitio web | React servido desde un CDN | Guarda el token de sesión en el navegador |
| API | Node.js en un contenedor en la nube | Autenticación con JWT; login con correo institucional mediante Microsoft Entra ID (OAuth 2.0) |
| Base de datos |   | PostgreSQL administrado Usuarios, eventos, inscripciones y pagos (sin datos de tarjeta) |
| Almacenamiento | Bucket de objetos | Afiches de eventos y CSV exportados |


| de archivos |   |   |
| --- | --- | --- |
| Pasarela de pagos | Proveedor externo | Devuelve la confirmación por webhook a la API |
| Correo | Servicio externo de envío | Envía la entrada QR y el restablecimiento de contraseña |
| App de Puerta | Aplicación móvil | Consulta la API para validar el QR |
| Panel de administración | Ruta /admin del mismo sitio | Mismo login que los usuarios |

## Flujos principales

- 1. Registro con correo y contraseña, o login con la cuenta institucional.

- 2. Inscripción: el usuario elige evento, la API crea la inscripción y redirige a la pasarela.

- 3. Pago: la pasarela notifica a la API por webhook; la API marca la inscripción como pagada y envía el QR por correo.

- 4. Ingreso: el personal de puerta escanea el QR y la API responde "válido" o "ya usado".

- 5. Gestión: el organizador sube el afiche y exporta la lista de inscritos (nombre, correo, carné, teléfono).

Si la pareja necesita un supuesto que el caso no define (por ejemplo, dónde se guardan los logs), lo declara en el informe y lo usa de forma consistente.

## Actividades

## 1. Alcance y activos

- o Delimitar qué entra y qué queda fuera del modelo.

- o Listar al menos 6 activos (datos o funciones) con su valor para el negocio.

- o Declarar los supuestos usados.

## 2. DFD en Threat Dragon

- o Nivel 0 (contexto): EventosU como un solo proceso y sus actores externos.

- o Nivel 1: todos los componentes del caso, con al menos 3 límites de confianza.

- o Cada flujo nombrado con su dato y protocolo (por ejemplo, Webhook de pago (HTTPS, JSON)).

## 3. Identificación con STRIDE

- o Mínimo 18 amenazas, al menos 2 por cada letra de STRIDE.

- o Al menos 6 sobre flujos que cruzan un límite de confianza.

- o Cada amenaza con: ID, elemento, categoría, escenario concreto (quién, cómo, qué obtiene).

- o Obligatorio analizar el webhook de pago, la validación del QR y la exportación del CSV.

## 4. Árbol de ataque

- o Uno para el objetivo "entrar a un evento sin pagar", con al menos 3 ramas y nodos O/Y.

- o Marcar el camino más barato para el atacante.

## 5. Priorización

- o Matriz de impacto × probabilidad (3×3) con criterios escritos.

- o Justificación de una línea para cada calificación.

## 6. Respuesta y trazabilidad


- o Respuesta y mitigación concreta para cada amenaza.

- o Para las 5 de mayor riesgo: requisito OWASP ASVS, historia de usuario o tarea, y control que lo verifica (SAST, SCA, DAST, IaC o prueba manual).

- o Al menos una amenaza aceptada, con justificación y responsable.

## Entregables

Un repositorio de GitHub con esta estructura, compartido con el catedrático; el enlace se sube a la plataforma del curso.

eventosu-threat-model/

├── README.md

├── model/eventosu.json

├── docs/informe.pdf

└── docs/amenazas.csv

\# integrantes, alcance y cómo abrir el modelo

\# modelo de Threat Dragon

\# informe final

\# tabla de amenazas (opcional, exportada)

## Estructura del informe:

- 1. Portada e integrantes.

- 2. Alcance, activos y supuestos.

- 3. DFD nivel 0 y nivel 1 (capturas de Threat Dragon).

- 4. Tabla de amenazas: ID, elemento, STRIDE, escenario, impacto, probabilidad, riesgo, respuesta, mitigación, estado.

- 5. Árbol de ataque.

- 6. Matriz de riesgo.

- 7. Trazabilidad de las 5 amenazas críticas.

- 8. Conclusiones: qué cambiarían del diseño de EventosU antes de programarlo.

El historial de commits debe mostrar el trabajo de ambos integrantes.

## Rúbrica de evaluación

Total: 10 puntos. El mayor peso está en el DFD, la calidad de las amenazas y la trazabilidad, porque ahí se demuestra el razonamiento.

| Criterio | Puntos | Excelente (100 %) | Suficiente (60 %) | Insuficiente (0–30 %) |
| --- | --- | --- | --- | --- |
| Alcance, activos y supuestos | 0.5 | 6+ activos con valor; supuestos claros | Activos sin valor o sin supuestos | Sin alcance definido |
| DFD y límites de confianza | 2 | Nivel 0 y 1 completos, 3+ límites, flujos con dato y protocolo | Falta un componente o flujos sin nombre | DFD incompleto o sin límites |
| Amenazas STRIDE | 2.5 | 18+ amenazas, 2+ por letra, escenarios concretos, los 3 flujos obligatorios | 12–17 amenazas o escenarios genéricos | Menos de 12 o copiadas de una lista |


| Criterio | Puntos | Excelente (100 %) | Suficiente (60 %) | Insuficiente (0–30 %) |
| --- | --- | --- | --- | --- |
| Árbol de ataque | 1 | 3+ ramas, nodos O/Y, camino más barato justificado | Ramas sin lógica O/Y Ausente |   |
| Priorización | 1 | Criterios escritos y cada calificación justificada | Matriz sin justificación | Ausente |
| Respuesta y trazabilidad | 2 | Mitigación concreta en todas; 5 críticas trazadas a ASVS, tarea y control; un riesgo aceptado con responsable | Trazabilidad parcial o mitigaciones vagas ("usar seguridad") | Sin trazabilidad |
| Repositorio, informe y colaboración | 0.5 | Estructura completa, informe claro, commits de ambos | Falta un archivo o commits de uno solo | No se entrega el repositorio |

Penalización por entrega tardía: 20 % por día, hasta 2 días.

## Reglas, recursos y recomendaciones

Es un ejercicio de diseño: no se ataca ningún sistema real, y todo se razona sobre la descripción de EventosU.

## Reglas:

- Dos personas con amenazas o textos idénticos obtienen cero en el criterio correspondiente.

- El modelo JSON debe abrir sin errores en Threat Dragon.

## Recursos:

- OWASP Threat Dragon y sus releases [URL 🔗](https://owasp.org/www-project-threat-dragon/)

- [• Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)

- [• OWASP Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)

- [• OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)

- Presentación y demo de la clase 9 (modelo de Juice Shop)

## Recomendaciones:

- Empiecen por los flujos que cruzan límites de confianza: webhook, login institucional y app de puerta.

- Escriban cada amenaza como una historia: "un atacante que … puede … y obtiene …".

- Una mitigación buena se puede probar; si no saben cómo verificarla, todavía es demasiado vaga.

- Este modelo es la base de la Fase 2 del proyecto final: el mismo método se aplica después a su propia aplicación.
