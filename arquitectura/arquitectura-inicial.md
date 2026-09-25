# Arquitectura inicial del sistema

## Diagrama de arquitectura

    ```mermaid
    flowchart TD
    %% ==========================
    %% ACTORES
    %% ==========================
    subgraph ACTORES ["ACTORES"]
        Cliente ["Cliente"]
        Seller ["Seller"]
        Admin ["Administrador"]
    end

    %% ==========================
    %% PRESENTACIÓN
    %% ==========================
    subgraph PRESENTACION ["PRESENTACIÓN"]
        Web ["Aplicación Web → API REST"]
    end

    %% ==========================
    %% LÓGICA DE NEGOCIO
    %% ==========================
    subgraph NEGOCIO ["LÓGICA DE NEGOCIO"]
        Usuarios ["Usuarios"]
        Sellers ["Sellers"]
        Catalogo ["Catálogo"]
        Carrito ["Carrito"]
        Pedidos ["Pedidos"]
    end

    %% ==========================
    %% DATOS
    %% ==========================
    subgraph DATOS ["DATOS"]
        BD ["Base de datos"]
    end

    %% ==========================
    %% SISTEMAS EXTERNOS
    %% ==========================
    subgraph EXTERNOS ["SISTEMAS EXTERNOS"]
        Pago ["Pasarela de pago"]
        ERP ["ERP"]
        Envio ["Servicio de envío"]
    end

    %% ==========================
    %% FLUJO PRINCIPAL
    %% ==========================
    ACTORES --> PRESENTACION
    PRESENTACION --> NEGOCIO
    NEGOCIO --> DATOS

    %% Integraciones
    DATOS --> |integraciones| EXTERNOS

    %% ==========================
    %% DISTRIBUCIÓN HORIZONTAL
    %% ==========================
    Cliente ~~~ Seller
    Seller ~~~ Admin

    Usuarios ~~~ Sellers
    Sellers ~~~ Catalogo
    Catalogo ~~~ Carrito
    Carrito ~~~ Pedidos

    Pago ~~~ ERP
    ERP ~~~ Envio

    %% ==========================
    %% ESTILOS
    %% ==========================
    style ACTORES fill:#222,stroke:#fff,stroke-width:2px,   color:#fff
    style PRESENTACION fill:#222,stroke:#fff,stroke-width:2px,  color:#fff
    style NEGOCIO fill:#222,stroke:#fff,stroke-width:2px,   color:#fff
    style DATOS fill:#222,stroke:#fff,stroke-width:2px, color:#fff
    style EXTERNOS fill:#222,stroke:#fff,stroke-width:2px,  color:#fff

    style Cliente fill:#222,stroke:#fff,color:#fff
    style Seller fill:#222,stroke:#fff,color:#fff
    style Admin fill:#222,stroke:#fff,color:#fff
    style Web fill:#222,stroke:#fff,color:#fff
    style Usuarios fill:#222,stroke:#fff,color:#fff
    style Sellers fill:#222,stroke:#fff,color:#fff
    style Catalogo fill:#222,stroke:#fff,color:#fff
    style Carrito fill:#222,stroke:#fff,color:#fff
    style Pedidos fill:#222,stroke:#fff,color:#fff
    style BD fill:#222,stroke:#fff,color:#fff
    style Pago fill:#222,stroke:#fff,color:#fff
    style ERP fill:#222,stroke:#fff,color:#fff
    style Envio fill:#222,stroke:#fff,color:#fff    

## Descripción

    La arquitectura inicial se organiza en tres capas   principales:
    - **Presentación:** permite la interacción de los   usuarios con el sistema mediante la aplicación web y la   API REST.
    - **Lógica de negocio:** contiene los principales módulos   responsables de las funcionalidades del sistema:  usuarios, sellers, catálogo, carrito y pedidos.
    - **Datos:** permite almacenar y consultar la información   mediante una base de d    atos.

    Además, el módulo de **Pedidos** se integra con     sistemas     externos como la   **pasarela de pago** y el   **servicio de envío**.


---

## Detalle de Responsabilidades por Capa

| Capa | Responsabilidad Principal | Tecnologías / Estándares Considerados |
| :--- | :--- | :--- |
| **Presentación** | Gestión de interfaces, validación inicial del lado del cliente y consumo de endpoints mediante API REST. | HTML5, CSS3, JavaScript / Framework SPA (React/Next.js o Blazor). |
| **Lógica de Negocio** | Ejecución de reglas del marketplace (cálculo de totales, aplicación de comisiones, orquestación de pedidos y autenticación). | API REST (Node.js / Express, ASP.NET Core o FastAPI). |
| **Datos** | Persistencia, integridad referencial y almacenamiento de catálogos, carritos y transacciones. | Base de datos relacional (PostgreSQL / SQL Server). |
| **Sistemas Externos** | Servicios desacoplados para pasarela de pago (procesamiento transaccional) y logística/envío. | Integraciones vía HTTPS / Webhooks seguros. |