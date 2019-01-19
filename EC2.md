# EC2

## General

- Servicio web con capacidad de computación modificable
- Permite escalar, horizontal y verticalmente, en forma rápida cuando los requerimientos de computación cambian
- Cambia el formato de predecir demanda, comprar equipamiento costoso que puede terminar resultando inútil, por ajustarse a la demana en forma *elástica*, optimizando los costos
- Al utilizar EBS para el dispositivo raíz, la data persiste indefinidamente, independiente de la vida de la instancia
- Al utilizar la *local instance store*, la data solo existe durante la vida de la instancia, especialmente útil para casos de uso en que la data generada no vive en la instancia
- Instancias con local instance storage son almacenadas en S3
- Toma menos de 10 minutos desde el momento de lanzar la instancia hasta que esté operativa
- Una Amazon Machine Image (AMI) es un ambiente "empaquetado" que incluye todo lo necesario para lanzar una instancia
- AWS provee varias AMIs con variados sistemas operativos y características
- Se pueden crear AMIs customizadas a partir de las entregadas por AWS
- Límite de 20 instancias On-Demand a través de la familia de instancias
- Límite de 20 instancias reservadas
- Límite de instancias Spot depende del límite dinámico por región
- Cuentas nuevas pueden partir con límite aún menor
- Generalmente, instancias más "grandes" tienen límites menores
- En caso de necesitar más, se puede solicitar a través de un formulario especial, indicando el caso de uso y el número requerido
- Hay un límite de envío de correos desde instancias, el cual puede ser aumentado a través de un formulario especial
- Soporte para Amazon Linux, Ubuntu, Windows Server, Red Hat, Fedora, Debian, CentOS, Gentoo Linux, Oracle Linux y FreeBSD
- Instancias usan memoria ECC (Error-Correcting Code)
- Cada instancia tiene asociada una IP privada que cambia solo cuando la instancia es terminada y una IP pública que cambia al momento de detenerla/iniciarla/terminarla/asociarla con una IP elástica
- SLA garantiza un uptime mensual de 99.99%

## Billing

- Solo se paga por hora (o en algunas instancias por segundo, con un mínimo de 60 segundos)
- Uso parcial de una hora se redondea hacia arriba
- Transferencia de datos entre regiones se cobra como Internet Data Transfer en ambos lados de la transferencia
- El ciclo de cobro comienza con la secuencia de booteo de la AMI, y termina cuando la instancia se termina
- Instancias detenidas no incurren costo de uso o transferencia de datos, pero si de almacenamiento de volúmenes EBS
- Instancias en distintas AZ tienen costo de transferencia entre regiones
- Instancias en distintas regiones tienen costo de Internet Data Transfer

## Hardware Information

- 5 familias de instancias:
	- General Purpose: ratios de memoria a CPU adecuados para aplicaciones de uso general y pueden ser de performance fija (M4, M5) o *burstable* (T2)
	- Compute Optimized (C4, C5, C5n): proporcionalmente más CPU que memoria, buenas para aplicaciones computacionalmente intensivas
	- Memory Optimized (X1e, X1, R4, R5): buenas para aplicaciones intensivas de memoria, como bases de datos o cachés
	- Accelerating Computing (P2, P3, G3, F1): usan procesamiento paralelo de GPUs NVIDIA Tesla para computación de alta performance y machine learning
	- GPU Graphics (G3): capacidad de gráficas 3D de alta performance para aplicaciones que usen OpenGL y DirectX
	- F1: computación reconfigurable basada en Xilinx FPGA
	- Storage Optimized (H1, I3, D2): alta capacidad de I/O con baja latencia, utilizando almacenamiento SSD, o HDD para I/O secuencial para data warehousing, Hadoop u otras aplicaciones intensivas de datos
- Para elegir instancias, considerar cuál recurso se utilizará más (memoria, CPU, almacenamiento)
- EC2 Compute Unit: utilizadas para asegurar capacidad de CPU sin importar el hardware subyacente. Permiten comparar la capacidad de CPU entre distintos tipos de instancia

