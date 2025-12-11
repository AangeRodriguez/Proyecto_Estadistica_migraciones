# Proyecto_Estadistica_migraciones

# Link Dashboard
https://rpubs.com/aangerodriguez/1374880

# Diccionario de datos:

Country_Name (Variable Nominal): Es la variable que se utilizará como unidad de análisis, contiene el nombre de los países analizados.

Country_code (Variable Nominal): Contiene el código de los nombres de lps países (Ejemplo: Indoneasia = IDS).

Migración_Neta_2024_(En_número_de_personas) (Variable Numérica Discreta): Contiene el total del número de personas que migran y emigran del país (Si el número es positivo, <ejemplo: "12435"> significa que en total hubo 12435 inmigrantes nuevos en el país. Si el número es negativo <ejemplo: -1000>, significa que en total, mil personas terminaron saliendo del país) en el año 2024.

Inactividad_de_PoblaciónApta_Trabajo_en_%_2024 (Variable Numérica Continua): Contiene, y durante el año 2024, en porcentaje, el número de personas que son aptas para trabajar, pero que se encuentran desempleadas. 

PBI_en_Dólares_2024 (Variable Numérica Continua): Mide, en el año 2024, en dólares, el PBI del país analizado. 

Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023 (Variable Numérica Continua): Mide en porcentaje, durante el año 2023, la estabilidad política y la ausencia de violencia dentro del país analizado. 

# Entrega Final

```{r}
library(rio)
library(dplyr)
```


Limpieza de datos: 

```{r}
Migra = import("Migra.xls")
```

```{r}
Migra = Migra[-c(1:2), ]
```

```{r}
colnames(Migra) = Migra[1, ]
```

```{r}
Migra = Migra[-1, ]
```

```{r}
InacPol = import("InactivPob.xls")
```
```{r}
InacPol = InacPol[-c(1:2), ]
```

```{r}
colnames(InacPol) = InacPol[1, ]
```

```{r}
InacPol = InacPol[-1, ]
```

```{r}
PBI = import("PBI.xls")
```

```{r}
PBI = PBI[-c(1:2), ]
```

```{r}
colnames(PBI) = PBI[1, ]
```

```{r}
PBI = PBI[-1, ]
```

```{r}
Estabilidad = import("Estabilidad.xls")
```

```{r}
Estabilidad = Estabilidad[-c(1:2), ]
```

```{r}
colnames(Estabilidad) = Estabilidad[1, ]
```

```{r}
Estabilidad = Estabilidad[-1, ]
```

Filtración de las variables a utilizar:


```{r}
Migra = select(Migra, `Country Name`, `Country Code`, `2024`)
```

```{r}
InacPol = select(InacPol, `Country Name`, `Country Code`, `2024`)
```

```{r}
PBI = select(PBI, `Country Name`, `Country Code`, `2024`)
```

```{r}
Estabilidad = select(Estabilidad, `Country Name`, `Country Code`, `2023`)
```

Cambio de Nombre

```{r}
Migra = rename(Migra, Migracion_Neta_2024 = `2024`)
```

```{r}
InacPol = rename(InacPol, Población_Inactiva_Trabajo_Porcentaje_2024 = `2024`)
```

```{r}
PBI = rename(PBI, PBI_en_Dólares_2024 = `2024`)
```

```{r}
Estabilidad = rename(Estabilidad, Estabilidad_Política_Porcentaje_2023 = `2023`)
```

Unión de la data: 

```{r}
Migraciones = merge(Migra, InacPol, by = c("Country Code"), all = TRUE)
```

```{r}
Migraciones = merge(Migraciones, PBI, by = "Country Code", all = TRUE)
```

```{r}
Migraciones = merge(Migraciones, Estabilidad, by = "Country Code", all = TRUE)
```

Limpieza Final Data:

```{r}
Migraciones = select(Migraciones, "Country Code", "Country Name.x", "Migracion_Neta_2024", "Población_Inactiva_Trabajo_Porcentaje_2024", "PBI_en_Dólares_2024", "Estabilidad_Política_Porcentaje_2023")
```

```{r}
Migraciones = Migraciones[-c(2, 4, 8, 35, 37, 45, 50, 62, 63, 64, 65, 66, 69, 74, 75, 80, 96, 97, 99, 103, 104, 105, 106, 108, 111, 129, 130, 135, 136, 137, 139, 140, 141, 143, 147, 148, 154, 157, 162, 171, 182, 184, 192, 197, 198, 199, 200, 205, 216, 218, 219, 226, 229, 231, 232, 237, 239, 241, 242, 250, 260),]
```


