# Route53

- Servicio de DNS
- Se puede registrar dominios directamente
- No se pueden mezclar políticas de ruteo para un mismo tipo de registro, excepto con la polítiva Multivalue Answer
- Hay un límite de 50 dominios, pero se puede ampliar contactando a AWS
- Se puede asociar health checks a los registros
- Hosted Zone: es análogo a un archivo de zona de DNS, representa una colección de registros que son administrados en conjunto, pertenecientes a un dominio padre
- Hay un máximo de 500 hosted zones, y 10000 registros por hosted zone, los cuales pueden ser ampliados contactando a AWS
- Se puede tener varias hosted zones para un solo dominio, generalmente usado para tener ambientes de prueba
- Registros soportados:
	- A
	- AAAA
	- CNAME
	- CAA
	- MX
	- NAPTR
	- NS
	- PTR
	- SOA
	- SPF
	- SRV
	- TXT
	- ALIAS
- Se puede activar logging de las consultas, los cuales son guardados en CloudWatch
- Soporta comodines
- Soporta IPv6 vía AAAA y PTR
- Permite tener DNS privado para utilizar dentro de un VPC
- Si todos los health check para un registro están fallando, AWS asume que todos están saludables

## Simple Routing

- Política de ruteo por defecto
- Puede tener mútiples valores (IPs)
- Solo puede haber un registro con esta política por zona
- Si hay más de una IP, se devuelven todos los valores en orden aleatorio
- Si se usa esta política con un registro ALIAS, solo se puede especificar un valor, no varios
- Usado generalmente cuando se tiene un solo recurso para el dominio, por ejemplo, un servidor web

## Weighted Routing

- Se especifican valores con distintos pesos, y se rutea en base a esta probabilidad
- La probabilidad se calcula como el valor divido entre la suma de los valores, es decir, si se tiene 25 y 100, la probabilidad de el primero será `25 / 125 = 0.2`
- Usado generalmente cuando se quiere probar un nuevo sitio y se quiere enviar una pequeña parte del tráfico

## Latency-based Routing

- Rutea basado en el valor que tenga menor latencia para el usuario final
- Se debe crear un set de registros para el recurso (EC2 o ELB) en cada región que participe en el balanceamiento
- Usado generalmente para mejorar tiempo de carga en sitios globales

## Failover Routing

- Usadas cuando se tiene una instalación activa/pasiva
- Por ejemplo, se tiene el sitio principal en la región eu-west-2, y el sitio secundario, para DR, en ap-southeast-1
- Route53 monitorea la salud del sitio principal utilizando un health check
- Se debe crear un health check, el cual puede monitorear un endpoint (IP o dominio), estado de otros health checks, o el estado de una alarma CloudWatch

## Geolocation Routing

- Se rutea el tráfico en función del lugar físico del usuario final
- Se puede especificar continente, país, o estado (en USA)
- Prioridad va a la región geográfica más pequeña
- La ubicación se calcula con mappeo de IPs
- Se recomienda añadir un registro por defecto, para IPs que no pueden ser mapeadas a una zona existente, de lo contrario Route53 devuelve una respuesta "no answer"
- Por ejemplo, se envía todo el tráfico de Europa a una instancia especialmente configurada para clientes europeos

## Multivalue Answer Routing

- Permite tener múltiples registros con diferentes valores para un mismo tipo de registro, opcionalmente asociando un health check a cada uno
- Se incluyen hasta 8 valores respuesta, asegúrandose que los recursos a donde apunta cada uno esté saludable
- Si no se asocia un health check a un registro, se asumirá que el recurso está saludable
- Si un recurso se cae, será retirado de la respuesta
- Si un recurso se cae después de que un cliente haya cacheado la respuesta, puede intentar en alguno de los otros 7 valores

## Geoproximity Routing

- Rutea en base a la ubicación geográfica del usuario y los recursos
- Se puede añadir un *bias* opcional, positivo o negativo, para aumentar o disminuir el tamaño de alguna área geográfica
- Para cada recurso, se puede especificar su región (si está en AWS) o su latitud y langitud (si no está en AWS)
- Debe utilizarse [Traffic Flow](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/traffic-flow.html)

## ALIAS Records
- Funciona como CNAME, pero puede ser apuntado directamente al apex, como un registro A
- Usado para dirigir recursos de AWS (como ALB, S3, etc) al apex del dominio
- No tiene costo si es apuntado a:
	-  ELB
	-  Distribución CloudFront
	-  Elastic Beanstalk
	-  S3 buckets configurados como sitios web estáticos
-  Ahorran tiempo porque Route53 automáticamente reconoce un cambio de IP en el recurso apuntado, no es necesario esperar al TTL