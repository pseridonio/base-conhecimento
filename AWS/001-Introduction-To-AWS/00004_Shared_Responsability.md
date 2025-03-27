# Modelo de Responsabilidade Compartilhada da AWS

O Modelo de Responsabilidade Compartilhada da AWS � um framework que define as responsabilidades de seguran�a e conformidade da AWS e de seus clientes. Ele divide as responsabilidades em duas categorias:

### Responsabilidades da AWS (Seguran�a da Nuvem)

1. **Seguran�a F�sica:**
   - A AWS � respons�vel pela seguran�a f�sica dos data centers. Isso inclui proteger as instala��es, controlar o acesso e garantir que o hardware esteja protegido contra amea�as f�sicas.

2. **Seguran�a da Infraestrutura:**
   - A AWS gerencia a infraestrutura que inclui hardware, software, rede e instala��es que executam os servi�os da AWS Cloud. Isso inclui a manuten��o dos servidores, armazenamento e equipamentos de rede.

3. **Seguran�a de Rede:**
   - A AWS � respons�vel por proteger a infraestrutura de rede global que conecta os data centers. Isso inclui garantir a comunica��o segura entre os data centers e proteger contra ataques de rede.

4. **Camada de Virtualiza��o:**
   - A AWS gerencia o hipervisor e a tecnologia de virtualiza��o subjacente que permite que v�rias m�quinas virtuais sejam executadas em um �nico servidor f�sico.

### Responsabilidades do Cliente (Seguran�a na Nuvem)

1. **Seguran�a dos Dados:**
   - Os clientes s�o respons�veis pela seguran�a de seus dados. Isso inclui criptografia de dados, integridade dos dados e confidencialidade dos dados. Os clientes devem garantir que seus dados estejam criptografados tanto em repouso quanto em tr�nsito.

2. **Gerenciamento de Identidade e Acesso (IAM):**
   - Os clientes devem gerenciar o acesso e as permiss�es dos usu�rios usando o AWS IAM. Isso inclui criar e gerenciar usu�rios, grupos, fun��es e pol�ticas do IAM para controlar quem pode acessar os recursos e quais a��es podem ser realizadas.

3. **Seguran�a de Aplica��es:**
   - Os clientes s�o respons�veis por proteger suas aplica��es que est�o sendo executadas na AWS. Isso inclui garantir que as aplica��es estejam livres de vulnerabilidades, implementar pr�ticas de codifica��o segura e atualizar e corrigir regularmente o software.

4. **Configura��o de Rede:**
   - Os clientes devem configurar suas pr�prias configura��es de rede, como configura��es do Virtual Private Cloud (VPC), grupos de seguran�a e listas de controle de acesso (ACLs). Isso inclui configurar firewalls, VPNs e outras medidas de seguran�a de rede.

5. **Configura��o do Sistema Operacional e Software:**
   - Os clientes s�o respons�veis por gerenciar os sistemas operacionais e o software que est�o sendo executados em suas m�quinas virtuais. Isso inclui instalar atualiza��es, patches e configurar as configura��es de seguran�a.

6. **Monitoramento e Registro:**
   - Os clientes devem implementar monitoramento e registro para detectar e responder a incidentes de seguran�a. A AWS fornece servi�os como Amazon CloudWatch e AWS CloudTrail para ajudar com o monitoramento e registro.

### Representa��o Visual

Aqui est� uma representa��o visual do Modelo de Responsabilidade Compartilhada da AWS:

| Responsabilidades da AWS   | Responsabilidades do Cliente       |
|----------------------------|------------------------------------|
| Seguran�a F�sica           | Seguran�a dos Dados                |
| Seguran�a da Infraestrutura| Gerenciamento de Identidade e Acesso (IAM) |
| Seguran�a de Rede          | Seguran�a de Aplica��es            |
| Camada de Virtualiza��o    | Configura��o de Rede               |
|                            | Configura��o do Sistema Operacional e Software |
|                            | Monitoramento e Registro           |


---

# AWS Shared Responsibility Model

The AWS Shared Responsibility Model is a framework that outlines the security and compliance responsibilities of AWS and its customers. It divides responsibilities into two categories:

### AWS Responsibilities (Security of the Cloud)

1. **Physical Security:**
   - AWS is responsible for the physical security of the data centers. This includes securing the facilities, controlling access, and ensuring that the hardware is protected from physical threats.

2. **Infrastructure Security:**
   - AWS manages the infrastructure that includes the hardware, software, networking, and facilities that run AWS Cloud services. This includes maintaining the servers, storage, and networking equipment.

3. **Network Security:**
   - AWS is responsible for protecting the global network infrastructure that connects the data centers. This includes ensuring secure communication between data centers and protecting against network attacks.

4. **Virtualization Layer:**
   - AWS manages the hypervisor and the underlying virtualization technology that allows multiple virtual machines to run on a single physical server.

### Customer Responsibilities (Security in the Cloud)

1. **Data Security:**
   - Customers are responsible for the security of their data. This includes data encryption, data integrity, and data confidentiality. Customers must ensure that their data is encrypted both at rest and in transit.

