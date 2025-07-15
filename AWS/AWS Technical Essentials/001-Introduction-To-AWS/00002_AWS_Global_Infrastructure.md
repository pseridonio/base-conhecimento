# Infraestrutura Global da AWS

## Visão Geral

A infraestrutura global da AWS é projetada para fornecer alta disponibilidade, tolerância a falhas e escalabilidade. Aqui está uma breve visão geral:

### Regiões

- Os serviços da AWS são distribuídos em várias regiões geográficas em todo o mundo.
- Cada região é uma área geográfica separada que possui várias localizações isoladas conhecidas como Zonas de Disponibilidade (AZs).

### Zonas de Disponibilidade (AZs)

- Cada região consiste em várias AZs.
- As AZs são fisicamente separadas dentro de uma região e são projetadas para serem isoladas de falhas em outras AZs.
- Elas fornecem conectividade de rede de baixa latência entre si.
- **Nota**: Uma AZ é um agrupamento lógico de um ou mais data centers. Cada AZ é projetada para ser isolada de falhas em outras AZs dentro da mesma região. As AZs dentro de uma região são conectadas por links de baixa latência.

### Data Centers

- Um data center é uma instalação física que abriga infraestrutura de TI, incluindo servidores, armazenamento e equipamentos de rede.
- Uma única AZ pode consistir em vários data centers.

### Localizações de Borda

- A AWS possui uma rede global de localizações de borda para entrega de conteúdo (Amazon CloudFront) e serviços de DNS (Amazon Route 53).
- Essas localizações de borda ajudam a entregar conteúdo com baixa latência para os usuários finais.

## Disponibilidade de Serviços

### Serviços Globais

- Alguns serviços da AWS estão disponíveis globalmente e não dependem de uma região específica. Exemplos incluem Amazon Route 53, AWS Identity and Access Management (IAM) e Amazon CloudFront.

### Serviços Regionais

- Muitos serviços da AWS são específicos de região, o que significa que estão disponíveis em regiões específicas. Exemplos incluem Amazon EC2, Amazon S3 e Amazon RDS.
- Quando você cria recursos nesses serviços, deve especificar a região onde os recursos estarão localizados.

## Separação e Considerações

### Residência de Dados

- Os serviços regionais permitem que você controle onde seus dados são armazenados, o que pode ser importante para requisitos de conformidade e residência de dados.

### Latência e Desempenho

- Ao escolher a região e as AZs apropriadas, você pode otimizar a latência e o desempenho de suas aplicações.

### Tolerância a Falhas

- Usar várias AZs dentro de uma região pode aumentar a tolerância a falhas e a disponibilidade.

## Resumo

Em resumo, enquanto alguns serviços da AWS estão disponíveis globalmente, muitos são específicos de região, permitindo que você escolha os melhores locais para seus recursos com base em fatores como latência, conformidade e tolerância a falhas.

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

## Visión General

La infraestructura global de AWS está diseñada para proporcionar alta disponibilidad, tolerancia a fallos y escalabilidad. Aquí hay una breve descripción:

### Regiones

- Los servicios de AWS están distribuidos en múltiples regiones geográficas en todo el mundo.
- Cada región es un área geográfica separada que tiene múltiples ubicaciones aisladas conocidas como Zonas de Disponibilidad (AZs).

### Zonas de Disponibilidad (AZs)

- Cada región consta de múltiples AZs.
- Las AZs están físicamente separadas dentro de una región y están diseñadas para estar aisladas de fallos en otras AZs.
- Proporcionan conectividad de red de baja latencia entre sí.
- **Nota**: Una AZ es un agrupamiento lógico de uno o más centros de datos. Cada AZ está diseñada para estar aislada de fallos en otras AZs dentro de la misma región. Las AZs dentro de una región están conectadas a través de enlaces de baja latencia.

### Centros de Datos

- Un centro de datos es una instalación física que alberga infraestructura de TI, incluidos servidores, almacenamiento y equipos de red.
- Una sola AZ puede consistir en múltiples centros de datos.

### Ubicaciones de Borde

- AWS tiene una red global de ubicaciones de borde para la entrega de contenido (Amazon CloudFront) y servicios de DNS (Amazon Route 53).
- Estas ubicaciones de borde ayudan a entregar contenido con baja latencia a los usuarios finales.

## Disponibilidad de Servicios

### Servicios Globales

- Algunos servicios de AWS están disponibles globalmente y no dependen de una región específica. Ejemplos incluyen Amazon Route 53, AWS Identity and Access Management (IAM) y Amazon CloudFront.

### Servicios Regionales

- Muchos servicios de AWS son específicos de región, lo que significa que están disponibles dentro de regiones específicas. Ejemplos incluyen Amazon EC2, Amazon S3 y Amazon RDS.
- Cuando crea recursos en estos servicios, debe especificar la región donde se ubicarán los recursos.

## Separación y Consideraciones

### Residencia de Datos

- Los servicios regionales le permiten controlar dónde se almacenan sus datos, lo cual puede ser importante para los requisitos de cumplimiento y residencia de datos.

### Latencia y Rendimiento

- Al elegir la región y las AZs apropiadas, puede optimizar la latencia y el rendimiento de sus aplicaciones.

### Tolerancia a Fallos

- Usar múltiples AZs dentro de una región puede mejorar la tolerancia a fallos y la disponibilidad.

## Resumen

En resumen, mientras que algunos servicios de AWS están disponibles globalmente, muchos son específicos de región, lo que le permite elegir las mejores ubicaciones para sus recursos en función de factores como latencia, cumplimiento y tolerancia a fallos.
