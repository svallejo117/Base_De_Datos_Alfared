# Diccionario de Datos de la Base de Datos Alfared

Este documento describe la estructura de la base de datos `Alfared`, incluyendo detalles sobre cada tabla, sus columnas, tipos de datos, restricciones y relaciones.

## 1. Tablas de la Base de Datos

### 1.1. Tabla `Roles`

La tabla `Roles` almacena los diferentes roles que pueden ser asignados a los usuarios del sistema. Cada rol tiene un identificador único y un nombre.

| Columna      | Tipo de Dato | Restricciones      | Descripción                               |
| :----------- | :----------- | :----------------- | :---------------------------------------- |
| `id_rol`     | `SERIAL`     | `PRIMARY KEY`      | Identificador único del rol.              |
| `nombre` |     `VARCHAR(50)`| `UNIQUE`, `NOT NULL` | Nombre descriptivo unico del rol (ej. Administrador, Contador). |




### 1.2. Tabla `Permisos`

La tabla `Permisos` almacena los distintos permisos que pueden ser asignados a los roles en el sistema.

| Columna          | Tipo de Dato  | Restricciones      | Descripción                               |
| :--------------- | :------------ | :----------------- | :---------------------------------------- |
| `id_permiso`     | `SERIAL`      | `PRIMARY KEY`      | Identificador único del permiso.          |
| `nombre`         | `VARCHAR(100)`| `UNIQUE`, `NOT NULL` | Nombre descriptivo del permiso (ej. gestion_usuarios, ver_reportes). |




### 1.3. Tabla `Roles_Permisos`

La tabla `Roles_Permisos` es una tabla de unión que establece la relación muchos a muchos entre los roles y los permisos, indicando qué permisos tiene cada rol.

| Columna      | Tipo de Dato | Restricciones                               | Descripción                               |
| :----------- | :----------- | :------------------------------------------ | :---------------------------------------- |
| `rol_id`     | `INT`        | `PRIMARY KEY`, `FOREIGN KEY (Roles)`        | Identificador del rol.                    |
| `permiso_id` | `INT`        | `PRIMARY KEY`, `FOREIGN KEY (Permisos)`     | Identificador del permiso.                |




### 1.4. Tabla `Usuarios`

La tabla `Usuarios` almacena la información de todos los usuarios del sistema, incluyendo administradores, contadores, técnicos, analistas, jefes y clientes. Se utiliza un enfoque de tabla única para manejar la herencia de roles.

| Columna         | Tipo de Dato  | Restricciones                               | Descripción                               |
| :-------------- | :------------ | :------------------------------------------ | :---------------------------------------- |
| `id_usuario`    | `SERIAL`      | `PRIMARY KEY`                               | Identificador único del usuario.          |
| `nombre`        | `VARCHAR(100)`| `NOT NULL`                                  | Nombre del usuario.                       |
| `apellido`      | `VARCHAR(100)`| `NOT NULL`                                  | Apellido del usuario.                     |
| `email`         | `VARCHAR(255)`| `UNIQUE`, `NOT NULL`                        | Correo electrónico del usuario, usado como identificador único. |
| `contrasena`    | `VARCHAR(255)`| `NOT NULL`                                  | Contraseña del usuario (se recomienda almacenar hashes). |
| `rol_id`        | `INT`         | `NOT NULL`, `FOREIGN KEY (Roles)`           | Clave foránea al rol asignado al usuario. |
| `activo`        | `BOOLEAN`     | `DEFAULT TRUE`                              | Indica si el usuario está activo (especialmente para `ClienteUsuario`). |
| `fecha_registro`| `TIMESTAMP`   | `DEFAULT TRUE`                              | Fecha de creacion del usuario |
| `ultimo_acceso` | `TIMESTAMP`   | `DEFAULT TRUE`                              | Ultimo acceso del usuario  |




### 1.5. Tabla `Documentos`

La tabla `Documentos` almacena información sobre los documentos gestionados en el sistema, como facturas, contratos, informes, etc.

