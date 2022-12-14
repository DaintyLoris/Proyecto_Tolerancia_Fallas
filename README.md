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
 
(El último equipo al que le alcanzo a revisar el Lunes)

</h2>

<h2 align="center"> Introdución </h2> 

¿Qué es la intolerancia a fallos (Fault Tolerance)?

Fault Tolerance es la característica incorporada en el sistema que permite su buen funcionamiento incluso después de que ocurre una falla en algunos de sus componentes. Un diseño tolerante a fallas puede causar una reducción en el nivel de productividad o un mayor tiempo de respuesta. Sin embargo, se asegura de que todo el sistema no falle. Por lo tanto, en resumen, funciona como un mecanismo de afrontamiento en un sistema que tiene como objetivo la autoestabilización.

¿Qué es Docker?

La tecnología Docker utiliza el kernel de Linux y sus funciones, como los grupos de control y los espacios de nombre, para dividir los procesos y ejecutarlos de manera independiente. El propósito de los contenedores es ejecutar varios procesos y aplicaciones por separado para que se pueda aprovechar mejor la infraestructura y, al mismo tiempo, conservar la seguridad que se obtendría con los sistemas individuales.

¿Qué es Kubernetes?

Kubernetes es una plataforma portable y extensible de código abierto para administrar cargas de trabajo y servicios. Kubernetes facilita la automatización y la configuración declarativa. Tiene un ecosistema grande y en rápido crecimiento. El soporte, las herramientas y los servicios para Kubernetes están ampliamente disponibles.

¿Qué es una malla de servicios?

Una malla de servicios o service mesh, se utiliza para controlar el intercambio de datos entre las distintas partes de una aplicación. A diferencia de otros sistemas que también administran esta comunicación, la malla de servicios es una capa visible y específica de la infraestructura integrada a la aplicación, la cual puede registrar si las distintas partes interactúan bien o no, a fin de facilitar la optimización de las comunicaciones y evitar el tiempo de inactividad a medida que crece una aplicación.

¿Qué es Istio?
Istio es una plataforma de malla de servicios con tecnología de open source que permite controlar el intercambio de datos entre los microservicios. Incluye API que le permiten integrarse a cualquier plataforma de registro, telemetría o sistema de políticas.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/service-mesh-1680.png" width="500">

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


<h2 align="center"> Failure injection </h2> 

Como se pudo apreciar anteriormente Kiali facilita la tarea de rastrear errores.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi1.jpg" width="500">

En este caso nos encontramos con lo que en apariencia es un cuello de botella en el flujo de datos, pues el sistema a medida que el tiempo avanza se ralentiza. Kiali además ofrece más información sobre los códigos de error que refuerzan la deducción anterior. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi2.jpg" width="300">

En este punto podemos recurrir a otra herramienta de Istio llamada Jaeger. Esta herramienta permite apreciar que sucede a los requests a medida que viajan por el sistema. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi3.jpg" width="500">

Si se elige el servicio que se tiene identificado como el causante del problema se obtiene una gráfica, la cual indica los tiempos de respuesta de los requests. Gracias a esto podemos apreciar que varios de los requests están tardando bastante en dar respuesta. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi4.jpg" width="500">

Este error nos provoca una cascada de otros errores dado que los requests nuevos son demorados por los viejos. Esto es un problema serio pues puede estar comprometiendo todo el sistema. 

Sabemos que en un sistema tolerante a fallas debería de ser posible tumbar un servicio y que el resto del sistema funcione, aun si no lo hace al 100%. No obstante en este caso nos estamos topando con un pod que realiza una llamada al exterior, por lo que ese elemento externo no puede tumbarse.

Para prevenir este problema podría dársele un timeout como se sugiere en la documentación de Istio, de modo que cualquiera request que no dé respuesta oportuna en un tiempo fijo será desechada. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi5.jpg" width="400">

Si volvemos a Kiali podemos ver como los Post ahora están cayendo en un 100% al llegar a ese segmento. Esto se debe a que al realizar la llamada al exterior y recibir respuesta el request se desecha, de modo que aun si no completa su recorrido al salir, sí que cumple dentro del sistema. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi6.jpg" width="500">

Esto también se puede observar en los códigos de error que Kiali está percibiendo. 

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi7.jpg" width="300">

Y si ahora se recurre una vez más a jaeger podemos observar el drástico descenso en los tiempos de respuesta de los request dentro del sistema.

<img src="https://github.com/DaintyLoris/Proyecto_Tolerancia_Fallas/blob/main/assets/fi8.jpg" width="500">

<h2 align="center"> Conclusión </h2> 

<div align="justify">
  
Programar no es una tarea sencilla, aún especializando en un área y rodeando de que que se especializa en otra, a menudo los problemas no se hacen esperar. Y es que a diferencia de un doctor que tiene un paciente que le pueda decir que le duele, o un arquitecto identificar riesgos en su maqueta, un programador no puede preguntarle al software que desarrolla que no le gusta o tener una referencia física de que está “mal conectado”. Sumando esto al hecho de que si o si un problema eventualmente surgirá a lo largo de la vida de un sistema informático, aprender a identificar y corregir fallos es imperativo, más a falta de experiencia de un programador en tratar con ciertos errores y no conocer a alguien que ya los haya tratado, corregir fallos puede ser muy frustrante.
  
No obstante hoy en día existen herramientas que nos permiten identificar errores más fácilmente, siendo tal el caso de las mallas de servicios, pues estas nos representan de manera gráfica como los microservicios de las aplicaciones se comunican, que va a donde y cual es el resultado de la interacción. Gracias a esto es posible identificar errores más fácilmente, aún si se presentara el peor caso donde ninguna persona que desarrolló o cuanto menos conozca el software esté disponible temporal o permanentemente, y la persona a cargo del sistema no tiene claro el funcionamiento del mismo. 
  
Aun si no nos encontramos en un caso tan desafortunado como el descrito previamente, es un hecho que todo programador agradece cualquier herramienta que le facilite la identificación de problemas en sistemas informáticos complejos, tenga o no experiencia puesto que a menudo un fallo puede deberse a múltiples causas que deben descartarse, mientras que el fallo como tal a su vez puede tener multiple soluciones que a su vez deben probarse para llegar a la más óptima. 
  
</div> 
