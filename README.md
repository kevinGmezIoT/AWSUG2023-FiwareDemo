# FIWARE Demo Repository

Este repositorio contiene un proyecto de demostración que muestra las capacidades de FIWARE presentado en el evento AWS UG Conf 2023. Los usuarios pueden desplegar fácilmente los servicios de FIWARE utilizando contenedores Docker. Los servicios incluidos en esta demo son Orion-ld, IoT Agent for JSON, Draco, una base de datos MongoDB y una PostgreSQL. La demo simula una granja con su campo siendo monitoreado por un dispositivo equipado con sensores de temperatura y humedad.

## Modelo de Datos

El modelo de datos para este proyecto está representado en el siguiente diagrama:

![Model diagram](images/Model_diagram.jpg)

Este diagrama incluye las siguientes entidades:

- Organization: Almacena información sobre la empresa que gestiona la granja.
- Person: Representa al supervisor de la granja.
- AgriFarm: Representa la granja en sí.
- AgriParcel: Representa las parcelas individuales dentro de la granja.
- AirQualityObserved: Contiene parámetros de calidad del aire para el área donde se encuentra la parcela, que se pueden obtener de fuentes externas.
- Device: Representa el dispositivo de monitoreo responsable de medir la temperatura y la humedad en las parcelas.
- Sensor: Se refiere al sensor de temperatura o humedad.

Los modelos de datos abiertos elegidos para esta demo son los siguientes:

