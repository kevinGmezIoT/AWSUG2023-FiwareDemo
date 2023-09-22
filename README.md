# FIWARE Demo Repository

Este repositorio contiene un proyecto de demostración que muestra las capacidades de FIWARE. Los usuarios pueden configurar fácilmente los servicios de FIWARE utilizando contenedores Docker. Los servicios incluidos en esta demo son Orion-ld, IoT Agent for JSON, Draco y una base de datos MongoDB. La demo simula una granja con su campo siendo monitoreado por un dispositivo equipado con sensores de temperatura y humedad.

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

## Requisitos Previos

Antes de ejecutar la demo, se recomienda crear una instancia de AWS de tipo t2.small con al menos 20GB de almacenamiento.

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
4. Ejecute el script de instalación:
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