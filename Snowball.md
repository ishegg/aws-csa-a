# Snowball

- Antes se llamaba Import/Export Disk, el cual se usaba para acelerar el movimiento de gran cantidad de datos, el cual se dejó de usar porque usuarios enviaban distintos tipos de discos
- Importa y exporta a S3
- Si la data que se quiere exportar está en Glacier, debe ser movida a Standard
- Se tiene hasta 90 días para finalizar una carga con Snowball
- No se puede usar para mover data de una region a otra
- Utiliza encriptación de 256-bits, con llaves manejadas por KMS
- Puede ser utilizado para enviar data sobre salud personal (es HIPAA-compliant)

## Snowball

- Dispositivo físico que AWS envía
- 80 TB de espacio en todas las regiones
- Utiliza múltiples capas de seguridad, tanto físicas como virtuales, para proteger la data
- Una vez que se verifica que la transferencia ha terminado con éxito, AWS realiza un borrado de software

## Snowball Edge

- 100 TB de espacio
- Además de almacenamiento, tiene capacidades de computación
- Es como un pequeño VPC
- Se pueden correr funciones Lambda
- Por ejemplo, se puede poner un Snowball Edge en un avión para calcular y almacenar data del vuelo, que luego es enviada a un S3 bucket

## Snowmobile

- Es un camión con un container
- Permite almacenar en la escala de exabytes
- Se pueden contratar 10 containers de 100 PB cada uno