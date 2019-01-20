# DynamoDB

- Base de datos NoSQL
- Latencia de un solo dígito en cualquier escala
- Soporta modelos de dato de documentos y key-value
- Muy rápido de escalar sin downtime (*push-button scaling*)
- Data almacenada en SSD
- Distribuido a través de 3 data centers separados físicamente (no AZs)
- Estructura:
	- Tablas
	- Ítems (como filas en una tabla normal)
	- Atributos (como columnas en una tabla normal)
- Soporte para operaciones `GET`/`PUT` usando una llave primaria
- La llave primaria es el único atributo requerido
- Solo se puede acceder vía API, no hay acceso al sistema operativo subyacente
- Dos tipos de consistencia:
	- Eventually Consistent Read (default): consistencia a través de todas las copias de la data en menos de un segundo (usualmente). Si se acaba de hacer una operación de escritura, una lectura puede traer data no actualizada. Maximiza el rendimiento de lectura
	- Strong Consistent Read: devuelve la data con todas las operaciones de escritura que fueron exitosas. Puede no estar disponible si hay problemas en la red. Para usarlo, se debe proveer el parámetro `ConsistentRead` al hacer una operación de lectura
- Read Capacity Unit: representa una strongly consistent read por segundo, o dos eventually consistent read por segundo, para ítems de hasta 4 KB
- Write Capacity Unit: representa una escritura por segundo para un ítem de hasta 1 KB. Escrituras transaccionales requieren 2 WCU
- Para un documento, el tamaño combinado de key/value no puede superar los 400 KB
- Puede ser bastante caro para aplicaciones con fuertes requerimientos de escritura, pero muy barato para requerimientos de lectura

## Billing

- Write Throughput: $0.0065/hora por cada 10 unidades
- Read Throughput: $0.0065/hora por cada 50 unidades
- Almacenamiento: $0.25/GB/mes
- Se cobra por las unidades provisionadas aunque no se usen
- Ejemplo: se requiere 1 millón de lecturas y 1 millón de lecturas por día, con 3 GB de data
	- 1000000 / 24 / 60 / 60 = 11.6 escrituras (y lecturas) por segundo
	- Por lo tanto, se requieren 12 WCUs y 12 RCUs
	- Precio WCU: (0.0065/10) * 12 * 24 = $9.1872/día
	- Precio RCU: (0.0065/50) * 12 * 24 = $0.0374/día