| Columna            | Tipo de Dato  | Restricciones                               | Descripción                               |
| :----------------- | :------------ | :------------------------------------------ | :---------------------------------------- |
| `id_documento`     | `SERIAL`      | `PRIMARY KEY`                               | Identificador único del documento.        |
| `tipo_documento`   | `VARCHAR(50)` | `NOT NULL`                                  | Tipo de documento (ej. Factura, Contrato). |
| `contenido`        | `TEXT`        |                                             | Contenido del documento.                  |
| `fecha_creacion`   | `DATE`        | `NOT NULL`, `DEFAULT CURRENT_DATE`          | Fecha de creación del documento.          |
| `contador_id`      | `INT`         | `FOREIGN KEY (Usuarios)`                    | Clave foránea al usuario `Contador` que maneja el documento. |




### 1.6. Tabla `Transacciones`

La tabla `Transacciones` registra las operaciones financieras o de servicio realizadas en el sistema.

| Columna              | Tipo de Dato  | Restricciones                               | Descripción                               |
| :------------------- | :------------ | :------------------------------------------ | :---------------------------------------- |
| `id_transaccion`     | `SERIAL`      | `PRIMARY KEY`                               | Identificador único de la transacción.    |
| `fecha_transaccion`  | `DATE`        | `NOT NULL`, `DEFAULT CURRENT_DATE`          | Fecha en que se realizó la transacción.   |
| `monto`              | `NUMERIC(10,2)`| `NOT NULL`                                 | Monto de la transacción.                  |
| `tipo_transaccion`   | `VARCHAR(50)` | `NOT NULL`                                  | Tipo de transacción (ej. Venta, Mantenimiento). |
| `estado_transaccion` | `VARCHAR(50)` | `NOT NULL`                                  | Estado actual de la transacción (ej. Completada, Pendiente). |
| `contador_id`        | `INT`         | `FOREIGN KEY (Usuarios)`                    | Clave foránea al usuario `Contador` que registró la transacción. |




### 1.7. Tabla `PQRS`

La tabla `PQRS` (Peticiones, Quejas, Reclamos, Sugerencias) almacena las interacciones de los clientes relacionadas con el servicio.

| Columna                    | Tipo de Dato  | Restricciones                               | Descripción                               |
| :------------------------- | :------------ | :------------------------------------------ | :---------------------------------------- |
| `id_pqrs`                  | `SERIAL`      | `PRIMARY KEY`                               | Identificador único de la PQRS.           |
| `tipo_pqrs`                | `VARCHAR(50)` | `NOT NULL`                                  | Tipo de PQRS (ej. Peticion, Queja, Reclamo, Sugerencia). |
| `descripcion`              | `TEXT`        | `NOT NULL`                                  | Descripción detallada de la PQRS.         |
| `fecha_creacion`           | `DATE`        | `NOT NULL`, `DEFAULT CURRENT_DATE`          | Fecha de creación de la PQRS.             |
| `estado_pqrs`              | `VARCHAR(50)` | `NOT NULL`                                  | Estado actual de la PQRS (ej. Pendiente, En Proceso, Resuelta, Cerrada). |
| `solicitante_id`           | `INT`         | `NOT NULL`, `FOREIGN KEY (Usuarios)`        | Clave foránea al `ClienteUsuario` que originó la PQRS. |
| `analista_id`              | `INT`         | `FOREIGN KEY (Usuarios)`                    | Clave foránea al `Analista` asignado a la PQRS. |
| `jefe_id`                  | `INT`         | `FOREIGN KEY (Usuarios)`                    | Clave foránea al `Jefe` que supervisa la PQRS. |




### 1.8. Tabla `Satisfacciones`

La tabla `Satisfacciones` registra el nivel de satisfacción de los clientes con los servicios o la resolución de sus PQRS.

| Columna                  | Tipo de Dato | Restricciones                               | Descripción                               |
| :----------------------- | :----------- | :------------------------------------------ | :---------------------------------------- |
| `id_satisfaccion`        | `SERIAL`     | `PRIMARY KEY`                               | Identificador único de la satisfacción.   |
| `nivel_satisfaccion`     | `INT`        | `NOT NULL`, `CHECK (1-5)`                   | Nivel de satisfacción del cliente (1 al 5). |
| `comentario`             | `TEXT`       |                                             | Comentario adicional del cliente.         |
| `fecha_registro    `     | `DATE`       | `NOT NULL`, `DEFAULT CURRENT_DATE`          | Fecha en que se registró la satisfacción. |
| `usuario_id`             | `INT`        | `NOT NULL`, `FOREIGN KEY (Usuarios)`        | Clave foránea al `ClienteUsuario` que expresó la satisfacción. |




