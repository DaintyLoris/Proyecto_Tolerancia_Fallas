# Proyecto Computacion Tolerante A Fallas

### Instalación

Para empezar se comienza con eliminar cualquier información necesaria que pudiera intervenir con la instalación, para lo que se usa el comando:
~~~
minikube delete
~~~

Después se inicializa minikube otorgando 4 gb de memoria:  
~~~
minikube start -memory 4096
~~~

La instalación tomará algo de tiempo, pero una vez finalizada nos aparecerá un mensaje como el siguiente:

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img1.jpg)

Lo siguiente que debe hacerse es invocar a los archivos .yaml mediante el comando: 

~~~
ls
1-istio-init.yaml 
2-istio-minikube-reduced.yaml 
3-kiali-secret.yaml 
4-application-full-stack.yaml
~~~

Después se aplica el siguiente comando para comenzar a trabajar con Istio:

~~~
kubectl apply -f 1-isito-init.yaml
~~~

Y se obtiene una pantalla como al siguiente:

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img2.jpg)

A partir de este momento se estará trabajando en el namespace de istio-system.
Si se desea visualizar los pods por ejemplo, es necesario especificar el namespace en el comando:

~~~
kubectl get po -n istio-system
~~~

En caso de que el comando se utilice inmediatamente después de la inicialización es probable que no todos los pods esten corriendo, por lo que se debería de esperar unos momentos y volver a usar el comando anterior hasta que todos los pods esten corriendo como a continuación

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img3.jpg)

El segundo archivo que se aplica contiene las definiciones de los recursos y componentes que proporciona Istio y que necesita para trabajar:
~~~
kubectl apply -f 2-istio-minikube-reduced.yaml 
~~~
Se puede apreciar la creación de múltiples recursos a continuación:
![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img4.jpg)

Una vez más se aconseja el uso del comando:
~~~
kubectl get po -n istio-system
~~~
Para poder apreciar cuando todos los pods esten corriendo. Esto tomará por lo menos entre 5 y 10 minutos. 
![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img5.jpg)

Hasta este punto Istio ya se encuentra instalado en el sistema.

Lo siguiente es aplicar la herramienta Kiali, la cual permitirá visualizar los resultados arrojados por Istio.
~~~
kubectl apply -f 3-kiali-secret.yaml 
~~~
En este archivo usuario y contraseña se encuentran codificados en base 64.
Una vez aplicado el comando el mensaje de confirmación es tan simple como se muestra a continuación:
~~~
secret/kiali created
~~~

A continuación se necesita implementar los proxies de Istio como “sidecars” en los pods, a fin de no tener que hacerlo en el archivo .ymal.
Cabe destacar que uno de los pods ya creado como parte del plano de control de Istio ya tienen su propio inyector de sidecars, aunque por defecto este no hace nada. Para hacer que el inyector de sidecars funcione se necesita establecer una etiqueta.
El siguiente comando nos deja apreciar la configuración de minikube:
~~~
kubectl describe ns default 
~~~
Aquí es posible apreciar que no existe ninguna etiquet disponible:

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img6.jpg)

Para crear una etiqueta en el namespace default se utiliza el comando: 
~~~
kubectl label namespace default istio-injection=enabled
~~~

A lo que se presenta el mensaje de confirmación. 

~~~
namespace/default labeled
~~~
Ahora pudiendo apreciar la etiqueta:

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img7.jpg)

Ya con las etiquetas hechas se procede a aplicar el 4to archivo .yaml:
~~~
kubectl apply -f 4-application-full-stack.yaml
~~~

A lo que se nos presenta una imagen como la siguiente:

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img8.jpg)

Los pods creados puede observarse con el comando:
~~~
$kubectl get po
~~~

Los pods creados toman unos minutos en inicializar, Cabe resaltar que los pods tienen dos contenedores dentro ya que Istio realiza la inyección del proxy.

![Proceso descrito](https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img9.jpg)
