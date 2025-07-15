# Hospedando um Aplicativo de Diretório de Funcionários na AWS

## **Visão Geral dos Conceitos da AWS**
- **Vocabulário da AWS**: Termos-chave e conceitos de computação em nuvem.
- **Gerenciamento de Identidade e Acesso em Nuvem**: Fornece acesso seguro aos recursos.
- **Diagrama de Arquitetura**: Configuração complexa com vários serviços trabalhando juntos.

## **Hospedagem no Amazon EC2**
Amazon EC2 (Elastic Compute Cloud) é um serviço de computação que permite hospedar máquinas virtuais.

### **Passos para Hospedar o Aplicativo**
1. **VPC Padrão**:
   - A AWS fornece uma rede privada para recursos chamada VPC padrão.
   - Instâncias EC2 devem estar em uma rede, e a VPC padrão facilita a configuração.

2. **Configurando a Instância EC2**:
   - Abra o Console de Gerenciamento da EC2.
   - Selecione o serviço **Amazon EC2**.
   - Inicie uma nova instância (**t2.micro**, elegível no nível gratuito).
   - Use o **Linux AMI (Amazon Machine Image)** como sistema operacional.
   - **Nomeie** a instância: `employee-web-app`.

3. **Configurações de Rede**:
   - Selecione a **VPC Padrão**.
   - Deixe **Sub-rede**: Sem preferência.
   - Crie um **Grupo de Segurança**: Adicione regras de entrada para permitir tráfego HTTP e HTTPS.

4. **IAM e Dados do Usuário**:
   - Selecione a **função IAM** para acessar os recursos.
   - Forneça um **script de Dados do Usuário**:
     - Faça o download do código-fonte do aplicativo.
     - Inicie o servidor web e o aplicativo automaticamente durante a inicialização da instância.

5. **Inicie a Instância**:
   - Configure todas as opções.
   - Clique em **Lançar Instância**.
   - Aguarde a inicialização da instância.

### **Acessando o Aplicativo**
- Copie o **endereço IP** da instância do console.
- Cole em um navegador para visualizar a página inicial do aplicativo.

## **Aprendizados e Próximas Aulas**
- Compreensão detalhada das configurações do EC2.
- Conceitos de rede na AWS.
- Integração do aplicativo com bancos de dados e outros serviços da AWS.

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

# Hospedaje de una Aplicación de Directorio de Empleados en AWS

## **Descripción General de Conceptos de AWS**
- **Vocabulario de AWS**: Términos clave y conceptos de computación en la nube.
- **Gestión de Identidad y Acceso en la Nube**: Proporciona acceso seguro a los recursos.
- **Diagrama de Arquitectura**: Configuración compleja con múltiples servicios trabajando juntos.

## **Hospedaje en Amazon EC2**
Amazon EC2 (Elastic Compute Cloud) es un servicio de computación que permite hospedar máquinas virtuales.

### **Pasos para Hospedar la Aplicación**
1. **VPC Predeterminada**:
   - AWS ofrece una red privada para recursos llamada VPC predeterminada.
   - Las instancias EC2 deben estar en una red, y la VPC predeterminada facilita la configuración.

2. **Configuración de la Instancia EC2**:
   - Abre la Consola de Gestión de EC2.
   - Selecciona el servicio **Amazon EC2**.
   - Lanza una nueva instancia (**t2.micro**, elegible para el nivel gratuito).
   - Usa **Linux AMI (Amazon Machine Image)** como sistema operativo.
   - **Nombra** la instancia: `employee-web-app`.

3. **Configuraciones de Red**:
   - Selecciona la **VPC Predeterminada**.
   - Deja **Subred**: Sin preferencia.
   - Crea un **Grupo de Seguridad**: Agrega reglas de entrada para permitir tráfico HTTP y HTTPS.

4. **IAM y Datos de Usuario**:
   - Selecciona la **función IAM** para acceder a los recursos.
   - Proporciona un **script de Datos de Usuario**:
     - Descarga el código fuente de la aplicación.
     - Inicia el servidor web y la aplicación automáticamente durante el arranque de la instancia.

5. **Lanza la Instancia**:
   - Configura todas las opciones.
   - Haz clic en **Lanzar Instancia**.
   - Espera a que la instancia se inicie.

### **Accediendo a la Aplicación**
- Copia la **dirección IP** de la instancia desde la consola.
- Pégala en un navegador para ver la página principal de la aplicación.

## **Aprendizajes y Próximas Lecciones**
- Comprensión detallada de las configuraciones de EC2.
- Conceptos de redes en AWS.
- Integración de la aplicación con bases de datos y otros servicios de AWS.

---