2. **Identity and Access Management (IAM):**
   - Customers must manage user access and permissions using AWS IAM. This includes creating and managing IAM users, groups, roles, and policies to control who can access resources and what actions they can perform.

3. **Application Security:**
   - Customers are responsible for securing their applications running on AWS. This includes ensuring that applications are free from vulnerabilities, implementing secure coding practices, and regularly updating and patching software.

4. **Network Configuration:**
   - Customers must configure their own network settings, such as Virtual Private Cloud (VPC) settings, security groups, and network access control lists (ACLs). This includes setting up firewalls, VPNs, and other network security measures.

5. **Operating System and Software Configuration:**
   - Customers are responsible for managing the operating systems and software running on their virtual machines. This includes installing updates, patches, and configuring security settings.

6. **Monitoring and Logging:**
   - Customers should implement monitoring and logging to detect and respond to security incidents. AWS provides services like Amazon CloudWatch and AWS CloudTrail to help with monitoring and logging.

### Visual Representation

Here is a visual representation of the AWS Shared Responsibility Model:


| AWS Responsibilities        | Customer Responsibilities         |
|-----------------------------|-----------------------------------|
| Physical Security           | Data Security                     |
| Infrastructure Security     | Identity and Access Management (IAM) |
| Network Security            | Application Security              |
| Virtualization Layer        | Network Configuration             |
|                             | Operating System and Software Configuration |
|                             | Monitoring and Logging            |


---

# Modelo de Responsabilidad Compartida de AWS

El Modelo de Responsabilidad Compartida de AWS es un marco que describe las responsabilidades de seguridad y cumplimiento de AWS y sus clientes. Divide las responsabilidades en dos categor�as:

### Responsabilidades de AWS (Seguridad de la Nube)

1. **Seguridad F�sica:**
   - AWS es responsable de la seguridad f�sica de los centros de datos. Esto incluye asegurar las instalaciones, controlar el acceso y garantizar que el hardware est� protegido contra amenazas f�sicas.

2. **Seguridad de la Infraestructura:**
   - AWS gestiona la infraestructura que incluye el hardware, software, redes e instalaciones que ejecutan los servicios de AWS Cloud. Esto incluye el mantenimiento de los servidores, el almacenamiento y el equipo de redes.

3. **Seguridad de la Red:**
   - AWS es responsable de proteger la infraestructura de red global que conecta los centros de datos. Esto incluye garantizar la comunicaci�n segura entre los centros de datos y proteger contra ataques de red.

4. **Capa de Virtualizaci�n:**
   - AWS gestiona el hipervisor y la tecnolog�a de virtualizaci�n subyacente que permite que m�ltiples m�quinas virtuales se ejecuten en un solo servidor f�sico.

### Responsabilidades del Cliente (Seguridad en la Nube)

1. **Seguridad de los Datos:**
   - Los clientes son responsables de la seguridad de sus datos. Esto incluye la encriptaci�n de datos, la integridad de los datos y la confidencialidad de los datos. Los clientes deben asegurarse de que sus datos est�n encriptados tanto en reposo como en tr�nsito.

2. **Gesti�n de Identidad y Acceso (IAM):**
   - Los clientes deben gestionar el acceso y los permisos de los usuarios utilizando AWS IAM. Esto incluye crear y gestionar usuarios, grupos, roles y pol�ticas de IAM para controlar qui�n puede acceder a los recursos y qu� acciones pueden realizar.

3. **Seguridad de Aplicaciones:**
   - Los clientes son responsables de asegurar sus aplicaciones que se ejecutan en AWS. Esto incluye garantizar que las aplicaciones est�n libres de vulnerabilidades, implementar pr�cticas de codificaci�n segura y actualizar y parchear regularmente el software.

4. **Configuraci�n de la Red:**
   - Los clientes deben configurar sus propias configuraciones de red, como configuraciones de Virtual Private Cloud (VPC), grupos de seguridad y listas de control de acceso (ACL). Esto incluye configurar firewalls, VPNs y otras medidas de seguridad de red.

5. **Configuraci�n del Sistema Operativo y Software:**
   - Los clientes son responsables de gestionar los sistemas operativos y el software que se ejecutan en sus m�quinas virtuales. Esto incluye instalar actualizaciones, parches y configurar las configuraciones de seguridad.

6. **Monitoreo y Registro:**
   - Los clientes deben implementar monitoreo y registro para detectar y responder a incidentes de seguridad. AWS proporciona servicios como Amazon CloudWatch y AWS CloudTrail para ayudar con el monitoreo y registro.

### Representaci�n Visual

Aqu� hay una representaci�n visual del Modelo de Responsabilidad Compartida de AWS:

| Responsabilidades de AWS    | Responsabilidades del Cliente     |
|-----------------------------|-----------------------------------|
| Seguridad F�sica            | Seguridad de los Datos            |
| Seguridad de la Infraestructura | Gesti�n de Identidad y Acceso (IAM) |
| Seguridad de la Red         | Seguridad de Aplicaciones         |
| Capa de Virtualizaci�n      | Configuraci�n de la Red           |
|                             | Configuraci�n del Sistema Operativo y Software |
|                             | Monitoreo y Registro              |

---

![Shared Responsibility Model](../.content/AWS_Shared_Responsability.png)