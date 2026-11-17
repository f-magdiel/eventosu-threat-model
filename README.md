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
- [Modelo Threat Dragon](ThreatDragonModels/Threat%20Model/Threat%20Model.json)

## Descripción

Este repositorio contiene el trabajo de modelado de amenazas para EventosU, basado en el caso descrito en la tarea y en el archivo de Threat Dragon.

## Documentos principales

- [Tabla de amenazas STRIDE](tabla%20de%20amenazas%20stride.md): resumen de las amenazas extraídas del modelo existente.
- [Priorización: matriz de impacto × probabilidad](priorizacion%20matriz%20impacto%20probabilidad.md): criterios, matriz y justificación de riesgos.
- [Alcance, activos y supuestos](alcance,%20activos%20y%20supuestos.md): alcance, activos y supuestos del análisis.
- [Árbol de ataque](docs/arbol-de-ataque.md): ataque para entrar a un evento sin pagar.

## Fuente del análisis

- [ThreatDragonModels/Threat Model/Threat Model.json](ThreatDragonModels/Threat%20Model/Threat%20Model.json)

## Cómo abrir el modelo

1. Abre OWASP Threat Dragon.
2. Selecciona la opción para abrir o importar un modelo existente.
3. Elige el archivo `ThreatDragonModels/Threat Model/Threat Model.json` de este repositorio.
4. Abre los diagramas **DFD nivel 0 (contexto)** y **DFD nivel 1 (componentes)** desde el selector de diagramas.