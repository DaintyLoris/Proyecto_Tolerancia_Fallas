<h1 align="center"> Proyecto Computacion Tolerante A Fallas </h1>

<p align="center">

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/cuceiLogo.png" width="400">

</p>

<h2 align="center">

Carrera: Ingeniería en computación (INCO)

Asignatura: COMPUTACION TOLERANTE A FALLAS (I7036_D06_2022B)

Profesor: Lopez Franco, Michel Emanuel

Alumnos: 

Anguiano Ortiz, Josue (215686936)

Perez Juarez, Juan Ramon (218744325)

</h2>


<h2 align="center"> Instalación </h2> 

Para empezar se comienza con eliminar cualquier información necesaria que pudiera intervenir con la instalación, para lo que se usa el comando:
~~~
minikube delete
~~~

Después se inicializa minikube otorgando 4 gb de memoria:  
~~~
minikube start -memory 4096
~~~

La instalación tomará algo de tiempo, pero una vez finalizada nos aparecerá un mensaje como el siguiente:

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img1.jpg" width="400">

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

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img2.jpg" width="500">

A partir de este momento se estará trabajando en el namespace de istio-system.
Si se desea visualizar los pods por ejemplo, es necesario especificar el namespace en el comando:

~~~
kubectl get po -n istio-system
~~~

En caso de que el comando se utilice inmediatamente después de la inicialización es probable que no todos los pods esten corriendo, por lo que se debería de esperar unos momentos y volver a usar el comando anterior hasta que todos los pods esten corriendo como a continuación

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img3.jpg" width="400">

El segundo archivo que se aplica contiene las definiciones de los recursos y componentes que proporciona Istio y que necesita para trabajar:
~~~
kubectl apply -f 2-istio-minikube-reduced.yaml 
~~~
Se puede apreciar la creación de múltiples recursos a continuación:

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img4.jpg" width="400">

Una vez más se aconseja el uso del comando:
~~~
kubectl get po -n istio-system
~~~
Para poder apreciar cuando todos los pods esten corriendo. Esto tomará por lo menos entre 5 y 10 minutos. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img5.jpg" width="400">

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

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img6.jpg" width="150">

Para crear una etiqueta en el namespace default se utiliza el comando: 
~~~
kubectl label namespace default istio-injection=enabled
~~~

A lo que se presenta el mensaje de confirmación. 

~~~
namespace/default labeled
~~~
Ahora pudiendo apreciar la etiqueta:

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img7.jpg" width="250">

Ya con las etiquetas hechas se procede a aplicar el 4to archivo .yaml:
~~~
kubectl apply -f 4-application-full-stack.yaml
~~~

A lo que se nos presenta una imagen como la siguiente:

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img8.jpg" width="500">

Los pods creados puede observarse con el comando:
~~~
$kubectl get po
~~~

Los pods creados toman unos minutos en inicializar, Cabe resaltar que los pods tienen dos contenedores dentro ya que Istio realiza la inyección del proxy.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/img9.jpg" width="500">

Y con eso la instalación debería estar terminada.

<h2 align="center"> Ejecución </h2> 

Ya habiendo explicado y realizado la instalación, lo primero que se debería hacer es
utilizar el comando
~~~
minikube ip
~~~
Esto dado que se necesita la dirección ip (que cambia cada vez que se reinicia
minikube) más el puerto escogido.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa1.png" width="500">

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa2.png" width="500">

La aplicación es a grandes rasgos un sistema que nos permite “rastrear” mediante
gps diferentes vehículos.
Por otra parte en caso de que nos interese apreciar la información que Istio a
recolectado, se usará el componente kiali que se muestra al desplegar los pod con
el comando:
~~~
kubectl get po -n info-system
~~~

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa3.png" width="500">

Además aplicando el comando:
~~~
kubectl get svc -n istio-system
~~~
Conseguimos un despliegue de los servicios entre los cuales se encuentra el de
Kiali, donde se pude apreciar en qué puerto está trabajando y acceder junto con la
ip que se extrajo al inicio.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa4.png" width="500">

Esto nos llevará a la página de login de Kiali.
El trabajo de Kiali es indicar que microservicios forman parte de la maya de servicios
de Istio, tanto como se conecta como la manera en que se desempeñan.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa5.png" width="500">

La pestaña que se nos despliega ya nos señala el comportamiento de los
microservicios.
Existen dos gráficos, uno por cada namespace, el de la derecha representando el
plano de control y el de la izquierda el de datos.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa6.png" width="500">

El trabajo de Istio es monitorear y recolectar información que pasa por su malla de
servicios, por ende la gráfica no será constante, y se mantendrá cambiando
constantemente mientras la aplicación siga en ejecución.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa7.png" width="500">

Es por ello que si bien en un principio no se detectan anomalías a falta de flujo de
información, eventualmente esto podría cambiar.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa8.png" width="500">

Cabe destacar que kiali brinda información sobre qué significa cada leyenda en el
gráfico.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/Programa9.png" width="200">

Esta es la forma en la que la colecta de datos de Istio es de utilidad, pues ayuda a
averiguar en donde se originan los errores, entre quienes, momento, y así poder
evaluar y deducir que esta mal con la aplicación.
