
# Moodle on Red Hat OpenShift Dedicated
<p align="left">
<img src="https://img.shields.io/badge/redhat-CC0000?style=for-the-badge&logo=redhat&logoColor=white" alt="Redhat">
<img src="https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white" alt="kubernetes">
<img src="https://img.shields.io/badge/docker-0db7ed?style=for-the-badge&logo=docker&logoColor=white" alt="Docker">
<img src="https://img.shields.io/badge/shell_script-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white" alt="shell">  
</p>

El propósito de este proyecto consiste en generar los objetos kubernetes en base a la imagen del nodo del repositorio oficial [moodle](https://moodle.org) para el despliegue sobre las plataformas de contenedores por medio de pipelines Tekton.

Se verifico el funcionamiento en [Sandbox RedHat OpenShift Dedicated](https://developers.redhat.com/developer-sandbox) (Openshift 4.14.1). 

<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/tekton311.PNG?raw=true" width="684" title="Run On Openshift">
</p>  
<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/pipelinerun.PNG?raw=true" width="684" title="Run On Openshift">
</p>  

## Instalación

0. Ingresar a tu Sandbox y abrir una terminal web desde la consola de openshift seleccionando en el namespace asignado y ejecutar el siguiente comando para la creación del pipeline tekton

```bash
oc apply -f https://raw.githubusercontent.com/maximilianoPizarro/moodle/master/pipeline.yaml
```

Output:
Welcome to the OpenShift Web Terminal. Type "help" for a list of installed CLI tools.
bash-4.4 ~ $ oc apply -f https://raw.githubusercontent.com/maximilianoPizarro/moodle/master/pipeline.yaml
persistentvolumeclaim/moodle-workspace created
task.tekton.dev/s2i-php-74 configured
pipeline.tekton.dev/moodle configured


<p align="left">
  <img src="https://github.com/maximilianoPizarro/moodle/blob/main/topology311ocp.PNG?raw=true" width="684" title="Run On Openshift">
</p>  