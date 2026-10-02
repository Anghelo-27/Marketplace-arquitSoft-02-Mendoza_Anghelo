flowchart TD

    %% Actores
    Cliente["Cliente"]
    Seller["Seller"]
    Admin["Administrador"]

    ClienteWeb["Cliente Web<br>(Navegador - HTML / CSS / JavaScript)"]

    Cliente --> ClienteWeb
    Seller --> ClienteWeb
    Admin --> ClienteWeb

    subgraph Backend ["monolito Marketplace Backend [Node.js 20 LTS - Express]<br>Una sola aplicación - un solo proceso - un solo despliegue"]
        direction TB

        Middlewares["Middlewares Express (transversales)<br>cors - express.json() - auth (JWT) - validación de entrada - manejo de errores - logger"]

        subgraph CapaPresentacion ["1. CAPA DE PRESENTACIÓN<br>Recibe peticiones HTTP, autentica, valida la entrada y responde JSON"]
            direction TB

            subgraph ModUsuarios ["módulo usuarios (src/modules/usuarios/)"]
                URoutes["usuarios.routes.js"]
                UController["usuarios.controller.js"]
                URoutes --> UController
            end

            subgraph ModSellers ["módulo sellers (src/modules/sellers/)"]
                SRoutes["sellers.routes.js"]
                SController["sellers.controller.js"]
                SRoutes --> SController
            end

            subgraph ModCatalogo ["módulo catálogo (src/modules/catalogo/)"]
                CRoutes["catalogo.routes.js"]
                CController["catalogo.controller.js"]
                CRoutes --> CController
            end

            subgraph ModCarrito ["módulo carrito (src/modules/carrito/)"]
                CarRoutes["carrito.routes.js"]
                CarController["carrito.controller.js"]
                CarRoutes --> CarController
            end

            subgraph ModPedidos ["módulo pedidos (src/modules/pedidos/)"]
                PRoutes["pedidos.routes.js"]
                PController["pedidos.controller.js"]
                PRoutes --> PController
            end
        end

        subgraph CapaNegocio ["2. CAPA DE LÓGICA DE NEGOCIO<br>Reglas de negocio y coordinación entre módulos"]
            UService["usuarios.service.js<br>registro, login, roles"]
            SService["sellers.service.js<br>alta de tiendas, validación"]
            CService["catalogo.service.js<br>productos, categorías, stock"]
            CarService["carrito.service.js<br>items, totales"]
            PService["pedidos.service.js<br>checkout, estados, pago/envío"]
        end

        subgraph CapaDatos ["3. CAPA DE DATOS<br>Persistencia y consultas a la base de datos"]
            URepository["usuarios.repository.js"]
            SRepository["sellers.repository.js"]
            CRepository["catalogo.repository.js"]
            CarRepository["carrito.repository.js"]
            PRepository["pedidos.repository.js"]

            SharedDb["Acceso a datos compartido<br>Sequelize (ORM) - modelos - pool de conexiones (src/shared/db)"]
        end

        Middlewares --> CapaPresentacion
    end

    %% Petición HTTP desde Cliente Web
    ClienteWeb -- "HTTPS / JSON /api/v1/*" --> Middlewares

    %% Conexiones Presentación -> Lógica de Negocio
    UController --> UService
    SController --> SService
    CController --> CService
    CarController --> CarService
    PController --> PService

    %% Uso entre módulos
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
    Pasarela["sistema externo<br>Pasarela de pagos<br>(p. ej. Culqi / Niubiz)"]
    Envios["sistema externo<br>Servicio de envíos<br>(API del courier)"]

    PService -- "HTTPS / REST" --> Pasarela
    PService -- "HTTPS / REST" --> Envios

    %% Base de Datos
    Postgres[("PostgreSQL<br>marketplace_db")]
    SharedDb -- "SQL - TCP 5432" --> Postgres