# Gerenciamento de Identidade e Acesso da AWS (IAM)

## Conceitos Principais

### Níveis de Controle de Acesso
1. **Nível de Aplicação**: Gerenciar autenticação de usuários dentro de um aplicativo (por exemplo, credenciais de login como nome de usuário e senha).
2. **Nível de Serviço AWS**:
   - Chamadas de API entre serviços AWS exigem credenciais assinadas e autenticadas (por exemplo, interação EC2 com S3).
   - O IAM fornece ferramentas para gerenciar essas credenciais.

### Autenticação e Autorização
1. **Autenticação**: Confirma quem é o usuário.
   - Ao criar uma conta AWS, os usuários verificam sua identidade usando um endereço de e-mail e senha.
   - Métodos comuns incluem:
     - Nome de usuário e senha
     - Autenticação baseada em token
     - Dados biométricos (por exemplo, impressões digitais)
   - Responde à pergunta: "Você é quem diz ser?"

2. **Autorização**: Concede permissão para os recursos AWS.
   - Determina as ações que um usuário pode realizar dentro da AWS, como:
     - Ler, editar, excluir ou criar recursos.
   - Responde à pergunta: "Quais ações você pode realizar?"

## Recursos do IAM

O IAM oferece recursos robustos para aumentar a segurança e gerenciar identidades de forma eficaz:

1. **Global**:
   - O IAM é global e não específico de nenhuma Região.
   - Configurações podem ser acessadas e usadas de qualquer Região no Console de Gerenciamento da AWS.

2. **Integrado com Serviços AWS**:
   - O IAM é integrado com muitos serviços AWS por padrão.

3. **Acesso Compartilhado**:
   - Permite conceder permissões a outras identidades para administrar e usar recursos AWS sem compartilhar senhas ou chaves.

4. **Autenticação Multifator (MFA)**:
   - Suporta MFA para segurança aprimorada.
   - MFA pode ser adicionada a contas e usuários individuais.

5. **Federação de Identidade**:
   - Suporta federação de identidade, permitindo que usuários com senhas em outro lugar (por exemplo, rede corporativa ou provedor de identidade) obtenham acesso temporário às contas AWS.

6. **Uso Gratuito**:
   - O IAM está disponível sem custo adicional para clientes AWS.

## Usuários e Grupos IAM

### Usuários IAM
- Representa uma pessoa ou serviço que interage com a AWS.
- Definido dentro da sua conta AWS, e sua atividade é cobrada na sua conta.
- Cada usuário deve ter credenciais únicas de login para evitar compartilhamento de credenciais.

#### Credenciais de Usuário IAM
- As credenciais consistem em um nome e tipos de acesso:
  - **Acesso ao Console de Gerenciamento da AWS**: Nome de usuário e senha.
  - **Acesso Programático**: Chaves de acesso para AWS CLI e AWS API.
- As credenciais de usuário IAM são permanentes até que administradores imponham uma rotação.
- Permissões podem ser concedidas diretamente no nível do usuário, mas escalar essa abordagem para muitos usuários pode se tornar complexo.

### Grupos IAM
- Coleção de usuários onde todos os membros herdam as permissões atribuídas ao grupo.
- Melhor prática: Use grupos para gerenciamento escalável de permissões.
- Os usuários podem ser organizados com base nas funções de trabalho (por exemplo, desenvolvedores, engenheiros de segurança, administradores).
- Características:
  - Os grupos podem ter muitos usuários.
  - Os usuários podem pertencer a vários grupos.
  - Os grupos não podem pertencer a outros grupos.

Exemplos:
1. Adicionar um novo desenvolvedor a um grupo de desenvolvedores para acesso instantâneo.
2. Transferir um usuário de um grupo para outro quando seu papel mudar.

## Políticas IAM

### Visão Geral
- As políticas IAM são documentos JSON usados para gerenciar acesso a serviços e recursos AWS.
- Políticas são avaliadas quando uma identidade IAM faz uma solicitação para determinar se a ação deve ser permitida ou negada.

