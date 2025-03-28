# Hospedando um Aplicativo de Diret�rio de Funcion�rios na AWS

## **Vis�o Geral dos Conceitos da AWS**
- **Vocabul�rio da AWS**: Termos-chave e conceitos de computa��o em nuvem.
- **Gerenciamento de Identidade e Acesso em Nuvem**: Fornece acesso seguro aos recursos.
- **Diagrama de Arquitetura**: Configura��o complexa com v�rios servi�os trabalhando juntos.

## **Hospedagem no Amazon EC2**
Amazon EC2 (Elastic Compute Cloud) � um servi�o de computa��o que permite hospedar m�quinas virtuais.

### **Passos para Hospedar o Aplicativo**
1. **VPC Padr�o**:
   - A AWS fornece uma rede privada para recursos chamada VPC padr�o.
   - Inst�ncias EC2 devem estar em uma rede, e a VPC padr�o facilita a configura��o.

2. **Configurando a Inst�ncia EC2**:
   - Abra o Console de Gerenciamento da EC2.
   - Selecione o servi�o **Amazon EC2**.
   - Inicie uma nova inst�ncia (**t2.micro**, eleg�vel no n�vel gratuito).
   - Use o **Linux AMI (Amazon Machine Image)** como sistema operacional.
   - **Nomeie** a inst�ncia: `employee-web-app`.

3. **Configura��es de Rede**:
   - Selecione a **VPC Padr�o**.
   - Deixe **Sub-rede**: Sem prefer�ncia.
   - Crie um **Grupo de Seguran�a**: Adicione regras de entrada para permitir tr�fego HTTP e HTTPS.

4. **IAM e Dados do Usu�rio**:
   - Selecione a **fun��o IAM** para acessar os recursos.
   - Forne�a um **script de Dados do Usu�rio**:
     - Fa�a o download do c�digo-fonte do aplicativo.
     - Inicie o servidor web e o aplicativo automaticamente durante a inicializa��o da inst�ncia.

5. **Inicie a Inst�ncia**:
   - Configure todas as op��es.
   - Clique em **Lan�ar Inst�ncia**.
   - Aguarde a inicializa��o da inst�ncia.

### **Acessando o Aplicativo**
- Copie o **endere�o IP** da inst�ncia do console.
- Cole em um navegador para visualizar a p�gina inicial do aplicativo.

## **Aprendizados e Pr�ximas Aulas**
- Compreens�o detalhada das configura��es do EC2.
- Conceitos de rede na AWS.
- Integra��o do aplicativo com bancos de dados e outros servi�os da AWS.

---

# Hosting an Employee Directory Application on AWS

## **AWS Concepts Overview**
- **AWS Vocabulary**: Key terms and concepts behind cloud computing.
- **Cloud Identity and Access Management**: Provides secured access to resources.
- **Architecture Diagram**: Complex setup with multiple services working together.

## **Hosting on Amazon EC2**
Amazon EC2 (Elastic Compute Cloud) is a compute service that allows hosting virtual machines.

### **Steps to Launch the Application**
1. **Default VPC**:
   - AWS provides a private network for resources known as the default VPC.
   - EC2 instances must reside in a network, and default VPC facilitates easy configuration.

2. **Configuring EC2 Instance**:
   - Open EC2 Management Console.
   - Select **Amazon EC2** service.
   - Launch a new instance (**t2.micro**, free-tier eligible).
   - Use **Linux AMI (Amazon Machine Image)** for the operating system.
   - **Name** the instance: `employee-web-app`.

3. **Network Settings**:
   - Select the **Default VPC**.
   - Leave **Subnet**: No preference.
   - Create a **Security Group**: Add inbound rules to allow HTTP & HTTPS traffic.

4. **IAM and User Data**:
   - Select the **IAM role** for accessing resources.
   - Provide a **User Data script**:
     - Download app's source code.
     - Start web server & application automatically during instance boot.

5. **Launch the Instance**:
   - Configure all settings.
   - Click **Launch Instance**.
   - Wait for the instance to boot up.

### **Accessing the Application**
- Copy the instance's **IP address** from the console.
- Paste it into a browser to view the application's homepage.

## **Learning and Upcoming Lessons**
- In-depth understanding of EC2 configurations.
- Networking concepts on AWS.
- Application integration with databases and other AWS services.

---

# Hospedaje de una Aplicaci�n de Directorio de Empleados en AWS

## **Descripci�n General de Conceptos de AWS**
- **Vocabulario de AWS**: T�rminos clave y conceptos de computaci�n en la nube.
- **Gesti�n de Identidad y Acceso en la Nube**: Proporciona acceso seguro a los recursos.
- **Diagrama de Arquitectura**: Configuraci�n compleja con m�ltiples servicios trabajando juntos.

## **Hospedaje en Amazon EC2**
Amazon EC2 (Elastic Compute Cloud) es un servicio de computaci�n que permite hospedar m�quinas virtuales.

### **Pasos para Hospedar la Aplicaci�n**
1. **VPC Predeterminada**:
   - AWS ofrece una red privada para recursos llamada VPC predeterminada.
   - Las instancias EC2 deben estar en una red, y la VPC predeterminada facilita la configuraci�n.

2. **Configuraci�n de la Instancia EC2**:
   - Abre la Consola de Gesti�n de EC2.
   - Selecciona el servicio **Amazon EC2**.
   - Lanza una nueva instancia (**t2.micro**, elegible para el nivel gratuito).
   - Usa **Linux AMI (Amazon Machine Image)** como sistema operativo.
   - **Nombra** la instancia: `employee-web-app`.

3. **Configuraciones de Red**:
   - Selecciona la **VPC Predeterminada**.
   - Deja **Subred**: Sin preferencia.
   - Crea un **Grupo de Seguridad**: Agrega reglas de entrada para permitir tr�fico HTTP y HTTPS.

4. **IAM y Datos de Usuario**:
   - Selecciona la **funci�n IAM** para acceder a los recursos.
   - Proporciona un **script de Datos de Usuario**:
     - Descarga el c�digo fuente de la aplicaci�n.
     - Inicia el servidor web y la aplicaci�n autom�ticamente durante el arranque de la instancia.

5. **Lanza la Instancia**:
   - Configura todas las opciones.
   - Haz clic en **Lanzar Instancia**.
   - Espera a que la instancia se inicie.

### **Accediendo a la Aplicaci�n**
- Copia la **direcci�n IP** de la instancia desde la consola.
- P�gala en un navegador para ver la p�gina principal de la aplicaci�n.

## **Aprendizajes y Pr�ximas Lecciones**
- Comprensi�n detallada de las configuraciones de EC2.
- Conceptos de redes en AWS.
- Integraci�n de la aplicaci�n con bases de datos y otros servicios de AWS.

---
