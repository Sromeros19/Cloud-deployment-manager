# Cloud deployment management

#### Google Cloud Deployment Manager es un servicio de implementación de infraestructura que automatiza la creación y la administración de recursos de Google Cloud. Escribe plantillas flexibles y archivos de configuración y úsalos para crear implementaciones que tengan una variedad de servicios de Google Cloud, como Cloud Storage, Compute Engine y Cloud SQL, configurados para funcionar en conjunto.

---

## Conceptos básicos de deployment management

---

### Configuración

Una configuración describe todos los recursos que deseas para una sola implementación. Una configuración es un archivo escrito en la sintaxis YAML que enumera cada uno de los recursos que deseas crear y sus propiedades de recursos respectivas. Una configuración debe contener una sección resources: seguida de la lista de recursos que crear.

### Plantillas

Una configuración puede contener plantillas, que son básicamente partes del archivo de configuración que se ha resumido en bloques de compilación individuales. Después de crear una plantilla, puedes volver a utilizarlas en todas las implementaciones según sea necesario.

### Recurso

Un recurso representa un recurso único de API. Este puede ser un recurso de API que proporcione un tipo de base administrado por Google o un recurso de API que suministre el proveedor de tipos. Por ejemplo, una instancia de Compute Engine es un recurso único, una instancia de Cloud SQL es un recurso único, y así sucesivamente.

Para especificar un recurso, proporciona un Tipo para esa instancia. Consulta la sección Tipos que figura a continuación para obtener más información sobre los tipos.

### Tipos

Para crear un recurso en Deployment Manager, debes especificar un type. Un tipo puede representar un recurso de API único, conocido como tipo de base, o un conjunto de recursos, conocido como un tipo compuesto, que se creará como parte de la implementación.

### Tipos de base y proveedores de tipos

Un tipo de base crea un recurso primitivo único. Por ejemplo, los tipos de base de propiedad de Google incluyen compute.v1.instance, storage.v1.bucket y sqladmin.v1beta4.database, todos los cuales los entrega la API respectiva de Compute Engine V1, la API de Cloud Storage V1 y la API de administrador de Cloud SQL v1beta4.

### Tipos compuestos

Un tipo compuesto contiene una o más plantillas que están preconfiguradas para funcionar en conjunto. Estas plantillas se expanden a un conjunto de tipos de base cuando se implementan en una implementación. Los tipos compuestos son básicamente plantillas alojadas que puedes agregar a Deployment Manager. Puedes crear tipos compuestos para soluciones comunes a fin de que la solución sea fácilmente reutilizable o crear configuraciones complejas que puedes volver a utilizar en el futuro.

### Manifiesto

Un manifiesto es un objeto de solo lectura que contiene la configuración original que proporcionaste, lo que incluye las plantillas importadas, así como la lista de recursos totalmente expandidos que creó Deployment Manager. Cada vez que actualizas una implementación, Deployment Manager genera un archivo de manifiesto nuevo para reflejar el estado nuevo de la implementación. A la hora de solucionar un problema con una implementación, es útil ver el manifiesto.

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

## Configuraciones

---

Una configuración define la estructura de tu implementación. Debes especificar una configuración para crear una implementación.

En este paso, se accederá a una configuración que crea una implementación con dos instancias de VM de Compute Engine. Una instancia de VM es uno de varios tipos de recursos que puedes implementar con Deployment Manager.

### Pasos

- Abre el archivo de configuración YAML
- Se cambia al directorio que creaste en Instalación y configuración:

```
cd deploymentmanager-samples/examples/v2/step_by_step_guide/step2_create_a_configuration
```

- Luego se abre two-vms.yaml:

```
nano two-vms.yaml
```

##### En la sección resources, ten en cuenta que hay dos recursos: **the-first-vm y the-second-vm.** Cada recurso tiene un campo **name, type y properties**:

##### **name**: es el nombre que defines para el recurso.

##### **type**: especifica qué tipo de recurso creas. Por ejemplo, una VM es compute.v1.instance. Del mismo modo, una instancia de Cloud SQL tiene el tipo sql.v1beta4.instance.

##### **properties**: especifica las propiedades del recurso. Las propiedades requeridas para crear el recurso son las mismas requeridas por la API del recurso. Por ejemplo, cuando creas una instancia de VM de Compute Engine, debes proporcionar un tipo de máquina, una imagen, una interfaz de red y una especificación de disco de arranque.

- Establecer archivo de configuración

En **two-vms.yaml**, reemplaza MY_PROJECT por el ID de tu proyecto.

```
# Copyright 2016 Google Inc. All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

resources:
- name: the-first-vm
  type: compute.v1.instance
  properties:
    zone: us-central1-f
    machineType: https://www.googleapis.com/compute/v1/projects/MY_PROJECT/zones/us-central1-f/machineTypes/f1-micro
    disks:
    - deviceName: boot
      type: PERSISTENT
      boot: true
      autoDelete: true
      initializeParams:
        sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/family/debian-9
    networkInterfaces:
    - network: https://www.googleapis.com/compute/v1/projects/MY_PROJECT/global/networks/default
      accessConfigs:
      - name: External NAT
        type: ONE_TO_ONE_NAT
- name: the-second-vm
  type: compute.v1.instance
  properties:
    zone: us-central1-f
    machineType: https://www.googleapis.com/compute/v1/projects/MY_PROJECT/zones/us-central1-f/machineTypes/g1-small
    disks:
    - deviceName: boot
      type: PERSISTENT
      boot: true
      autoDelete: true
      initializeParams:
        sourceImage: https://www.googleapis.com/compute/v1/projects/debian-cloud/global/images/family/debian-9
    networkInterfaces:
    - network: https://www.googleapis.com/compute/v1/projects/MY_PROJECT/global/networks/default
      accessConfigs:
      - name: External NAT
        type: ONE_TO_ONE_NAT
```

