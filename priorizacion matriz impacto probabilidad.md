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
| T03 | QR falsificado o predecible | Alto | Media | Alto |
| T13 | CSV de inscritos accesible por URL pública | Alto | Media | Alto |
| T21 | Control de acceso de /admin solo en el frontend | Alto | Media | Alto |
| T23 | JWT forjado con rol de administrador | Alto | Media | Alto |

## Observación final

La priorización se realiza conforme al criterio de que un riesgo crítico requiere atención inmediata, ya que compromete la integridad del negocio, la seguridad de acceso y la protección de datos personales.
