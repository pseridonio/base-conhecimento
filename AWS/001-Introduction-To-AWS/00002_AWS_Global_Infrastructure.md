# Infraestrutura Global da AWS

## Vis�o Geral

A infraestrutura global da AWS � projetada para fornecer alta disponibilidade, toler�ncia a falhas e escalabilidade. Aqui est� uma breve vis�o geral:

### Regi�es

- Os servi�os da AWS s�o distribu�dos em v�rias regi�es geogr�ficas em todo o mundo.
- Cada regi�o � uma �rea geogr�fica separada que possui v�rias localiza��es isoladas conhecidas como Zonas de Disponibilidade (AZs).

### Zonas de Disponibilidade (AZs)

- Cada regi�o consiste em v�rias AZs.
- As AZs s�o fisicamente separadas dentro de uma regi�o e s�o projetadas para serem isoladas de falhas em outras AZs.
- Elas fornecem conectividade de rede de baixa lat�ncia entre si.
- **Nota**: Uma AZ � um agrupamento l�gico de um ou mais data centers. Cada AZ � projetada para ser isolada de falhas em outras AZs dentro da mesma regi�o. As AZs dentro de uma regi�o s�o conectadas por links de baixa lat�ncia.

### Data Centers

- Um data center � uma instala��o f�sica que abriga infraestrutura de TI, incluindo servidores, armazenamento e equipamentos de rede.
- Uma �nica AZ pode consistir em v�rios data centers.

### Localiza��es de Borda

- A AWS possui uma rede global de localiza��es de borda para entrega de conte�do (Amazon CloudFront) e servi�os de DNS (Amazon Route 53).
- Essas localiza��es de borda ajudam a entregar conte�do com baixa lat�ncia para os usu�rios finais.

## Disponibilidade de Servi�os

### Servi�os Globais

- Alguns servi�os da AWS est�o dispon�veis globalmente e n�o dependem de uma regi�o espec�fica. Exemplos incluem Amazon Route 53, AWS Identity and Access Management (IAM) e Amazon CloudFront.

### Servi�os Regionais

- Muitos servi�os da AWS s�o espec�ficos de regi�o, o que significa que est�o dispon�veis em regi�es espec�ficas. Exemplos incluem Amazon EC2, Amazon S3 e Amazon RDS.
- Quando voc� cria recursos nesses servi�os, deve especificar a regi�o onde os recursos estar�o localizados.

## Separa��o e Considera��es

### Resid�ncia de Dados

- Os servi�os regionais permitem que voc� controle onde seus dados s�o armazenados, o que pode ser importante para requisitos de conformidade e resid�ncia de dados.

### Lat�ncia e Desempenho

- Ao escolher a regi�o e as AZs apropriadas, voc� pode otimizar a lat�ncia e o desempenho de suas aplica��es.

### Toler�ncia a Falhas

- Usar v�rias AZs dentro de uma regi�o pode aumentar a toler�ncia a falhas e a disponibilidade.

## Resumo

Em resumo, enquanto alguns servi�os da AWS est�o dispon�veis globalmente, muitos s�o espec�ficos de regi�o, permitindo que voc� escolha os melhores locais para seus recursos com base em fatores como lat�ncia, conformidade e toler�ncia a falhas.

---

# AWS Global Infrastructure

## Overview

The AWS global infrastructure is designed to provide high availability, fault tolerance, and scalability. Here's a brief overview:

### Regions

- AWS services are distributed across multiple geographic regions worldwide.
- Each region is a separate geographic area that has multiple, isolated locations known as Availability Zones (AZs).

### Availability Zones (AZs)

- Each region consists of multiple AZs.
- AZs are physically separated within a region and are designed to be isolated from failures in other AZs.
- They provide low-latency network connectivity to each other.
- **Note**: An AZ is a logical grouping of one or more data centers. Each AZ is designed to be isolated from failures in other AZs within the same region. AZs within a region are connected through low-latency links.

### Data Centers

- A data center is a physical facility that houses IT infrastructure, including servers, storage, and networking equipment.
- A single AZ can consist of multiple data centers.

### Edge Locations

