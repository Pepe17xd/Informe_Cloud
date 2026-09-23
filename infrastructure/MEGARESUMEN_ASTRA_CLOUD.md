# MEGARESUMEN: Proyecto Astra Cloud 🚀

Este documento contiene el resumen absoluto de la arquitectura, decisiones de diseño y cumplimiento de rúbrica del proyecto Astra Cloud. Está estructurado estrictamente en dos capas para cada concepto: una explicación intuitiva y una explicación puramente técnica.

---

## 1. Arquitectura en la Nube (AWS y Balanceo de Carga)

> **💡 En simple:** Imagina un restaurante muy popular. Para que los clientes no hagan una fila enorme en una sola puerta, hay un anfitrión inteligente en la entrada que revisa quién llega y reparte a las personas entre varios cajeros que están libres. Si un cajero se enferma o se cae, el anfitrión se da cuenta y deja de mandarle gente. Además, el menú y las fotos del local están pegados afuera en la calle, para que la gente los vea sin ocupar espacio dentro del restaurante.

> **⚙️ En técnico:** La plataforma opera sobre una arquitectura *Cloud-Native* en AWS. El tráfico público ingresa mediante un **API Gateway** que actúa como proxy inverso hacia un **Application Load Balancer (ALB)**. El ALB distribuye la carga entre contenedores dockerizados en instancias **EC2** utilizando *Target Groups*. Se garantizó la resiliencia mediante *Health Checks* estrictos en la ruta `/health`, aislando nodos defectuosos para evitar errores 502 Bad Gateway. Además, los archivos estáticos se entregan mediante **Amazon S3** (Offloading de contenido), y el despliegue del frontend se delega a **AWS Amplify**.

---

## 2. Ecosistema de Microservicios (El Backend)

> **💡 En simple:** En lugar de tener un solo "cerebro gigante" que haga todo el trabajo y que colapse por completo si algo falla, dividimos la plataforma en 6 "trabajadores especialistas". Uno solo revisa contraseñas, otro guarda el catálogo de películas, otro crea los grupos de amigos, otro anota los likes, otro hace los cálculos estadísticos, y el último maneja exclusivamente el chat en vivo para que sea rapidísimo.

> **⚙️ En técnico:** Se implementó un diseño orientado a dominios (DDD) separando el backend en 6 microservicios desacoplados: **Identity Service** para emisión y validación de JWTs; **Catalog Service** (Java Spring Boot) para los metadatos multimedia; **Community Service** (FastAPI) para la administración relacional de perfiles y salas; **Interaction Service** (Node.js) para las reacciones y listas; **Analytics Service** para extraer métricas transversales; y el **Cinema Session Service** (FastAPI), un orquestador que corre netamente en RAM para el control síncrono del reproductor.

---

## 3. Persistencia Políglota (Las Bases de Datos)

> **💡 En simple:** No usamos un solo tipo de archivador para guardar la información. Lo que es estricto, fijo y que no debe perderse ni mezclarse (como el correo de un usuario o el ID de una película) va en una caja fuerte muy organizada de filas y columnas. Pero lo que cambia miles de veces por segundo y no tiene una estructura fija (como los likes, comentarios e historiales) se guarda en una bóveda flexible donde podemos arrojar datos rapidísimo sin que se trabe.

> **⚙️ En técnico:** El proyecto cumple con la rúbrica de persistencia políglota. Para asegurar la integridad referencial (ACID) de las entidades estructurales, se implementaron RDBMS: **PostgreSQL** para el microservicio de Catálogo y **MySQL** para el de Comunidad. Para manejar el alto *throughput* y la variabilidad de esquema de las acciones de los usuarios (likes/reviews), se optó por una base de datos NoSQL documental utilizando **MongoDB** en el servicio de Interacciones.

---

## 4. Comunicación entre Microservicios (Orquestación)

> **💡 En simple:** Los trabajadores de nuestra aplicación no son ciegos ni están aislados; se hablan por teléfono. Por ejemplo, cuando abres una sala de cine, el encargado del chat llama al encargado de la comunidad para preguntarle: *"Oye, ¿esta sala de verdad existe?"*. Luego, llama al de interacciones para decirle: *"Dime cuántos likes tiene la película para yo anunciarlo en el chat a través de un Bot automático"*.

