# SWF

- Servicio que permite coordinar trabajo a través de componentes distribuidos
- Útil para procesamiento de media, aplicaciones back-end, procesamiento de flujos de negocio y pipelines de analítica
- Tasks: invocaciones de varios pasos de procesamiento en una aplicación, que pueden ser realizados por código, servicios, humanos o scripts
- Starter: aplicación que puede iniciar un workflow
- Worker: programa que interactúa con SWF para obtener tasks, procesarlas y devolver los resultados
- Decider: pograma que controla la coordinación de tasks acorde a la lógica de la aplicación
- Workes y Deciders pueden ser servicios de AWS, o máquinas detrás de firewalls
- Maneja las interacciones entre los workers y deciders, permitiendo al decider obtener visiones consistentes sobre el progreso de las tasks, e iniciar nuevas tasks
- Almacena tasks, las asigna a workers cuando están listos y monitorea el progreso
- Se asegura de que una task sea asignada una sola vez y que nunca sea duplicada
- Al manejar el estado de la aplicación, workers y deciders no deben llevar estar al tanto de este
- A diferencia de SQS que es orientado a mensajes, SWF es orientado a tareas

## Domains

- El workflow, los tipos de actividad y la execución misma del workflow se agrupan bajo Domains, los cuales pueden ser registrados en la Management Console, o vía la API de SWF con `RegisterDomain`
- Parámetros:
	- Name
	- Description
	- Workflow execution retention period in days
- Parámetros son especificados en JSON
- El workflow máximo puede ser de 1 año y su valor se mide en segundos