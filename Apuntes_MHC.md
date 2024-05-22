# 8. Uso de R para CC

## Clase 8.2.mp4

Típicamente se divide la serie de tiempo en 2/3 para la calibración y 1/3 para la validación. **Sin embargo no hay una regla.**

Considerando los cambios de las series hidrometeorológicas durante las últimas décadas, se recomienda calibrar con los últimos años y validar con los primeros años de registro.

Para el caso de AIRGR GR2M, los parámetros de calibración son X1 (almacenamiento máximo) y X5 (parámetro de intercambio, o de recesión). Normalmente, X5 < X1, pero hay casos en los que es mayor, esto es porque el modelo está recibiendo algún tipo de ingreso adicional.

**Si al calibrar un modelo resulta que la precipitación es menor que la escorentía, se debe probablemente a que hay un error en las mediciones de la precipitación.** En el ejemplo expuesto por Pilar, indica que el modelo ha corrido bien a pesar de este error debido a que el parámetro X5 tomó un valor que hizo posible la calibración aceptable (el modelo interpreta que debe recibir un ingreso adicional, por lo que X5 > X1).

Quedé en 00:21:10