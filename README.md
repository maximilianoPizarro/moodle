
# Moodle on Red Hat OpenShift Dedicated
<p align="left">
<img src="https://img.shields.io/badge/moodle-FF7F50?style=for-the-badge&logo=moodle&logoColor=white" alt="Moodle">
<img src="https://img.shields.io/badge/redhat-CC0000?style=for-the-badge&logo=redhat&logoColor=white" alt="Redhat">
<img src="https://img.shields.io/badge/openshift-%23121011.svg?style=for-the-badge&logo=openshift&logoColor=white" alt="OpenShift">
<img src="https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white" alt="kubernetes">
<img src="https://img.shields.io/badge/tekton-%23121011.svg?style=for-the-badge&logo=tekton&logoColor=white" alt="Tekton">
<img src="https://img.shields.io/badge/shell_script-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white" alt="shell">
<a href="https://www.linkedin.com/in/maximiliano-gregorio-pizarro-consultor-it"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" alt="linkedin">     
</p>

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/cover.PNG?raw=true" width="900" title="Run On Openshift">
</p>  

El propósito de este proyecto consiste en autogenerar los objetos kubernetes y deployar en un ambiente de Dev, en base al codigo fuente del repositorio oficial [Moodle](https://github.com/moodle/moodle) por medio de un Pipeline [Tekton](https://tekton.dev/) para el despliegue sobre las plataformas de contenedores [Kubernetes](https://kubernetes.io/) que cuenten con el Operador de Pipelines Instalado.

Para la construcción de la imagen del contenedor de moodle se utiliza la imagen oficial de RHEL8 del catálogo de Red Hat para [php-74](https://catalog.redhat.com/software/containers/rhel8/php-74/5f5211e39df2542e4756afd1?architecture=amd64&image=65952d8538d591f19afa95ed5952d8538d591f19afa95ed) con el agregado de la extensión php-zip requerida para la instalación de moodle. 

Se verifico el funcionamiento en [Sandbox RedHat OpenShift Dedicated](https://developers.redhat.com/developer-sandbox) (Openshift 4.14.1) a partir del release [MOODLE_311_STABLE](https://github.com/moodle/moodle/tree/MOODLE_311_STABLE) con una base de datos mariadb proporsionada por el catalogo de OpenShift. 

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/image311.PNG?raw=true" width="900" title="Run On Openshift">
</p>  

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/topology311ocp.PNG?raw=true" width="900" title="Run On Openshift">
</p>    

## Instalación Pipeline Tekton en OpenShift

0. Ingresar a tu Sandbox y abrir una terminal web desde la consola de openshift seleccionando en el namespace asignado y ejecutar el siguiente comando para la creación del Pipeline Tekton

```bash
oc apply -f https://raw.githubusercontent.com/maximilianoPizarro/moodle/master/pipeline.yaml
```

```bash
Output:
Welcome to the OpenShift Web Terminal. Type "help" for a list of installed CLI tools.
bash-4.4 ~ $ oc apply -f https://raw.githubusercontent.com/maximilianoPizarro/moodle/master/pipeline.yaml
persistentvolumeclaim/moodle-workspace created
task.tekton.dev/s2i-php-74 configured
pipeline.tekton.dev/moodle configured
```

## Ejecución Pipeline Tekton

1. Desde la sección de Pipelines actualizar Parameters con los valores de <NAMESPACE> correspondiente, tener en cuenta para el caso del dns el dominio base puede cambiar segun la región.

```bash
IMAGE_NAME=image-registry.openshift-image-registry.svc:5000/<NAMESPACE>/moodle
EXTERNAL_URL=moodle-<NAMESPACE>.apps.sandbox-m2.ll9k.p1.openshiftapps.com
VERSION=<NAMESPACE>-dev/php-74
```

```bash
Output:
IMAGE_NAME=image-registry.openshift-image-registry.svc:5000/maximilianopizar-2-dev/moodle
EXTERNAL_URL=moodle-maximilianopizar-2-dev.apps.sandbox-m2.ll9k.p1.openshiftapps.com
VERSION=maximilianopizar-2-dev/php-74
```

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/parameters.PNG?raw=true" width="900" title="parameters">
</p>  

NOTA: Puede modificar los parametros desde el objeto moodle dentro del archivo pipeline.yaml como alternativa o desde el formulario de ejecución.

2. Desde Pipeline builder instalar y agregar la tasks [yq](https://github.com/mikefarah/yq) disponible en el catálogo de Pipelines. Importante: no guardar el cambio en Pipeline, seleccionar cancelar para que no se actualice el Pipeline con yq vacio.

<p align="left">
  <img src="https://github.com/maximilianoPizarro/botpress-server-v12/blob/master/examples/image/Captura3.PNG?raw=true" width="900" title="Run On Openshift">
</p>  

3. Desde la seccion de Pipeline seleccionar moodle y ejecutar Pipeline con el workspace "moodle-workspace" configurado.

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/workspace.PNG?raw=true" width="900" title="workspace">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/pipelinerun.PNG?raw=true" width="900" title="piperun">  
</p>  

4. Luego de la ejecución con exito de la tasks 'oc-apply-manifest' , modicar el objeto moodle-configuration con los valores de <NAMESPACE> desde la seccion de ConfigMaps, la opcion 'Edit ConfigMap', aplicar restart-rollout al deployment de moodle y verificar que el pod escale a 1 réplica.

```bash
  $CFG->dbhost    = 'mariadb.<NAMESPACE>-dev.svc.cluster.local';
  $CFG->wwwroot   = 'https://moodle-<NAMESPACE>-dev.apps.sandbox-m2.ll9k.p1.openshiftapps.com';
```

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/configphp.PNG?raw=true" width="900" title="configmap">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/restart-rollout.PNG?raw=true" width="900" title="rollout">  
</p>



## Instalación Moodle

0. Desde la vista Topology seleccionar la aplicación moodle en donde encontraremos al dns del sitio.

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/install311.1.PNG?raw=true" width="900" title="Run On Openshift">
</p>

1. Vemos que comple los requerimientos mínimos y seleccionamos continuar.

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/install311.PNG?raw=true" width="900" title="Run On Openshift">
</p>

2. Generamos la cuenta administrador principal del sitio.

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/install311.2.PNG?raw=true" width="900" title="Run On Openshift">
</p>

3. Configuramos el nombre y la region.

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/install311.3.PNG?raw=true" width="900" title="Run On Openshift">
</p>

4. Vamos a recibir un correo de moodle con la confirmación del sitio, aceptamos los términos y condiciones.

<p align="left">      
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/install311.4.PNG?raw=true" width="900" title="Run On Openshift">
</p>

5. Autenticamos con las credenciales del administrador principal y listo.

<p align="left">  
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/screenshot/install311.5.PNG?raw=true" width="900" title="Run On Openshift">
</p>  
