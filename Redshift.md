# Redshift

- Servicio de data warehousing
- En la escala de petabytes
- Ideal para OLAP
- 10 veces más rapido que RDS
- Usa bloques de 1 MB para almacenamiento de columnas
- Completamente manejado por AWS
- Se puede cargar data desde S3, DynamoDB, EMR, Glue, Data Pipeline o cualquier servidor con SSH habilitado
- Se parte con un solo nodo de 160 GB
- Se puede escalar a multi nodos
	- Leader Node: maneja conexiones de clientes y recibe queries
	- Compute Node: almacena data y realiza queries y cómputos. Hasta 128 nodos
- Nodos no pueden ser accedidos directamente
- Columnar Data Storage: se almacena la data en organizada por columnas en vez de filas. Esto la hace ideal para queries analíticas sobre grandes sets de datos. Las columnas son almacenadas secuencialmente, lo que disminuye el I/O necesario, mejorando sustancialmente la query performance
- La data almacenada en columnas puede ser comprimida con mucha efectividad debido a que por lo general data el columnas es similar
- No se requieren índices o vistas materializadas, utilizando menos espacio
- Automáticamente se selecciona el mejor método de compresión basado en la data de cada columna
- Massively Parallel Processing: se distribuye automáticamente la data y la carga de queries a lo largo de todos los nodos
- Tráfico se encripta con SSL
- Encripción at-rest usando AES-256
- Por defecto, el manejo de llaves es automático, pero se puede usar KMS o HSM para manejar las llaves
- Disponible solo en una AZ, pero se pueden restaurar snapshots a distintas AZ en caso de un corte de disponibilidad
- Data es respaldada en S3 continuamente. Siempre se intenta tener 3 copias de la data (original, respaldo en S3 y replica en el nodo de cómputo)
- Por defecto, respaldos se guardan por 1 día, pero pueden ser hasta 35

## Billing

- Compute Node Hours: total número de horas a lo largo de todos los nodos de cómputo. 1 unidad por nodo por hora
- Leader nodes no tienen costo
- Costos por respaldos
- Costo por transferencia de datos dentro de un VPC, no fuera