- AWS has a global network of edge locations for content delivery (Amazon CloudFront) and DNS services (Amazon Route 53).
- These edge locations help to deliver content with low latency to end-users.

## Service Availability

### Global Services

- Some AWS services are globally available and do not depend on a specific region. Examples include Amazon Route 53, AWS Identity and Access Management (IAM), and Amazon CloudFront.

### Regional Services

- Many AWS services are region-specific, meaning they are available within specific regions. Examples include Amazon EC2, Amazon S3, and Amazon RDS.
- When you create resources in these services, you must specify the region where the resources will be located.

## Separation and Considerations

### Data Residency

- Regional services allow you to control where your data is stored, which can be important for compliance and data residency requirements.

### Latency and Performance

- By choosing the appropriate region and AZs, you can optimize latency and performance for your applications.

### Fault Tolerance

- Using multiple AZs within a region can enhance fault tolerance and availability.

## Summary

In summary, while some AWS services are globally available, many are region-specific, allowing you to choose the best locations for your resources based on factors like latency, compliance, and fault tolerance.

---

# Infraestructura Global de AWS

## Visi�n General

La infraestructura global de AWS est� dise�ada para proporcionar alta disponibilidad, tolerancia a fallos y escalabilidad. Aqu� hay una breve descripci�n:

### Regiones

- Los servicios de AWS est�n distribuidos en m�ltiples regiones geogr�ficas en todo el mundo.
- Cada regi�n es un �rea geogr�fica separada que tiene m�ltiples ubicaciones aisladas conocidas como Zonas de Disponibilidad (AZs).

### Zonas de Disponibilidad (AZs)

- Cada regi�n consta de m�ltiples AZs.
- Las AZs est�n f�sicamente separadas dentro de una regi�n y est�n dise�adas para estar aisladas de fallos en otras AZs.
- Proporcionan conectividad de red de baja latencia entre s�.
- **Nota**: Una AZ es un agrupamiento l�gico de uno o m�s centros de datos. Cada AZ est� dise�ada para estar aislada de fallos en otras AZs dentro de la misma regi�n. Las AZs dentro de una regi�n est�n conectadas a trav�s de enlaces de baja latencia.

### Centros de Datos

- Un centro de datos es una instalaci�n f�sica que alberga infraestructura de TI, incluidos servidores, almacenamiento y equipos de red.
- Una sola AZ puede consistir en m�ltiples centros de datos.

### Ubicaciones de Borde

- AWS tiene una red global de ubicaciones de borde para la entrega de contenido (Amazon CloudFront) y servicios de DNS (Amazon Route 53).
- Estas ubicaciones de borde ayudan a entregar contenido con baja latencia a los usuarios finales.

## Disponibilidad de Servicios

### Servicios Globales

- Algunos servicios de AWS est�n disponibles globalmente y no dependen de una regi�n espec�fica. Ejemplos incluyen Amazon Route 53, AWS Identity and Access Management (IAM) y Amazon CloudFront.

### Servicios Regionales

- Muchos servicios de AWS son espec�ficos de regi�n, lo que significa que est�n disponibles dentro de regiones espec�ficas. Ejemplos incluyen Amazon EC2, Amazon S3 y Amazon RDS.
- Cuando crea recursos en estos servicios, debe especificar la regi�n donde se ubicar�n los recursos.

## Separaci�n y Consideraciones

### Residencia de Datos

- Los servicios regionales le permiten controlar d�nde se almacenan sus datos, lo cual puede ser importante para los requisitos de cumplimiento y residencia de datos.

### Latencia y Rendimiento

- Al elegir la regi�n y las AZs apropiadas, puede optimizar la latencia y el rendimiento de sus aplicaciones.

### Tolerancia a Fallos

- Usar m�ltiples AZs dentro de una regi�n puede mejorar la tolerancia a fallos y la disponibilidad.

## Resumen

En resumen, mientras que algunos servicios de AWS est�n disponibles globalmente, muchos son espec�ficos de regi�n, lo que le permite elegir las mejores ubicaciones para sus recursos en funci�n de factores como latencia, cumplimiento y tolerancia a fallos.
