# 7. Impactos del Cambio Climático

## Clase 7.1
### Corrección de sesgo - Bias Correction

Coupled Model Intercomparison Project: Coordina grupos de modeladores climáticos para hacer ejercicios en conjunto.
CMIP5: período histórico hasta el 2005.
CMIP5: período histórico hasta el 2014.

**Problemas con los modelos de escalamiento:**
1) Problemas en la representación de precipitación en climas semiáridos: meses con precipitación nula. Los métodos estadísticos se empiezan a complicar con poca precipitación.
2) Problemas en la representación de problemas orográficos.
3) Problemas en la representación de procesos locales.

- Lo mejor para solucionar estos problemas es utilizar modelos regionales de simulación climática (escalamiento dinámico) acoplados con modelos estocásticos para generar series de tiempo.
**Los modelos regionales son muy caros y los modelos estocásticos son muy complejos. Por eso se opta por el escalamiento estadístico principalmente**

### Modelo Hidrológicos

Una vez que tenemos las series escaladas, pasamos al modelo hidrológico.

En el modelo hidrológico, obtenemos el caudal por medio de la precipitación, temperatura y componentes de la cuenca:

$$Q = f(T, P, \theta)$$

Lo primero es calibrar el modelo hidrológico.
Se pueden obtener proyecciones de escorrentía corriendo el modelo hidrológico utilizando las proyecciones de cada GCMs.

**Condiciones no estacionarias:** se refieren a cambios en las características hidrológicas a lo largo del tiempo, esto significa que las propiedades estadísticas de variables como la precipitación, el caudal y la temperatura no permanecen constantes.

## Clase 7.2

Si bien hay cierta homogeneidad dentro de los modelos, hay salvedades. De partida la grilla no es la misma para todos los modelos.

Para los GCMs, el cero en términos de longitud parte en Greenwich y se da la vuelta hacia el Este hasta llegar a Greenwich nuevamente, donde llega a 360. La latitud tiene valores negativos y positivos dependiendo de la posición respecto al ecuador.

![Coordenadas modelos GCM](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Coordenadas%20GCM.jpg)

La imagen anterior es para los GCM. Esto puede cambiar para otros productos, como CR2MET. Hay que abrir el archivo y hay que ver como están estrucutrados los datos: como está la latitud, la longitud, el tiempo. **Esto es de las primeras cosas que uno tiene que hacer.**

La gracia de los GCM es que los centros se pusieron de acuerdo en las coordenadas, se llegó a cierta convención. Pero otros datos pueden tener otros criterios.
Sin embargo, para los GCM igual se pueden presentar algunas particularidades como la que se menciona en la **Nota Importante.**

Las grillas de los modelos GCM no son las mismas. También puede pasar que un modelo tenga 10 corridas y otro tenga 1 sola para las proyecciones futuras. Estas cosas hay que revisarlas para cada modelo.

Respecto a la grilla desde la cual descargar data de los GCM, se puede hacer dos dos formas:
1) Elegir el nodo más cercano.
2) Hacer una interpolación espacial de los nodos más cercanos al punto a evaluar.

Hay que tener en cuenta que las grillas son super gruesas, por lo que hacer una interpolación espacial puede no ser tan conveniente. Si por ejemplo estamos haciendo un estudio en el valle central de Chile, al hacer una interpolación espacial vamos a tomar grillas que están en la costa, por lo que estaremos tomando data que no es característico del clima que queremos estudiar.
Con la interpolación espacial podríamos estar tomando datos que no son característicos de nuestra área de estudio y estar tomando datos que no nos hacen sentido.

Es importante que abras en netcdf, explores la información dentro del archivo y luego cierres el netcdf.

>NOTAS IMPORTANTES:
> - Los GCM a **escala diaria** sí tienen diferencias entre modelos respecto a como consideran el calendario. Algunos modelos consideran que todos los meses son de 30 dás (años de 360 días). En general la escala mensual funciona bien, pero otros modelos consideran años bisiestos y otros no. **Hay que tener cuidado.**

>Preguntas:
> - El GCM ACCESS-CM2 tiene un archivo para el período histórico y otro archivo para el período futuro. Serían dos archivos para este GCM. No todos son así? Hay algunos que tienen un archivo netcdf por año?
> - Si los GCM tienen datos mensuales de precipitación, esto es la precipitación total mensual?
> - A nivel mensual hace sentido interpolar espacialmente en precipitaciones, pero a nivel diario podríamos estar sobreestimando el número de días de lluvia (comentario de Cristian Chadwick en 28:37). **¿Porqué esto es así? De partida, ¿porque a nivel mensual hace sentido la interpolación espacial? y porque a nivel diario estaríamos sobreestimando los días de lluvia?**

## Clase 7.3

Para que R pueda leer bien un archivo, es super importante que haya solo un punto (".") en el nombre del archivo y que esté justo antes del formato.
En el archivo netcdf de CR2MET se puede ver que la data de precipitación ya viene en unidades de mm, por lo que no es necesario hacer una conversión.

En la siguiente imagen se ve como una cuenca en estudio es abarcado por varias grillas. Se ve que el nodo de arriba a la izquierda no está en el área de influencia de la cuenca, pero el resto estaría explicando el clima en ciertas partes de la cuenca.
A cada una de esas áreas se le asignarían los valores asociados a la información del nodo al centro de cada grilla. El valor promedio para la cuenca (ya sea precipitación, temepratura, etc), sería el promedio ponderado de cada grilla.

![Nodos de las grillas de GCM](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Nodos%20Grillas.jpg)

En la siguiente imagen se ve como sería el cálculo de la precipitación promedio para la Cuenca de Quilimarí.
Si se hace este promedio ponderado a nivel mensual, estaría bien. 
Sin embargo, a nivel diario habría que tener cuidado: uno podría obtener el promedio diario, pero tienes que considerar que en ocasiones algunas partes de la cuenca estarán con lluvia y otras no. Se estaría promediando todo esto espacialmente, pero hay que ver bien como manejar los datos de acuerdo al proceso físico que estás buscando. Tienes que evaluar si te sirve el promedio de la cuenca o no.