```{r}
Migraciones = Migraciones %>%
  mutate(`Migracion_Neta_2024` = as.numeric(`Migracion_Neta_2024`))
```

```{r}
Migraciones = Migraciones %>%
  mutate(`Población_Inactiva_Trabajo_Porcentaje_2024` = as.numeric(`Población_Inactiva_Trabajo_Porcentaje_2024`))
```


```{r}
Migraciones = Migraciones %>%
  mutate(`PBI_en_Dólares_2024` = as.numeric(`PBI_en_Dólares_2024`))
```


```{r}
Migraciones = Migraciones %>%
  mutate(`Estabilidad_Política_Porcentaje_2023` = as.numeric(`Estabilidad_Política_Porcentaje_2023`))
```


```{r}
str(Migraciones)
```
último cambio de nombre a las variables: 

```{r}
Migraciones = rename(Migraciones, `Migración_Neta_2024_(En_número_de_personas)` = Migracion_Neta_2024)
```

```{r}
Migraciones = rename(Migraciones, `Country Name` = `Country Name.x`)
```

```{r}
Migraciones = rename(Migraciones, `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023` = `Estabilidad_Política_Porcentaje_2023`)
```

```{r}
Migraciones = rename(Migraciones, `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024` = `Población_Inactiva_Trabajo_Porcentaje_2024`)
```

Cubrir datos faltantes: 

```{r Media de PBI}
mediaPBI = mean(Migraciones$PBI_en_Dólares_2024,na.rm = TRUE)
```

```{r Media de Estabilidad Política}
mediaESTPOL = mean(Migraciones$`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, na.rm = TRUE)
```


```{r Completar Casos de PBI}
Migraciones = Migraciones %>%
  mutate(PBI_en_Dólares_2024 = case_when(is.na(PBI_en_Dólares_2024)~mediaPBI,
                                         TRUE ~ PBI_en_Dólares_2024))
```

```{r Completar Casos de Estabilidad Política}
Migraciones = Migraciones %>%
  mutate(`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023` = case_when(is.na(`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`)~mediaESTPOL,
                                                                                         TRUE ~ `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`))
```


```{r Librería Regresiones}
library(lmtest)
library(car)
```



### Regresión Gaussiana Migraciones Netas

```{r Gaussiana Migraciones Netas}
Gaussiana = lm(`Migración_Neta_2024_(En_número_de_personas)` ~ 
                             `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024` + `PBI_en_Dólares_2024` + 
                             `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, 
                           data = Migraciones)

summary(Gaussiana)
```

    Evaluando la regresión:
                            Linealidad:
                            
```{r Linealidad Migraciones}
plot(Gaussiana, 1)

mean(Gaussiana$residuals)
```
    
                            Homocedasticidad:
                            
```{r Homocedasticidad Migraciones}
plot(Gaussiana, 3)

bptest(Gaussiana)
```
                            
                            
                            Normalidad de los residuos:
                            
```{r Normalidad de Residuos Migraciones}
plot(Gaussiana, 2)

shapiro.test(Gaussiana$residuals)
```
                            
                            No multicolinealidad:
                            
```{r No Multicolinealidad Migraciones}
vif(Gaussiana)
```
                            
                            
                            Valores influyentes:

```{r Valores Influyentes Migraciones}
plot(Gaussiana, 5)
```

Regresión Gaussiana Inactividad de Trabajo

```{r Regresión Gaussiana de Inactividad para el Trabajo}
GaussianaTrabajo = lm(`Inactividad_de_PoblaciónApta_Trabajo_en_%_2024` ~ `Migración_Neta_2024_(En_número_de_personas)` + `PBI_en_Dólares_2024` +   `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023` , data = Migraciones)

summary(GaussianaTrabajo)
```
    Evaluando la regresión:
                            Linealidad:
                            
```{r Linealidad InacTrabajo}
plot(GaussianaTrabajo, 1)

mean(GaussianaTrabajo$residuals)
```

 
                            Homocedasticidad:
                            
```{r Homocedasticidad InacTrabajo}
plot(GaussianaTrabajo, 3)

bptest(GaussianaTrabajo)
```

 
                            Normalidad de los residuos:
                            
```{r Normalidad de Residuos InacTrabajo}
plot(GaussianaTrabajo, 2)

shapiro.test(GaussianaTrabajo$residuals)
```


   No multicolinealidad:
                            
```{r No Multicolinealidad InacTrabajo}
vif(GaussianaTrabajo)
```
                            
                            
                            Valores influyentes:

```{r Valores Influyentes InacTrabajo}
plot(GaussianaTrabajo, 5)
```

 Gaussiana de PBI en Dólares


```{r Regresión PBI}
GaussianaPBI = lm(`PBI_en_Dólares_2024` ~ `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024` +  `Migración_Neta_2024_(En_número_de_personas)` + `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, data = Migraciones)

