# RabbitMQ Server

Servidor de mensajeria que actua como broker central para la comunicacion asincronica entre los microservicios productores y consumidores del sistema de rastreo GPS.

## Tecnologias

- RabbitMQ 3.x con plugin de Management
- Docker

## Arquitectura

RabbitMQ es el componente central de la arquitectura event-driven del sistema:

```
[GPS Producer]  ---> gps.exchange  ---> gps.coordenada.queue ---> [GPS Consumer]
[Ruta Producer] ---> ruta.exchange ---> gps.ruta.queue       ---> [Ruta Consumer]
```

## Colas y Exchanges

| Exchange | Tipo | Queue | Routing Key | Productor | Consumidor |
|----------|------|-------|-------------|-----------|------------|
| `gps.exchange` | Topic | `gps.coordenada.queue` | `gps.coordenada` | gps-producer | gps-consumer |
| `ruta.exchange` | Topic | `gps.ruta.queue` | `gps.ruta` | ruta-producer | ruta-consumer |

## Puertos

| Puerto | Protocolo | Descripcion |
|--------|-----------|-------------|
| 5672 | AMQP | Puerto principal para conexion de los microservicios |
| 15672 | HTTP | Interfaz web de administracion (Management UI) |

## Credenciales por Defecto

| Usuario | Contrasena |
|---------|------------|
| guest | guest |

> La interfaz de administración esta disponible en `http://localhost:15672`.

## Ejecucion con Docker Compose

### Prerrequisitos

- Docker y Docker Compose instalados
- Red externa `app-network` creada:

```bash
docker network create app-network
```

### Iniciar el servicio

```bash
docker compose up -d
```

### Verificar estado

```bash
docker compose ps
```

## Persistencia

Los datos de RabbitMQ (colas, mensajes, configuracion) se persisten mediante el volumen Docker `rabbitmq_data` montado en `/var/lib/rabbitmq`.

## Orden de Despliegue

RabbitMQ debe estar ejecutandose **antes** de iniciar los microservicios productores y consumidores:

1. Iniciar `rabbitmq-server`
3. Iniciar `gps-producer`, `gps-consumer`, `ruta-producer`, `ruta-consumer`
