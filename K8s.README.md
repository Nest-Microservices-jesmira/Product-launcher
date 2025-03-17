# Helm commands

* Crear configuración `helm create <nombre>`
* Aplicar configuración inicial: `helm install <nombre> .`
* Aplicar actualizaciones: `helm upgrade <nombre> .`

# K8s commands

* Obtener pods, deployments y services: `kubectl get <pods | deployments | services>`
* Revisar todos pods: `kubectl describe pods`
* Revisar un pod: `kubectl describe pod <nombre>`
* Eliminar pod: `kubectl delete pod <nombre>`
* Revisar logs: `kubectl logs <nombre>`

> ⚠️ **IMPORTANTE:** ⚠️ 
> Si  tenemos problemas con que  no actualiza el pod, eliminamos el pod que sea antiguo ( miramos si la hora coincide con la de creación del pod)

# Crear deployment:
```
kubectl create deployment <nombre> --image=<registro/url/imagen> --dry-run=client -o yaml > deployment.yml
```

# Crear service
```
kubectl create service clusterip <nombre> --tcp=<8888> --dry-run=client -o yaml > service.yml 
**kubectl create service nodeport <nombre> --tcp=<3000> --dry-run=client -o yaml > service.yml**
```
* **clusterip**: solo se puede acceder desde dentro del cluster (ejemplo: acceder al nats server)
* **nodeport**: se puede acceder desde fuera del cluster( acceder desde el exterior de la red ejemplo: http://<localhost>:3000)
 


> ⚠️ **IMPORTANTE:** ⚠️ Por favor, lee esta sección antes 
> Si utilizamos windows, hacerlo con  el git bash, no con la powershell de windows ya que lo codidfica en utf-16 y no en utf-8. Sino tendremos errores de compatibilidad.




# Secrets

* Crear secretos, varios a la vez, o uno por uno.
```
kubectl create secret generic <nombre> --from-literal=key=value

kubectl create secret generic secret1 --from-literal=key1=value1 --from-literal=key2=value2
```
* Obtener los secretos `kubectl get secrets`
* Ver el contenido de un secreto `kubectl get secrets <nombre> -o yaml`

## Editar un secret
La forma más fácil es borrarlo y volverlo a crear pero si es más de un secret, no vamos a querer perder los demás.
Recordar que los secrets están en `base64`, por lo que si queremos editar un secret, debemos hacerlo en `base64`.

1. Editar el secret con `kubectl edit secret <nombre>` esto invocará el editor
2. Cambiar el valor (se puede usar un editor en [línea para convertir a base64](https://www.rapidtables.com/web/tools/base64-decode.html))
3. Tocar **i** para insertar líneas y editar el archivo
4. Poner el valor a decodificar en una nueva línea
5. Presionar **esc** y luego `:. ! base64 -D` para decodificar el valor
6. Presionar **i** para insertar o editar el valor
7. Presionar **esc** y luego `:. ! base64` para codificar el valor
8. Editar nuevamente el archivo **i** y dejar la línea en su posición
9. Presionar **esc** y luego **:wq** para guardar y salir



## Configurar secretos de Google Cloud para obtener las imágenes

1. Crear secreto:
```
kubectl create secret docker-registry gcr-json-key --docker-server=SERVIDOR-DE-GOOGLE-docker.pkg.dev --docker-username=_json_key --docker-password="$(cat 'PATH/DE/Tienda Microservices IAM.json')" --docker-email=TU_CORREO@gmail.com
```

2. Path del secreto para que use la llave:
```
kubectl patch serviceaccounts default -p '{ "imagePullSecrets": [{ "name":"gcr-json-key" }] }'
```


## Exportar y aplicar configuraciones con archivos (secrets en este caso)
* Para exportar los archivos de configuración

```
kubectl get secret <nombre> -o yaml > <nombre>.yml
```

* Aplicar la configuración basado en el archivo
```
kubectl create -f <nombre>.yml
```

## Soluciones

Si al ejecutar el primer helm install como el siguiente:
```	
helm install ourchart .\ourchart
# Error: unable to build kubernetes objects from release manifest: error parsing : error converting YAML to JSON: yaml: invalid leading UTF-8 octet

```
Es bastante obvio: Helm funciona con UTF-8 y mis archivos .yaml parecen estar codificados de forma diferente. Una mirada rápida a la parte inferior de mi VSCode lo confirma:

![Aparece como UTF-16](https://blog.kaniski.eu/wp-content/uploads/2020/09/utf16-2.png)

¿Cómo puedo solucionarlo?

Como uso PowerShell, es bastante fácil: en lugar de hacer la redirección de salida simple (“ > “), canalizo la salida al cmdlet Out-File con la opción -Encoding UTF8 , en todos los casos, que se encarga de la codificación (y la establece en UTF-8 con BOM , lo cual está bien para Helm):

```
kubectl create deployment <nombre> --image=<registro/url/imagen> --dry-run=client --output=yaml | Out-File -FilePath deployment.yaml -Encoding UTF8

```	


```	
$imgsec= '{"imagePullSecrets": [{"name": "gcr-json-key"}]}' | ConvertTo-Json
kubectl patch serviceaccounts default -p "$imgsec"
```