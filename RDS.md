# RDS

- Base de datos relacional
- Completamente manejado por AWS, no hay acceso al sistema operativo subyacente
- 6 RDBMS disponibles:
	- MySQL
	- SQL Server
	- PostgreSQL
	- Oracle
	- Aurora
	- MariaDB
- Ideal para OLTP
- AWS no entrega IPs para las instancias, si no endpoints DNS
- Si no se activa "Publicly accessible", instancia será puesta en una subnet privada
- Es necesario poner una regla en el Security Group de la instancia que permita acceso al puerto 3306 (o el que se haya usado) desde la IP de EC2 (o su Security Group)
- Para poner en un VPC una instancia RDS accedible públicamente, es necesario activar DNS Resolution y DNS Hostnames en el VPC
- Parámetros de configuración iniciales son elegidos automáticamente por AWS de acuerdo al tamaño de la instancia y su cantidad de almacenamiento
- Para cambiar parámetros de configuración, se debe crear un nuevo Parameter Group, y asignarlo a la base de datos
	- Valores pueden ser constantes o fórmulas
	- Si los cambios son en parámetros dinámicos, estos se aplican inmediatamente, sin importar la opción "Aplicar inmediatamente"
	- Si los cambios son en parámetros estáticos, se aplicarán cuando se reinicie manualmente la instancia
- Al modificar una instancia, ya sea en clase o almacenamiento, estos cambios serán aplicados en la ventana de mantención especificada, a menos que se use la opción de aplicar inmediatamente
- Se puede aumentar el almacenamiento de una instancia sin downtime
- Tipos de almacenamiento:
	- General Purpose (SSD): adecuado para usos genéricos con uso moderado de I/O, base de 3 IOPS/GB elevable a 3000 IOPS
	- Provisioned IOPS (SSD): entrega performance I/O rápida, predecible y consistente, recomendado para OLTP
		- Rangos por motor:
			- MariaDB: 1000-40000 IOPS, 100 GB - 32 TB
			- SQL Server E&S: 1000-32000 IOPS, 20 GB - 16 TB
			- SQL Server W&E: 1000-32000 IOPS, 100 GB - 16 TB
			- MySQL: 1000-40000 IOPS, 100 GB - 32 TB
			- Oracle: 1000-40000 IOPS, 100 GB - 32 TB
			- PostgreSQL: 1000-40000 IOPS, 100 GB - 32 TB
	- Magnetic Storage: adecuado para bases de datos pequeñas, con data accedida con poca frecuencia, no recomendado para producción
- No se puede modificar el tipo de instancia sin downtime
- Por defecto, se pueden correr hasta 40 instancias RDS (de las cuales hasta 10 pueden ser Oracle o SQL Server)

## Backups

- Al restaurar un respaldo, la versión restaurada de la base de datos será una nueva instancia RDS con un nuevo endpoint DNS

### Automated Backups

- Permiten recuperar una base de datos a cualquier momento en un período de retención
- Período de retención puede ser entre 1 y 35 días (por defecto,  7 días)
- Se toma un respaldo completo diario, y además se almacenan logs de transacciones durante el día, lo que permite restaurar una base de datos a un segundo exacto (típicamente hasta los últimos 5 minutos, ver `Latest Restorable Time`)
- Encendidos por defecto
- Respaldos son almacenados en S3, y AWS entrega almacenamiento gratis igual al tamaño de la base de datos
- Respaldos se realizan durante una ventana de tiempo predefinida
- Durante el respaldo, I/O puede ser suspendido y se puede experimentar más latencia de lo normal (puede ser evitado con Multi-AZ)
- Son eliminados al eliminar la instancia RDS

### Snapshots

- Son realizados manualmente
- Son almacenados incluso después de eliminar la instancia RDS

## Encryption

- Soportado at-rest para MySQL, Oracle, SQL Server, PostgreSQL, MariaDB y Aurora
- Soporte para SSL
- Encriptación realizada usando KMS
- Una vez que se encripta una instancia, la data en almacenamiento es encriptada, y también los respaldos, réplicas de lectura y snapshots
- No se puede encriptar una instancia RDS que ya existe, es necesario crear un snapshot, copiarlo y encriptar esa copia

## Multi-AZ

- Réplicas síncronas en instancias en distintas AZs
- Para Disaster Recovery, no performance
- Tienen failover automático, se envía el tráfico a una instancia saludable sin tener que cambiar el endpoint
- Automatic Backups son realizados en copia Multi-AZ, lo que evita suspensión de I/O
- Cuando hay mantención planificada, falla en la instancia, o falla en la AZ, también hay failover automático
- Puede causar latencia elevada debido a la repliación síncrona, comparado con una instancia sin Multi-AZ
- No se puede acceder a instancias standy, ni para lectura ni escritura
- Failover toman por lo general entre 1 y 2 minutos
- AWS notifica en caso de failover
- Disponible para SQL Server, Orcale, MySQL, PostgreSQL y MariaDB

## Read Replicas

- Réplicas asíncronas
- Copias de solo lectura
- Máximo de 5 réplicas por defecto
- Automatic Backups deben estar prendidos
- Cada réplica tiene su propio endpoint DNS
- Ideal para mejorar la performance en aplicaciones principalmente de lectura, no para Disaster Recovery
- Se puede tener réplicas de réplicas, lo que aumenta la latencia debido al *daisy-chaining*
- Réplicas pueden tener Multi-AZ
- Se pueden crear Read Replicas de instancias con Multi-AZ
- Réplicas pueden ser promovidas a sus propias bases de datos, lo que rompe la replicación
- Se pueden tener réplicas en distintas regiones
- Tráfico entre fuente y réplicas está encriptado
- Réplica se cobra igual que la instancia principal
- No hay costo por replicar data de la instancia primaria a una réplica
- Réplica puede ser encriptada incluso si la fuente no lo está
- Disponible para MySQL, PostgreSQL, MariaDB y Aurora

## Billing

- Se cobra por:
	- Horas de instancia, basado en la clase (horas parciales son cobradas como horas completas)
	- Almacenamiento (GB/mes)
	- I/O mensual
	- Privisioned IOPS mensual
	- Almacenamiento de respaldos
	- Data Transfer, entrante y saliente
- Se cobra por cada hora que la base de datos corre en estado `available`
- Cuando una instancia se detiene, se cobra por almacenamiento y respaldos, no por horas de instancia
- Con Multi-AZ, se cobra:
	- Horas de instancia de Multi-AZ
	- I/O mensual es mayor en Multi-AZ, por lo general el I/O de escritura se dobla, mientras que el de lectura se mantiene igual
	- No hay cambio en cobro por respaldos
	- No hay cobro por transferencia de data entre la instancia primaria y las standby