### Elementos
1. **Versão**: Especifica a sintaxe da linguagem de política (por exemplo, "2012-10-17").
2. **Efeito**: `Allow` (Permitir) ou `Deny` (Negar).
3. **Ação**: Descreve ações permitidas ou negadas (por exemplo, `"s3:*"`).
4. **Recurso**: Especifica os objetos cobertos (por exemplo, `"*"` para todos os recursos).
5. **Condição**: Adiciona restrições adicionais com base em critérios.

### Exemplos de Políticas IAM
#### Política de Administrador:
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
- Permite todas as ações em todos os recursos na conta AWS.

#### Política Granular:
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
- Nega acesso a ações S3, a menos que os recursos estejam em uma conta AWS confiável.

## Funções IAM

### Visão Geral
- As funções IAM são identidades que podem ser assumidas por entidades que precisam de acesso temporário a credenciais AWS.
- Fornecem credenciais adquiridas programaticamente, temporárias e automaticamente rotacionadas.

### Características
1. **Credenciais AWS Temporárias**:
   - As funções IAM não usam credenciais de login como nomes de usuário e senhas.
   - Credenciais fornecidas por funções IAM expiram e são assumidas programaticamente.
2. **Acesso Baseado em Funções**:
   - Funções IAM facilitam a comunicação entre serviços AWS dentro de uma conta permitindo chamadas de API.
3. **Usuários Federados**:
   - Funções IAM podem conceder acesso AWS a identidades externas (por exemplo, diretórios de usuários empresariais).
   - O AWS IAM Identity Center suporta essa funcionalidade, permitindo acesso baseado em funções para grandes organizações.

### Caso de Uso
- Atribuir uma função IAM a uma instância EC2 permite que aplicativos na instância assinem chamadas de API para S3 com segurança usando credenciais temporárias.
- As funções IAM também são úteis para permissões granulares, conforme mostrado em políticas aplicadas a serviços como S3 ou DynamoDB.

## Monitoramento e Auditoria

1. **AWS CloudTrail**:
   - Acompanha chamadas de API feitas dentro da sua conta para fins de auditoria e monitoramento.
   - Ajuda a detectar ações não autorizadas e garante conformidade com padrões de segurança.

2. **AWS Config**:
   - Gerencia e monitora configurações em recursos AWS.
   - Avalia configurações IAM para garantir que atendam aos requisitos de conformidade.

## Melhores Práticas do IAM

1. **Proteger o Usuário Root da AWS**:
   - Evite compartilhar credenciais do usuário root.
   - Exclua as chaves de acesso do usuário root, sempre que possível.
   - Ative MFA na conta root para maior segurança.

2. **Siga o Princípio do Menor Privilégio**:
   - Conceda apenas as permissões necessárias para tarefas específicas.
   - Comece com permissões mínimas em uma política IAM e adicione permissões conforme necessário.

3. **Use IAM de Forma Apropriada**:
   - IAM foi projetado para proteger o acesso a contas AWS e recursos.
   - Evite usar IAM para autenticação de sites ou segurança de sistemas operacionais/redes.

4. **Use Funções IAM Sempre que Possível**:
   - Funções fornecem credenciais temporárias, tornando-as mais seguras e eficientes do que credenciais de usuário IAM de longo prazo.
   - Credenciais de funções são rotacionadas programaticamente e expiram entre 15 minutos a 36 horas.

5. **Considere Usar um Provedor de Identidade**:
   - Use provedores de identidade (por exemplo, AWS IAM Identity Center ou IdPs de terceiros) para gerenciamento centralizado de identidade.
   - Isso elimina a necessidade de criar usuários IAM individuais para cada pessoa ou entidade que precisa de acesso.

6. **Revise Regularmente e Remova Credenciais Não Utilizadas**:
   - Use informações de "último acesso" do IAM para identificar usuários, funções, permissões ou políticas não utilizadas.
   - Remova credenciais não utilizadas para minimizar riscos de segurança.

## Recursos

Para mais informações, consulte os seguintes recursos:

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
   - Answers the question: “Are you who you say you are?”

2. **Authorization**: Grants permission to AWS resources.
   - Determines actions a user can perform within AWS, such as:
     - Reading, editing, deleting, or creating resources.
   - Answers the question: “What actions can you perform?”

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

¡Por supuesto! Aquí tienes la versión en español del markdown:

---

# Administración de Identidades y Accesos de AWS (IAM)

## Conceptos Clave

