# Contexto

Este proyecto analiza el impacto del uso de diversos suplementos en la ganancia de masa muscular en personas sometidas a 
entrenamiento de fuerza. Para ello, se hizo una revisión sistemática en PUBMED que se puede apreciar en output/diagrama_revision.png.
Los resultados se extrajeron a mano y se estandarizaron a %LMB (% de masa libre de grasa), creando data/datos_limpios.csv.
Después se procedió a hacer el metaanálisis por mean difference (MD)

# Información del proyecto

## Estructura
* `/data`: Contiene todos los .csv que se usaron durante el análisis.
* `/output`: Contiene todos los .png generados por R.
* `metaanalisis.Rmd`: Cuaderno en el que se hizo el análisis.

## ¿Cómo ver el reporte?
Descarga el archivo `metaanalisis.html` y ábrelo en cualquier navegador o visualízalo directamente aquí:
[text](https://rubengil-dev.github.io/metaanalisis-suplementacion-hipertrofia/)

En el se explican por encima todos los conceptos estadísticos necesarios para entender el metaanálisis. Pero si no tienes conocimientos sobre el tema,
lee "Entiende el metaanálisis".

# Entiende el metaanálisis

## Selección de Estudios y Extracción de Datos

### Búsqueda
La búsqueda exacta que se realizó en Pubmed fue:
```
((muscle hypertrophy[Title/Abstract]) OR (muscle growth[Title/Abstract]) OR (skeletal muscle mass[Title/Abstract]) OR (lean body mass[Title/Abstract]) OR (free fat mass[Title/Abstract])) AND ((supplementation[Title/Abstract]) OR (ergogenic aid[Title/Abstract]) OR (ergogenic supplement[Title/Abstract]) OR (supplement intake[Title/Abstract]) OR (sports supplements[Title/Abstract]) OR (dietary supplements[Title/Abstract]) OR (creatine monohydrate[Title/Abstract]) OR (beta-HMB[Title/Abstract]) OR (beta-hydroxy-beta-methylbutyrate[Title/Abstract]) OR (protein[Title/Abstract]) OR (essential amino acids[Title/Abstract]) OR (beta-alanine[Title/Abstract]) OR (caffeine[Title/Abstract]) OR (sodium supplements[Title/Abstract])) AND ((resistance training[Title/Abstract]) OR (strength training[Title/Abstract]) OR (RET[Title/Abstract])) NOT (disease[Title/Abstract]) NOT (disorder[Title/Abstract]) NOT (syndrome[Title/Abstract]) NOT (surgery[Title/Abstract]) NOT (chronic[Title/Abstract]) NOT (injury[Title/Abstract]) NOT (pain[Title/Abstract]) NOT (sarcopenia[Title/Abstract]) NOT (cachexia[Title/Abstract]) NOT (obesity[Title/Abstract]) NOT (overweight[Title/Abstract]) NOT (diabetes[Title/Abstract]) NOT (metabolic syndrome[Title/Abstract]) NOT (metabolic disorder[Title/Abstract]) NOT (neurodegenerative disease[Title/Abstract]) NOT (dementia[Title/Abstract]) NOT (cancer[Title/Abstract]) NOT (stroke[Title/Abstract]) NOT (rats[Title/Abstract]) NOT (mouse[Title/Abstract])
```
### Criterios de inclusión
* Los estudios eran randomized controlled trial (RCT) donde se compara un suplemento con un placebo.

* Los estudios se publicaron entre enero de 2015 y junio de 2025 ambos incluidos.

* Los estudios mencionaban la utilización de uno o más suplementos ergogénicos acompañados de una pauta de entrenamiento de resistencia (RET).

* Los estudios se hicieron en humanos adultos (+18) sin restricción por sexo o nivel de entrenamiento.

### Criterios de exclusión

* Los participantes presentaban cualquier problema de salud.

* Los participantes presentaban obesidad o impedimentos para la realización de ejercicio de resistencia.

* El artículo completo no estaba disponible.

* El resultado del estudio no medía la masa corporal magra (LBM).

* El diseño del estudio no incluía doble ciego.

* El diseño del estudio comparaba un suplemento con otro.

### Resultado

La búsqueda devolvió 865 estudios (+5 encontrados por revisión bibliográfica) de los que finalmente en la revisión se incluyen 26.

## Obtención de datos

Se revisaron manualmente los 26 artículos, extrayendo todos los datos, siendo la ∆%LBM el principal outcome. Para los estudios que no daban este resultado directamente se siguieron las secciones 6.5.2.2 y 6.2.5.8 del manual de Cochrane.

## Análisis Estadístico

Se utilizaron las librerías meta y metafor para realizar un metaanálisis tipo diferncia de medias (MD) y dmetar para el análisis de outliers. También se llevó a cabo un análisis de subgrupos y una metarregresión de alguna de las variables.

* La MD siempre va acompañada de su intervalo de confianza (95% CI).
* Se aplicó el modelo de efectos aleatorios (REM) porque se asume que cada estudio es diferente y aporta una fuente de variabilidad adicional (heterogeneidad).
* Los subgrupos analizados son: Edad, Sexo, Timing de la suplementación, Duración del estudio, Suplemento utilizado, Estado de entrenamiento de los participantes.
* Las metarregresioens se hicieron para edad y duración independientemente y una metarregresión global. 

## Resultados

* El análisis de outliers detecto que "Lowery RP 2016", "Mobley CB_01 2017", "Holwerda AM 2018", "Eghbali E 2024" estaban fuera del IC de MD. Así que fueron descartados.
* Tanto el test de Egger (p = 0.803) como los funnel plots coinciden en que no hay sesgo de publicación entre los estudios seleccionados.

### Resultados del Metaanálisis (post eliminación de outliers)

* Efecto Global: El grupo suplementado ganó un 0.72% (95% CI: 0.26 - 1.19) más de LBM. Estadísticamente hay diferencia, pero en realidad, la magnitud es despreciable.
* Heterogeneidad: La elimación de los outliers hizó que la enorme heterogeneidad inicial (93.4%) bajase a un 47.1%. Esto implica que 4 estudios de los 26 representaban casi el 50% de la heterogeneidad. Su eliminación está justificada.

### Resultados del análisis por subgrupos

Para entender si alguna de las variables analizadas influye directamente en el efecto observado tenemos que examinar Chi². En caso de que los subgrupos fueran distintos, el p_value debería ser inferior a 0.05. Todos menos el subgrupo suplementos tiene un p_valor por encima de 0.05, así que podemos afirmar que ninguno influye en el resultado.

Ahora, observaremos los p_valores de cada subgrupo de suplemento y aplicamos la misma lógica. Si el p_valor es inferior a 0.05 la diferencia de medias observada es estadísticamente significativa. En este caso, sólo la proteína tiene un p_valor estadísticamente significativo.

**Conclusión**: De todas las variables observadas, sólo el tipo de suplemento es realmente relevante a la hora de ganar masa muscular, y dentro de los suplementos, sólo la proteína es estadísticamente efectiva, con un efecto de 0.77% de ganancia de LBM respecto a un placebo.

### Metarregresión

El objetivo es entender si alguna variable se relaciona de manera lineal con el efecto observado. El coeficiente explica esta relación, pero sólo es relevante cuando el test de moderadores (QM), es significativo. Cómo todos los p_valores de QM para las 3 regresiones son superiores a 0.05, podemos decir que ni la edad, ni la duración del estudio, ni todas las variables juntas explican directamente el efecto observado. Esto tiene coherencia con R²'s de 0 (nada de heterogeneidad explicada) y con altos valores de QE (heterogeneidad restante sin explicar).

## Limitaciones

* Sólo se han incluido publicaciones en inglés y en PubMed. No se han revisado otras BBDD, pudiendo dejar estudios relevantes fuera.
* Dada la magnitud del estudio, muchas variables relevantes parala hipertrofia han quedado sin analizar: frecuencia de entrenamientos, tipo de entrenamiento de resistencia, dosis de suplemento aportada...
* Sólo analizar una variable no se aporta una visión global acerca de la ganancia muscular.
* El bajo número de participantes en la mayoría de RCTs, la no diferenciación por sexo en muchos de ellos y la no especificación de la raza pueden impactar al análisis.
* Además, a modo de conclusión personal, tras finalizar el estudio, puedo concluir que hacer un estudio de suplementación en general puede ser un error ya que el scope es demasiado grande y heterogéneo.

## Conclusiones

1. La suplementación tiene un efecto positivo en la ganancia de LBM.
2. Factores como la edad, el sexo, la duración del estudio, el estado de entrenamiento previo y el momento de la toma del suplemento no influyen en la ganancia de LBM.
3. El tipo de suplemento utilizado sí puede alterar los resultados obtenidos siendo la proteína el suplemento con mejor rendimiento.

*Sin embargo, se recomienda considerar estos datos con cautela debido a las limitaciones expuestas con anterioridad.