summary(GaussianaPBI)
```
 Evaluando la regresión:
                            Linealidad:
                            
```{r Linealidad PBI}
plot(GaussianaPBI, 1)

mean(GaussianaPBI$residuals)
```
              Homocedasticidad:
              
```{r Homocedasticidad PBI}
plot(GaussianaPBI, 3)

bptest(GaussianaPBI)
```
              
              
              Normalidad de los residuos:
              
```{r Normalidad de Residuos PBI}
plot(GaussianaPBI, 2)

shapiro.test(GaussianaPBI$residuals)
```
              
              
              No multicolinealidad:
              
```{r No multicolinealidad PBI}
vif(GaussianaPBI)
```
              
              
              Valores influyentes:

```{r Valores Inlfuyentes PBI}
plot(GaussianaPBI, 5)
```

 Gaussiana de Porcentaje de Estabilidad Política 

```{r Regresión Gaussiana Estabilidad Política}
GaussianaEstPol = lm(`Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023` ~ `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024` +  `Migración_Neta_2024_(En_número_de_personas)` + `PBI_en_Dólares_2024`, data = Migraciones)

summary(GaussianaEstPol)
```


 Evaluando la regresión:
                            Linealidad:
                            
```{r Linealidad Estabilidad Política}
plot(GaussianaEstPol, 1)

mean(GaussianaEstPol$residuals)
```

                Homocedasticidad:
                
```{r Homocedasticidad Estabilidad Política}
plot(GaussianaEstPol, 3)

bptest(GaussianaEstPol)
```
                
                
                Normalidad de los residuos:
                
```{r Normalidad de Residuos Estabilidad Política}
plot(GaussianaEstPol, 2)

shapiro.test(GaussianaEstPol$residuals)
```
                
                
                No Multicolinealidad:
                
```{r No Multicolinealidad Estabilidad Política}
vif(GaussianaEstPol)
```
                
                
                Valores influyentes:


```{r Valores Influyentes Estabilidad Política}
plot(GaussianaEstPol, 5)
```


 Gráficos: 

```{r Librería ggplot}
library(ggplot2)
```

  ### Gráfico para Migraciones Netas:
   
```{r Gráfico Migraciones}
GráficoMigraNeta= ggplot(Migraciones, aes(x = `Country Name`, 
                                           y = `Migración_Neta_2024_(En_número_de_personas)`)) +
  geom_col(fill = "steelblue") + 
  theme_minimal() +
  labs(title = "Migración Neta por País (2024)",
       x = "País",
       y = "Número de Personas") +
  theme(axis.text.x = element_text(angle = 90, hjust = 1, size = 5))

GráficoMigraNeta
```
   

Tabla de comparación R-ajustado:

```{r Tabla de comparación R Ajustado}
TablaResumen = data.frame(
  TablaModelo = c("Migración Neta", "Inactividad Laboral", "PBI", "Estabilidad política"),
  RAjustado = c(summary(Gaussiana)$adj.r.squared,
                 summary(GaussianaTrabajo)$adj.r.squared,
                 summary(GaussianaPBI)$adj.r.squared,
                 summary(GaussianaEstPol)$adj.r.squared)
)

TablaResumen
```
La regresión que tiene a Estabilidad Política como variable dependiente resulta la más explicativa. Posee el mayor valor de R ajustado. 
   
   Gráfico Para Estabilidad Política:
   
```{r Gráfico simple Estabilidad Política}
ggplot(Migraciones, aes(x = `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`)) +
  geom_histogram(bins = 208, fill = "orange", color = "black") +
  theme_minimal() +
  labs(title = "Distribución de Estabilidad Política",
       x = "Número del País",
       y = "Porcentaje de la estabilidad política")
```
   
   
   Gráfico para PBI:
   
```{r Gráfico Simple PBI}
ggplot(Migraciones, aes(x = `Country Name`, y = `PBI_en_Dólares_2024`)) +
  geom_col(fill = "darkred") +
  theme_minimal() +
  labs(title = "Distribución del PBI por País (2024)",
       x = "País",
       y = "PBI (Dólares)") +
  theme(axis.text.x = element_text(angle = 90, hjust = 1, size = 5))
