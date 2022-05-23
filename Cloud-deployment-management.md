# Cloud deployment management

#### Google Cloud Deployment Manager es un servicio de implementación de infraestructura que automatiza la creación y la administración de recursos de Google Cloud. Escribe plantillas flexibles y archivos de configuración y úsalos para crear implementaciones que tengan una variedad de servicios de Google Cloud, como Cloud Storage, Compute Engine y Cloud SQL, configurados para funcionar en conjunto.

## Conceptos básicos de deployment management

### **Configuración**

Una configuración describe todos los recursos que deseas para una sola implementación. Una configuración es un archivo escrito en la sintaxis YAML que enumera cada uno de los recursos que deseas crear y sus propiedades de recursos respectivas. Una configuración debe contener una sección resources: seguida de la lista de recursos que crear.

**name**: es el nombre que defines para el recurso.

**type**: especifica qué tipo de recurso creas. Por ejemplo, una VM es compute.v1.instance. Del mismo modo, una instancia de Cloud SQL tiene el tipo sql.v1beta4.instance.

**properties**: especifica las propiedades del recurso. Las propiedades requeridas para crear el recurso son las mismas requeridas por la API del recurso. Por ejemplo, cuando creas una instancia de VM de Compute Engine, debes proporcionar un tipo de máquina, una imagen, una interfaz de red y una especificación de disco de arranque.

### **Plantillas**

Una configuración puede contener plantillas, que son básicamente partes del archivo de configuración que se ha resumido en bloques de compilación individuales. Después de crear una plantilla, puedes volver a utilizarlas en todas las implementaciones según sea necesario.

Una plantilla es un archivo separado que define un conjunto de recursos. Se puede usar Python o Jinja2 a fin de crear plantillas para Deployment Manager.

Una ventaja de usar plantillas para tu implementación es la capacidad de crear y definir propiedades personalizadas, que te permiten volver a usar plantillas en zonas, regiones y proyectos.

Las propiedades de las plantillas son variables arbitrarias. Cualquier archivo de configuración o de plantilla puede proporcionar un valor para una propiedad de la plantilla sin modificarla. Por lo tanto, puedes cambiar el valor de una propiedad por varias configuraciones sin cambiar la plantilla en sí misma.

### **Recurso**

Un recurso representa un recurso único de API. Este puede ser un recurso de API que proporcione un tipo de base administrado por Google o un recurso de API que suministre el proveedor de tipos. Por ejemplo, una instancia de Compute Engine es un recurso único, una instancia de Cloud SQL es un recurso único, y así sucesivamente.

Para especificar un recurso, proporciona un Tipo para esa instancia. Consulta la sección Tipos que figura a continuación para obtener más información sobre los tipos.

### **Tipos**

Para crear un recurso en Deployment Manager, debes especificar un type. Un tipo puede representar un recurso de API único, conocido como tipo de base, o un conjunto de recursos, conocido como un tipo compuesto, que se creará como parte de la implementación.

### **Tipos de base y proveedores de tipos**

Un tipo de base crea un recurso primitivo único. Por ejemplo, los tipos de base de propiedad de Google incluyen compute.v1.instance, storage.v1.bucket y sqladmin.v1beta4.database, todos los cuales los entrega la API respectiva de Compute Engine V1, la API de Cloud Storage V1 y la API de administrador de Cloud SQL v1beta4.

### **Tipos compuestos**

Un tipo compuesto contiene una o más plantillas que están preconfiguradas para funcionar en conjunto. Estas plantillas se expanden a un conjunto de tipos de base cuando se implementan en una implementación. Los tipos compuestos son básicamente plantillas alojadas que puedes agregar a Deployment Manager. Puedes crear tipos compuestos para soluciones comunes a fin de que la solución sea fácilmente reutilizable o crear configuraciones complejas que puedes volver a utilizar en el futuro.

### **Manifiesto**

Un manifiesto es un objeto de solo lectura que contiene la configuración original que proporcionaste, lo que incluye las plantillas importadas, así como la lista de recursos totalmente expandidos que creó Deployment Manager. Cada vez que actualizas una implementación, Deployment Manager genera un archivo de manifiesto nuevo para reflejar el estado nuevo de la implementación. A la hora de solucionar un problema con una implementación, es útil ver el manifiesto.

### **Deployment**

Una implementación es una colección de recursos que se implementan y administran juntos mediante el uso de una configuración.

### **Referencias**

Cuando se definen las propiedades para la configuración o las plantillas, puedes usar referencias a las propiedades de otros recursos en lugar de proporcionar valores de manera directa.

Con referencias, puedes:

- Acceder a propiedades que no se definen hasta que se crea el recurso. Por ejemplo, cuando defines una máquina virtual en la configuración, aún no conoces su dirección IP. Sin embargo, todavía puedes usar una referencia a la dirección IP. Cuando implementas la configuración, primero se crea la VM y Deployment Manager obtiene la dirección IP externa cuando está disponible.

### Deployment Manager frente a CloudFormation y ARM

Google Cloud Deployment Manager resultará familiar para los equipos de TI que han trabajado con AWS CloudFormation o Ansible, donde las plantillas también están escritas en formato YAML. Terraform utiliza un lenguaje de configuración y una sintaxis patentados.

Las plantillas de AWS CloudFormation pueden incluir metadatos, resultados y condiciones, que son similares a los esquemas de Deployment Manager.

Para los que esten familiarizados con Microsoft Azure Resource Manager (BRAZO) también verán similitudes en la oferta de plantillas nativas de Google. ARM usa JSON como sintaxis de plantilla, pero dado que YAML es esencialmente un superconjunto JSON, la sintaxis y la estructura del archivo son similares. Las tres herramientas IaC nativas de la nube brindan capacidades similares, aunque CloudFormation admite la mayor parte de los servicios nativos.

### Cómo implementar la infraestructura de Google Cloud como código

Los administradores de la nube pueden acceder a Deployment Manager a través de Google Cloud Console. La IU muestra una vista jerárquica de una implementación completa, pero las configuraciones generalmente se activan a través de la herramienta de línea de comandos de gcloud. Los archivos de configuración, plantilla y esquema son textuales, por lo que los usuarios pueden crearlos con cualquier editor de texto.

## Instalación y configuración

Se explicará como usar deployment management con la herramienta gcloud para preparar el entorno local.

### Crear proyecto

Deployment manager necesita un proyecto de google cloud.

- Como primer paso se abre la Cloud console

- Si no tiene una cuenta en google cloud puede crear una de manera gratuita y además le dan 300 creditos para utilizar en proyectos durante 90 días.
- Cuando se te solicite crear un proyecto, ingresa un nombre y un ID del proyecto que se usará para identificarlo o acepta los valores predeterminados y haz clic en Crear.
- Habilita las API para los recursos de Google Cloud que deseas administrar
- Habilitar Deployment Manager
- Instala Google cloud CLI
- ##### Para instalar la CLI de gcloud:

##### Descarga y autentica gcloud.

##### Otra opción es usar Cloud Shell, que ya tiene gcloud instalado.

##### Determina tu ID del proyecto:

```
gcloud config set project myproject
```

- En my proyect es el nombre del proyecto que creaste

- Configura tu zona y región predeterminadas.

```gcloud config set compute/region us-central1
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-f
```

En este ejemplo se clona un repositorio de Github

```
git clone https://github.com/GoogleCloudPlatform/deploymentmanager-samples

# open the samples folder
cd deploymentmanager-samples/examples/v2/step_by_step_guide

```