### Niveles de Control de Acceso
1. **Nivel de Aplicación**: Gestión de la autenticación de usuarios dentro de una aplicación (por ejemplo, credenciales de inicio de sesión como nombre de usuario y contraseña).
2. **Nivel de Servicio AWS**:
   - Las llamadas a la API entre servicios de AWS requieren credenciales firmadas y autenticadas (por ejemplo, interacción de EC2 con S3).
   - IAM proporciona herramientas para gestionar estas credenciales.

### Autenticación y Autorización
1. **Autenticación**: Confirma quién es el usuario.
   - Al crear una cuenta de AWS, los usuarios verifican su identidad usando una dirección de correo electrónico y una contraseña.
   - Métodos comunes incluyen:
     - Nombre de usuario y contraseña.
     - Autenticación basada en tokens.
     - Datos biométricos (por ejemplo, huellas dactilares).
   - Responde a la pregunta: "¿Eres quien dices ser?"

2. **Autorización**: Otorga permisos para los recursos de AWS.
   - Determina las acciones que un usuario puede realizar dentro de AWS, como:
     - Leer, editar, eliminar o crear recursos.
   - Responde a la pregunta: "¿Qué acciones puedes realizar?"

## Funciones Principales de IAM

IAM ofrece funciones robustas para mejorar la seguridad y gestionar identidades de forma eficiente:

1. **Global**:
   - IAM es global y no específico de ninguna región.
   - Las configuraciones pueden accederse y utilizarse desde cualquier región en la consola de administración de AWS.

2. **Integrado con Servicios AWS**:
   - IAM está integrado por defecto con muchos servicios de AWS.

3. **Acceso Compartido**:
   - Permite otorgar permisos a otras identidades para administrar y usar recursos de AWS sin compartir contraseñas o claves.

4. **Autenticación Multifactor (MFA)**:
   - Soporta MFA para aumentar la seguridad.
   - MFA puede añadirse a cuentas y usuarios individuales.

5. **Federación de Identidades**:
   - Soporta la federación de identidades, permitiendo que los usuarios con credenciales en otros lugares (por ejemplo, redes corporativas o proveedores de identidad) obtengan acceso temporal a las cuentas de AWS.

6. **Uso Gratuito**:
   - IAM está disponible sin costo adicional para los clientes de AWS.

## Usuarios y Grupos de IAM

### Usuarios IAM
- Representa a una persona o servicio que interactúa con AWS.
- Definido dentro de tu cuenta de AWS, y su actividad se factura a tu cuenta.
- Cada usuario debe tener credenciales de inicio de sesión únicas para evitar el intercambio de credenciales.

#### Credenciales de Usuario IAM
- Las credenciales consisten en un nombre y tipos de acceso:
  - **Acceso a la Consola de Administración de AWS**: Nombre de usuario y contraseña.
  - **Acceso Programático**: Claves de acceso para AWS CLI y AWS API.
- Las credenciales de usuario IAM son permanentes hasta que los administradores imponen una rotación.
- Las autorizaciones pueden otorgarse directamente a nivel de usuario, pero escalar este enfoque para muchos usuarios puede volverse complejo.

### Grupos IAM
- Colección de usuarios donde todos los miembros heredan los permisos asignados al grupo.
- Mejor práctica: Usar grupos para la gestión escalable de permisos.
- Los usuarios pueden organizarse según sus funciones laborales (por ejemplo, desarrolladores, ingenieros de seguridad, administradores).
- Características:
  - Los grupos pueden contener múltiples usuarios.
  - Los usuarios pueden pertenecer a múltiples grupos.
  - Los grupos no pueden pertenecer a otros grupos.

Ejemplos:
1. Agregar un nuevo desarrollador a un grupo de desarrolladores para acceso instantáneo.
2. Transferir un usuario de un grupo a otro cuando su función cambia.

## Políticas de IAM

### Visión General
- Las políticas de IAM son documentos JSON usados para gestionar el acceso a servicios y recursos de AWS.
- Las políticas se evalúan cuando una identidad de IAM realiza una solicitud para determinar si la acción debe permitirse o denegarse.

