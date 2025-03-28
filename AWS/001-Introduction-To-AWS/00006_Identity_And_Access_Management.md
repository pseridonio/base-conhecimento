# Gerenciamento de Identidade e Acesso da AWS (IAM)

## Conceitos Principais

### N�veis de Controle de Acesso
1. **N�vel de Aplica��o**: Gerenciar autentica��o de usu�rios dentro de um aplicativo (por exemplo, credenciais de login como nome de usu�rio e senha).
2. **N�vel de Servi�o AWS**:
   - Chamadas de API entre servi�os AWS exigem credenciais assinadas e autenticadas (por exemplo, intera��o EC2 com S3).
   - O IAM fornece ferramentas para gerenciar essas credenciais.

### Autentica��o e Autoriza��o
1. **Autentica��o**: Confirma quem � o usu�rio.
   - Ao criar uma conta AWS, os usu�rios verificam sua identidade usando um endere�o de e-mail e senha.
   - M�todos comuns incluem:
     - Nome de usu�rio e senha
     - Autentica��o baseada em token
     - Dados biom�tricos (por exemplo, impress�es digitais)
   - Responde � pergunta: "Voc� � quem diz ser?"

2. **Autoriza��o**: Concede permiss�o para os recursos AWS.
   - Determina as a��es que um usu�rio pode realizar dentro da AWS, como:
     - Ler, editar, excluir ou criar recursos.
   - Responde � pergunta: "Quais a��es voc� pode realizar?"

## Recursos do IAM

O IAM oferece recursos robustos para aumentar a seguran�a e gerenciar identidades de forma eficaz:

1. **Global**:
   - O IAM � global e n�o espec�fico de nenhuma Regi�o.
   - Configura��es podem ser acessadas e usadas de qualquer Regi�o no Console de Gerenciamento da AWS.

2. **Integrado com Servi�os AWS**:
   - O IAM � integrado com muitos servi�os AWS por padr�o.

3. **Acesso Compartilhado**:
   - Permite conceder permiss�es a outras identidades para administrar e usar recursos AWS sem compartilhar senhas ou chaves.

4. **Autentica��o Multifator (MFA)**:
   - Suporta MFA para seguran�a aprimorada.
   - MFA pode ser adicionada a contas e usu�rios individuais.

5. **Federa��o de Identidade**:
   - Suporta federa��o de identidade, permitindo que usu�rios com senhas em outro lugar (por exemplo, rede corporativa ou provedor de identidade) obtenham acesso tempor�rio �s contas AWS.

6. **Uso Gratuito**:
   - O IAM est� dispon�vel sem custo adicional para clientes AWS.

## Usu�rios e Grupos IAM

### Usu�rios IAM
- Representa uma pessoa ou servi�o que interage com a AWS.
- Definido dentro da sua conta AWS, e sua atividade � cobrada na sua conta.
- Cada usu�rio deve ter credenciais �nicas de login para evitar compartilhamento de credenciais.

#### Credenciais de Usu�rio IAM
- As credenciais consistem em um nome e tipos de acesso:
  - **Acesso ao Console de Gerenciamento da AWS**: Nome de usu�rio e senha.
  - **Acesso Program�tico**: Chaves de acesso para AWS CLI e AWS API.
- As credenciais de usu�rio IAM s�o permanentes at� que administradores imponham uma rota��o.
- Permiss�es podem ser concedidas diretamente no n�vel do usu�rio, mas escalar essa abordagem para muitos usu�rios pode se tornar complexo.

### Grupos IAM
- Cole��o de usu�rios onde todos os membros herdam as permiss�es atribu�das ao grupo.
- Melhor pr�tica: Use grupos para gerenciamento escal�vel de permiss�es.
- Os usu�rios podem ser organizados com base nas fun��es de trabalho (por exemplo, desenvolvedores, engenheiros de seguran�a, administradores).
- Caracter�sticas:
  - Os grupos podem ter muitos usu�rios.
  - Os usu�rios podem pertencer a v�rios grupos.
  - Os grupos n�o podem pertencer a outros grupos.

Exemplos:
1. Adicionar um novo desenvolvedor a um grupo de desenvolvedores para acesso instant�neo.
2. Transferir um usu�rio de um grupo para outro quando seu papel mudar.

## Pol�ticas IAM