### 1.9. Tabla `Facturas`

La tabla `Facturas` almacena la información de las facturas generadas para los clientes.

| Columna                   | Tipo de Dato  | Restricciones                               | Descripción                               |
| :------------------------ | :------------ | :------------------------------------------ | :---------------------------------------- |
| `id_factura`              | `SERIAL`      | `PRIMARY KEY`                               | Identificador único de la factura.        |
| `fecha_emision`           | `DATE`        | `NOT NULL`, `DEFAULT CURRENT_DATE`          | Fecha de emisión de la factura.           |
| `fecha_vencimiento`       | `DATE`        | `NOT NULL`                                  | Fecha de vencimiento de la factura.       |
| `monto`                   | `NUMERIC(10,2)`| `NOT NULL`                                 | Monto total de la factura.                |
| `estado`                  | `VARCHAR(50)` | `DEFAULT FALSE`                             | Estado de la factura                      |
| `estado_revision`         | `VARCHAR(50)` | `DEFAULT FALSE`                             | Estado de la factura administrativa       |
| `metodo_pago_preferido`   | `VARCHAR(50)` |                                             | Método de pago preferido por el cliente.  |
| `usuario_id`              | `INT`         | `NOT NULL`, `FOREIGN KEY (Usuarios)`        | Clave foránea al `ClienteUsuario` asociado a la factura. |




### 1.10. Tabla `Pagos`

La tabla `Pagos` registra los pagos realizados por los clientes para sus facturas.

| Columna         | Tipo de Dato  | Restricciones                               | Descripción                               |
| :-------------- | :------------ | :------------------------------------------ | :---------------------------------------- |
| `id_pago`       | `SERIAL`      | `PRIMARY KEY`                               | Identificador único del pago.             |
| `monto`         | `NUMERIC(10,2)`| `NOT NULL`                                 | Monto del pago realizado.                 |
| `fecha_pago`    | `DATE`        | `NOT NULL`, `DEFAULT CURRENT_DATE`          | Fecha en que se realizó el pago.          |
| `metodo_pago`   | `VARCHAR(50)` | `NOT NULL`                                  | Método de pago utilizado (ej. Tarjeta de Crédito, PSE). |
| `estado`        | `VARCHAR(50)` | `NOT NULL`                                  | Estado del pago (ej. Completado, Pendiente, Fallido). |
| `factura_id`    | `INT`         | `NOT NULL`, `FOREIGN KEY (Facturas)`        | Clave foránea a la factura asociada al pago. |

### 1.11. Tabla `Notificar Usuario`

La tabla `Notificar Usuario` le da las notificaciones a los usuarios principalmente de los montos de pago realizados.

| Columna               | Tipo de Dato   | Restricciones                                          | Descripción                               |
| :--------------       | :------------  | :------------------------------------------            | :---------------------------------------- |
| `id_notificacion`     | `SERIAL`       | `PRIMARY KEY`                                          | Identificador único de la notificación.             |
| `usuario_id`          | `INT`          | `NOT NULL, FK → Usuarios(id_usuario) ON DELETE CASCADE`| Usuario destinatario.                 |
| `mensaje`             | `TEXT`         | `NOT NULL `                                            | Texto del mensaje.             |
| `tipo_notificacion`   | `NUMERIC(10,2)`| `NOT NULL`                                             | Tipo de notificación (EMAIL, SMS, PUSH, etc.).                 |
| `fecha_envio`         | `SERIAL`       | `DEFAULT CURRENT_TIMESTAMP`                            | Fecha en que se envía.             |
| `enviado`             | `NUMERIC(10,2)`| `DEFAULT FALSE`                                        | Si ya fue enviado o no.                 |

### 1.12. Tabla `Atencion al cliente`

La tabla `Atencion al cliente` le especifica a el usuario los mensajes y notificaciones de atencion al cliente de la empresa.