```
   
   
   Gráfico sobre Población inactiva laboralmente:
   
   
```{r Grafico Simple InacTrabajo}
ggplot(Migraciones, aes(x = `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024`)) +
  geom_histogram(bins = 208, fill = "red4", color = "yellow2") + 
  theme_minimal() +
  labs(title = "Distribución de Inactividad Laboral",
       subtitle = "Histograma de Frecuencia",
       y = "Porcentaje de Inactividad (%)",
       x = "Númeo del país")
```
   
   
   
    Gráfico sobre Población Apta para el trabajo que se encuentra inactiva:

```{r Gráfico Corrleación InacTrabajo}
GráficoInactividad = ggplot(Migraciones, aes(x = `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024`, 
                        y = `Migración_Neta_2024_(En_número_de_personas)`)) +
  geom_point(color = "steelblue", size = 3) +  
  geom_smooth(method = "lm", color = "red", se = FALSE) + 
  theme_minimal() +
  labs(title = "Inactividad de población apta para el trabajo a partir de las migraciones netas \n en los países",
       x = "Inactividad (%)",
       y = "Migración Neta")
  
GráficoInactividad  
```



       Gráfico sobre Producto Bruto Interno (En dólares) en los estados:
          
```{r Gráfico Correlación PBI}
GráficoPBI = ggplot(Migraciones, aes(x = `PBI_en_Dólares_2024`, 
                        y = `Migración_Neta_2024_(En_número_de_personas)`)) +
  geom_point(color = "darkred", size = 3) +
  geom_smooth(method = "lm", color = "blue", se = FALSE) +
  theme_minimal() +
  labs(title = "PBI de los países a partir de las migraciones netas",
       x = "PBI (Dólares)",
       y = "Migración Neta")

GráficoPBI
```


  ### Gráfico sobre Establilidad Política 

```{r Gráfico Correlación Estabilidad Política}
GráficoEstPol = ggplot(Migraciones, aes(x = `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, 
                        y = `Migración_Neta_2024_(En_número_de_personas)`)) +
  geom_point(color = "forestgreen", size = 3) +
  geom_smooth(method = "lm", color = "orange", se = FALSE) +
  theme_minimal() +
  labs(title = "Estabilidad Política en los estados \n a partir de las migraciones netas",
       x = "Estabilidad Política (%)",
       y = "Migración Neta")

GráficoEstPol
```

### Gráfico de Correlación entre Inactividad laboral y estabilidad política:

```{r Gráfico Correlación Estabilidad Política e Inactividad Laboral}
GráficoInacEstPol = ggplot(Migraciones, aes(x = `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, 
                        y = `Inactividad_de_PoblaciónApta_Trabajo_en_%_2024`)) +
  geom_point(color = "blue4", size = 3) +
  geom_smooth(method = "lm", color = "orange", se = FALSE) +
  theme_minimal() +
  labs(title = "Estabilidad Política en los estados \n a partir de la inactividad de personas aptas para trabajar",
       x = "Estabilidad Política (%)",
       y = "Inactividad Laboral")

GráficoInacEstPol
```

  Gráfico sobre Estabilidad Política y PBI:
  
```{r Gráfico Correlación Estabilidad Política y PBI}
GráficoPBIEstPol = ggplot(Migraciones, aes(x = `Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023`, 
                                           y = `PBI_en_Dólares_2024`)) +
  geom_point(color = "orange3", size = 3) +
  geom_smooth(method = "lm", color = "black", se = FALSE) +
  theme_minimal() +
  labs(title = "PBI en los estados \n a partir de la Estabilidad Política",
       x = "Estabilidad Política (%)",
       y = "PBI (Dólares)")

GráficoPBIEstPol
```
  

Análisis Factorial Exploratorio:

```{r Librería Factorial}
library(psych)
library(corrplot)
```

```{r Subdata Factorial}
Factorial = c("Migración_Neta_2024_(En_número_de_personas)", 
             "Inactividad_de_PoblaciónApta_Trabajo_en_%_2024", 
             "PBI_en_Dólares_2024", 
             "Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023")
```

```{r Data Factorial}
DataFactorial = Migraciones[, Factorial]
```

```{r Limpieza Data Factorial}
DataFactorial = na.omit(DataFactorial)
```

        La matriz de correlación:
        
```{r Matriz de Correlación}
MatrizCorrelación = cor(DataFactorial)

