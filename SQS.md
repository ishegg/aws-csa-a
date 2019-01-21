# SQS

- Servicio que da acceso a una cola de mensajes, la cual puede almacenar mensajes mientras se espera que un computador los procese
- Es pull-based, no push-based
- Cualquier component puede obtener mensajes de la cola a través de la API de SQS
- Una única cola puede ser compartida por varios componentes, sin que se necesiten coordinar entre ellos
- Mensajes pueden permanecer en la cola desde 1 minuto a 14 días
- Período de retención por defecto es de 4 días
- Usado para desacoplar componentes de una aplicación
- Mensajes pueden contener hasta 256 KB de texto en cualquier formato
- Garantiza que un mensaje es enviado por lo menos una vez
- La cola actúa como un buffer entre el componente produciendo y guardando la data, y el componente que recibe la data para procesar
- Se puede configurar un grupo de AutoScaling para que provisione instancias basado en el número de mensajes en la cola
- 2 tipos de polling:
	- Short Polling: vuelve inmediatamente, incluso si el la cola está vacía
	- Long Polling: no vuelve con una respuesta hasta que haya un mensaje en la cola, o hasta que se cumpla el timeout
- 2 tipos de cola
	- Standard
	- FIFO

## Standard Queues

- Son el tipo de cola por defecto
- Permite tener un número casi ilimitado de transacciones por segundo
- Ocasionalmente, debido a la arquitectura altamente distribuida, puede pasar que más de una copia de un mensaje sea enviado en desorden
- Generalmente, los mensajes se llegan en el mismo orden con el que se enviaron
- Por lo tanto, el orden no es garantizado

## FIFO Queues

- Complementa la Standard Queue
- Implementa first in, first out
- Se garantiza un solo procesamiento por mensaje
- El orden en que los mensajes son enviados y recibidos está estrictamente preservado
- Mensajes permanecen disponibles hasta que un consumidor lo procese lo elimina
- No existe posibilidad de tener duplicados en la cola
- Soporte para grupos de mensajes, lo que permite múltiples grupos de mensajes ordenados en una sola cola
- Limitada a 300 transacciones por segundo

## Visibility Timeout

- Es la cantidad de tiempo que un mensaje permanece invisible en la cola mientras un consumidor lo toma para procesamiento
- Si el mensaje es procesado antes que termine el tiemop, el mensaje será eliminado de la cola
- Si no es procesado antes que termine el tiempo, el mensaje volverá a ser visible y puede ser procesador por otro consumidor, lo que puede resultar en el mensaje siendo procesado dos veces
- Por defecto, es de 30 segundos
- El máximo es de 12 horas