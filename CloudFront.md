# CloudFront

## General

- Es un Content Delivery Network
- Edge Location: ubicación donde el contenido sera guardado (no es lo mismo que una AZ)
- Edge Locations no son read-only, también se puede escribir en ellas
- Origin: puede ser un S3 bucket, una EC2 instance, un ELB o Route53
- Distribution: nombre dado al CDN, consistente de varios Edge Locations
	- Web Distribution: usado para sitios web
	- RTMP: Usado para streaming de media
- Cuando hay un request, primero va a la Edge Location más cercana al usuario. Si el objeto no existe, se busca en S3, se obtiene, se guarda en la Edge Location y se devuelve al usuario
- Puede ser usado con servidores que no estén en AWS
- Objetos son guardados durante la vida del TTL
- Se puede eliminar objetos del caché, pero tiene un costo
- Pueden haber múltilpes orígenes para una distribución
- Una vez activada una distribución, se puede restringir acceso directo al bucket de origen
- Se puede personalizar el TTL mínimo, máximo y por defecto, y el caché de objetos
- Se puede restringir el acceso de visitas con URLs o cookies firmadas
- Se pueden especificar `CNAME`s alternativos
- Se puede usar certificado SSL propio o el de CloudFront
- Se puede especificar un objeto por defecto al entrar a la raíz de la URL
- Se puede crear páginas de error para mostrar a cliente
- Se pueden habilitar restricciones geográficas