## Seguridad

- Con CloudTrail se puede tener un historial de todas las llamadas a la API de EC2, incluyendo VPC y EBS
- Con Security Groups se puede especificar qué puertos pueden ser accedidos en las instancias asociadas y desde qué IPs (entregando rangos CIDR)

## Elastic IP

- IPs públicas de las instancias cambian cuando una se detiene o termina, por lo que es necesario poder asociar una IP elástica, la que no cambia
- Límite de 5 por región, debido a la escasez de direcciones IPv4, que puede ser aumentado aplicando en un formulario especial (si se aumenta es específico para la región solicitada)
- IPs elásticas se cobran cuando no están en uso
- Mapeo de IP elástica toma algunos minutos desde que se da la instrucción hasta que se propaga por los sistemas de AWS
- Se puede solicitar la configuración de un DNS inverso de una IP elástica en un formulario especial

## Availability Zones

- Cada AZ corre en un lugar físico único, con infraestructura independiente
- Equipamiento no es comartido con otras AZ
- AZs tienen nombres distintos para distintas cuentas, para evitar hotspots
- Entre instancias, se incurre costo de tráfico solo si se cumple alguna de las siguientes condiciones (pero solo se cobra una):
	- La otra instancia está en otra AZ, sin importar el tipo de IP
	- Se usa IP elástica o pública, sin importar en que AZ está la otra instancia

## Nitro Hypervisor

- Un hypervisor es software o hardware que crea y corre máquinas virtuales
- Nitro provee asilación de CPU y memoria entre instancias
- Eventualmente todos los tipos de instancia correrán Nitro, pero por ahora hay algunas que aún corren Xen
- API no cambiará entre instancias que usen Nitro e instancias que usen Xen

## Enhanced Networking

- Provee mayor performance de packet-per-second, menor latencia entre instancias, y mínima interferencia de red
- Instancia requiere drivers apropiados para poder habilitarla
- Solo está soportada dentro de una VPC, no en EC2-Classic, debido a los requerimientos avanzados de redes
- No tiene costo extra

## Elastic Block Storage

- Provee volúmenes de almacenamiento persistente en bloques
- Es replicado automáticamente dentro de la AZ
- A diferencia de data almacenada en una local instance store, la data en un EBS persistirá independientemente de la vida de la instancia
- Para lograr que sea independiente, se debe poner la bandera "Delete On Terminate" en falso al poner el EBS como volúmen raíz de una instancia
- Existen en formato SSD y HDD
- HDD están separados en:
	- Throughput Optimized (st1): ideales para data accedida frecuentemente, trabajos con flujo intensivo y gran I/O, y trabajos con ETLs
	- Cold HDD (sc1): costo mas bajo por GB, es ideal para trabajos con menos frecuencia de acceso
- Para elegir el tipo de disco, definir si la aplicación depende principalmente de flujo (HDD) o IOPS (SSD)
- Se pueden adjuntar varios discos EBS a una instancia, pero no un disco EBS a varias instancias (utilizar EFS para eso)
- Snapshots son almacenados en S3
- Snapshots de EBS pueden ser accedidos con la API de EC2, no con la API de S3
- Snapshots pueden ser tomados en un disco en uso, pero no es es recomendable ya que solo se captura lo que ya está en disco y se puede omitir data que aun esté en cache. Por ello, se recomienda desadjuntar el disco, luego tomar el snapshot, y volver a adjuntar el volumen. Para volumenes que son raíz en la instancia, se recomienda detenerla para luego realizar el proceso de snapshot
- Cada snapshot tiene un identificador único, lo que permite restaurar a cualquier snapshot realizado en el pasado
- Cuando otros usan el snapshot de uno, no hay cobros, pero cuando se usan snapshots de otro, se cobra uso normal de EBS
- Otros no pueden modificar data en un snapshot ya que se realiza una copia, no se usa el original
- Snapshots pueden ser encriptados