MatrizCorrelación
```
       Gráfico de Correlaciones:
       
```{r}
corrplot(MatrizCorrelación, 
         method = "circle",     
         type = "upper",          
         addCoef.col = "black",   
         tl.col = "black",        
         tl.cex = 0.5,            
         diag = FALSE)
```
       
       
        Prueba de Kaiser Meyer:
        
```{r Kaiser Meyer}
KMO = KMO(DataFactorial)
KMO
```
          Test de Barlett:
```{r Test de Barlett}
Bartlett= cortest.bartlett(MatrizCorrelación, n = nrow(DataFactorial))
Bartlett
```

```{r Verificación Matriz}
library(matrixcalc)
is.singular.matrix(MatrizCorrelación) 
```
        De ahí sigue el análisis paralelo: 
        
```{r Análisis Paralelo}
AnálisisParalelo =fa.parallel(DataFactorial, fa = "fa", correct = T, plot = T)
```
        
```{r Modelo Factorial}
ModeloFactorial = fa(DataFactorial, 
               nfactors = 2, 
               cor = 'mixed', 
               rotate = "varimax",
               fm = "minres")

ModeloFactorial
```

```{r}
DiagramaEstructural = fa.diagram(ModeloFactorial, 
           main = "Diagrama del Modelo Factorial", 
           digits = 2,                             
           cut = 0.3,                              
           simple = FALSE)

DiagramaEstructural
```



Clusterización:


```{r Librería Clusterización}
library(cluster)
library(factoextra)
```


```{r Subdata Cluster}
PreparaciónCluster= c("Migración_Neta_2024_(En_número_de_personas)", 
              "Inactividad_de_PoblaciónApta_Trabajo_en_%_2024", 
              "PBI_en_Dólares_2024", 
              "Estabilidad_política_y_ausencia_de_violencia/terrorismo_en_%_2023")
```

```{r Data Cluster}
DataCluster = Migraciones[, PreparaciónCluster]
```

```{r Rownames Cluster}
rownames(DataCluster) = Migraciones$`Country Name`
```


Sigue la estandarización de coeficientes:

```{r Data escalada}
DataEscalada = scale(DataCluster)
```

Para decidir el número de Clusters:

```{r Número de Clústers}
fviz_nbclust(DataEscalada, pam, method = "wss") + 
  labs(title = "Método del Codo (WSS)")
```

    Método de la silueta:
    
```{r Silueta PAM}
fviz_nbclust(DataEscalada, pam, method = "silhouette") + 
  labs(title = "Método de la Silueta")
```


    
```{r Grupos}
Grupos = 4
```
    
Se aplicará el cluster PAM

```{r Cluster PAM}
set.seed(123) 
PAM = pam(DataEscalada, k = Grupos)

PAM
```
```{r Gráfico PAM}
fviz_cluster(PAM, data = DataEscalada, 
             ellipse.type = "convex",
             main = "Cluster PAM")
```

Cluster AGNES: 

```{r Cluster AGNES}
AGNES = hcut(DataEscalada, 
                 k = Grupos, 
                 func_hclust = "agnes", 
                 method = "ward.D2")
```

```{r Gráfico AGNES}
fviz_dend(AGNES, 
          rect = TRUE,          
          cex = 0.5,            
          main = "Dendrograma AGNES (Ward)")
```

Cluster DIANA: 

```{r Cluster DIANA}
DIANA = hcut(DataEscalada, 
                 k = Grupos, 
                 func_hclust = "diana")
```

```{r Gráfico DIANA}
fviz_dend(DIANA, 
          rect = TRUE, 
          cex = 0.5,
          main = "Dendrograma DIANA")
```
Comparación de Siluetas:

```{r Silueta PAM}
fviz_silhouette(PAM, print.summary = FALSE) + 
  labs(title = "Silueta PAM")
```

Silueta AGNES: 

Silueta AGNES:

```{r Silueta AGNES}
fviz_silhouette(AGNES, print.summary = FALSE) + 
  labs(title = "Silueta AGNES (Ward)")
```

Silueta DIANA:  

```{r Silueta DIANA}
fviz_silhouette(DIANA, print.summary = FALSE) + 
  labs(title = "Silueta DIANA (Divisivo)")
```

Tabla Comparación de asignación de clústeres:

```{r Tabla Comparación}
table(PAM = PAM$clustering, AGNES = AGNES$cluster)

table(PAM = PAM$clustering, DIANA = DIANA$cluster)
```