### Vis�o Geral
- As pol�ticas IAM s�o documentos JSON usados para gerenciar acesso a servi�os e recursos AWS.
- Pol�ticas s�o avaliadas quando uma identidade IAM faz uma solicita��o para determinar se a a��o deve ser permitida ou negada.

### Elementos
1. **Vers�o**: Especifica a sintaxe da linguagem de pol�tica (por exemplo, "2012-10-17").
2. **Efeito**: `Allow` (Permitir) ou `Deny` (Negar).
3. **A��o**: Descreve a��es permitidas ou negadas (por exemplo, `"s3:*"`).
4. **Recurso**: Especifica os objetos cobertos (por exemplo, `"*"` para todos os recursos).
5. **Condi��o**: Adiciona restri��es adicionais com base em crit�rios.

### Exemplos de Pol�ticas IAM
#### Pol�tica de Administrador:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }]
}
```
- Permite todas as a��es em todos os recursos na conta AWS.

#### Pol�tica Granular:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyS3AccessOutsideMyBoundary",
      "Effect": "Deny",
      "Action": [
        "s3:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceAccount": [
            "222222222222"
          ]
        }
      }
    }
  ]
}
```
- Nega acesso a a��es S3, a menos que os recursos estejam em uma conta AWS confi�vel.

## Fun��es IAM

### Vis�o Geral
- As fun��es IAM s�o identidades que podem ser assumidas por entidades que precisam de acesso tempor�rio a credenciais AWS.
- Fornecem credenciais adquiridas programaticamente, tempor�rias e automaticamente rotacionadas.

### Caracter�sticas
1. **Credenciais AWS Tempor�rias**:
   - As fun��es IAM n�o usam credenciais de login como nomes de usu�rio e senhas.
   - Credenciais fornecidas por fun��es IAM expiram e s�o assumidas programaticamente.
2. **Acesso Baseado em Fun��es**:
   - Fun��es IAM facilitam a comunica��o entre servi�os AWS dentro de uma conta permitindo chamadas de API.
3. **Usu�rios Federados**:
   - Fun��es IAM podem conceder acesso AWS a identidades externas (por exemplo, diret�rios de usu�rios empresariais).
   - O AWS IAM Identity Center suporta essa funcionalidade, permitindo acesso baseado em fun��es para grandes organiza��es.

### Caso de Uso
- Atribuir uma fun��o IAM a uma inst�ncia EC2 permite que aplicativos na inst�ncia assinem chamadas de API para S3 com seguran�a usando credenciais tempor�rias.
- As fun��es IAM tamb�m s�o �teis para permiss�es granulares, conforme mostrado em pol�ticas aplicadas a servi�os como S3 ou DynamoDB.

## Monitoramento e Auditoria

1. **AWS CloudTrail**:
   - Acompanha chamadas de API feitas dentro da sua conta para fins de auditoria e monitoramento.
   - Ajuda a detectar a��es n�o autorizadas e garante conformidade com padr�es de seguran�a.

2. **AWS Config**:
   - Gerencia e monitora configura��es em recursos AWS.
   - Avalia configura��es IAM para garantir que atendam aos requisitos de conformidade.

## Melhores Pr�ticas do IAM

1. **Proteger o Usu�rio Root da AWS**:
   - Evite compartilhar credenciais do usu�rio root.
   - Exclua as chaves de acesso do usu�rio root, sempre que poss�vel.
   - Ative MFA na conta root para maior seguran�a.

2. **Siga o Princ�pio do Menor Privil�gio**:
   - Conceda apenas as permiss�es necess�rias para tarefas espec�ficas.
   - Comece com permiss�es m�nimas em uma pol�tica IAM e adicione permiss�es conforme necess�rio.

3. **Use IAM de Forma Apropriada**:
   - IAM foi projetado para proteger o acesso a contas AWS e recursos.
   - Evite usar IAM para autentica��o de sites ou seguran�a de sistemas operacionais/redes.

4. **Use Fun��es IAM Sempre que Poss�vel**:
   - Fun��es fornecem credenciais tempor�rias, tornando-as mais seguras e eficientes do que credenciais de usu�rio IAM de longo prazo.
   - Credenciais de fun��es s�o rotacionadas programaticamente e expiram entre 15 minutos a 36 horas.

