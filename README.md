

## Dev

1. Clonar el repositorio
2. Crear un .env basado en el .env.template
3. Ejecutar el comando `git submodule update --init --recursive` para inicializar los sub-módulos
4. Ejecutar el comando `docker compose up --build`  

### Pasos para crear los Git Submodules


1. Crear un nuevo repositorio en GitHub
2. Clonar el repositorio en la máquina local
3. Añadir el submodule, donde `repository_url` es la url del repositorio y `directory_name` es el nombre de la carpeta donde quieres que se guarde el sub-módulo (no debe de existir en el proyecto)
```
git submodule add <repository_url> <directory_name>
```
4. Añadir los cambios al repositorio (git add, git commit, git push)
Ej:
```
git add .
git commit -m "Add submodule"
git push
```
5. Inicializar y actualizar Sub-módulos, cuando alguien clona el repositorio por primera vez, debe de ejecutar el siguiente comando para inicializar y actualizar los sub-módulos
```
git submodule update --init --recursive
```
6. Para actualizar las referencias de los sub-módulos
```
git submodule update --remote
```


## Importante
Si se trabaja en el repositorio que tiene los sub-módulos, **primero actualizar y hacer push** en el sub-módulo y **después** en el repositorio principal. 

Si se hace al revés, se perderán las referencias de los sub-módulos en el repositorio principal y tendremos que resolver conflictos.


# Prod

1. Clonar el repositorio
2. Crear un .env basado en el .env.template
3. Ejecutar el comando
```
docker compose -f docker-compose.prod.yml build
```

# Cloudbuild
https://cloud.google.com
Buscar "Cloud Build"
Buscar "Artifact Registry"
Buscar "Cloud Build Triggers"
Buscar "Secret Manager"

Si tenemos problemas con el secret manager y los permisos, podemos crear un secreto de tipo "Secret Manager" con nombre "orders_database_url" y valor "postgres://postgres:123456@localhost:5432/ordersdb"

Y ejecutar en la terminal:
```	
gcloud projects add-iam-policy-binding PROYECTO_ID --member=serviceAccount:CUENTA_DE_SERVICIO --role=roles/secretmanager.secretAccessor
```

donde CUENTA_DE_SERVICIO es el ID de la cuenta de servicio de Google Cloud ejemplo: `000000000000-compute@developer.gserviceaccount.com` y PROYECTO_ID es el ID del secret `207454884487`
