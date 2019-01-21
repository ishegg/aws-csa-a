# Kinesis

- Stream es data que es continuamente generada por miles de fuentes, las cuales típicamente envían esta data simultáneamente en pequeñas cantidades (orden de KBs)
- Plataforma que recibe streams
- Retención de data hasta por 7 días (por defecto 24 horas)
- Producers: dispositivos que producen la data para los streams (celulares, computadores, etc)
- Consumers: servicios que consumen la data (EC2)
- Data puede ser enviada a DynamoDB, S3, Elastic Map Reduce, Redshift, etc

## Kinesis Streams

- Streams consisten de shards
- 5 transacciones por segundo para lecturas, hasta un máximo total de data de 2 MB/s
- Hasta 1000 transacciones por segundo  para escrituras, con un máximo total de data de 1 MB/s (incluyendo llaves de partición)
- La capacidad de data del stream es una función del número de shards que se especifican
- La capacidad total del stream es la suma de las capacidades de los shards

## Kinesis Firehose

- Maneja automáticamente los streams, no hay necesidad de especificar shards
- No hay período de retención, cuando llega la data es analizada por funciones Lambda o enviada a almacenamiento inmediatamente

## Kinesis Analytics

- Permite correr queries SQL para analizar la data
- Permite almacenar los resultados en S3, Redshift o Elasticsearch