5. **Considere Usar um Provedor de Identidade**:
   - Use provedores de identidade (por exemplo, AWS IAM Identity Center ou IdPs de terceiros) para gerenciamento centralizado de identidade.
   - Isso elimina a necessidade de criar usu�rios IAM individuais para cada pessoa ou entidade que precisa de acesso.

6. **Revise Regularmente e Remova Credenciais N�o Utilizadas**:
   - Use informa��es de "�ltimo acesso" do IAM para identificar usu�rios, fun��es, permiss�es ou pol�ticas n�o utilizadas.
   - Remova credenciais n�o utilizadas para minimizar riscos de seguran�a.

## Recursos

Para mais informa��es, consulte os seguintes recursos:

- [AWS user guide: What Is IAM?](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/introduction.html)
- [AWS user guide: IAM Identities (User, User Groups, and Roles)](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/id.html)
- [AWS user guide: Access Management for AWS Resources](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/access.html)
- [AWS user guide: Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS blog: How to Create and Manage Users within AWS IAM Identity Center](https://aws.amazon.com/blogs/security/how-to-create-and-manage-users-within-aws-sso/)

---

# AWS Identity and Access Management (IAM)

## Key Concepts

### Access Control Levels
1. **Application Level**: Manage user authentication within an app (e.g., login credentials like username and password).
2. **AWS Service Level**:
   - API calls between AWS services require signed and authenticated credentials (e.g., EC2 to S3 interaction).
   - IAM provides tools to manage these credentials.

### Authentication and Authorization
1. **Authentication**: Confirms who the user is.
   - When creating an AWS account, users verify their identity using an email address and password.
   - Common methods include:
     - Username and password
     - Token-based authentication
     - Biometric data (e.g., fingerprints)
   - Answers the question: �Are you who you say you are?�

2. **Authorization**: Grants permission to AWS resources.
   - Determines actions a user can perform within AWS, such as:
     - Reading, editing, deleting, or creating resources.
   - Answers the question: �What actions can you perform?�

## IAM Features

IAM offers robust features to enhance security and manage identities effectively:

1. **Global**:
   - IAM is global and not specific to any one Region.
   - Configurations can be accessed and used from any Region in the AWS Management Console.

2. **Integrated with AWS Services**:
   - IAM is integrated with many AWS services by default.

3. **Shared Access**:
   - Allows granting other identities permission to administer and use AWS resources without sharing passwords or keys.

4. **Multi-factor Authentication (MFA)**:
   - Supports MFA for enhanced security.
   - MFA can be added to accounts and individual users.

5. **Identity Federation**:
   - Supports identity federation, enabling users with passwords elsewhere (e.g., corporate network or identity provider) to gain temporary access to AWS accounts.

6. **Free to Use**:
   - IAM is available at no additional charge to AWS customers.

## IAM Users and Groups

### IAM Users
- Represents a person or service that interacts with AWS.
- Defined within your AWS account, and their activity is billed to your account.
- Each user should have their own unique login credentials to prevent credential sharing.

#### IAM User Credentials
- Credentials consist of a name and access types:
  - **AWS Management Console Access**: Username and password.
  - **Programmatic Access**: Access keys for AWS CLI and AWS API.
- IAM user credentials are permanent until administrators enforce a rotation.
- Permissions can be granted directly at the user level, but scaling this approach for many users can become complex.

### IAM Groups
- A collection of users where all members inherit the permissions assigned to the group.
- Best practice: Use groups for scalable permission management.
- Users can be organized based on job functions (e.g., developers, security engineers, admins).
- Features:
  - Groups can have many users.
  - Users can belong to multiple groups.
  - Groups cannot belong to other groups.

Examples:
1. Adding a new developer to a developer group for instant access.
2. Transitioning a user from one group to another when their role changes.

## IAM Policies

### Overview
- IAM policies are JSON-based documents used to manage access to AWS services and resources.
- Policies are evaluated when an IAM identity makes a request to determine whether the action should be allowed or denied.

### Elements
1. **Version**: Specifies policy language syntax (e.g., "2012-10-17").
2. **Effect**: `Allow` or `Deny`.
3. **Action**: Describes allowed or denied actions (e.g., `"s3:*"`).
4. **Resource**: Specifies the objects covered (e.g., `"*"` for all resources).
5. **Condition**: Adds additional restrictions based on criteria.

### IAM Policy Examples
#### Administrator Policy:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }]
}
```
- Allows all actions on all resources in the AWS account.

#### Granular Policy:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyS3AccessOutsideMyBoundary",
      "Effect": "Deny",
      "Action": [
        "s3:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceAccount": [
            "222222222222"
          ]
        }
      }
    }
  ]
}
```
- Denies access to S3 actions unless the resources are in a trusted AWS account.