| Columna               | Tipo de Dato               | Restricciones                                          | Descripción                               |
| :--------------       | :------------              | :------------------------------------------            | :---------------------------------------- |
| `id_atencion`         | `SERIAL`                   | `PRIMARY KEY`                                          | Identificador de la atención.             |
| `cliente_id`          | `INT`                      | `NOT NULL, FK → Usuarios(id_usuario) ON DELETE CASCADE`| Cliente atendido.                 |
| `tipo_atencion`       | `VARCHAR(100)`             | `NOT NULL `                                            | Categoría de atención (Soporte, Comercial, etc.).             |
| `descripcion`         | `TEXT`                     | ``                                                     | Detalles de la atención brindada.                 |
| `fecha_atencion`      | `DEFAULT CURRENT_TIMESTAMP`| `DEFAULT CURRENT_TIMESTAMP`                            | Fecha de la atención.             |

### 1.13. Tabla `Contador_documento`

La tabla `Contador_documento` es la tabla que relaciona el rol de los contadores con los documentos.

| Columna                | Tipo de Dato    | Restricciones                                              | Descripción                               |
| :--------------        | :------------   | :------------------------------------------                | :---------------------------------------- |
| `id_contador documento`| `SERIAL`        | `PRIMARY KEY`                                              | Identificador único de asignación.             |
| `contador_id`          | `INT`           | `NOT NULL, FK → Usuarios(id_usuario) ON DELETE CASCADE`    | Usuario con rol CONTADOR asignado|
| `documento_id`         | `INT`           | `NOT NULL, FK → Documentos(id_documento) ON DELETE CASCADE`| Documento gestionado|
| `fecha_asignacion`     | `TIMESTAMP`     | `DEFAULT CURRENT_TIMESTAMP`                                | Fecha de asignación.|

### 1.12. Tabla `Contador_transaccion`

La tabla `Contador_transaccion` es la tabla que relaciona el rol de los contadores con las transacciones.

| Columna                | Tipo de Dato    | Restricciones                                                   | Descripción                               |
| :--------------        | :------------   | :------------------------------------------                     | :---------------------------------------- |
| `id_contador documento`| `SERIAL`        | `PRIMARY KEY`                                                   | Identificador único de asignación.             |
| `contador_id`          | `INT`           | `NOT NULL, FK → Usuarios(id_usuario) ON DELETE CASCADE`         | Usuario con rol CONTADOR asignado|
| `transaccion_id`       | `INT`           | `NOT NULL, FK → Transacciones(id_transaccion) ON DELETE CASCADE`| Documento gestionado|
| `fecha_asignacion`     | `TIMESTAMP`     | `DEFAULT CURRENT_TIMESTAMP`                                     | Fecha de asignación.|

## 2. Relaciones entre Tablas

Las siguientes son las relaciones clave entre las tablas, establecidas mediante claves foráneas:

*   **`Usuarios.id_rol`** se relaciona con **`Roles.id_rol`**: Un usuario tiene un rol.
*   **`Roles_Permisos.id_rol`** se relaciona con **`Roles.id_rol`**: Define qué roles tienen qué permisos.
*   **`Roles_Permisos.id_permiso`** se relaciona con **`Permisos.id_permiso`**: Define qué permisos tienen qué roles.
*   **`Documentos.id_contador`** se relaciona con **`Usuarios.id_usuario`**: Indica qué contador gestiona un documento.
*   **`Transacciones.id_contador`** se relaciona con **`Usuarios.id_usuario`**: Indica qué contador registra una transacción.
*   **`PQRS.id_usuario_solicitante`** se relaciona con **`Usuarios.id_usuario`**: Identifica al cliente que solicitó la PQRS.
*   **`PQRS.id_analista`** se relaciona con **`Usuarios.id_usuario`**: Identifica al analista asignado a la PQRS.
*   **`PQRS.id_jefe`** se relaciona con **`Usuarios.id_usuario`**: Identifica al jefe que supervisa la PQRS.
*   **`Satisfacciones.id_cliente_usuario`** se relaciona con **`Usuarios.id_usuario`**: Identifica al cliente que expresó la satisfacción.
*   **`Facturas.id_cliente_usuario`** se relaciona con **`Usuarios.id_usuario`**: Identifica al cliente asociado a la factura.
*   **`Pagos.id_factura`** se relaciona con **`Facturas.id_factura`**: Asocia un pago a una factura específica.


