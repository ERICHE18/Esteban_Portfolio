# __USING R FOR PRACTICE AND VARIFICATION__


```r
install.packages("readr")
install.packages("tidyverse")
install.packages("dplyr")
library(tidyverse)
library(ggplot2)
library(dplyr)
library(readr)

read_csv("arg_def_15_21.csv")
```
### __Total__
```r
total_def <- data %>% summarize (total = sum(cantidad))
View(total_def)
```
|      Total  |
|--------------------|
|    2516784 |


### __Total by year and mortality rate__
```r
total_anuales <- data %>%
  group_by(anio_def) %>% 
  summarize(total_anual = sum(cantidad)) %>%
  mutate(porcentaje = round((total_anual)/ sum   (total_anual) * 100, digits = 2)) %>% 
  mutate(porcentaje = paste0(porcentaje, "%")) %>% 
  mutate(tasa_mortalidad = (total_anual / 46044703) * 1000) %>%
  arrange(desc(total_anual)) %>% 
  as.data.frame(total_anuales)
```

| anio_def | total_anual | porcentaje | tasa_mortalidad |
|----------|-------------|------------|-----------------|
| 2021     | 432242      | 17.17%     | 9.387442        |
| 2020     | 377592      | 15%        | 8.200552        |
| 2016     | 352506      | 14.01%     | 7.655734        |
| 2017     | 342344      | 13.6%      | 7.435035        |
| 2019     | 341057      | 13.55%     | 7.407084        |
| 2018     | 337600      | 13.41%     | 7.332005        |
| 2015     | 333443      | 13.25%     | 7.241723        |


### __Most deadly months__ 
```r
deadly_months_porcent <- data %>%
  group_by(mes_def) %>% 
  summarize(total = sum(cantidad)) %>% 
  mutate(porcentaje = round((total)/ sum (total) * 100, digits = 2)) %>% 
  mutate(porcentaje = paste0(porcentaje, "%")) %>% 
  arrange(desc(total))
```

| mes_def |  total | porcentaje |
|--------:|-------:|-----------:|
|      07 | 251728 |        10% |
|      06 | 245580 |      9.76% |
|      08 | 233816 |      9.29% |
|      05 | 225513 |      8.96% |
|      09 | 216390 |       8.6% |
|      10 | 210741 |      8.37% |
|      04 | 194821 |      7.74% |
|      01 | 193142 |      7.67% |
|      12 | 192492 |      7.65% |
|      11 | 189832 |      7.54% |
|      03 | 188189 |      7.48% |
|      02 | 173118 |      6.88% |
|      00 |   1422 |      0.06% |

            
### __Comparison of men and women__
```r
total_sexs_porcent <- data %>% 
  group_by(sexo_nombre) %>% 
  summarize(total_sexs_def = sum(cantidad)) %>% 
  mutate(porcentaje = round((total_sexs_def)/ sum   (total_sexs_def) * 100, digits = 3)) %>% 
  mutate(porcentaje = paste0(porcentaje, "%")) %>% 
  arrange(desc(total_sexs_def))

View(total_sexs_porcent)
```

|     | Gender         | Total    | Porcentaje |
| --- | -------------- | -------- | ---------- |
| 1   | Varones        | 1296900  | 51.53%     |
| 2   | Mujeres        | 1211129  | 48.122%    |
| 3   | Sin especificar | 8657     | 0.344%     |
| 4   | Ignorado       | 98       | 0.004%     |


