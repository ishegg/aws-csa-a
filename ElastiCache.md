# ElastiCache

- Servicio web que permite desplegar un cache in-memory en la nube
- Utilizado para mejorar significativamente la latencia y el rendimiento de aplicaciones con altas necesidades de lecturas
- Está diseñado para ser accedido dentro del VPC
- Se puede usar una instancia NAT para acceder desde fuera del VPC, la cual debe estar en el mismo VPC, estar en una subnet pública, y tener una IP elástica asociada

## Memcached

- Sistema de cacheo en memoria
- No tiene capacidad de Multi-AZ

## Redis

- Key-value en memoria
- Capacidad de Multi-AZ
- Soporte para replicación master/slave