### Elementos
1. **Versión**: Especifica la sintaxis del lenguaje de la política (por ejemplo, "2012-10-17").
2. **Efecto**: `Allow` (Permitir) o `Deny` (Denegar).
3. **Acción**: Describe las acciones permitidas o denegadas (por ejemplo, `"s3:*"`).
4. **Recurso**: Especifica los objetos cubiertos (por ejemplo, `"*"` para todos los recursos).
5. **Condición**: Agrega restricciones adicionales basadas en criterios.

### Ejemplos de Políticas de IAM
#### Política de Administrador:
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

#### Política Granular:
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
- Niega el acceso a acciones de S3 a menos que los recursos estén en una cuenta confiable de AWS.

## Roles de IAM

### Visión General
- Los roles de IAM son identidades que pueden ser asumidas por entidades que necesitan acceso temporal a credenciales de AWS.
- Proporcionan credenciales adquiridas programáticamente, temporales y rotadas automáticamente.

### Características
1. **Credenciales Temporales de AWS**:
   - Los roles de IAM no utilizan credenciales de inicio de sesión como nombres de usuario y contraseñas.
   - Las credenciales proporcionadas por los roles de IAM expiran y son asumidas programáticamente.

2. **Acceso Basado en Roles**:
   - Los roles de IAM facilitan la comunicación entre servicios de AWS dentro de una cuenta permitiendo llamadas a la API.

3. **Usuarios Federados**:
   - Los roles de IAM pueden otorgar acceso a AWS a identidades externas (por ejemplo, directorios de usuarios empresariales).
   - AWS IAM Identity Center soporta esta funcionalidad, permitiendo acceso basado en roles para grandes organizaciones.

### Caso de Uso
- Asignar un rol de IAM a una instancia EC2 permite que las aplicaciones en la instancia firmen llamadas a la API para S3 de forma segura usando credenciales temporales.
- Los roles de IAM también son útiles para permisos granulares, como se muestra en las políticas aplicadas a servicios como S3 o DynamoDB.

## Monitoreo y Auditoría

1. **AWS CloudTrail**:
   - Rastrea las llamadas a la API realizadas dentro de tu cuenta para auditoría y monitoreo.
   - Ayuda a detectar acciones no autorizadas y garantiza el cumplimiento de estándares de seguridad.

2. **AWS Config**:
   - Gestiona y supervisa configuraciones en recursos de AWS.
   - Evalúa configuraciones de IAM para garantizar que cumplan con los requisitos de conformidad.

## Mejores Prácticas de IAM

1. **Proteger al Usuario Root de AWS**:
   - Evitar compartir las credenciales del usuario root.
   - Eliminar las claves de acceso del usuario root, siempre que sea posible.
   - Activar MFA en la cuenta root para mayor seguridad.

2. **Seguir el Principio de Menor Privilegio**:
   - Conceder solo los permisos necesarios para tareas específicas.
   - Comenzar con permisos mínimos en una política de IAM y agregar permisos según sea necesario.

3. **Usar IAM de Forma Apropiada**:
   - IAM está diseñado para proteger el acceso a cuentas y recursos de AWS.
   - Evitar usar IAM para autenticación de sitios web o seguridad de sistemas operativos/redes.

4. **Usar Roles de IAM Siempre que Sea Posible**:
   - Los roles proporcionan credenciales temporales, haciéndolos más seguros y eficientes que las credenciales de usuarios IAM a largo plazo.

5. **Considerar Usar un Proveedor de Identidad**:
   - Usar proveedores de identidad (por ejemplo, AWS IAM Identity Center o proveedores de identidad externos) para una gestión centralizada de identidades.

6. **Revisar Regularmente y Eliminar Credenciales No Utilizadas**:
   - Identificar usuarios, roles, permisos o políticas no utilizadas utilizando información de "último acceso" en IAM.

## Recursos

Para más información, consulta los siguientes recursos:

- [AWS user guide: What Is IAM?](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/introduction.html)
- [AWS user guide: IAM Identities (User, User Groups, and Roles)](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/id.html)
- [AWS user guide: Access Management for AWS Resources](https://docs.aws.amazon.com/en_us/IAM/latest/UserGuide/access.html)
- [AWS user guide: Security Best Practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS blog: How to Create and Manage Users within AWS IAM Identity Center](https://aws.amazon.com/blogs/security/how-to-create-and-manage-users-within-aws-sso/)