## Elastic File Storage

- Para acceder desde EC2, se debe montar el sistema de archivos en una instancia basada en Linux utilizando el nombre DNS del EFS
- Utiliza el protocolo NFSv4.1
- EFSs pueden ser montados en un servidor on-premise
- Instancias dentro del mismo VPC pueden acceder a un EFS directamente, e instancias EC2-Classic pueden acceder usando ClassicLink. Servidores on-premise pueden montar el sistema de archivos usando Direct Connect hacia el VPC
- Pueden conectarse desde una a miles instancias al mismo EFS

## CloudWatch

- Resolución de 1 minuto
- Funciona para todos los tipos de instancia y todos los sistemas operativos
- Métricas almacenadas por 2 semanas
- Se puede accedar a métricas más antiguas que 2 semanas usando el comando `mon-get-stats` en la consola y almacenando el resultado en S3 o SimpleDB
- Métricas se almacenan por 2 semanas incluso para instancias detenidas o terminadas

## Reserved Instances

- Al usar una instancia reservada se puede acceder a un gran descuento, dependiendo de si el contrato es por 1 o 3 años
- 2 tipos:
	- Standard: solo se puede usar un tipo de instancia
	- Convertible: se puede cambiar la configuración de la instancia durante el contrato, aún recibiendo el descuento
- Cuando se limita a una AZ específica (zonal RIs), la capacidad exacta se reserva para el uso solicitado
- Cuando se limita a una region (regional RIs), automáticamente se aplica descuento al uso a través de distintas AZs, siendo más fácil obtener mejores descuentos
- Usar zonal RIs para aprovechar reserva de capacidad; si no, usar regional RIs
- Se puede cambiar una zonal RI para que sera regional con el comando `ModifyReservedInstances` en la API o desde la Management Console
- Se puede convertir una instancia normal que ya esté corriendo a Reserved Instance
- Se puede modificar la AZ, si es zonal/regional, si es EC2-VPC o EC2-Classic y el tamaño de una Reserved Instance, y tipo de instancia si es Convertible
- 3 tipos de pago:
	- All Upfront: se paga todo el contrato al principio (mayor descuento)
	- Partial Upfront: se paga un pie, y el resto a un precio por hora descontado
	- No Upfront: sin pago inicial, se paga a un precio por hora descontado
- La RI y sus descuentos se activan apenas se apruebe el pago
- RIs no aplican para instancias Spot o corriendo en un Dedicated Host
- Con Consolidated Billing, AWS automáticamente optimiza cuáles instancias son cobradas con descuento para asegurar que las cuentas consolidadas paguen lo mínimo posible
- Hay descuentos en compra de RIs al por mayor (determinados al momento de compra)
- RIs no pueden ser movidas a otra región mientras dure el contrato
- No hay que pagar para cambiar parámetros de una Convertible RI
- No hay límite de cambio de parámetros para una Convertible RI
- Se puede vender/comprar una RI en el Reserved Instance Marketplace a/de otros negocios y organizaciones
- Solo se puede vender RIs si se está registrado en el marketplace, ya se pagó y tiene más de 30 días
- Se venden redondeadas al mes más cercano
- Se puede usar una RI mientras esté en el marketplace, hasta el momento que sea vendida, luego de que sea vendida el costo pasará a ser el normal
- Se puede revender
- Se requiere una cuenta bancaria de US para vender
- La venta tiene un costo por servicio de 12% del pie

## On-Demand Capacity Reservations

- Permite crear y administrar capacidad reservada en EC2
- Se debe elegir una AZ y la cantidad de instancias, además de tipo de instancia y tenencia
- A diferencia de una Zonal RI, no requiere de un contrato a 1 o 3 años
- Con Capacity Reservation, se pagan las instancias reservadas estén corriendo o no
- Se pueden modificar y terminar después de haber iniciado la Capacity Reservation


## EC2 Fleet

