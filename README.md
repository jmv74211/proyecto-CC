# Análisis de tendencias (geolocalizadas) en Twitter

Proyecto de la asignatura Cloud Computing para el curso 2018/2019 del Máster en Ingeniería Informática, realizado por Gema Correa Fernández. Pincha [aquí](https://gecofer.github.io/proyecto-CC/) para acceder al enlace de la documentación.

**Tabla de Contenidos**

- [Descripción del proyecto](#id1)
- [Arquitecturas software](#id2)
  - [Arquitectura seleccionada](#id3)
- [Microservicios a desarrollar](#id4)
  - [Comunicación entre los microservicios](#id5)

## Novedades



## Descripción del proyecto <a name="id1"></a>

Twitter junto con Instagram son dos de las plataformas sociales más usadas actualmente, por eso mismo, miles de usuarios comparten todo tipo de información en ellas. Este tipo de comportamientos benefician a las empresas dándoles potestad en la obtención de información muy valiosa, cómo por ejemplo ver qué tendencias o _trending topics_ son los más comentados o qué ciudades son las más comentadas en la red. En este caso, yo me voy a centrar en la obtención de datos geolocalizados, es decir,  en la extracción de _trending topics_ o tendencias de los usuarios en Twitter para una región determinada. Para así, poder clasificar las tendencias y establecer la tendencia mayoritaria para una region determinada.


## Arquitecturas software <a name="id2"></a>

Actualmente, las arquitecturas software modernas buscan la consistencia en la velocidad de respuesta al usuario. Sin embargo, en el mercado existen muchos tipos de arquitecturas, es por ello que a veces se hace difícil concretar qué arquitectura se va a utilizar [[2][2]]:

- __Arquitectura en capas__: arquitectura cliente-servidor, tiene 3 o más capas, normalmente se suelen incluir la capa de presentación, la de aplicación, la de lógica de negocio y la de acceso a datos. El problema principal es que solo permite escalado dentro de cada una de las capas, siendo al final alguna de ellas un cuello de botella.
- __Arquitectura dirigida por eventos__: tiene una cola de eventos que se originan en el usuario, pero también de una parte a otra de la arquitectura. Es difícil de testear y su desarrollo es más complicado que la anterior.
- __Arquitectura microkernel__: arquitectura casi monolítica, con un núcleo central al que se pueden añadir funcionalidades mediante _plugins_, siendo su problema principal la escalabilidad, ya que el núcleo puede representar un cuello de botella.
- __Arquitectura basada en microservicios__: arquitectura muy popular, se caracteriza por usar unidades que se van a desplegar de forma independiente y por poder usar tecnologías subyacentes que van desde la virtualización completa en la nube hasta el uso de contenedores _Docker_ en una sola máquina virtual.
- __Arquitectura basada en espacios__: arquitectura antigua, de la década de los 90.

### Arquitectura seleccionada <a name="id3"></a>

Entonces, resulta bastante claro que de todas las arquitecturas comentadas anteriormente vayamos a hacer uso de la __arquitectura basada en microservicios__, ya que es la que más actual y la que menos problemas presenta, además de que nos permite tener diferentes servicios trabajando de forma totalmente independiente unos de otros.

## Microservicios a desarrollar <a name="id4"></a>

<p align="center">
  <img width="210" height="90" src="docs/images/twitter+python.png">
</p>

Para realizar la arquitectura se va a hacer uso del lenguaje [Python](https://www.python.org), y para el desarrollo de los microservicios se puede usar cualquier microframework web para Python, en este caso existe la posibilidad de usar [Django](https://www.djangoproject.com) (más complejo o pensado para un proyecto de grandes dimensiones o que crezca) o [Flask](http://flask.pocoo.org) (más sencillo). Es por ello, que tras haber buscado documentación, hablado con usuarios de ambos microframework y ser la primera vez que uso este tipo de tecnología/herramienta, me he decantado por Flask. Los microservicios previstos a desarrollar son los siguientes:

1. __Consultar API de Twitter__: en este microservicio solo nos vamos a centrar en acceder, consultar y bajarnos información de la API, información obtenida en un [JSON](https://www.json.org).
2. __Procesar información__: en este microservicio vamos a quedarnos con los datos referentes a las tendencias según su localización, en un JSON: [`API.trends_place(id[, exclude])`](http://docs.tweepy.org/en/v3.5.0/api.html).
3. __Almacenar información__: en este microservicio nos vamos a centrar en crear una estructura para los datos que hemos realizado, usando principalmente una BD como [MongoDB](https://www.mongodb.com/es).
4. __Mostrar información__: en este microservicio solo nos interesa mostrar la información relevante de alguna manera específica.
5. Además, necesitamos un sistema de centralización de [__logs__](https://www.elastic.co/products/logstash), al cual todos deben comunicarse.

<p align="center">
  <img width="460" height="350" src="docs/images/estructura_microservicios.png">
</p>

### Comunicación entre los microservicios <a name="id5"></a>

La comunicación entre servicios será realizada por _brokers_, en concreto con [RabbitMQ](https://www.rabbitmq.com), que es un sistema de manejo de colas.

## Bibliotecas de Python para la API de Twitter

Actualmente, La API de Twitter nos permite acceder a toda esa información de forma más simple que la de Instagram, es por eso que me he decantado por esta red social. A continuación, se muestran algunos datos a los que podemos acceder [[1][1]]:

- __Tweets__: búsqueda, publicación, filtrado, etc.
- __Anuncios__: gestión de campañas, análisis, etc.
- __Contenido multimedia__: subir y acceder a fotos, vídeos, GIF animados, etc.
- __Tendencias__: _trending topics_.
- __Geo__: información sobre lugares conocidos, lugares cerca de una ubicación, etc.


Python cuenta muchas bibliotecas desarrolladas para la API de Twitter. Sin embargo, al no haber usado nunca ninguna me es dífil elegir que biblioteca es la mejor. Es por ello, que voy hacer uso de [tweepy](https://github.com/tweepy/tweepy) ya que he oído hablar de ella bastante bien y tiene bastante documetación en la web. De todas maneras, existen otras librerías cómo [twython](https://github.com/ryanmcgrath/twython), [python-twitter](https://github.com/bear/python-twitter) o [TwitterAPI](https://github.com/geduldig/TwitterAPI).



### Tests en Python (código sin test código roto)

Para testear en Python [[3][3]], puedo usar algunas de las librerías que me permiten implementar pruebas unitarias en dicho lenguaje como [unittest](https://docs.python.org/3.5/library/unittest.html), [doctest](https://docs.python.org/3.5/library/doctest.html) o [pytest](https://docs.pytest.org/en/latest/) [[4][4]]. En este caso yo voy hacer uso de la biblioteca [unittest](https://docs.python.org/3.5/library/unittest.html), ya que nos ofrece toda la potencia del lenguaje para probar nuestros programas, lo que significa que ayuda a determinar rápidamente el impacto de cualquier modificación en el resto del código.

Para realizar la configuración de los test correctamente, voy hacer uso de [Travis CL](https://www.travis-ci.org), que es un sistema distribuido de generación e integración continua libre, que me permite conectar mi repositorio de Github y testear después de cada push que haga [[5][5]] [[6][6]].

Para saber más información sobre los [tests](https://gecofer.github.io/proyecto-CC/).

### Despliegue

#### PaaS

Cuando se quiere desplegar una aplicación sobre una infraestructura ya definida y que no va a cambiar se necesita un _Platform as a Service_ o PaaS. Entre los posibles servicios que hay [Heroku](https://www.heroku.com) o [OpenShift](https://www.openshift.com), vamos a escoger [Heroku](https://www.heroku.com), ya que es un servicio fiable, gratuito, ofrece muchas opciones a la hora de elegir el lenguaje y permite integrar Github con Travis.

Despliegue: https://glacial-castle-84194.herokuapp.com

Para saber más información sobre el [despliegue](https://gecofer.github.io/proyecto-CC/).

## Correcciones de aprovisionamiento del hito3

- Corrección de @jmv74211 al aprovisionamiento de @gecofer disponible en este [enlace](https://github.com/jmv74211/Proyecto-cloud-computing/blob/master/docs/hitos/correcci%C3%B3n_a_%40Gecofer.md)

- Comprobación de [@jmv74211](https://github.com/jmv74211/) al provisionamiento en este [enlace](https://github.com/Gecofer/proyecto-CC/blob/master/docs/corrección_a_%40jmv74211.md#comprobación-de-la-aplicación-en-azure)

## Licencia

Proyecto bajo licencia [GNU GLP V3](https://github.com/Gecofer/proyecto-CC/blob/master/LICENSE).


[1]: https://stackabuse.com/accessing-the-twitter-api-with-python/
[2]: https://github.com/JJ/CC/blob/master/documentos/temas/Arquitecturas_para_la_nube.md
[3]: https://github.com/JJ/tests-python
[4]: https://recursospython.com/guias-y-manuales/unit-testing-doc-testing/
[5]: https://www.smartfile.com/blog/testing-python-with-travis-ci/
[6]: https://github.com/softwaresaved/build_and_test_examples

## Enlaces de Interés

- [Publics APIs](https://github.com/toddmotto/public-apis#books)

___Nota__: Se debe tener en cuenta que la realización de un proceso de desarrollo conlleva modificaciones en el futuro, pudiendo modificar la documentación o añadiendo nuevas funcionalidades._