![Nodos de las grillas de GCM](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Cuenca%20Quilimari.jpg)

Cristian Chadwick comenta que hay problemas con los métodos de escalamiento estadístico cuando hay muchos valores nulos, lo cual es muy común en varias zonas de Chile. Menciona otras maneras de hacer el escalamiento, como trabajar los datos a escalas más gruesas (por temporadas por ejemplo) y después desagregar (hacer corrección de sesgo a nivel anual y despues desagregar a nivel mensual o diario, hay método matemáticos para esto). La otra manera es reemplazar los valores 0 por otro valor muy pequeño, entre 0 y 0.1, lo cual te arregla el problema matemático y podría servir para hacer el escalamiento estadístico (**ojo que recuerdo que me mencionaron que este truco en realidad no iba a servir, pero es lo que recuerdo, no guardé la nota. Corroborar con alguien más esto**)

>NOTAS IMPORTANTES:
> - Los archivos netcdf también son ráster y aplican los mismos comandos que aplican para los raster.
> - Meteorológicamente si yo tengo una Pp menor a 1 mm, se considera igual a 0 mm. Pero matemáticamente esto es un valor y podría servirme para ajustar a una distribución.
> - Cuando tienes muchos valores 0, se te produce un problema para hacer el escalamiento. Se puede hacer, pero es más desafiante.

>Preguntas:
> - Hay métodos matemáticos para desagregar datos: pasar de escala anual a mensual o diario. ¿Cuáles son estos métodos?



# 8. Uso de R para CC

## Clase 8.1

Más que los valores de precipitación futura, nos interesa saber cuánta agua disponible habrá en una cuenca a futuro.

airGR: Modelo de Precipitacion-Escorrentía implementado en R, modelos de tipo agregado.

Los modelos hidrológicos agergados no se pueden utilizar para resolver cualquier tipo de problema, solo algunos en particular.

Las proyecciones hidrológicas requiere de 4 pasaos principales:
1) Procesamiento de datos observados (paso típico de cualquier análisis hidrológico) -> Cuál es la Pp histórica? cuál es la temperatura? cuál es el caudal? cual es el régimen hidrológico?
2) Procesamiento de datos de proyecciones climáticas -> GCMs (CMIP5, CMIP6).
3) Escalamiento de los datos de GCM a la escala local -> Modelos estadísticos o dinámicos.
4) Obtención de las proyecciones de escorentía mediante un modelo -> Se utiliza Modelo Hidrológico que sepamos que simula bien.

El Modelo Hidrológico es una representación matemática que busca representar el balance hídrico en una cuenca.

Los modelos de airGR han dmeostrado ser buenos modelos para muchas cuencas alrededor del mundo, y en Chile se han obtenido muy buenos resultados para alrededor de 87 cuencas.

![Caracteristicas de modelos hidrologicos incluidos en airGR](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/paquete%20airGR.jpg)

Los Free param. son los parámetros a calibrar para cada modelo.

En la siguiente imagen se ve un esquema de un modelo de tipo agregado. 
En la imagen se utilizan datos observados de ET, Pp y Caudal, promediados para la cuenca completa. Los datos observados de caudal son utilizados para calibrar/comparar con las simulaciones.
Een este caso, la Pp y la ET serían las **forzantes** del modelo.

![Esquema de Modelo Agregado](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Esquema%20Modelo%20Agregado.jpg)

Se trabaja con el modelo airGR GR2M, que tiene 2 parámetros a calibrar:

1) X1: Es el tamaño de almacenamiento de una cuenca (Production store).
2) X5: Es un parámetro que representa el intercambio en una cuenca (Routing store).

X5 es el parámetro que se hace cargo cuando hay problemas con los datos de entrada. Por ejemplo, si al calibrar el código en R (o en cualquier otro lenguaje) se dá cuenta que la cantidad de preciptación ingresada no permite acercarse a los valores de caudal observado (precipitación subestimada), entonces en este parámetro se va a reflejar una entrada de agua adicional que debiese haber. Lo mismo ocurre para el caso contrario, en donde la precipitación ingresada simula valores de caudal por sobre lo observado.

