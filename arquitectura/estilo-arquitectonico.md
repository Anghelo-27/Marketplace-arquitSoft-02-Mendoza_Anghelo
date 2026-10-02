flowchart TD
    %% Actores
    Cliente["Cliente"]
    Seller["Seller"]
    Admin["Administrador"]

    ClienteWeb["Cliente Web<br/><i>[Navegador · HTML / CSS / JavaScript]</i>"]

    Cliente ---> ClienteWeb
    Seller ---> ClienteWeb
    Admin ---> ClienteWeb

    subgraph Backend ["«monolito» Marketplace Backend [Node.js 20 LTS · Express]<br/><i>Una sola aplicación · un solo proceso · un solo despliegue</i>"]
        direction TB

        Middlewares["<b>Middlewares Express (transversales)</b><br/>cors · express.json() · auth (JWT) · validación de entrada · manejo de errores · logger"]

        subgraph CapaPresentacion ["1. CAPA DE PRESENTACIÓN<br/><i>Recibe peticiones HTTP, autentica, valida la entrada y responde JSON</i>"]
            direction TB
            subgraph ModUsuarios ["módulo usuarios<br/><code>src/modules/usuarios/</code>"]
                URoutes["usuarios.routes.js"]
                UController["usuarios.controller.js"]
                URoutes --> UController
            end

            subgraph ModSellers ["módulo sellers<br/><code>src/modules/sellers/</code>"]
                SRoutes["sellers.routes.js"]
                SController["sellers.controller.js"]
                SRoutes --> SController
            end

            subgraph ModCatalogo ["módulo catálogo<br/><code>src/modules/catalogo/</code>"]
                CRoutes["catalogo.routes.js"]
                CController["catalogo.controller.js"]
                CRoutes --> CController
            end

            subgraph ModCarrito ["módulo carrito<br/><code>src/modules/carrito/</code>"]
                CarRoutes["carrito.routes.js"]
                CarController["carrito.controller.js"]
                CarRoutes --> CarController
            end

            subgraph ModPedidos ["módulo pedidos<br/><code>src/modules/pedidos/</code>"]
                PRoutes["pedidos.routes.js"]
                PController["pedidos.controller.js"]
                PRoutes --> PController
            end
        end

        subgraph CapaNegocio ["2. CAPA DE LÓGICA DE NEGOCIO<br/><i>Reglas de negocio y coordinación entre módulos</i>"]
            UService["<b>usuarios.service.js</b><br/>registro, login, roles"]
            SService["<b>sellers.service.js</b><br/>alta de tiendas, validación"]
            CService["<b>catalogo.service.js</b><br/>productos, categorías, stock"]
            CarService["<b>carrito.service.js</b><br/>items, totales"]
            PService["<b>pedidos.service.js</b><br/>checkout, estados, pago/envío"]
        end

        subgraph CapaDatos ["3. CAPA DE DATOS<br/><i>Persistencia y consultas a la base de datos</i>"]
            URepository["usuarios.repository.js"]
            SRepository["sellers.repository.js"]
            CRepository["catalogo.repository.js"]
            CarRepository["carrito.repository.js"]
            PRepository["pedidos.repository.js"]

            SharedDb["<b>Acceso a datos compartido</b><br/>Sequelize (ORM) · modelos · pool de conexiones (src/shared/db)"]
        end

        Middlewares --> CapaPresentacion
    end

    %% Petición HTTP desde Cliente Web
    ClienteWeb -- "HTTPS / JSON<br/>/api/v1/*" --> Middlewares

    %% Conexiones Presentación -> Lógica de Negocio
    UController --> UService
    SController --> SService
    CController --> CService
    CarController --> CarService
    PController --> PService

    %% Uso entre módulos (solo a través de su service)
    PService -.-> UService
    PService -.-> SService
    PService -.-> CService
    PService -.-> CarService

    %% Conexiones Lógica de Negocio -> Capa de Datos
    UService --> URepository
    SService --> SRepository
    CService --> CRepository
    CarService --> CarRepository
    PService --> PRepository

    %% Conexiones Repositorios -> Acceso Compartido
    URepository --> SharedDb
    SRepository --> SharedDb
    CRepository --> SharedDb
    CarRepository --> SharedDb
    PRepository --> SharedDb

    %% Conexiones a Sistemas Externos
    Pasarela["«sistema externo»<br/><b>Pasarela de pagos</b><br/>(p. ej. Culqi / Niubiz)"]
    Envios["«sistema externo»<br/><b>Servicio de envíos</b><br/>(API del courier)"]

    PService -- "HTTPS / REST" --> Pasarela
    PService -- "HTTPS / REST" --> Envios

    %% Base de Datos
    Postgres[("<b>PostgreSQL</b><br/>marketplace_db")]
    SharedDb -- "SQL · TCP 5432" --> Postgres