# 8. Uso de R para CC

## Clase 8.2.mp4

Típicamente se divide la serie de tiempo en 2/3 para la calibración y 1/3 para la validación. **Sin embargo no hay una regla.**

Considerando los cambios de las series hidrometeorológicas durante las últimas décadas, se recomienda calibrar con los últimos años y validar con los primeros años de registro.

Para el caso de AIRGR GR2M, los parámetros de calibración son X1 (almacenamiento máximo) y X5 (parámetro de intercambio, o de recesión). Normalmente, X5 < X1, pero hay casos en los que es mayor, esto es porque el modelo está recibiendo algún tipo de ingreso adicional.

**Si al calibrar un modelo resulta que la precipitación es menor que la escorentía, se debe probablemente a que hay un error en las mediciones de la precipitación.** En el ejemplo expuesto por Pilar, indica que el modelo ha corrido bien a pesar de este error debido a que el parámetro X5 tomó un valor que hizo posible la calibración aceptable (el modelo interpreta que debe recibir un ingreso adicional, por lo que X5 > X1).

El modelo PERM, por ejemplo, tiene 5 parámetros a calibrar.

Como evaluamos si el modelo lo está haciendo bien?
    1. Coeficiente de determinación r^2: busca el grado de linealidad entre los datos simulados y observados. Igual a 1 significa que la linealidad es perfecta. Sin embargo, no es capaz de captar un sesgo consistente entre dos series (Es decir, si al simular una escorrentía linealmente representa bien los datos observados, tendremos un r^2 cercano a uno. Pero si los valores son consistentemente superior o inferiores a los observados, entonces el modelo está sobre-estimando o sub-estimando, respectivamente. Esto no nos va a servir. Para corregir esto s eocupa el próximo método) Este método es análogo al que uno hace en Excel para determinar el coeficiente de correlación en un gráfico, sin hacerlo pasar por 0. En caso de hacerlo pasar por 0, se está agregando el sesgo y sería análogo al siguiente método.

    2. Coeficiente de determinación r^2 corregido por la pendiente b: es calcular el r^2 multiplicado por una pendiente.

    3. Nash-Sutcliffe efficiency E: índice de Nash, mide la eficiencia del modelo. Vemos que tan bien lo hace el modelo en comparación con sólo usar el promedio de los datos observados para simular los caudales en la cuenca. Un valor igual a 1 significa que el modelo tiene buena eficiencia, si es igual a 0 significa que el modelo lo hace igual que el promedio. 
        - Este índice va de -inf a 1.
        - Un N-SE E menor a 0 implica que el promedio ofrece una mejor estimación que los valores simulados por el modelo.
        - Un N-SE E mayor que cero es bueno, pero ojalá tener un modelo lo más cercano a 1 y mayor a 0,5, pero va a depender del criterio.
        - El tener exponenetes cuadrados en la ecuación le da mucho peso a los peaks. Si quiero que se castigue más si no simula bien los peaks (que es lo mismo a querer que el modelo lo haga bien en los peaks), puedo incluso aumentar el exponente. Si no me interesa que el modelo se ajuste a valores peaks, entonces puedo cambiar el exponente o calcular el N-SE del logaritmo de los datos.

    4. Nash-Sutcliffe efficiency E exponente modificado: es para abordar lo último del párrafo anterior.

    5. Eficiencia de Kling-Gupta (KGE): mide ajuste entre series observadas y simuladas utilizando la eficiencia KGE. Considera un término de sesgo y un término de variabilidad. **Se utilizará un modelo agregado basado en el KGE en aquellos donde nos interese tener una buena representación de la variabilidad y la media (caudales altos o bajos).** Este índice tambien se va a fijar en que tan bien lo hace el modelo respecto a la variabilidad. Uno busca un KGE lo más cercano a 1 posible, valores de 0,5 ó 0,6 son aceptables.

El modelo no se calibra con GCM, se calibra con registros de estaciones o datos satelitales o productos de re-análisis o precipitaciones/evaporaciones satelitales. Todos estos representan cosas que pasaron, y debieran tener relación con los datos de escorrentía que se han medido.

El GCM lo que representa son condiciones globales y está bien que las precipitaciones de un GCM no concuerden con los datos de precipitación en un lugar y fecha dadas, ya que esa no es su pega. La pega de los productos de re-análisis, por ejemplo, sí es "achuntarle" a lo que pasó.

Uno nunca calibra el modelo con el GCM, **OJO!**

Un rezago en la respuesta de caudal, y que es constante, saldrá en alguna de las métricas. Unos malos índices de eficiencia van a acusar este rezago, indicando que el modelo no está pudiendo responder bien a este rezago.

Un ejemplo de sesgo podría ser la componente nieve, que puede representar un "lag" en la respuesta de la cuenca. Recordar que el módulo CEMANEIGE de AIRGR es capaz de representar la componente nival.

