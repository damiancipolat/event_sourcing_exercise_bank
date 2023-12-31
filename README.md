<img src="https://github.com/damiancipolat/node-bff/blob/master/doc/node.png?raw=true" width="150px" align="right" />

# Intro:

En este repositorio se presenta la solucion a un ejercicio.

**Readme sections**

- [Stack](#stack)
- [Configuration](#configuration)
- [Commands](#commands)
- [Architecture](#arquitectura)
- [Endpoints](#endpoints)
- [Docker](#docker)

## **Stack**:

- **Node.js v18**
- Docker/docker-compose
- Test: jest
- config: dotenv
- express.js
- pino: For logging.

## **Configuration**:

En este proyecto se usa el formato .env para la configuracion.

```console
PORT=8000
DB_HOST= 127.0.0.1
DB_PORT= 3306
DB_USER= test
DB_PWD= 1234
DB_NAME= test
```

## **Commands**:

Estos son algunos comandos utiles:

- Run unit test:

```console
damian@challenge:~$ npm test
```

- Run coverage report.

```console
damian@challenge:~$ npm run coverage
```

- Run app in development mode:

```console
damian@challenge:~$ npm run dev
```

- Run app:

```console
damian@challenge:~$ npm start
```

- Create and run docker container:

```console
damian@challenge:~$ docker compose up
```

## **Arquitectura**:

**Introducción:**
El proyecto se basa en un servicio que utiliza una base de datos MYSQL y expone una interfaz REST utilizando NODEJS.

En cuanto al diseño, opte por un enfoque modular en el que las características principales de funcionamiento se encuentran encapsuladas dentro de módulos. Además, hemos seguido la práctica de utilizar elementos de Diseño de Dominio para modelar el dominio con objetos y eventos.

Desde una perspectiva arquitectónica, hemos adoptado el patrón de diseño 'Event Sourcing', en el cual todas las operaciones del sistema se consideran como una secuencia de eventos. Estos eventos se almacenan en una base de datos SQL que consideramos como nuestro "event store", lo que es esencial para llevar un seguimiento completo de los cambios de estado a lo largo de cada evento.

A diferencia de otros enfoques, en este patrón no se utilizan tablas con entidades separadas, sino que todo se registra en una única tabla de eventos, desde la cual podemos reconstruir el estado de la aplicación mediante la secuencia de eventos asociada a cada objeto.

### Decidiones de diseño:

Base de datos: Opte por una base de datos SQL debido a su facilidad de implementación, aunque en otros contextos también habría sido válida una base de datos NoSQL.

Monolítico: Decidi implementar un único servicio, ya que, al tratarse de un desafío, no era necesario introducir colas de eventos para la comunicación asincrónica; nuestro enfoque se centró en la implementación del Event Sourcing.

**Patrones de diseño**:
Se uso el patrón de diseño "Strategy", que se puede observar en la clase `src/modules/balances/balance.service.ts` y el módulo `src/modules/balances/calcBalance.ts`, así como en `ICalcBalance.ts`. En estos casos, el patrón "Strategy" se utiliza para implementar algoritmos intercambiables a través de una interfaz TypeScript, permitiendo una flexibilidad en la elección del algoritmo a utilizar.

**Polimorfismo:**
Aplique el concepto de polimorfismo en `src/modules/eventStore/event.repository.ts` y `IEventRepository.ts`. Esta implementación permite encapsular la capa de gestión de eventos y facilita futuros cambios en la implementación de la base de datos sin afectar a la interfaz.

### AWS:

Para llevar acabo el diseño que elabore en AWS, puede realizarse con la siquiente arquitectura que se describe en este diagrama:

<img src="https://github.com/damiancipolat/Xepelin_challenge/blob/main/doc/aws_diagram.png?raw=true" width="650px" />

## **Endpoints**:

Esta es la documentación de la API que te permitirá interactuar con el sistema de cuentas y transacciones.

##### Obtener el saldo de una cuenta

Descripción:
Este endpoint te permite obtener el saldo de una cuenta específica utilizando su identificador único.
GET /accounts/<id>/balance

CURL;

```console
curl --location 'http://127.0.0.1:8000/accounts/52fd94e9-baca-46d7-a83a-5698ab84434b/balance'
```

##### Crear una nueva cuenta

Descripción:
Crea una nueva cuenta proporcionando los datos necesarios, como nombre, apellido, correo electrónico y número de cuenta.
POST /accounts

CURL

```console
curl --location 'http://127.0.0.1:8000/accounts' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name":"damian",
    "surname":"Cipolat",
    "email":"test@gmail.com",
    "accountNumber":"100011"
}'
```

##### Realizar una retirada (withdraw)

Descripción:
Esta solicitud permite realizar una retirada de fondos de una cuenta específica.
POST /transactions

CURL

```console
curl --location 'http://127.0.0.1:8000/transactions' \
--header 'Content-Type: application/json' \
--data '{
    "type":"withdraw",
    "ammount":10,
    "accountId":"52fd94e9-baca-46d7-a83a-5698ab84434b"
}'
```

##### Realizar un depósito (deposit)

Descripción
Esta solicitud permite realizar un depósito de fondos en una cuenta específica.
POST /transactions

CURL

```console
curl --location 'http://127.0.0.1:8000/transactions' \
--header 'Content-Type: application/json' \
--data '{
    "type":"deposit",
    "ammount":10,
    "accountId":"52fd94e9-baca-46d7-a83a-5698ab84434b"
}'
```

## **Docker**:

El proyecto esta completamente dockerizado para poder ejecutarse sin mayores problemas.

Levantar proyecto (para mac):

```console
docker compose up
```

Se incluye tambie un phpmyadmin para simplificar el acceso a la bd.

```console
http://127.0.0.1:8080/
user:test
pwd:1234
```

Se incluye al ejecutar el comando para "docker compose up",
la ejecución de la migration que crea las tablas de la bd.
