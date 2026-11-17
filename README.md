# eventosu-threat-model

## Integrantes

| Nombre completo | Número de carné |
| --- | --- |
| Francisco Magdiel Asicona Mateo | 26006399 |
| Sergio Rolando Oliva del Valle | 26005694 |

## Índice

- [Tabla de amenazas STRIDE](tabla%20de%20amenazas%20stride.md)
- [Priorización: matriz de impacto × probabilidad](priorizacion%20matriz%20impacto%20probabilidad.md)
- [Respuesta y trazabilidad](Respuesta%20y%20trazabilidad.md)
- [Alcance, activos y supuestos](alcance,%20activos%20y%20supuestos.md)
- [Árbol de ataque](docs/arbol-de-ataque.md)
- [Modelo Threat Dragon](model/eventosu.json)

## Descripción

Este repositorio contiene el trabajo de modelado de amenazas para EventosU, basado en el caso descrito en la tarea y en el archivo de Threat Dragon.

## Documentos principales

- [Tabla de amenazas STRIDE](tabla%20de%20amenazas%20stride.md): resumen de las amenazas extraídas del modelo existente.
- [Priorización: matriz de impacto × probabilidad](priorizacion%20matriz%20impacto%20probabilidad.md): criterios, matriz y justificación de riesgos.
- [Alcance, activos y supuestos](alcance,%20activos%20y%20supuestos.md): alcance, activos y supuestos del análisis.
- [Árbol de ataque](docs/arbol-de-ataque.md): ataque para entrar a un evento sin pagar.

## Fuente del análisis

- [model/eventosu.json](model/eventosu.json)

## Cómo abrir el modelo

1. Abre OWASP Threat Dragon.
2. Selecciona la opción para abrir o importar un modelo existente.
3. Elige el archivo `model/eventosu.json` de este repositorio.
4. Abre los diagramas **DFD nivel 0 (contexto)** y **DFD nivel 1 (componentes)** desde el selector de diagramas.
## Alcance del modelo

Se analizan los clientes web y de puerta, API, base de datos, bucket y las integraciones con identidad, pagos y correo de EventosU. Se excluyen las implementaciones internas de los proveedores externos y pruebas sobre sistemas reales. Los controles se proponen para una aplicación aún en diseño.

## Entrega e informe

- [Informe PDF](docs/informe.pdf): versión para revisión con capturas de ambos DFD incorporadas.
- [Informe HTML](docs/informe.html): fuente de presentación generada.
- [Amenazas CSV](docs/amenazas.csv): 24 amenazas, con respuesta y mitigación.
- [Validación de entrega](docs/validacion-entrega.md): resultados y pendientes.

Para regenerar desde los documentos Markdown y el modelo original:

```sh
python3 docs/generar-informe.py
sh docs/render-pdf.sh
python3 docs/validar-entrega.py
```

El generador conserva `ThreatDragonModels/Threat Model/Threat Model.json` como fuente del modelo y copia su contenido a la ruta de entrega `model/eventosu.json`. Editar el modelo original y regenerar evita divergencias.

Guardar las capturas de Threat Dragon como `docs/figuras/dfd-nivel-0.png` y `docs/figuras/dfd-nivel-1.png` antes de regenerar. El renderizador requiere Google Chrome en macOS. El historial ya contiene aportes de ambos integrantes; compartir el repositorio y entregar el enlace en la plataforma son pasos externos pendientes de comprobar.