Es ideal buscar un modelo que se ajuste al proceso hidrológico que quiero representar. No todos los modelos se va a ajustar a todos los procesos hidrológicos, o en todas las cuencas. Hay cuencas que tienen lagos más grandes en donde la respuesta de la cuenca ante una precipitación puede no ser inmediata.

Los modelos no buscan hacer una **predicción**, sino que buscan hacer **proyecciones** de valores promedios.

**Pilar Barría recomienda utilizar todas las métricas de evaluación, no quedarse con sólo una métrica. Solo de esta forma nos podemos quedar tranquilos respectos a que se está representando bien el comportamiento de la serie observada**

**Residuos:** Corresponden a la diferencia entre los valores observados y simulados.

Se espera que los residuos de los modelos hidrológicos cumplan con las siguientes características:
    1. Deben ser independientes.
    2. Ser homocedásticos: que la varianza no dependa del período considerado. Esto significa que la varianza sea constante en el tiempo.
    3. Deben tener una distribución normal (simétricos).

Que los residuos tengan una cierta tendencia (un comportamiento lineal, por ejemplo) significa que el modelo no lo está haciendo bien. Significa que hay una estructura de los datos que no está siendo capturada y que está quedando en los resuduos. Como hay una estructura que no está siendo capturada, seguramente tendremos que cambiar de modelo que sí capture esta estructura.

**Se puede ocupar el paquete hydroGOF de R para ver las comparaciones gráficas de las series observadas y simuladas.**

## Clase 8.3.mp4

Se hace un ejercicio práctico en esta clase. Es la Guía de modelación AIRGR, por lo que se programa en R.

El método de Oudin para estimar la evapotranspiración toma sólo como variabe de entrada la temperatura. Si es un estudio estamos interesados en determinar la evapotranspiración, es recomendable utilizar etudios satelitales (como SSEBop).

En la clase, Pilar nos muestra la diferencia entre ET de Hargreaves, ET de Ooudin y la precipitación. Se identifica que hay marcada diferencia entre ambas ET, lo que muestra que hay una gran incertidumbre en su determinación. La ET de Oudin es mucho menor, mientras que la de Hargreaves es mucho mayor. En ocasiones Hargreaves es incluso mayor a la precipitación, lo que indica que tampoco es un valor razonable.

Comentario de Ignacio Toro: Parte de la incertidumbre de la estimación de la ET se captura en los parámetros del modelo. Con este cálculo de ET lo que se incorpora es la temperatura en la cuenca. Es necesario evaluar la sensibilidad de la temperatura en el modelo. Ante proyecciones, la temperatura puede cambair mucho y el modelo puede ser muy sensible a este cambio. Puede que tenga el doble o triple de ET en el futuro, y la etimación puede ser no muy buena debido a que la estimación puede ser muy sensible (o no) ante el cambio de la temperatura. Es bueno tener en mente la sensibilidad de la ET respecto a la T.

A partir de acá en la Clase se implementa un Código para usar los Modelos Hidrológicos. Por el momento me saltaré esta parte para hacer cosas más de teoría. Queda pendiente retomar.

# 9. Downscalling de GCMs
## Clase 9.1.mp4

NetCDF: archivos que contienen los GCM y los contienen de manera grillada.
Cada nodo de la grilla explica el clima dentro de su área de influencia.

Al GCM le puede pasar que tiene una grilla demasiado gruesa, teniendo una amplia área de influencia. También le puede pasar que presente un sesgo consistente.

Los datos observados pueden ser datos puntuales de una estación o datos provenientes de productos grillados. Ejemplos de datos grillados en Chile tenemos: CR2MET, o productos satelitales grillados como ERA.

Downscaling, 2 grandes grupos: 
    1. Dynamical
    2. Statistical

Dynamical usa un enfoque físico y se suelen llamar Regional Climate Models (RCM), porque son modelos de cambio climático en una región pequeñita. Producen resultados basándose en ecuaciones que mantienen consitencia de los procesos físicos.

Statistical se divide en 3 grupos: 
    1. de tipo Regresivo (regresiones): función matemática que busca la relación de corrección.
    2. generadores de clima: busco generar con ecuaciones estadísticas series de clima que tengan todos los estadísticos de las series locales.
    3. asociados a patrones de clima: simular, por ejemplo, años NIÑO y años NIÑA y simulo estos años pasando de una fase a la otra dependiendo de algunas condiciones. Este metodo va de la mano con los generadores de clima.

Desventajas de Statistical:
    1. Relación entre el predictor y lo que se quiere predecir puede ser no estacionario: Pueden haber cambios en el tiempo y el metodo puede no ser tan bueno para captar estos cambios en el tiempo.
    2. No se tiene retroalimentación del clima: por ejemplo si en cierta zona la tempertura aumenta y esto aumenta derretimiento de nieve y si esto va a producir un cambio de ciertas condiciones fisicas que va a producir otro efecto climático, este método no va a captar esto, mientras que un Modelo Regional sí podría captarlo (dependiendo de cómo esté construido).

El Downscaling Estadístico busca alguna relación estadística entre el GCM y el clima local.