> Como se indicó anteriormente, el modelo airGR GR2M ha demostrado buenos resultados en Chile. Pero para cuencas nivopluviales probar con el modelo anual (airGR GR1A) o con el módulo CemaNeige (https://hydrogr.github.io/airGR/)

>NOTAS IMPORTANTES:
> - CAMELS y CR2MET tienen datos históricos promedio de distintas cuencas desde el año 2019. Para cuencas que no estén en CAMELS o CR2MET se pueden ocupar las tipicas técnicas de la hidrología como isoyetas u ocupar algunas estaciones de pp o tas que luego se pueden extrapolar a la supeficie de la cuenca.
> - Según Reed et al. (2004), es más común encontrar modelos de tipo agregados con mejores resultados en comparación con modelos de tipo distribuido. Sin embargo, esto se debe a que se está evaluando netamente el proceso de precipitación-escorrentía.

Los modelos de airGR son de baja complejidad, ya que buscan los otros procesos físicos propios de una cuenca (fuera de precipitación-escorrentía) quedan promediadas dentro del modelo.

El **modelo PERM** es otro modelo del tipo agregado. Permite representar la componente de derretimiento de nieve (ventaja respecto a airGR), por lo que se puede utilizar para cuencas nivales. Pero airGR es más parsimonioso para cuencas pluviales.
Este modelo tiene mejores resultados en cuencas pluvio-nivales, pero se tienen que calibrar 5 parámetros y no está implementado en R.

Se refire a mas parsimonioso cuando tiene menos parámetros que calibrar.

## Clase 8.2

Si quisieramos saber como va a cambiar la respuesta de la cuenca (escorrentía) frente a, por ejemplo, un cambio en el tipo de suelo, necesitariamos evaluarlo con un modelo de tipo distribuido o semi-distribuido. Sin emabrgo, muchas veces vamos a querer saber como va a responder la cuenca ante un evento de precipitación, por lo que un modelo de tipo agregado estaría bien.

Los productos de re-análisis (como ERA-INTERIM) pueden ser usados como datos observados.

La evapotranspiración es un tema que genera incertidumbre en los modelos hidrológicos. Si queremos un modelo que tenga bien identificada la ET, podemos utilizar estimacione satelitales de la ET para tener valores realistas (SSEBop, por ejemplo.)

**Calibración:** Período en que se calibran o calculan los parámetros del modelo para simular caudales.
**Validación:** Período en que se utiliza el modelo calibrado y se comparan las simulaciones con datos de caudal observado. Es un período que no se utiliza en calibración.

En la calibración buscamos que el modelo aprenda de los datos observados. En la validación verificamos que tan bueno fue este aprendizaje, comparando los resultados con datos que el modelo no "vió" en el período de calibración.
El desempeño del modelo se evalúa para ambos períodos: calibraciín y validación.

Como hidrólogo siempre debo preguntarme qué es lo que quiero lograr con el modelo que estoy calibrando, que quiero simular? quiero simular condiciones más secas? Entonces quizás me conviene calibrar con los datos más secos.

Para el caso de AIRGR GR2M, los parámetros de calibración son X1 (almacenamiento máximo) y X5 (parámetro de intercambio, o de recesión). Normalmente, X5 < X1, pero hay casos en los que es mayor, esto es porque el modelo está recibiendo algún tipo de ingreso adicional.

Normalmente se cumple que X1 > X5.

**Si al calibrar un modelo resulta que la precipitación es menor que la escorentía, se debe probablemente a que hay un error en las mediciones de la precipitación.** En el ejemplo expuesto por Pilar, indica que el modelo ha corrido bien a pesar de este error debido a que el parámetro X5 tomó un valor que hizo posible la calibración aceptable (el modelo interpreta que debe recibir un ingreso adicional, por lo que X5 > X1).

X1 puede ir de 0 a 5000. Este último es un valor elevado, para cuencas con lagos o aportes subterraneos.
X5 puede ir entre -1 y 2.

El modelo PERM, por ejemplo, tiene 5 parámetros a calibrar.

Una vez realizada la **calibración**, procedemos con la **validación**.

Como evaluamos si el modelo lo está haciendo bien?  

1. Coeficiente de determinación $$r^2$$: busca el grado de linealidad entre los datos simulados y observados. Igual a 1 significa que la linealidad es perfecta. Sin embargo, no es capaz de captar un sesgo consistente entre dos series (Es decir, si al simular una escorrentía linealmente representa bien los datos observados, tendremos un $$r^2$$ cercano a uno. Pero si los valores son consistentemente superiores o inferiores a los observados, entonces el modelo está sobre-estimando o sub-estimando, respectivamente. Esto no nos va a servir. Para corregir esto se ocupa el próximo método). Este método es análogo al que uno hace en Excel para determinar el coeficiente de correlación en un gráfico, sin hacerlo pasar por 0. En caso de hacerlo pasar por 0, se está agregando el sesgo y sería análogo al siguiente método.

2. Coeficiente de determinación $$r^2$$ corregido por la pendiente b: es calcular el $$r^2$$ multiplicado por una pendiente.

3. Nash-Sutcliffe efficiency E: índice de Nash, mide la eficiencia del modelo. Vemos que tan bien lo hace el modelo en comparación con sólo usar el promedio de los datos observados para simular los caudales en la cuenca. Un valor igual a 1 significa que el modelo tiene buena eficiencia, si es igual a 0 significa que el modelo lo hace igual que el promedio. 
    - Este índice va de -inf a 1.
    - Un N-SE E menor a 0 implica que el promedio ofrece una mejor estimación que los valores simulados por el modelo. Mejor ocupar el promedio de los datos observados en vez de usar el modelo.
    - Un N-SE E mayor que cero es bueno, pero ojalá tener un modelo lo más cercano a 1 y mayor a 0.5, pero va a depender del criterio.
    - El tener exponenetes cuadrados en la ecuación le da mucho peso a los peaks. Si quiero que se castigue más si no simula bien los peaks (que es lo mismo a querer que el modelo lo haga bien en los peaks), puedo incluso aumentar el exponente. Si no me interesa que el modelo se ajuste a valores peaks, entonces puedo cambiar el exponente o calcular el N-SE del logaritmo de los datos.

4. Nash-Sutcliffe efficiency E exponente modificado: es para abordar lo último del párrafo anterior.

5. Eficiencia de Kling-Gupta (KGE): mide ajuste entre series observadas y simuladas utilizando la eficiencia KGE. Considera un término de sesgo y un término de variabilidad. **Se utilizará un modelo agregado basado en el KGE en aquellos casos donde nos interese tener una buena representación de la variabilidad y la media (caudales altos o bajos).** Este índice tambien se va a fijar en que tan bien lo hace el modelo respecto a la variabilidad. Uno busca un KGE lo más cercano a 1 posible, valores de 0.5 ó 0.6 son aceptables.

El índice KGE es buen índice porque se hace cargo del sesgo y también considera la variabilidad en toda la serie de tiempo.

El modelo no se calibra con GCM, se calibra con registros de estaciones o datos satelitales o productos de re-análisis o precipitaciones/evaporaciones satelitales. Todos estos representan cosas que pasaron, y debieran tener relación con los datos de escorrentía que se han medido.

El GCM lo que representa son condiciones globales y está bien que las precipitaciones de un GCM no concuerden con los datos de precipitación en un lugar y fecha dadas, ya que esa no es su pega. La pega de los productos de re-análisis, por ejemplo, sí es "achuntarle" a lo que pasó.

Uno nunca calibra el modelo con el GCM, **OJO!**

Un rezago en la respuesta de caudal, y que es constante, saldrá en alguna de las métricas. Unos malos índices de eficiencia van a acusar este rezago, indicando que el modelo no está pudiendo responder bien a este rezago.

Un ejemplo de sesgo podría ser la componente nieve, que puede representar un "lag" en la respuesta de la cuenca. Recordar que el módulo CEMANEIGE de AIRGR es capaz de representar la componente nival.

Es ideal buscar un modelo que se ajuste al proceso hidrológico que quiero representar. No todos los modelos se van a ajustar a todos los procesos hidrológicos, o en todas las cuencas. Hay cuencas que tienen lagos más grandes en donde la respuesta de la cuenca ante una precipitación no va a ser inmediata. Lo mismo pasa con presencia de nieve/glaciar en la cuenca.

Los modelos no buscan hacer una **predicción**, sino que buscan hacer **proyecciones** de valores promedios.

**Predicción**: se basa en el análisis de datos históricos y actuales para estimar el futuro. Utiliza modelos matemáticos y estadísticos para hacer una estimación sobre un resultado específico. Por ejemplo, predecir el tiempo basado en datos meteorológicos.

**Proyección**: también se basa en datos históricos, pero se enfoca más en extender una tendencia o patrón en el futuro. No se trata de un evento específico, sino de una extensión de lo que podría suceder si las tendencias actuales continúan. Por ejemplo, proyectar el crecimiento de la población en una ciudad.

**Pilar Barría recomienda utilizar todas las métricas de evaluación, no quedarse con sólo una métrica. Cada métrica indica cosas distintas. Solo de esta forma nos podemos quedar tranquilos respectos a que se está representando bien el comportamiento de la serie observada**

**Residuos:** Corresponden a la diferencia entre los valores observados y simulados.

Se espera que los residuos de los modelos hidrológicos cumplan con las siguientes características:
    1. Deben ser independientes. Que no tengan dependencia entre ellos.
    2. Ser homocedásticos: que la varianza no dependa del período considerado. Esto significa que la varianza sea constante en el tiempo. Si el modelo muestra mucha variabilidad de sus datos simulados para un período específico de tiempo, entonces el modelo no lo esta haciendo tan bien.
    3. Deben tener una distribución normal (es decir, que sean simétricos).

Que los residuos tengan una cierta tendencia (un comportamiento lineal, por ejemplo) significa que el modelo no lo está haciendo bien. Significa que hay una estructura de los datos que no está siendo capturada y que está quedando en los residuos. Como hay una estructura que no está siendo capturada, seguramente tendremos que cambiar de modelo que sí capture esta estructura.

**Se puede ocupar el paquete hydroGOF de R para ver las comparaciones gráficas de las series observadas y simuladas.**

>NOTAS IMPORTANTES:
> - No es recomendable utilizar la ET de Oudin para tomar alguna decisión o algún plan de riego. Es mejor utilizar datos mas precisos en estos casos (como SSEBop).
> - Típicamente se divide la serie de tiempo en 2/3 para la calibración y 1/3 para la validación. **Sin embargo no hay una regla, va a depender del caso**
> - Considerando los cambios de las series hidrometeorológicas durante las últimas décadas, se recomienda calibrar con los últimos años y validar con los primeros años de registro.

>Preguntas:
> - ¿Cómo puedo saber o darme cuenta si los residuos son dependientes o independientes?
> - Se indica que los residuos deben tener una distribución normal. ¿Cuál es el procedimiento para verificar esto? ¿Debo hacer un histograma?


## Clase 8.3

El método de Oudin para estimar la evapotranspiración toma sólo como variabe de entrada la temperatura. Si en un estudio estamos interesados en determinar la evapotranspiración, es recomendable utilizar productos satelitales (como SSEBop).

En la clase, Pilar nos muestra la diferencia entre ET de Hargreaves, ET de Oudin y la precipitación. Se identifica que hay marcada diferencia entre ambas ET, lo que muestra que hay una gran incertidumbre en su determinación. La ET de Oudin es mucho menor, mientras que la de Hargreaves es mucho mayor. En ocasiones Hargreaves es incluso mayor a la precipitación, lo que indica que tampoco es un valor razonable. En el siguiente gráfico se ve la precipitación junto con las ET de Oudin y Hargreaves (total anual) para la cuenca estudiada (Cauquenes):

![ET y Pp total anual](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/ET_y_PP.jpg)

Parte de la incertidumbre de la estimación de la ET se captura en los parámetros del modelo. Con este cálculo de ET lo que se incorpora es la temperatura en la cuenca. Es necesario evaluar más de un método par representar la evaporación y además evaluar la sensibilidad de la temperatura en el modelo. Ante proyecciones, la temperatura puede cambiar mucho y la ET puede ser muy sensible a este cambio. Puede que tenga el doble o triple de ET en el futuro, y la estimación puede ser no muy buena debido a que el método para determinar la ET puede ser muy sensible (o no) ante el cambio de la temperatura. Es bueno tener en mente la sensibilidad de la ET respecto a la T.

Explicación de algunos paquetes de R:
- zoo: permite trabajar fechas y cambiar su formato.
- hydroGOF: tiene las metricas para evaluar modelos hidrologicos (Nash, KGE, etc.)
- hydroTSM: para trabajar con series de tiempo y para analizar residuos.

Si se quisiera saber que pasos seguir para correr cualquiera de los modelos implementados en airGR, debes buscar el modelo en la barra de 'Help':

![Ayuda modelos airGR](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Ayuda%20airGR.jpg)

### Período de calentamiento:
El período de calentamiento en la calibración de un modelo hidrológico se refiere a un intervalo inicial del tiempo de simulación durante el cual el modelo se "prepara" y ajusta sus variables internas antes de que los resultados sean considerados fiables. 
Durante este período, el modelo ajusta sus condiciones iniciales, como niveles de agua en ríos y reservas de humedad en el suelo, para alinearse con las condiciones reales del entorno que está simulando. La idea es evitar el impacto de las condiciones iniciales no realistas que podrían sesgar los resultados.
Es como darle un margen para "entrar en calor" y estabilizarse, asegurando que las salidas del modelo reflejen con mayor precisión la realidad hidrológica del área estudiada.

Si no cuentas con una suficiente cantidad de datos para la calibración, podría ser buena idea que repitas el primer año de registros (por ejemplo), para que utilice este tiempo como de calentamiento.
Pero si tienes varios años de datos, entonces no habría problemas. Simplemente sacrificas los resultados del período que toma el modelo como de calentamiento (primer año, por ejemplo).

En Chile estamos pasando por un período de Mega Sequía desde el año 2010. Es muy común que para este período los modelos sobre-estimen los caudales de este último período.
Se ha identificado que debido a la Mega Sequía la respuesta de las cuencas ha cambiado debido a los caudales bajos. Incluso el rendimiento de las cuencas ha cambiado, lo que provoca problemas en el modelo. 

No hay una receta única para la modelación hidrológica, nosotros tenemos que hacernos las preguntas necesarias para tener un modelo adecuado.

Si tuvieras pocos datos para calibrar y si, por ejemplo, tienes data mensual, podrías utilizar un modelo de tipo diario para calibrar. De esta forma tendrías mas datos con los que trabajar.

>NOTAS IMPORTANTES:
> - Es bueno tener en mente la sensibilidad de la ET con respecto a la temperatura.
> - Si tuvieramos nuestros datos de caudales mensuales en $$(m^3)/s$$ y quisieramos agruparlo a nivel anual, debiésemos obtener el promedio. Sin emabrgo, si están en $$mm$$ se deben sumar los datos mensuales para agruparlos a nivel anual (de hecho, no podemos promediar).
> - Es normal que los modelos hidrológicos tengan algúntipo de error. Lo importante es que los errores no sean persistentes.
> - Ojo que la función CreateRunOptions de airGR te va a tirar una alerta siempre que no le indiques un período de calentamiento. De no indicar alguno, va a tomar el primer año como de calentamiento.

>Preguntas:
> - ¿La ET nunca debería sea mayor que la Pp? (como pasa con Hargreaves en el gráfico expuesto) ¿Podría pasar que la ET es mayor que la Pp por evaporación de lagos u otro cuerpos de agua superficial? ¿o no debería pasar que la ET sea mayor que la Pp porque es una condición física que no se puede dar en el modelo GR2M?
> - Resuelve la duda relacionada con la segunda nota importante: ¿porque los caudales mensuales no se suman al agruparlo a nivel anual? ¿porque sumarlo sería un error? ¿lo que tenemos son datos de caudal promedio mensual? ¿porque sería un error obtener el promedio de los datos de caudal en $$mm$$ si queremos agruparlos a nivel anual?
> - Como era el calculo para normalizar la data de Q? para pasarla a mm.

>Tareas:
> - En esta clase Pilar muestra la funcion ggof. Prueba a ver que cambia cuando cambias el ftype y/o el FUN. Como estamos viendo caudales, debieramos estar viendo promedios y no sumas, pero ve como cambian los datos cambiando FUN entre mean y sum (Pilar lo tiene en la clase como sum, el codigo lo tiene como mean). Corrobora si cambia algo. Tambien verifica con los valores de caudales en el Excel mismo.

# 9. Downscalling de GCMs
## Clase 9.1

NetCDF: archivos que contienen los GCM y los contienen de manera grillada.
Cada nodo de la grilla explica el clima dentro de su área de influencia.

Al GCM le puede pasar que tiene una grilla demasiado gruesa, teniendo una amplia área de influencia. También le puede pasar que presente un sesgo consistente.

Los datos observados pueden ser datos puntuales de una estación o datos provenientes de productos grillados. Ejemplos de datos grillados en Chile tenemos: CR2MET, o productos satelitales grillados como ERA.

Downscaling, 2 grandes grupos: 
    1. Dynamical
    2. Statistical

Dynamical usa un enfoque físico y se suelen llamar Regional Climate Models (RCM), porque son modelos de cambio climático en una región pequeñita. Producen resultados basándose en ecuaciones que mantienen consistencia de los procesos físicos.

Statistical se divide en 3 grupos: 
    1. de tipo Regresivo (regresiones): función matemática que busca la relación de corrección.
    2. generadores de clima: busco generar con ecuaciones estadísticas series de clima que tengan todos los estadísticos de las series locales.
    3. asociados a patrones de clima: simular, por ejemplo, años NIÑO y años NIÑA y simulo estos años pasando de una fase a la otra dependiendo de algunas condiciones. Este metodo va de la mano con los generadores de clima.

Desventajas de Statistical:
    1. Relación entre el predictor y lo que se quiere predecir puede ser no estacionario: Pueden haber cambios en el tiempo y el metodo puede no ser tan bueno para captar estos cambios en el tiempo.
    2. No se tiene retroalimentación del clima: por ejemplo si en cierta zona la tempertura aumenta y esto aumenta derretimiento de nieve y si esto va a producir un cambio de ciertas condiciones fisicas que va a producir otro efecto climático, este método no va a captar esto, mientras que un Modelo Regional sí podría captarlo (dependiendo de cómo esté construido).

El Downscaling Estadístico busca alguna relación estadística entre el GCM y el clima local.

## Clase 9.2

Los Downscaling de tipo Regresivo buscan una función que quite el sesgo de las proyecciones de los GCM.

**Quantile Mapping Bias Correction**

Es un método de corrección de sesgo que toma como supuesto corregir los percentiles de la curva acumulada del GCM con respecto a la histórica.

Cada dato de clima del GCM se pasa por la curva de Función de Distribución Acumulada (CDF) del GCM para calcular su probabilidad y se usa esa probabilidad en la CDF del Histórico para calcular el clima sin sesgo:

![Quantile Mapping Bias Correction](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Quantile%20Mapping%20Bias%20Correction.jpg)

![Quantile Mapping 2](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Quantile%20Mapping%202.jpg)

Matemáticamente, esto se expresa asi:

$$x_{BC\space GCM} = F_{Obs}^{-1}(F_{GCM\space H}(x_{GCM}))$$ 

La distribución Gamma es muy utilizada para precipitaciones, corresponde a la de la siguiente imagen:

![Ddistribucion Gamma](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Distribucion%20Gamma.jpg)

La distribución Gamma es una distribución estrictamente no negativa.

- Este método se usa (originalmente y comúnmente) a nivel mensual.
- Se ha utilizado también a nivel diario y se puede usar a nivel anual.
- Para el caso de **cuencas semi-áridas (o período seco)** puede que no funcione bien a **nivel mensual en las precipitaciones.** (en años sin precipitaciones podría haber problemas para implementar el método).

>NOTAS IMPORTANTES:
> - La distribución Gamma es muy utilizada para precipitaciones.
> - La distribución normal es muy utilizada para temperatura.
> - Luego de aplicar la corrección de sesgo a los datos historicos del GCM, el promedio y la desviación estándar de estos quedan muy parecidos a los del período histórico observado.

## Clase 9.3

**Delta Change**

- Consiste en extraer las tasas de cambio de variables climáticas del GCM y generar clima con dichas tasas de cambio.

$\Delta$ Precipitaciones (%)  
$\Delta$ Temperaturas (°C) 

![Delta Change](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Delta%20Change.jpg)

El delta change se estima entre los datos del GCM.

Una vez obtenidas las tasas de cambio de los GCM, se aplican al clima histórico observado para obtener las proyecciones futuras:

![Delta Change 2](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Delta%20Change%202.jpg)

En la siguiente figura se muestra el procedimiento para aplicar Delta Change:

![Delta Change 3](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Delta%20Change%203.jpg)

El Delta Change es aplicado a la precipitación de manera multiplicativa, por lo que tanto la media como la desviación estándar se ven afectadas:

![Delta Change 4](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Delta%20Change%204.jpg)

Para el caso de la temperatura se aplica de manera aditiva, por lo que la media se ve afectada pero no la desviación estándar:

![Delta Change 5](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Delta%20Change%205.jpg)

>NOTAS IMPORTANTES:
> - La tasa de cambio de precipitación se trabaja en porcentaje, a difernecia de la temperatura que es en magnitud. Esto es porque ante una disminución en la proyección de la precipitación, nos podríamos enfrentar a precipitaciones negativas, lo que no es físicamente posible. Debido a esto, los cambios proyectados en la precipitación se trabajan con porcentajes.
> - En la resolución del ejercicio (clase 9.4) se puede ver que el delta change para precipitación se aplica a las series de precipitaciones medias mensuales de los GCM (historico y futuro). Estos valores de delta change mensuales son los que luego se aplican a la serie histórica observada para obtener los valores de precipitación futura.
> - En la misma resolución del ejercicio se ve que el Delta Change se aplica para los datos de los GCM sin haber aplicado ningún tipo de downscaling (son datos crudos, no escalados).

>Preguntas:
> - En el ejercicio se vió que el delta change se calcula con los promedios mensuales de la precipitación. ¿Se puede hacer el delta change con los promedios de la serie completa? ¿habria sido correcto obtener el promedio entre 2065-2100 y el promedio entre 1979-2014 y sobre estos calcular el delta change?

## Clase 9.4

La fórmula del **Delta Change** se estima con los datos del GCM. Ojo ahí! Es sólo con los datos del GCM.
Una vez que tengamos el delta de cambio de Precipitación y Temperatura, esto lo aplicamos a nuestros datos históricos observados.
Es importante recalcar que con el Delta Change se está calculando el cambio en el clima para una ventana de tiempo (30 años usualmente), no se está obteniendo una serie continua.

La función **Quantile Mapping** usa datos observados y del GCM. Es recomendable partir entendiendo Quantile Mapping para entender los demás. Para el Balance Hídrico Nacional se ocupó el Quantile Delta Mapping.

Recordar que el procedimiento de **Quantile Mapping** es como el que se muestra en la siguiente figura:

![Procedimiento Quantile Mapping](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/QM%201.jpg)

Con el Delta Change yo obtengo una tasa de cambio en una ventana temporal que se lo aplico a los datos históricos. Con funciones de escalamiento (tipo Quantile Mapping) lo que hago es corregir el sesgo de una serie de GCM.
Lo que hago con el QM es obtener una serie del GCM sin sesgo.

Delta Change es recomendable para ocupar porque es muy sencillo, no es un método muy sofisticado.

>NOTAS IMPORTANTES:
> - Delta Change es bueno para ver disponibilidades de agua.
> - Para evaluar eventos extremos, será necesario aplicar métodos más sofisticados de escalamiento.

>Preguntas:
> - No entendí lo que dijeron respecto a los problemas de QM. La serie del GCM va cambiando en el tiempo y puede haber una serie de precipitación que quede fuera de la distribución. Averiguar sobre los problemas de Quantile Mapping.

## Clase 9.5

Al momento de aplicar Quantile Mapping (downscaling) es común asumir una distribución Gamma para la precipitación y una distribución Normal para la temperatura. La distribución Gumbel también puede ser una buena opción para el caso de la temperatura, en caso de que los datos originales presenten alguna asimetría.

En las siguientes dos imágenes se puede ver un ejemplo de Quantile Mapping aplicado a los datos de precipitación que ha resultado **muy bien tanto para el promedio como para la desviación estándar**, lo que indica que la distribución Gamma escogida inicialemnte fué una decisión correcta.

![Ejemplo BC muy bien](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Ejemplo_BC_muybien.jpg)
![Ejemplo BC muy bien 2](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Ejemplo_BC_muybien2.jpg)

En cambio, en las siguientes dos imágenes se puede ver como la distribución Normal elegida para realizar el Quantile Mapping a los datos de temperatura no fue lo más acertado. **Como se puede ver, para el caso del promedio la corrección de sesgo ha resultado ser buena, pero se identifican problemas para el caso de la desviación estándar.** En este caso pudo haber sido mejor elegir otra distribución en lugar de la Normal.

![Ejemplo BC bien](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Ejemplo_BC_bien.jpg)
![Ejemplo BC problemas](https://github.com/felipegarciaesp/Apuntes_MHC/blob/main/Ejemplo_BC_problemas.jpg)

En caso de que nos pasara que la corrección de sesgo presenta problemas, habría que hacer un **test de bondad de ajuste y probar con otra distribución.**

Para ambas distribuciones (Normal y Gamma) se deben obtener parámetros ($$\mu$$, $$\sigma$$, $$\alpha$$ y $$\beta$$), **los cuales deben ser calculados para cada mes**. Es decir, se tendrá un set de parámetros que representen a todos los eneros, todos los febreros, todos los marzos, etc.

Se obtienen las distribuciones correspondientes a cada mes tanto para los datos observados como para cada GCM al que se le quiere aplicar Quantile Mapping.

En resumen, se va a tener que cada mes de cada set de datos que estamos utilizando (datos observados y datos de los GCM) va a tener su propia distribución.

>NOTAS IMPORTANTES:
> - Si al aplicar Quantile Mapping resulta que el promedio y la desviación estándar de los datos corregidos no se ajustan al promedio y desviación estándar de los datos observados, se puede deber a que la distribución escogida no es la óptima. Esto se debe a que los datos originales en realidad no se adaptan a la distribución escogida, por lo que una solución sería cambiar la distribución.
> - El Delta Change funciona super bien y es recomendable usarlo si uno tiene que utilizar un solo método.
> - El método de escalamiento a elegir dependerá del problema específico que se quiere resolver.

>Preguntas:
> - Como puedo identificar cuál es el método de escalamiento apropiado para utilizar? Como identificar si debo utilizar QM, QDM, UQM, MBCn, SDM, etc.? Digamos, de que depende?
> - ¿Qué método de escalamiento es recomendable utilizar para eventos extremos?

>TAREAS:
> - En esta clase Cristian Chadwick habla respecto a ajustar distribuciones por el método de máxima verosimilitud y por método de los momentos. Averigua a que corresponden estos dos métodos. La distribución gamma que se ve en clases está ajustada por el método de los momentos (se calcula un alfa y un beta).
> - Averiguar sobre el método Scale Distribution Mapping. Cristian Cahdwick ha indicado que entender las ecuaciones es un dolor de cabeza.

# 10. Incertidumbre
## Clase 10.1 

La incertidumbre es un estado de conocimiento incompleto, que se debe a una falta de infrmación o a un desacuerdo respecto a lo que es conocido.
La incertidumbre puede deberse a la imprecisión de los datos, definición ambigua de un término, proyección incierta del comportamiento humano, etc.

En el caso de proyecciones climáticas, la incertidumbre se identifica en la dispersión de las simulaciones.

Un modelo de una zona con menor cantidad de datos observados tendrá mayor incertidumbre en sus simulaciones.

>Experimento determinístico
>Un experimento determinístico es aquel en el que el resultado está completamente determinado por las condiciones iniciales y las leyes que rigen el sistema, sin ningún tipo de aleatoriedad o incertidumbre. En otras palabras, si se conocen todas las variables y las condiciones iniciales, se puede predecir exactamente el resultado del experimento. Los resultados del experimento serán los mismos independiente de la cantidad de veces que repita el experimento (siempre y cuando se mantengan constantes las variables y condiciones iniciales).
>Un ejemplo clásico de un experimento determinístico es el lanzamiento de un objeto bajo las leyes de la física clásica: si se conocen la posición, la velocidad inicial, y las fuerzas actuantes, se puede predecir exactamente la trayectoria y la posición futura del objeto.

>Experimento estocástico
>Un experimento estocástico es un tipo de experimento en el cual el resultado no puede predecirse con certeza debido a la presencia de elementos aleatorios. En otras palabras, incluso si se conocen las condiciones iniciales y las leyes que rigen el sistema, el resultado no se puede determinar de antemano y puede variar cada vez que se realiza el experimento.
>Un ejemplo clásico de un experimento estocástico es el lanzamiento de un dado: aunque se conocen todas las posibles caras del dado, no se puede predecir con certeza cuál será el resultado de un lanzamiento específico, ya que cada lanzamiento es independiente y tiene un elemento de aleatoriedad.

Aunque "estocástico" y "aleatorio" a menudo se usan de manera intercambiable, hay una diferencia sutil entre ellos.

**Aleatorio** se refiere a cualquier cosa que ocurre sin un patrón predecible o sin causa determinable. Es puramente el resultado de la casualidad, como el lanzamiento de una moneda.

**Estocástico**, por otro lado, se usa más en contextos matemáticos o científicos y suele implicar un proceso que tiene algún componente aleatorio, pero también puede estar influenciado por otros factores. Por ejemplo, un proceso estocástico puede tener patrones probabilísticos definidos que describen cómo evolucionan los resultados aleatorios a lo largo del tiempo.

En resumen:
- **Aleatorio**: Completamente al azar, sin patrón predecible.
- **Estocástico**: Incluye elementos aleatorios pero puede seguir ciertas leyes probabilísticas o patrones.

La naturaleza del clima es aleatoria ya que tiene componentes que no se pueden parametrizar de manera determinística, por lo que los modelos tienen una componente de ruido aleatoria (o caótica).
No se puede decir con certeza cuánto va a llover un día específico, pero se puede indicar si es probable o no que llueva dependiendo de la estación del año o por revisión de variables como circulación terrestre o las presiones.

Se utilizan muchos modelos para tener mayor confianza respecto a la señal de cambio climático simulada. El nivel de dispersión entre todos los modelos de CC evaluados es lo que se denomina como **incertidumbre**.

**La incertidumbre se va a abordar con un enfoque probabilístico.**

>Los "forzantes radiativos" son factores que alteran el equilibrio energético de la Tierra al influir en la cantidad de energía solar que nuestro planeta recibe o la cantidad de energía térmica que escapa al espacio. Estos forzantes pueden ser tanto naturales como antropogénicos (originados por actividades humanas).
>Ejemplos de forzantes radiativos incluyen:
>- **Gases de efecto invernadero**: Como el dióxido de carbono (CO₂), el metano (CH₄), y el óxido nitroso (N₂O), que atrapan el calor en la atmósfera.
>- **Aerosoles**: Pequeñas partículas en suspensión en el aire, como el polvo, las cenizas volcánicas, y las emisiones industriales, que pueden reflejar o absorber la luz solar.
>- **Cambios en la radiación solar**: Variaciones en la actividad del Sol que afectan la cantidad de energía que llega a la Tierra.
>- **Albedo**: La reflectividad de la superficie terrestre, que puede cambiar por factores como la cobertura de nieve y hielo, o el uso del suelo.
>Estos forzantes juegan un papel crucial en el clima del planeta, y su estudio es fundamental para comprender y predecir el cambio climático.
>En los modelos de cambio climático, las nomenclaturas SSP2-4.5 y SSP5-8.5 hacen referencia a distintas concentraciones de estos forzantes radiativos, siendo el segundo más pesimista que el primero en las proyecciones.

**Equifinalidad de los modelos hidrológicos:** Se refiere a que distintos sets de parámetros de entrada con los que se define un modelo hidrológico podrían dar simulaciones que estadísticamente se ajustan bien a los datos observados. **Esto significa que hay múltiples soluciones posibles que pueden explicar los mismos fenómenos, lo que puede dificultar la identificación del modelo más preciso o realista.**

La **equifinalidad** se puede abordar mediante metodologías como GLUE (Generalised Likelihood Uncertainty Estimation) o como Monte Carlo (para hacer simulaciones repetitivas considerando sets de parametros dentro de los rangos físicamente plausibles).

En R la **equifinalidad** se puede abordar con el paquete **hydroPSO**. Se puede utilizar con airGR y con WEAP.

Los productos de reanálisis podrían llegar a tener mejores resultados en el hemisferio norte frente al hemisferio sur debido a la baja densidad de medición en este último hemisferio.
Recordar que los productos de reanálisis corresponden a simulaciones que son reconstrucciones de lo que ha ocurrido en base a la información observada disponible.



Entender las fuentes de incertidumbres de las proyecciones de cambio climático y de los modelos hidrológicos nos permite poder identificar:
1. Aquellas incertidumbres que podemos disminuir o acotar por medio de mayor información. 
2. Aquellas que en realidad son parte de la naturaleza del fenómeno y tenemos que aprender a lidiar con ellas y presentar los resultados de maneras que sean útiles a pesar de estar ligadas a un desconocimiento o incertidumbre.


>Un **producto de reanálisis** es un conjunto de datos generados mediante modelos que combinan observaciones meteorológicas y climáticas con algoritmos estadísticos o dinámicos para crear una representación más completa y coherente de las condiciones atmosféricas pasadas. Estos productos son útiles para estudios climáticos, hidrológicos y para validar modelos atmosféricos.
>**CR2MET** y **CHIRPS** son ejemplos de productos de reanálisis. 
>- **CR2MET** es un conjunto de datos de precipitación y temperaturas extremas desarrollado por el Centro de Ciencia del Clima y la Resiliencia (CR2), que utiliza modelos estadísticos y observaciones de diferentes fuentes.
>- **CHIRPS** (Climate Hazards Group InfraRed Precipitation with Station data) es otro conjunto de datos de precipitación que combina imágenes de satélites con datos de estaciones meteorológicas para proporcionar una cobertura global de precipitación.

>La diferencia principal entre un producto de reanálisis y un modelo de Circulación General (GCM, por sus siglas en inglés) radica en su propósito y en cómo se construyen:  
> 
>**Producto de Reanálisis**
>- **Propósito:** Crear una representación coherente y completa del estado de la atmósfera en el pasado usando tanto observaciones como modelos.
>- **Método:** Utiliza datos históricos de observaciones (satélites, estaciones meteorológicas, globos sonda, etc.) y los integra con modelos numéricos a través de un proceso llamado asimilación de datos. 
>- **Resultado:** Un conjunto de datos retrospectivos que proporcionan una visión detallada del clima y las condiciones meteorológicas pasadas.  
> 
>**Modelo de Circulación General (GCM)**
>- **Propósito:** Simular el comportamiento de la atmósfera y el clima para predecir el tiempo y el clima futuro, así como estudiar escenarios climáticos bajo diferentes condiciones.
>- **Método:** Utiliza principios físicos y ecuaciones matemáticas que describen los procesos atmosféricos, oceánicos y terrestres. Los GCM no dependen de observaciones pasadas para su funcionamiento, aunque pueden usar datos históricos para la calibración y validación.
>- **Resultado:** Proyecciones y simulaciones del comportamiento del clima en el futuro, basadas en diferentes escenarios y variables climáticas.  
>  
>En resumen, mientras que los productos de reanálisis buscan recrear el pasado con alta precisión mediante la integración de observaciones y modelos, los GCM están diseñados para predecir el futuro basándose en principios físicos y escenarios hipotéticos.

>Preguntas:
> - Etiendo que la finalidad de los GCM es proyectar el clima para ventanas futuras de tiempo y que los productos de reanalisis están diseñados con la finalidad de representar el clima en tiempos pasados. Por lo tanto, me resulta mucho más coherente calibrar un modelo hidrologico con productos de reanalisis frente a GCM escalados para el periodo historico. Pero aún así me queda la duda ¿no es conveniente/aceptable/recomendable calibrar con un GCM escalado en periodo historico con datos observados? Entiendo que a lo mejor el problema puede ser que al escalar el GCM lo que estoy haciendo es ajustar sus estadísticos a nivel mensual con los estadisticos a nivel mensual de los datos observados, lo cual no indica que el GCM para un dia o mes particular me va a dar el dato de precipitación o temperatura exacta para cierto día o mes, entiendo que un producto de reanálisis si va a hacer eso (**preguntar esto igual**). Pero de todas formas, sería muy 'desfachatado' hacer una calibración con un GCM escalado?

>Tarea:
> - Entender y hacer un ejercicio práctico de como se utiliza la metodología GLUE.