### __Total by cause of death__
```R
total_causa_def <- data %>% 
  group_by(grupo_causa_defuncion_CIE10) %>% 
  summarize(total_por_causa = sum(cantidad)) %>% 
  mutate(porcentaje = round((total_por_causa)/ sum   (total_por_causa) * 100, digits = 2)) %>% 
  mutate(porcentaje = paste0(porcentaje, "%")) %>% 
  arrange(desc(total_por_causa))

View(total_causa_def)
```
| grupo_causa_defuncion_CIE10                                     | total_por_causa | porcentaje |
|------------------------------------------------------------------|----------------|------------|
| 0900 ENF  DEL SISTEMA CIRCULATORIO                               | 692307         | 27.51%     |
| 0200 T MALIGNOS                                                  | 429719         | 17.07%     |
| 1000 ENF SISTEMA RESPIRATORIO                                    | 427872         | 17%        |
| 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS                      | 236544         | 9.4%       |
| 1900 SIGNOS, SINTOMAS Y AFECCIONES MAL DEFINIDAS Y DESCONOCIDAS  | 156731         | 6.23%      |
| 1700 CAUSAS EXTERNAS                                             | 129363         | 5.14%      |
| 1800 DEMAS CAUSAS DEFINIDAS                                      | 123220         | 4.9%       |
| 1300 ENF  DEL SISTEMA URINARIO                                   | 92111          | 3.66%      |
| 0300 DIABETES MELLITUS                                           | 65723          | 2.61%      |
| 1200 ENF  DEL HIGADO                                             | 40021          | 1.59%      |
| 1500 AFECC  PERINATALES                                          | 21169          | 0.84%      |
| 0299 C IN SITU, TUM BEN  Y COMP INCIERTO                          | 19502          | 0.77%      |
| 0800 TRASTORNOS MENTALES Y DEL COMP                               | 18839          | 0.75%      |
| 0500 TRAST METABOLICOS                                           | 15147          | 0.6%       |
| 1600 MALFORM  CONGENITAS, DEFORM  Y ANOM  CROMOSOMICAS            | 14894          | 0.59%      |
| 1100 APEND , HERNIA CAV  ABDOM Y OBSTR  INTEST                    | 13125          | 0.52%      |
| 0700 ENF  DE ALZHEIMER                                           | 10370          | 0.41%      |
| 0400 DEF  Y ANEMIAS NUTRICION                                    | 6012           | 0.24%      |
| 1400 EMBARAZO, PARTO Y PUERPERIO                                  | 2122           | 0.08%      |
| 0600 MENINGITIS                                                  | 1993           | 0.08%      |
     
            
### __Total by Age group__
```R
total_por_edades <- data %>%
  group_by(grupo_etario) %>% 
  summarize(total_por_edades = sum(cantidad)) %>% 
  mutate(porcentaje = round((total_por_edades)/ sum   (total_por_edades) * 100, digits = 2)) %>% 
  mutate(porcentaje = paste0(porcentaje, "%")) %>% 
  arrange(desc(total_por_edades))

View(total_por_edades)
``` 
| grupo_etario         | total_por_edades | porcentaje |
|----------------------|-------------------|------------------|
| 07. de 80 anios y mas | 997080            | 39.62%         |
| 06.de 70 a 79 anios   | 592922            | 23.56%         |
| 05.de 60 a 69 anios   | 416732            | 16.56%         |
| 04.de 50 a 59 anios   | 209595            | 8.33%          |
| 02.de 20 a 39 anios   | 107111            | 4.26%          |
| 03.de 40 a 49 anios   | 103711            | 4.12%          |
| 01.menor a 20 anios   | 75626             | 3%             |
| 08.sin especificar    | 14007             | 0.56%          |

### __Total by age group and gender__
```R
total_por_edades <- data %>%
  group_by(grupo_etario, sexo_nombre) %>% 
  summarize(total_por_edades = sum(cantidad)) %>%
  arrange(desc(total_por_edades))
 
View(total_por_edades)
print(total_por_edades)
```

