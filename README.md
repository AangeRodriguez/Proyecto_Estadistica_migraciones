# Proyecto_Estadistica_migraciones


Diccionario de datos:

Country_Name (Variable Nominal): Es la variable que se utilizará como unidad de análisis, contiene el nombre de los países analizados.

Country_code (Variable Nominal): Contiene el código de los nombres de lps países (Ejemplo: Indoneasia = IDS).

Migración_Neta_2024_(En_número_de_personas) (Variable Numérica Discreta): Contiene el total del número de personas que migran y emigran del país (Si el número es positivo, <ejemplo: "12435"> significa que en total hubo 12435 inmigrantes nuevos en el país. Si el número es negativo <ejemplo: -1000>, significa que en total, mil personas terminaron saliendo del país) en el año 2024.

Inactividad_de_PoblaciónApta_Trabajo_en_%_2024 (Variable Numérica Continua): Contiene, y durante el año 2024, en porcentaje, el número de personas que son aptas para trabajar, pero que se encuentran desempleadas. 

PBI_en_Dólares_2024 (Variable Numérica Continua): Mide, en el año 2024, en dólares, el PBI del país analizado. 

Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023 (Variable Numérica Continua): Mide en porcentaje, durante el año 2023, la estabilidad política y la ausencia de violencia dentro del país analizado. 



```{r}
library(rio)
library(dplyr)
```

```{r}
Migraciones = import("Migraciones_2024.xlsx")
```

```{r}
str(Migraciones)
```


```{r}
mediaPBI = mean(Migraciones$PBI_en_Dólares_2024,na.rm = TRUE)
```

```{r}
mediaESTPOL = mean(Migraciones$`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, na.rm = TRUE)
```


```{r}
Migraciones = Migraciones %>%
  mutate(PBI_en_Dólares_2024 = case_when(is.na(PBI_en_Dólares_2024)~mediaPBI,
                                         TRUE ~ PBI_en_Dólares_2024))
```

```{r}
Migraciones = Migraciones %>%
  mutate(`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023` = case_when(is.na(`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`)~mediaESTPOL,
                                                                                         TRUE ~ `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`))
```
