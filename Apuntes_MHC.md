# 8. Uso de R para CC

## Clase 8.2.mp4

Típicamente se divide la serie de tiempo en 2/3 para la calibración y 1/3 para la validación. **Sin embargo no hay una regla.**

Considerando los cambios de las series hidrometeorológicas durante las últimas décadas, se recomienda calibrar con los últimos años y validar con los primeros años de registro.

Para el caso de AIRGR GR2M, los parámetros de calibración son X1 (almacenamiento máximo) y X5 (parámetro de intercambio, o de recesión). Normalmente, X5 < X1, pero hay casos en los que es mayor, esto es porque el modelo está recibiendo algún tipo de ingreso adicional.

**Si al calibrar un modelo resulta que la precipitación es menor que la escorentía, se debe probablemente a que hay un error en las mediciones de la precipitación.** En el ejemplo expuesto por Pilar, indica que el modelo ha corrido bien a pesar de este error debido a que el parámetro X5 tomó un valor que hizo posible la calibración aceptable (el modelo interpreta que debe recibir un ingreso adicional, por lo que X5 > X1).

El modelo PERM, por ejemplo, tiene 5 parámetros a calibrar.

Como evaluamos si el modelo lo está haciendo bien?
    1. Coeficiente de determinación r^2: busca el grado de linealidad entre los datos simulados y observados. Igual a 1 significa que la linealidad es perfecta. Sin embargo, no es capaz de captar un sesgo consistente entre dos series (Es decir, si al simular una escorrentía linealmente representa bien los datos observados, tendremos un r^2 cercano a uno. Pero si los valores son consistentemente superior o inferiores a los observados, entonces el modelo está sobre-estimando o sub-estimando, respectivamente. Esto no nos va a servir. Para corregir esto s eocupa el próximo método) Este método es análogo al que uno hace en Excel para determinar el coeficiente de correlación en un gráfico, sin hacerlo pasar por 0. En caso de hacerlo pasar por 0, se está agregando el sesgo y sería análogo al siguiente método.

    2. Coeficiente de determinación r^2 corregido por la pendiente b: 

El modelo no se calibra con GCM, se calibra con registros de estaciones o datos satelitales o productos de re-análisis o precipitaciones/evaporaciones satelitales. Todos estos representan cosas que pasaron, y debieran tener relación con los datos de escorrentía que se han medido.

El GCM lo que representa son condiciones globales y está bien que las precipitaciones de un GCM no concuerden con los datos de precipitación en un lugar y fecha dadas, ya que esa no es su pega. La pega de los productos de re-análisis, por ejemplo, sí es "achuntarle" a lo que pasó.

Uno nunca calibra el modelo con el GCM, **OJO!**

Un rezago en la respuesta de caudal, y que es constante, saldrá en alguna de las métricas. Unos malos índices de eficiencia van a acusar este rezago, indicando que el modelo no está pudiendo responder bien a este rezago.

Un ejemplo de sesgo podría ser la componente nieve, que puede representar un "lag" en la respuesta de la cuenca. Recordar que el módulo CEMANEIGE de AIRGR es capaz de representar la componente nival.

Quedé en 00:32:00