- Con un solo request a la API, permite provisionar instancias con diferentes tipos y AZs, ya sea como RI, O-D o Spot
- Solo se pagan las instancias subyacentes
- No puede ser multi región
- Flotas pueden tener tags
- Flotas ueden ser modificadas
- Se puede especificar la AMI a utilizar
- Por defecto, EC2 Fleet utilizará la opción On-Demand con el precio más bajo

## Spot Instances

- Son instancias que sobran y pueden ahorrar hasta un 90% del precio On-Demand
- Pueden ser interrumpidas por AWS en cualquier momento con un aviso previo de 2 minutos
- Precio se ajusta automáticamente de acuerdo a oferta y demanda
- Mientras se usa una instancia Spot, se paga el precio de la instancia al momento de iniciarla la primera hora, y si cambia, las siguientes horas se van ajustando al precio nuevo
- Se puede configurar un minimum y maximum Spot price, que automáticamente contrata y termina instancias basadas en el precio
- Por defecto el maximum Spot price es el precio On-Demand
- Cuando se interrumpe, se puede configurar para que la isntacia sea terminada, detenida, o hibernada
- Por defecto, instancias interrumpidas son terminadas
- Instancias hibernadas persisten la data en RAM a disco
- Si no hay suficiente espacio en disco, hibernación fallará y se terminara la instancia
- Ciclos de iniciar/detener e hibernar/continuar son manejados por AWS y no pueden ser modificados a mano
- Si la instancia es terminada o detenida por AWS en la primera hora, no se cobrará por ese uso, pero si la termina uno, se cobrará redondeado al segundo más cercano
- Si se intenta levantar una Spot Fleet que sobrepasa el límite de instancias regional, las instancias individuales que exceden el límite fallarán
- Spot Fleets pueden ser multi AZ, pero no multi región
- Spot instances pueden tener tags, pero no Spot Fleets

## Micro Instances

- Proveen una pequeña y consistente cantidad de CPU
- Recomendadas para aplicaciones con bajo flujo o sitios web con necesidad ocasional de aumentar ciclos de computación
- Tienen posibilidad de hacer burst de CPU de hasta 2 ECUs cuando hay ciclos disponibles
- Revisar en CloudWatch si la métrica de CPU reporta un 100% de utilización, y cambiar tipo de instancia a una más grande de ser así
- Amazon DevPay no está disponible para estas instancias

## Compute Optimized Instances

- Diseñadas para para aplicaciones con alta necesidad de poder computacional, como servidores web de alta performance, modelado científico, analíticas distribuidas y machine learning
- Utilizan el Elastic Network Adapter y tienen Enhanced Networking por defecto
- EN isntancias C5, la memoria reportada por Linux no es la misma que la publicitada, ya que se reserva espacio para virtualizaciones

## Aceclerated Computing Instances

- Diseñadas para aplicaciones con paralelización masiva, como trabajos con miles de hilos

## Cluster Instances

- Combinan recursos de alto poder computacional con gran performance de red
- Un Cluster Placement Group es una entidad lógica que permite crear clusters lanzando instancias como parte del grupo, entregando baja latencia entre estas

## Hibernate

- Al hibernar una instancia, la memoria RAM es persistina a al volúmen EBS raíz
- Al ser reiniciada, la memoria RAM vuelve a ser cargada y la instancia vuelve a su estado anterior
- Debe ser habilitado cuando se lanza la instancia
- Se puede utilizar desde la Management Console o con la llamada a la API `StopInstances` con parámetro adicional `Hibernate`
- Cuando se entra en hibernación, se mantienen los volumenes, la RAM, la IP privada y la IP elástica (si aplica)
- EBS debe ser encriptado al momento de lanzar la instancia
- Memoria RAM es encriptada al momento de moverla al EBS
- No se puede mantener una instancia en hibernación por más de 60 días
- Se puede usar cualquier AMI
- Si no hay suficiente espacio en EBS para guardar los contenidos de la RAM, hibernación fallará y se terminara la instancia