> **⚙️ En técnico:** Se cumple el requerimiento de comunicación inter-servicios mediante llamadas síncronas HTTP/REST. Aprovechando el patrón de inicialización perezosa (*Lazy Initialization*) en el *Cinema Session Service*, cuando se recibe la primera solicitud de polling, el backend de Cinema realiza un `GET` interno al **Community Service** para validar la persistencia real del *Watch Room*. Simultáneamente, ejecuta una petición al **Interaction Service** para agregar los *scores* y likes, inyectando un mensaje del sistema (el Bot) en el payload de respuesta hacia los clientes.

---

## 5. Sincronización en Tiempo Real (Cinema Session)

> **💡 En simple:** Cuando le das pausa a la película, tu pantalla le avisa a nuestro trabajador veloz (Cinema Session). Como este trabajador es tan rápido y no anota nada en discos o libretas permanentes (todo lo guarda en su memoria a corto plazo), cuando el resto de usuarios le preguntan la fracción de segundo después *"¿Cómo va el video?"*, él responde instantáneamente *"¡Está pausado!"*, logrando que todas las pantallas se detengan mágicamente a la vez.

> **⚙️ En técnico:** El *Cinema Session Service* fue diseñado como un componente *Stateless In-Memory*. Al evitar las penalizaciones de latencia que conlleva el I/O (lectura/escritura en disco) contra una base de datos relacional, el estado de reproducción y los arreglos de chat viven temporalmente en RAM. Ante la ausencia de WebSockets nativos, el cliente emplea un mecanismo de **HTTP Short-Polling** cada 1.5 segundos para la ingesta y recuperación concurrente de estado, resolviendo posibles condiciones de carrera (*Race Conditions*) en memoria y simulando un entorno de tiempo real.

---

## 6. Data Analytics (El Panel de Administración)

> **💡 En simple:** Tenemos a un analista muy inteligente que, en lugar de estorbarle a los cajeros pidiéndoles los recibos diarios, saca una copia paralela gigante de todo lo que pasó en la plataforma. Usando esa copia, agrupa y calcula en segundos cuántas salas activas hay, quiénes son los actores favoritos o cuáles son los clubes con más fans, todo para poder pintarte unas hermosas gráficas en tu pantalla de administrador sin interrumpir el funcionamiento de la app.

> **⚙️ En técnico:** Se configuró una infraestructura de analítica *Serverless* en AWS. Utilizando **AWS Glue Data Catalog**, se unifican lógicamente los esquemas dispersos de las diversas bases de datos operativas. El *Analytics Service* consume **Amazon Athena** mediante SDKs (`boto3`), lanzando consultas SQL distribuidas. Esto aplica el principio de **Offloading analítico**, permitiendo generar reportes pesados de KPIs sin degradar la capacidad transaccional ni el rendimiento de las bases de datos de producción (MySQL/Postgres).

---

## 7. Capa de Frontend (Aplicación Cliente)

> **💡 En simple:** El frontend es la cara visible de la aplicación. En lugar de que nuestros servidores dibujen cada botón y página para cada usuario (lo que los cansaría muchísimo), le mandamos la "aplicación vacía" a tu navegador una sola vez. Tu computadora hace el trabajo de pintar las pantallas y solo le pide a nuestros servidores los datos puros. Además, jala los pósters de las películas directamente del almacén principal (S3) para ahorrarle trabajo extra a los cajeros.

> **⚙️ En técnico:** Se implementó una *SPA (Single Page Application)* robusta usando **React.js**. En lugar de emplear SSR (*Server-Side Rendering*), el frontend se desacopló completamente de los microservicios backend y fue alojado globalmente en **AWS Amplify**. La aplicación gestiona la sesión de forma cliente-servidor mediante el almacenamiento seguro del JWT, decodificando localmente el *payload* para renderizado condicional de rutas. Consume recursos estáticos directamente desde Amazon S3 y orquesta el consumo de las APIs expuestas por el API Gateway, manejando los estados de carga y error mediante componentes dinámicos.