## IAM Roles

### Overview
- IAM roles are identities that can be assumed by entities needing temporary access to AWS credentials.
- Provide programmatically acquired, temporary credentials for signing API calls, which are automatically rotated.

### Features
1. **Temporary AWS Credentials**:
   - IAM roles do not use login credentials like usernames and passwords.
   - Credentials provided by IAM roles expire and are assumed programmatically.
2. **Role-Based Access**:
   - IAM roles facilitate communication between AWS services within an account by enabling API calls.
3. **Federated Users**:
   - IAM roles can grant AWS access to external identities (e.g., enterprise user directories).
   - AWS IAM Identity Center supports this feature, enabling role-based access for large organizations.

### Example Use Case
- Assigning an IAM role to an EC2 instance allows applications running on the instance to sign API calls to S3 securely using temporary credentials.
- IAM roles are also useful for granular permissions, as shown in example policies applied to services like S3 or DynamoDB.

## Monitoring and Auditing

1. **AWS CloudTrail**:
   - Tracks API calls made within your account for auditing and monitoring purposes.
   - Helps detect unauthorized actions and ensures compliance with security standards.

2. **AWS Config**:
   - Manages and monitors configurations across AWS resources.
   - Evaluates IAM configurations to ensure they meet compliance requirements.

## IAM Best Practices

1. **Lock Down the AWS Root User**:
   - Avoid sharing root user credentials.
   - Delete root user access keys where possible.
   - Activate MFA on the root account for extra security.

2. **Follow the Principle of Least Privilege**:
   - Grant only the permissions required for specific tasks.
   - Start with minimal permissions in an IAM policy and add permissions as necessary.

3. **Use IAM Appropriately**:
   - IAM is designed to secure access to AWS accounts and resources.
   - Avoid using IAM for website authentication or operating system/network security.

4. **Use IAM Roles When Possible**:
   - Roles provide temporary credentials, making them more secure and efficient than long-term IAM user credentials.
   - Credentials for roles are programmatically rotated and expire between 15 minutes to 36 hours.

5. **Consider Using an Identity Provider**:
   - Use identity providers (e.g., AWS IAM Identity Center or third-party IdPs) for centralized identity management.
   - This eliminates the need for creating individual IAM users for each person or entity requiring access.

6. **Regularly Review and Remove Unused Credentials**:
   - Use IAM's "last accessed" information to identify unused users, roles, permissions, or policies.
   - Remove unused credentials to minimize security risks.

## Resources

For more information, see the following resources:

- [AWS user guide: What Is IAM?](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/introduction.html)
- [AWS user guide: IAM Identities (User, User Groups, and Roles)](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/id.html)
- [AWS user guide: Access Management for AWS Resources](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/access.html)
- [AWS user guide: Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS blog: How to Create and Manage Users within AWS IAM Identity Center](https://aws.amazon.com/blogs/security/how-to-create-and-manage-users-within-aws-sso/)

---

�Por supuesto! Aqu� tienes la versi�n en espa�ol del markdown:

---

# Administraci�n de Identidades y Accesos de AWS (IAM)

## Conceptos Clave

### Niveles de Control de Acceso
1. **Nivel de Aplicaci�n**: Gesti�n de la autenticaci�n de usuarios dentro de una aplicaci�n (por ejemplo, credenciales de inicio de sesi�n como nombre de usuario y contrase�a).
2. **Nivel de Servicio AWS**:
   - Las llamadas a la API entre servicios de AWS requieren credenciales firmadas y autenticadas (por ejemplo, interacci�n de EC2 con S3).
   - IAM proporciona herramientas para gestionar estas credenciales.

### Autenticaci�n y Autorizaci�n
1. **Autenticaci�n**: Confirma qui�n es el usuario.
   - Al crear una cuenta de AWS, los usuarios verifican su identidad usando una direcci�n de correo electr�nico y una contrase�a.
   - M�todos comunes incluyen:
     - Nombre de usuario y contrase�a.
     - Autenticaci�n basada en tokens.
     - Datos biom�tricos (por ejemplo, huellas dactilares).
   - Responde a la pregunta: "�Eres quien dices ser?"

2. **Autorizaci�n**: Otorga permisos para los recursos de AWS.
   - Determina las acciones que un usuario puede realizar dentro de AWS, como:
     - Leer, editar, eliminar o crear recursos.
   - Responde a la pregunta: "�Qu� acciones puedes realizar?"

## Funciones Principales de IAM

IAM ofrece funciones robustas para mejorar la seguridad y gestionar identidades de forma eficiente:

1. **Global**:
   - IAM es global y no espec�fico de ninguna regi�n.
   - Las configuraciones pueden accederse y utilizarse desde cualquier regi�n en la consola de administraci�n de AWS.

2. **Integrado con Servicios AWS**:
   - IAM est� integrado por defecto con muchos servicios de AWS.

3. **Acceso Compartido**:
   - Permite otorgar permisos a otras identidades para administrar y usar recursos de AWS sin compartir contrase�as o claves.

4. **Autenticaci�n Multifactor (MFA)**:
   - Soporta MFA para aumentar la seguridad.
   - MFA puede a�adirse a cuentas y usuarios individuales.

5. **Federaci�n de Identidades**:
   - Soporta la federaci�n de identidades, permitiendo que los usuarios con credenciales en otros lugares (por ejemplo, redes corporativas o proveedores de identidad) obtengan acceso temporal a las cuentas de AWS.

6. **Uso Gratuito**:
   - IAM est� disponible sin costo adicional para los clientes de AWS.

## Usuarios y Grupos de IAM

### Usuarios IAM
- Representa a una persona o servicio que interact�a con AWS.
- Definido dentro de tu cuenta de AWS, y su actividad se factura a tu cuenta.
- Cada usuario debe tener credenciales de inicio de sesi�n �nicas para evitar el intercambio de credenciales.

#### Credenciales de Usuario IAM
- Las credenciales consisten en un nombre y tipos de acceso:
  - **Acceso a la Consola de Administraci�n de AWS**: Nombre de usuario y contrase�a.
  - **Acceso Program�tico**: Claves de acceso para AWS CLI y AWS API.
- Las credenciales de usuario IAM son permanentes hasta que los administradores imponen una rotaci�n.
- Las autorizaciones pueden otorgarse directamente a nivel de usuario, pero escalar este enfoque para muchos usuarios puede volverse complejo.

### Grupos IAM
- Colecci�n de usuarios donde todos los miembros heredan los permisos asignados al grupo.
- Mejor pr�ctica: Usar grupos para la gesti�n escalable de permisos.
- Los usuarios pueden organizarse seg�n sus funciones laborales (por ejemplo, desarrolladores, ingenieros de seguridad, administradores).
- Caracter�sticas:
  - Los grupos pueden contener m�ltiples usuarios.
  - Los usuarios pueden pertenecer a m�ltiples grupos.
  - Los grupos no pueden pertenecer a otros grupos.

Ejemplos:
1. Agregar un nuevo desarrollador a un grupo de desarrolladores para acceso instant�neo.
2. Transferir un usuario de un grupo a otro cuando su funci�n cambia.

## Pol�ticas de IAM

### Visi�n General
- Las pol�ticas de IAM son documentos JSON usados para gestionar el acceso a servicios y recursos de AWS.
- Las pol�ticas se eval�an cuando una identidad de IAM realiza una solicitud para determinar si la acci�n debe permitirse o denegarse.

### Elementos
1. **Versi�n**: Especifica la sintaxis del lenguaje de la pol�tica (por ejemplo, "2012-10-17").
2. **Efecto**: `Allow` (Permitir) o `Deny` (Denegar).
3. **Acci�n**: Describe las acciones permitidas o denegadas (por ejemplo, `"s3:*"`).
4. **Recurso**: Especifica los objetos cubiertos (por ejemplo, `"*"` para todos los recursos).
5. **Condici�n**: Agrega restricciones adicionales basadas en criterios.

### Ejemplos de Pol�ticas de IAM
#### Pol�tica de Administrador:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }]
}
```
- Permite todas las acciones en todos los recursos de la cuenta de AWS.

#### Pol�tica Granular:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyS3AccessOutsideMyBoundary",
      "Effect": "Deny",
      "Action": [
        "s3:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:ResourceAccount": [
            "222222222222"
          ]
        }
      }
    }
  ]
}
```
- Niega el acceso a acciones de S3 a menos que los recursos est�n en una cuenta confiable de AWS.