|  grupo_etario         | sexo_nombre | total_por_edades |
|----------------------|-------------|-----------------|
| 07. de 80 anios y mas | Mujeres     | 607165          |
| 07. de 80 anios y mas | Varones     | 386992          |
| 05.de 60 a 69 anios   | Varones     | 257354          |
| 05.de 60 a 69 anios   | Mujeres     | 157880          |
| 06.de 70 a 79 anios   | Varones     | 335098          |
| 06.de 70 a 79 anios   | Mujeres     | 255807          |
| 04.de 50 a 59 anios   | Varones     | 130434          |
| 04.de 50 a 59 anios   | Mujeres     | 78458           |
| 01.menor a 20 anios   | Varones     | 44801           |
| 02.de 20 a 39 anios   | Varones     | 71742           |
| 03.de 40 a 49 anios   | Varones     | 62556           |
| 01.menor a 20 anios   | Mujeres     | 30615           |
| 02.de 20 a 39 anios   | Mujeres     | 35034           |
| 03.de 40 a 49 anios   | Mujeres     | 40853           |
| 08.sin especificar   | Varones     | 7923            |
| 08.sin especificar   | Mujeres     | 5317            |
| 08.sin especificar   | Sin especificar| 765         |
## Deaths by External causes

### __External causes by gender__
```R
external_cause_sex <- data %>%
  filter(grupo_causa_defuncion_CIE10 == "1700 CAUSAS EXTERNAS") %>%
  group_by(sexo_nombre) %>% 
  summarize(total_por_edades = sum(cantidad)) %>% 
  arrange(desc(total_por_edades))
View(external_cause_sex)
```

| sexo_nombre      | total_por_edades |
|:----------------|----------------:|
| 1. Varones      |            95206 |
| 2. Mujeres      |            33640 |
| 9. Sin especificar |              513 |
| 3. Ignorado       |                4 |
            

### __External cause between 0-39 years old__
```R
external_cause_20_39 <- data %>%
  filter(grupo_causa_defuncion_CIE10 == "1700 CAUSAS EXTERNAS") %>%
  filter(grupo_etario %in% c('02.de 20 a 39 anios',     '01.menor a 20 anios')) %>%
  group_by(sexo_nombre) %>% 
  summarize(total = sum(cantidad)) %>% 
  arrange(desc(total))
            
View(external_cause_20_39)
print(external_cause_20_39)
```
| sexo_nombre      | total |
|:----------------|------:|
| 1. Varones      | 49657 |
| 2. Mujeres      | 11810 |
| 9. Sin especificar |   197 |
| 3. Ignorado       |     2 |

### __COVID-19__ 
# The impact of Covid-19 starting from 2020
```r
COVID_year <- data %>%
  filter(grupo_causa_defuncion_CIE10 == "0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS") %>%
  group_by(anio_def) %>% 
  summarize(total_por_edades = sum(cantidad)) %>% 
  arrange(desc(total_por_edades))

print(COVID_year)
```
| anio_def | total_por_edades |
|---------:|----------------:|
|     2021 |            99626 |
|     2020 |            66672 |
|     2019 |            14541 |
|     2017 |            14232 |
|     2018 |            14014 |
|     2016 |            13880 |
|     2015 |            13579 |

### __COVID-19 - MONTHS__
```r
COVID_months <- data %>%
  filter(grupo_causa_defuncion_CIE10 == "0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS") %>%
  group_by(mes_anio_defuncion) %>% 
  summarize(total_por_edades = sum(cantidad)) %>% 
  arrange(desc(total_por_edades))

print(COVID_months, n = 20)
```

| mes_anio_defuncion | total_por_edades |
|--------------------|-----------------|
| 05/2021            | 20328           |
| 06/2021            | 20243           |
| 10/2020            | 14114           |
| 09/2020            | 12754           |
| 04/2021            | 12747           |
| 07/2021            | 12171           |
| 11/2020            | 9660            |
| 08/2020            | 8861            |
| 01/2021            | 7416            |
| 08/2021            | 6817            |
| 12/2020            | 6165            |
| 07/2020            | 5926            |
| 03/2021            | 5737            |
| 02/2021            | 5493            |
| 09/2021            | 3033            |
| 06/2020            | 2613            |
| 12/2021            | 2035            |
| 10/2021            | 1867            |
| 11/2021            | 1739            |
| 05/2020            | 1569            |

            
            
            