- Guarda los cambios.

## Implementar recursos

Pasos para implementar la configuración

- Ejecuta el comando:

```
gcloud deployment-manager deployments create deployment-with-2-vms --config two-vms.yaml
```

- Espera la indicación de que creaste la implementación con éxito (ten en cuenta que el ID de la operación real será diferente):

```
Waiting for create operation-1432319707382-516afeb5d00f1-b864f0e7-b7103978...done.
Create operation operation-1432319707382-516afeb5d00f1-b864f0e7-b7103978 completed successfully.
NAME           TYPE                 STATE      ERRORS  INTENT
the-first-vm   compute.v1.instance  COMPLETED  []
the-second-vm  compute.v1.instance  COMPLETED  []
```

- Para obtener una lista de los recursos que creaste, ejecuta este comando:

```
gcloud deployment-manager resources list --deployment deployment-with-2-vms
```

- Para obtener información más detallada sobre la implementación, como la hora de inicio y finalización, y el ID de la operación si necesitas depurar una implementación, ejecuta este comando:

```
gcloud deployment-manager deployments describe deployment-with-2-vms
```

- Para borrar la implementación se ejecuta el siguiente comando:

```
gcloud deployment-manager deployments delete deployment-with-2-vms
```

## Referencias

---

Puedes usar las referencias para definir las propiedades de tu configuración o plantillas en lugar de proporcionar valores directamente.

Con las referencias, puedes acceder a propiedades que no están definidas hasta que se cree el recurso. Por ejemplo, cuando defines una VM en tu configuración, no conoces su dirección IP. Sin embargo, puedes crear una referencia para la dirección IP.

**Pasos:**

- Se abre una nueva configuración

Abre un **two-vms.yaml** nuevo, que define una red denominada **a-new-network:**

```
cd deploymentmanager-samples/examples/v2/step_by_step_guide/step4_use_references

nano two-vms.yaml
```

- Visualiza las referencias de red

#### En la sección properties de ambas instancias de máquinas virtuales, verifica que el valor de network tenga una referencia a la propiedad selfLink de la nueva red, de modo que la propiedad de interfaz de red se vea así:

```
networkInterfaces:
- network: $(ref.a-new-network.selfLink)
```

- Implementa la configuración

```
gcloud deployment-manager deployments create deployment-with-references --config two-vms.yaml
```

Para ver la configuración se utiliza el siguiente comando:

```
gcloud deployment-manager deployments describe deployment-with-references
```

## Plantillas

---

Una plantilla es un archivo separado que define un conjunto de recursos. Puedes volver a usar plantillas en diferentes implementaciones, lo que crea coherencia en implementaciones complejas.

Puedes usar Python o Jinja2 a fin de crear plantillas para Deployment Manager.

### Plantillas de python

#### Para usar una plantilla de python se deben cumplir los siguientes requisitos.

- La plantilla debe escribirse en Python 3.x.

- La plantilla debe definir un método llamado **GenerateConfig(context)** o **generate_config(context)**. Si usas ambos nombres de método en la misma plantilla, el método **generate_config()** tendrá prioridad.

- El objeto context contiene metadatos sobre la implementación y el entorno, como el nombre de la implementación, el proyecto actual, etcétera. En los próximos pasos, usarás estas variables específicas de la implementación.

### Usar plantillas de muestra

Desde el repositorio de muestras, abre vm-template.py

```
cd deploymentmanager-samples/examples/v2/step_by_step_guide/step5_create_a_template/python

nano vm-template.py
```

### Importa plantillas

Después de crear las plantillas, debes importarlas a tu configuración. Abre el two-vms.yaml nuevo:

```
cd deploymentmanager-samples/examples/v2/step_by_step_guide/step5_create_a_template/python

nano two-vms.yaml
```

- Guarda la configuración y luego implementa

```
gcloud deployment-manager deployments create deployment-with-templates --config two-vms.yaml
```

## Usar varias plantillas

Para plantillas que incorporan otras plantillas

- Como primer paso se abre la plantilla para una red

Abre la plantilla llamada compute-engine-template.py:

```
cd deploymentmanager-samples/examples/v2/step_by_step_guide/step6_use_multiple_templates/python

nano compute-engine-template.py
```

##### Esta plantilla importa otras plantillas para todos los recursos en la implementación: **vm-template.py, vm-template-2.py** para máquinas virtuales, **network-template.py** para una red y **firewall-template.py** para una regla de firewall.

- Vizualiza la configuración

```
cd deploymentmanager-samples/examples/v2/step_by_step_guide/step6_use_multiple_templates/python

nano config-with-many-templates.yaml
```

## Configurar propiedades de la plantilla y usar variables del entorno

---

Una ventaja de usar plantillas para tu implementación es la capacidad de crear y definir propiedades personalizadas, que te permiten volver a usar plantillas en zonas, regiones y proyectos.

Las propiedades de las plantillas son variables arbitrarias. Cualquier archivo de configuración o de plantilla puede proporcionar un valor para una propiedad de la plantilla sin modificarla. Por lo tanto, puedes cambiar el valor de una propiedad por varias configuraciones sin cambiar la plantilla en sí misma.