## Roles de IAM

### Visi�n General
- Los roles de IAM son identidades que pueden ser asumidas por entidades que necesitan acceso temporal a credenciales de AWS.
- Proporcionan credenciales adquiridas program�ticamente, temporales y rotadas autom�ticamente.

### Caracter�sticas
1. **Credenciales Temporales de AWS**:
   - Los roles de IAM no utilizan credenciales de inicio de sesi�n como nombres de usuario y contrase�as.
   - Las credenciales proporcionadas por los roles de IAM expiran y son asumidas program�ticamente.

2. **Acceso Basado en Roles**:
   - Los roles de IAM facilitan la comunicaci�n entre servicios de AWS dentro de una cuenta permitiendo llamadas a la API.

3. **Usuarios Federados**:
   - Los roles de IAM pueden otorgar acceso a AWS a identidades externas (por ejemplo, directorios de usuarios empresariales).
   - AWS IAM Identity Center soporta esta funcionalidad, permitiendo acceso basado en roles para grandes organizaciones.

### Caso de Uso
- Asignar un rol de IAM a una instancia EC2 permite que las aplicaciones en la instancia firmen llamadas a la API para S3 de forma segura usando credenciales temporales.
- Los roles de IAM tambi�n son �tiles para permisos granulares, como se muestra en las pol�ticas aplicadas a servicios como S3 o DynamoDB.

## Monitoreo y Auditor�a

1. **AWS CloudTrail**:
   - Rastrea las llamadas a la API realizadas dentro de tu cuenta para auditor�a y monitoreo.
   - Ayuda a detectar acciones no autorizadas y garantiza el cumplimiento de est�ndares de seguridad.

2. **AWS Config**:
   - Gestiona y supervisa configuraciones en recursos de AWS.
   - Eval�a configuraciones de IAM para garantizar que cumplan con los requisitos de conformidad.

## Mejores Pr�cticas de IAM

1. **Proteger al Usuario Root de AWS**:
   - Evitar compartir las credenciales del usuario root.
   - Eliminar las claves de acceso del usuario root, siempre que sea posible.
   - Activar MFA en la cuenta root para mayor seguridad.

2. **Seguir el Principio de Menor Privilegio**:
   - Conceder solo los permisos necesarios para tareas espec�ficas.
   - Comenzar con permisos m�nimos en una pol�tica de IAM y agregar permisos seg�n sea necesario.

3. **Usar IAM de Forma Apropiada**:
   - IAM est� dise�ado para proteger el acceso a cuentas y recursos de AWS.
   - Evitar usar IAM para autenticaci�n de sitios web o seguridad de sistemas operativos/redes.

4. **Usar Roles de IAM Siempre que Sea Posible**:
   - Los roles proporcionan credenciales temporales, haci�ndolos m�s seguros y eficientes que las credenciales de usuarios IAM a largo plazo.

5. **Considerar Usar un Proveedor de Identidad**:
   - Usar proveedores de identidad (por ejemplo, AWS IAM Identity Center o proveedores de identidad externos) para una gesti�n centralizada de identidades.

6. **Revisar Regularmente y Eliminar Credenciales No Utilizadas**:
   - Identificar usuarios, roles, permisos o pol�ticas no utilizadas utilizando informaci�n de "�ltimo acceso" en IAM.

## Recursos

Para m�s informaci�n, consulta los siguientes recursos:

- [AWS user guide: What Is IAM?](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/introduction.html)
- [AWS user guide: IAM Identities (User, User Groups, and Roles)](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/id.html)
- [AWS user guide: Access Management for AWS Resources](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/access.html)
- [AWS user guide: Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS blog: How to Create and Manage Users within AWS IAM Identity Center](https://aws.amazon.com/blogs/security/how-to-create-and-manage-users-within-aws-sso/)