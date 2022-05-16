# Cloud deployment management

#### Google Cloud Deployment Manager es un servicio de implementación de infraestructura que automatiza la creación y la administración de recursos de Google Cloud. Escribe plantillas flexibles y archivos de configuración y úsalos para crear implementaciones que tengan una variedad de servicios de Google Cloud, como Cloud Storage, Compute Engine y Cloud SQL, configurados para funcionar en conjunto.

---

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

`gcloud config set project myproject `

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
