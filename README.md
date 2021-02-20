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

```
export DIGITALOCEAN_API_TOKEN = [TOKEN-DigitalOcean]
```

3. Luego usted se debe ubicar en el subdirectorio de packer y ejecutar: 
``` 
packer build ubuntu2004-digitalocean.json
```

Al terminar este paso usted ya tiene una imagen en DigitalOcean que puede verificar en la sección de imágenes 

4. Ingresar al subdirectorio de terraform y ejecutar :
```
terraform init
terraform plan
terraform apply
```
5. Al terminar el paso 3 usted contará con una infraestructura en la nube que contiene Jupyter y MongoBD para trabajar con los datos de COVID 19 ya mencionados. La puede revisar en la sección de Droplets junto con su respectiva dirección IP

6. Puede ingresar a la maquina creada mediante la dirección IP con : 
```
ssh -L localhost:8889:localhost:8889 root@[DirecciónIP]
```
7.Ejecute el siguiente script en bash que le permitirá importar los archivos CSV hacia MongoBD: 

```
#!/usr/bin/env bash
exit_code=0
mongo "${1}" --quiet --eval "db.dropDatabase()"
exit_code=$((exit_code + $?))

for file in jhu/csse_covid_19_data/csse_covid_19_daily_reports/10-*.csv; do
mongoimport --uri "${1}" --collection daily --type csv --headerline --file "${file}" &
exit_code=$((exit_code + $?))
done
wait

echo "$0 finished with code $exit_code."
exit $exit_code

```
Para ejecutarlo:

```
chmod a+x mongoimport-everything.sh
./mongoimport-everything.sh "mongodb://localhost/covid19"
```

8. Siguiendo en la máquina ejecute la siguiente línea que le permitirá tener acceso desde su navegador:
```
jupyter notebook list 
```

En su navegador copie la url que se le indica y utilice el token si fuese necesario. 
9. Con Jupyter en su navegador, ahora puede acceder a la base de datos creada. Para esto, tiene que crear un cuaderno en Jupyter y copiar las siguientes líneas de código.
```
from datetime import datetime
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017/')
db = client.covid19
collection = db.daily

for result in collection.find({"Country_Region": "Mexico", "Province_State":"Michoacan"}):
  print (result)
  ```
Una alternativa es crear el script.py en su máquina virtual de Jupyter y ejecutar el código mediante -> *python3 script.py*

Terminado,usted ya tiene una plataforma que le permitirá analizar datos de COVID 19. Pero debe tener en consideración que solo se está importando el mes de octubre a la base de datos, para cambiar este comportamiento edita la línea del for.  Cambia 10-*.csv por *.csv para importar toda la información o coloca el número del mes que te interesa. Puedes consultar el formato del nombre de los archivos navegando el directorio jhu que descarga el script download-latest-JHU.sh en la ruta: hu/csse_covid_19_data/csse_covid_19_daily_reports/
  
 ### FIN !!! 



