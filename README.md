# Plataforma de analítica de datos de COVID-19
Durante la pandemia COVID-19 el trabajo de Universidad de Johns Hopkins se ha vuelto de vital importancia para mantener a las personas informadas sobre el progreso del virus en sus comunidades, en sus países y en el mundo.
Johns Hopkins publica los datos gratuitamente para que cualquiera los use.  Estos datos se entregan como archivos CSV que se deben descargar para luego consultar.  Hacer que los datos actualizados sean más accesibles ayudará a que las personas puedan crear análisis y aplicaciones directamente sobre el conjunto de datos. 
En este contexto se propone la construcción de una plataforma analítica de datos, la cual mediante Vagrant permite la instalación de un entorno Multi Machine para la descarga, manipulación y visualización de datos de COVID-19. La plataforma necesita de la instalación de los siguientes elementos: 

- Jupyter
- MongoBD
### Prerrequisitos 
Previamente tener instalado y configurado en el Host:
- Packer 
- Ansible 
- Terraform 
### Instrucciones
1. Utilice este repositorio en su ordenado.Es fundamental que usted tenga en su directorio de trabajo los siguientes subdirectorios con los siguientes archivos : 

- ansible (download-latest-JHU.sh y install.yml)
- packer (ubuntu2004-digitalocean.json)
- terraform (create_droplet.tf,versions.tf y terraform.tfvar)

El caso de archivo * terraform.tfvars *  debe tener el token y clave SSH entregada por DigitalOcean

2. Para comenzar usted se debe crear un token en DigitalOcean el cual debe utilizar de la siguiente manera : 

''''
export DIGITALOCEAN_API_TOKEN = [TOKEN-DigitalOcean]
'' 

3. Luego usted se debe ubicar en el subdirectorio de packer y ejecutar: 
'' 
packer build ubuntu2004-digitalocean.json
'' 
