# Aurora

- Base de datos relacional
- Compatible con MySQL
- Velocidad y disponibilidad de bases de datos comerciales, con la simpleza y efectividad de costo de bases de datos de código abierto
- 5 veces mejor performance que MySQL
- Un décimo del costo de bases de datos comerciales, con performance y disponibilidad similar
- Empieza con almacenamiento de 10 GB, y escala automáticamente en bloques de 10 GB hasta 64 TB
- Puede escalar hasta 32 vCPUs y 244 GB de RAM, lo cual se hace normalmente durante una ventana de mantención, ya que toma un par de minutos
- Mantiene 2 copias de la data en cada AZ, con un mínimo de 3 AZs (6 copias)
- Maneja transparentemente la pérdida de hasta 2 copias de data sin impacto en la disponibilidad de escritra, y hasta 3 copias sin impaco en la disponibilidad de lectura
- Bloques de data y discos son continuamente escaneados por errores y reparados automáticamente
- Se puede tener hasta 15 réplicas, con failover automático, que siguen la lógica Tier 0 > Tier 1 > ... > Tier 15
- Al usar el cluster endpoint, si hay una falla en la instancia, AWS automáticamente envía el tráfico a la siguiente instancia en la cadena, por lo que no es necesario cambiar el endpoint usado en la aplicación
- Respaldos se mantienen 1 día por defecto, con posibilidad de aumentar a 35 días