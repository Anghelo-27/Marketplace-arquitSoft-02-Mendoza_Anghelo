# Atributos de Calidad

Los atributos de calidad definen cómo debe comportarse el sistema frente a escenarios de alta demanda, como campañas comerciales donde interactúan múltiples usuarios concurrentes.

| ID | Atributo de calidad | Escenario de calidad |
| :--- | :--- | :--- |
| **AC01** | Rendimiento | Las consultas de productos y operaciones del carrito deben responder rápidamente incluso cuando exista una alta cantidad de usuarios concurrentes. |
| **AC02** | Disponibilidad | El sistema debe permanecer disponible durante la campaña comercial y permitir que los usuarios realicen sus operaciones. |
| **AC03** | Escalabilidad | El sistema debe poder soportar un incremento de usuarios y solicitudes sin afectar significativamente su funcionamiento. |
| **AC04** | Seguridad | Los datos de los usuarios, cuentas y operaciones de compra deben estar protegidos frente a accesos no autorizados. |
| **AC05** | Mantenibilidad | El sistema debe estar organizado de manera que permita realizar cambios y correcciones sin afectar innecesariamente otras funcionalidades. |

---

## Métricas y Criterios de Evaluación

| ID | Atributo | Métrica Objetivo | Criterio de Aceptación |
| :--- | :--- | :--- | :--- |
| **AC01** | Rendimiento | Tiempo de respuesta | Las consultas del catálogo deben responder en menos de 2 segundos bajo carga estándar. |
| **AC02** | Disponibilidad | Nivel de servicio (SLA) | Disponibilidad mínima del 99.5% durante campañas comerciales. |
| **AC03** | Escalabilidad | Concurrencia | Soportar incrementos de hasta 5 veces el tráfico base sin degradación crítica. |
| **AC04** | Seguridad | Cifrado | Comunicaciones bajo HTTPS (TLS 1.3) y almacenamiento seguro de credenciales. |