[https://raw.githubusercontent.com/smart-data-models/dataModel.Device/master/context.jsonld](https://raw.githubusercontent.com/smart-data-models/dataModel.Device/master/context.jsonld)
[https://raw.githubusercontent.com/smart-data-models/dataModel.Environment/master/context.jsonld](https://raw.githubusercontent.com/smart-data-models/dataModel.Environment/master/context.jsonld)
[https://raw.githubusercontent.com/smart-data-models/dataModel.OCF/master/context.jsonld](https://raw.githubusercontent.com/smart-data-models/dataModel.OCF/master/context.jsonld)
[https://raw.githubusercontent.com/smart-data-models/dataModel.Agrifood/master/context.jsonld](https://raw.githubusercontent.com/smart-data-models/dataModel.Agrifood/master/context.jsonld)
[https://raw.githubusercontent.com/smart-data-models/dataModel.Organization/master/context.jsonld](https://raw.githubusercontent.com/smart-data-models/dataModel.Organization/master/context.jsonld)
[https://raw.githubusercontent.com/smart-data-models/dataModel.SAREF/master/context.jsonld](https://raw.githubusercontent.com/smart-data-models/dataModel.SAREF/master/context.jsonld)

Estos modelos de datos se agregan en la sección __iot-agent__ del archivo __docker-compose.yml__

## Arquitectura

La arquitectura de los componentes de FIWARE para esta demo está representado en el siguiente diagrama:

![Architecture diagram](images/Arquitectura_fiware.jpg)

Esta arquitectura está conformada por:

- ORION-LD: Componente principal de toda plataforma Powered by Fiware. Se encarga de la gestión de las Entidades, relaciones y se conecta a todos los demás componentes de manera estandarizada por NGSI-LD. Usa una base de datos MongoDB. Este componente es administrado por el puerto 1026.
- IDAS: También llamado IoT Agent. Para esta demo se utiliza el tipo Json. Este componente se encarga de traducir una trama Json al estándar NGSI-LD para que pueda ser interpretada por ORION-LD. Usa una base de datos MongoDB. Este componente es administrado por el puerto 4041 y recibe la trama Json de los dispositivos por el puerto 7896.
- DRACO: Es el componente que permite conectarse a una base de datos de históricos, ya que ORION-LD solo almacena valores instáneos. Está basado en Apache NiFi. Para la demo se conecta a una BD PostgreSQL por el puerto 5432. Este contenedor usa los puertos 5050 para suscribirse a ORION-LD y 9090 para la UI de configuración de la base de datos.
- SENSOR: Es el dispositivo que envía los datos de los sensores al IDAS en formato JSON, puede ser uno o varios sensores y, para esta demo, enviará los datos por el protocolo http.
- POSTGRESQL: Almacena los datos históricos en una tabla del mismo nombre que la entidad, en este caso Sensor.
- MONGODB: Base de datos en donde se almacena toda la información de ORION-LD y el IDAS. Normalmente no se interactúa con esta base de datos. Acepta conexiones por el puerto 27017.

## Requisitos Previos

Antes de ejecutar la demo, se recomienda crear una instancia de AWS de tipo t2.small con al menos 20GB de almacenamiento.
Habilitar los puertos de la imagen:

![EC2 Ports](images/Puertos_EC2.jpg)

## Procedimiento de Instalación

Para configurar y ejecutar la demo, siga estos pasos:

1. Clone este repositorio en su máquina local:

   ```bash
   git clone https://github.com/kevinGmezIoT/AWSUG2023-FiwareDemo.git
   ```
2. Navegue al directorio del proyecto:
   ```bash
   cd AWSUG2023-FiwareDemo
   ```
3. Cambie al usuario root (sudo):
   ```bash
   sudo su
   ```
4. Ejecute el script de instalación (Instalará Docker):
   ```bash
   bash installation.sh
   ```
5. Inicie los contenedores de Docker:
   ```bash
   docker compose up
   ```
Para que se ejecuten en segundo plano:
   ```bash
   docker compose up -d
   ```
Para detener los contenedores:
   ```bash
   docker compose stop
   ```

## Configurar NiFi

Para ingresar a la UI nos dirigimos a la dirección: http://<URL-EC2-Instancia>:9090/nifi
Aquí hacemos ubicar el mouse en __Template__ y arrastrar al espacio de trabajo.

![NIFI 1](images/Nifi_1.jpg)

Seleccionar ORION-TO-POSTGRESQL.

![NIFI 2](images/Nifi_2.jpg)

Se verá un conjunto de bloques. Hacer clic derecho en el espacio de trabajo fuera de los bloques y elegir __Configure__

![NIFI 3](images/Nifi_3.jpg)

Ubicarse en la pestaña __CONTROLLER SERVICES__ y luego dar clic en el engranaje.

![NIFI 4](images/Nifi_4.jpg)

Luego ingresar a la pestaña PROPERTIES y modificar las propiedades:
- Database Connection URL: Aquí reemplazar correctamente el hostname de la BD
- Database User: Colocar el nombre de usuario de la BD
- Password: Colocar la contraseña de la BD, una vez colocada no se vuelve a mostrar.
Estos valores se colocan de acuerdo a los parámetros en el archivo __.env__

![NIFI 5](images/Nifi_5.jpg)

Luego hacer clic en __APPLY__ y hacer clic en el símbolo de rayo al costado del engranaje.
Finalmente hacer clic en __ENABLE__

![NIFI 6](images/Nifi_6.jpg)

Salir de todas las ventanas y volver al espacio de trabajo. Aquí hacer clic derecho al bloque central y elegir __Configure__.

![NIFI 7](images/Nifi_7.jpg)

Seleccionar la pestaña __PROPERTIES__ y modificar las propiedades:

- NGSI Version: Cambiar de v2 a ld. Esto porque utilizamos NGSI-LD.
- Data Model: Elegir db-by-entity-type. Esto le asignará el nombre de la entidad a la tabla generada.
- Default Service: Colocar __awsug__. Este es el nombre de servicio elegido para esta demo.
- Default Service path: Colocar solo /. Este es el path más simple para esta demo.

Finalmente clic en __APPLY__

![NIFI 8](images/Nifi_8.jpg)

La última acción en NiFi es seleccionar a los tres bloques más grandes usando la tecla SHIFT y hacer clic en el botón Start como se ve en la figura:

![NIFI 9](images/Nifi_9.jpg)

## Manejando los componentes de FIWARE

Los siguientes pasos se pueden realizar en POSTMAN o con comandos cUrl

# Pruebas

Prueba de Orion:
```bash
curl --location 'http://<URL-EC2-Instancia>:1026/version'
```

Prueba de IoT Agent:
```bash
curl --location 'http://<URL-EC2-Instancia>:4041/iot/about'
```

# Pasos para establecer el sistema completo

1. Crear Grupo de Servicio:
```bash
curl --location 'http://<URL-EC2-Instancia>:4041/iot/services' \
--header 'fiware-service: awsug' \
--header 'fiware-servicepath: /' \
--header 'Content-Type: application/json' \
--data '{
    "services": [
        {
            "apikey": "f6ad88cb-fbcd-413e-b71e-df3147943718",
            "cbroker": "http://orion:1026",
            "entity_type": "Sensor",
            "resource": "/iot/json"
        }
    ]
}'
```

