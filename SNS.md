# SNS

- Permite levantar, operar y enviar notificaciones
- Capacidad de enviar notificaciones push a Apple, Google, FireOS, Windows
- Puede enviar notificaciones vía SMS, correo, a una cola SQS o a cualquier endpoint HTTP
- Puede gatillar una función Lambda, la cual recibe el payload del mensaje como parámetro de entrada
- Permite agrupar múltiples recipientes usando topics
- Topic: un punto de acceso donde recipientes pueden suscribirse dinámicamente para copias idénticas de la misma notificación
- Un topic puede enviar notificaciones a múltiples tipos de endpoint
- Todos los mensajes son almacenados con redundancia a través de múltiples AZs
- No requiere pulling, es basado en push e instantáneo
- Es utilizado por AutoScaling y CloudWatch para notificar eventos

## Billing

- $0.50 por cada millón de requests
- $0.06 por cada 100000 notificaciones sobre HTTP
- $0.75 por cada 100 notificacinoes sobre SMS
- $2.00 por cada 100000 notificaciones sobre email