# Mortality analysis in Argentina: trends and patterns during the period 2015-2021

- AUTOR: ESTEBAN R.
- PROYECT: ARGENTINA DEATHS (FROM 2015 TO 2021)
- TOOL: SQL SERVER

## Index
1. Introductories queries
2. Demographic queries
3. The 3 most common diseases by age
4. Queries about covid-19
5. Deaths by external cause
6. Conclusions


## __1. Introductories queries__
First, i started with some basic queries:

__Previewing the table__
```sql
SELECT TOP 7 * 
FROM [dbo].[arg_def_15_21];
```
jurisdiccion | mes_anio_defuncion | mes_def | anio_def | sexo_id | sexo_nombre | grupo_etario | grupo_causa_defuncion_CIE10 | cod_causa_muerte_CIE10 | cantidad
--- | --- | --- | --- | --- | --- | --- | --- | --- | ---
1.Centro | 6.Prov. Bs.As. | 09/2015 | 9 | 2015 | 1 | 1.Varones | 01.menor a 20 anios | 1500 AFECC PERINATALES | P21 | 1
1.Centro | 6.Prov. Bs.As. | 09/2015 | 9 | 2015 | 1 | 1.Varones | 01.menor a 20 anios | 1600 MALFORM CONGENITAS, DEFORM Y ANOM CROMOSOMICAS | Q60 | 1
1.Centro | 6.Prov. Bs.As. | 08/2015 | 8 | 2015 | 1 | 1.Varones | 01.menor a 20 anios | 1600 MALFORM CONGENITAS, DEFORM Y ANOM CROMOSOMICAS | Q24 | 1
1.Centro | 6.Prov. Bs.As. | 05/2015 | 5 | 2015 | 1 | 1.Varones | 01.menor a 20 anios | 1000 ENF SISTEMA RESPIRATORIO | J18 | 1
1.Centro | 6.Prov. Bs.As. | 10/2015 | 10 | 2015 | 1 | 1.Varones | 01.menor a 20 anios | 1500 AFECC PERINATALES | P36 | 1
1.Centro | 6.Prov. Bs.As. | 07/2015 | 7 | 2015 | 2 | 2.Mujeres | 01.menor a 20 anios | 1500 AFECC PERINATALES | P23 | 1
1.Centro | 6.Prov. Bs.As. | 07/2015 | 7 | 2015 | 1 | 1.Varones | 01.menor a 20 anios | 1500 AFECC PERINATALES | P36 | 1

__Number of rows__:
```sql
SELECT COUNT(*)
FROM [dbo].[arg_def_15_21];
```

| Number of Rows |
| --- |
| 2516784 |

**Total deaths** 
```sql
SELECT COUNT(cantidad) AS TOTAL_deaths
FROM [dbo].[arg_def_15_21];
```
| TOTAL_deaths  |
 | -------------|
| 2516784      |

Thus, every row correspond to a individual death.

## __2. Demographic queries__
### a. Queries about gender

In this query i wanted to know the total deaths by gender and see the diference between. Some of them are not specified in the dataset.

```sql
SELECT 
    sexo_nombre AS Sex, 
    COUNT(cantidad) AS Total, 
    FORMAT(CAST(COUNT(*) AS float) / (SELECT         COUNT(cantidad) FROM [dbo].[arg_def_15_21]) * 100, 'N2') + '%' AS Porcent
FROM [dbo].[arg_def_15_21]
GROUP BY sexo_nombre
ORDER BY Porcent DESC;
```


| Sex              | Total   | Porcent  |
|-----------------|---------|----------|
| 1. Males       | 1296900 | 51.53%   |
| 2. Females       | 1211129 | 48.12%   |
| 9. Not specified | 8657   | 0.34%   |
| 3. Unknown     | 98      | 0.00%   |

Diference between men and women:

```sql
SELECT 
	ABS(SUM(CASE WHEN sexo_nombre = '1.Varones' THEN cantidad ELSE -cantidad END)) AS Diference_Total_Males
FROM [dbo].[arg_def_15_21]
WHERE sexo_nombre IN ('1.Varones', '2.Mujeres') 
AND sexo_nombre NOT LIKE '9.Sin especificar'
AND sexo_nombre NOT LIKE '3.Ignorado';
```


| Diference |
|-----------------------|
|         85771         |

#### __Male deaths by year__

```sql
SELECT 
	anio_def AS YEAR, 
	COUNT(cantidad) AS TOTAL_MALES
FROM [dbo].[arg_def_15_21]
WHERE sexo_id = 1 
GROUP BY anio_def
ORDER BY anio_def DESC;
```
| YEAR | TOTAL_MALES |
|------|-------------|
| 2021 | 224991      |
| 2020 | 196129      |
| 2019 | 173846      |
| 2018 | 174327      |
| 2017 | 174560      |
| 2016 | 181053      |
| 2015 | 171994      |

In this query, we are observing __the increase of deaths in 2020 and 2021__. We will see this pattern in more queries, so you may be thinking of __COVID-19__, and I have to say that's correct. However, we will see that phenomenon later.

#### __Females deaths by year__

```sql
SELECT  
	anio_def AS YEAR, 
	COUNT(cantidad) AS TOTAL_FEMALES
FROM [dbo].[arg_def_15_21]
WHERE sexo_id = 2 
GROUP BY anio_def
ORDER BY anio_def DESC;
```

| YEAR | TOTAL_FEMALES |
|------|---------------|
| 2021 | 204527        |
| 2020 | 178259        |
| 2019 | 166009        |
| 2018 | 162806        |
| 2017 | 167054        |
| 2016 | 171207        |
| 2015 | 161267        |

Same like males deaths, we see an increment.

### __B. Age group queries__

```sql
SELECT 
	grupo_etario AS Age_group, 
	COUNT(cantidad) AS Total_deaths,
	FORMAT (CAST(COUNT(*) AS float) / (SELECT COUNT(cantidad) FROM [dbo].[arg_def_15_21]) * 100, 'N2') + '%' Porcent 
FROM [dbo].[arg_def_15_21]
GROUP BY grupo_etario 
ORDER BY TOTAL_DEATHS DESC;
```

| Age_group               | Total_deaths | Porcent  |
|------------------------|-------------|----------|
| 07. de 80 anios y mas   | 997080      | 39.62%   |
| 06.de 70 a 79 anios     | 592922      | 23.56%   |
| 05.de 60 a 69 anios     | 416732      | 16.56%   |
| 04.de 50 a 59 anios     | 209595      | 8.33%    |
| 02.de 20 a 39 anios     | 107111      | 4.26%    |
| 03.de 40 a 49 anios     | 103711      | 4.12%    |
| 01.menor a 20 anios     | 75626       | 3.00%    |
| 08.sin especificar      | 14007       | 0.56%    |
*Traduction: (anios, años - years)*

We observe that the __elderly population (+60)__ accounts for __79.74%__ of deaths in Argentina between 2015-2021 and the rest is distributed among different age groups.

#### __Deaths by gender and age__

```sql
SELECT 
	sexo_nombre AS Gender,
	grupo_etario AS Age_group, 
	COUNT (cantidad) AS Total_deaths,
	FORMAT (CAST(COUNT(*) AS float) / (SELECT COUNT(cantidad) FROM [dbo].[arg_def_15_21]) * 100, 'N2') + '%' Porcent 
FROM [dbo].[arg_def_15_21]
WHERE sexo_nombre NOT LIKE ('9.Sin Especificar')
AND sexo_nombre NOT LIKE ('3.Ignorado')
GROUP BY grupo_etario, sexo_nombre 
ORDER BY TOTAL_DEATHS DESC;
```

|Gender|Age Group|Total Deaths|Percentage|
|------|---------|-----------|----------|
|2.Mujeres|07. de 80 anios y mas|607165|24.12%|
|1.Varones|07. de 80 anios y mas|386992|15.38%|
|1.Varones|06.de 70 a 79 anios|335098|13.31%|
|1.Varones|05.de 60 a 69 anios|257354|10.23%|
|2.Mujeres|06.de 70 a 79 anios|255807|10.16%|
|2.Mujeres|05.de 60 a 69 anios|157880|6.27%|
|1.Varones|04.de 50 a 59 anios|130434|5.18%|
|2.Mujeres|04.de 50 a 59 anios|78458|3.12%|
|1.Varones|02.de 20 a 39 anios|71742|2.85%|
|1.Varones|03.de 40 a 49 anios|62556|2.49%|
|1.Varones|01.menor a 20 anios|44801|1.78%|
|2.Mujeres|03.de 40 a 49 anios|40853|1.62%|
|2.Mujeres|02.de 20 a 39 anios|35034|1.39%|
|2.Mujeres|01.menor a 20 anios|30615|1.22%|
|1.Varones|08.sin especificar|7923|0.31%|
|2.Mujeres|08.sin especificar|5317|0.21%|

*Traduction:
(Hombres: Males) -
(Mujeres: Females)* - (anios, años - years)

Here we observe that __women have a higher life expectancy, with the highest number of deaths occurring above 80+__, while in the rest of the age groups the death of men tends to predominate.

![Comparison between men and women](https://github.com/ERICHE18/Esteban_Portfolio/blob/main/Projects/Mortality%20analysis%20in%20Argentina:%20trends%20and%20patterns%20during%20the%20period%202015-2021/Visualization/Tableau/Comparison%20between%20men%20and%20women.png?raw=true)

### __C. Queries about dates__

### __Deaths by year__
```sql
SELECT 
	anio_def AS Year,
	COUNT (cantidad) AS Total,
	FORMAT(CAST(COUNT(*) AS float) / (SELECT COUNT(*) FROM [dbo].[arg_def_15_21]) * 100, 'N2') + '%' AS Porcent
FROM [dbo].[arg_def_15_21]
GROUP BY anio_def, cantidad
ORDER BY Porcent DESC;
```
| Year | Total  | Porcent |
|------|--------|---------|
| 2021 | 432242 | 17.17%  |
| 2020 | 377592 | 15.00%  |
| 2016 | 352506 | 14.01%  |
| 2017 | 342344 | 13.60%  |
| 2019 | 341057 | 13.55%  |
| 2018 | 337600 | 13.41%  |
| 2015 | 333443 | 13.25%  |

The years appear in order from the most deadly to the least deadly. We se again the increment in 2020 and 2021.

![Years](https://github.com/ERICHE18/Esteban_Portfolio/blob/main/Projects/Mortality%20analysis%20in%20Argentina:%20trends%20and%20patterns%20during%20the%20period%202015-2021/Visualization/Tableau/Years.png?raw=true)


### __Most deadly months__

```sql
SELECT 
	mes_def AS Month, 
	COUNT(cantidad) AS Total,
	FORMAT(CAST(COUNT(*) AS float) / (SELECT COUNT(cantidad) FROM [dbo].[arg_def_15_21]) * 100, 'N2') + '%' AS Porcent
FROM [dbo].[arg_def_15_21]
GROUP BY mes_def 
ORDER BY Total DESC;
```

| Month | Total  | Porcent |
|-------|--------|---------|
| 7     | 251728 | 10.00%  |
| 6     | 245580 | 9.76%   |
| 8     | 233816 | 9.29%   |
| 5     | 225513 | 8.96%   |
| 9     | 216390 | 8.60%   |
| 10    | 210741 | 8.37%   |
| 4     | 194821 | 7.74%   |
| 1     | 193142 | 7.67%   |
| 12    | 192492 | 7.65%   |
| 11    | 189832 | 7.54%   |
| 3     | 188189 | 7.48%   |
| 2     | 173118 | 6.88%   |
| 0     | 1422   | 0.06%   |

We see that __the"deadliest" month is 07 (July)__, which is the beginning of winter in Argentina, facilitating the appearance of certain diseases as we will see later. On the other hand, the __"healthiest" month is 02 (February)__, corresponding to summer. In addition, we observe that there are a total of 1422 deaths that do not have a defined month.

![Months.png](https://raw.githubusercontent.com/ERICHE18/Esteban_Portfolio/main/Projects/Mortality%20analysis%20in%20Argentina:%20trends%20and%20patterns%20during%20the%20period%202015-2021/Visualization/Tableau/Months.png)


#### __The 10 deadliest months from 2015 to 2021__
```sql
SELECT 
	anio_def AS Year, 
	mes_def AS Month, 
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
GROUP BY anio_def, mes_def 
ORDER BY Total DESC;
```

| Year | Month | Total |
|------|-------|-------|
| 2021 | 5     | 51612 |
| 2021 | 6     | 51214 |
| 2021 | 7     | 44006 |
| 2020 | 10    | 40911 |
| 2020 | 9     | 40513 |
| 2021 | 4     | 40056 |
| 2017 | 6     | 37476 |
| 2020 | 8     | 37192 |
| 2021 | 8     | 37125 |
| 2016 | 6     | 36162 |

Out of 10, __8 are months from the years 2020-2021__, with 5 from 2021 and 3 from 2020, making 2021 one of the unfortunately deadliest years. For the rest of the years, they never exceeded the 37,476 deaths of 2017. Interestingly, in our selected years, 2017 and 2016 had more deaths despite not being in a pandemic.

## __3. About the Causes of death__
This query returns the nomenclatures of the causes of death.

| Cause                                               |
|-----------------------------------------------------|
| 0500 TRAST METABOLICOS                              |
| 1700 CAUSAS EXTERNAS                                |
| 0800 TRASTORNOS MENTALES Y DEL COMP                  |
| 0600 MENINGITIS                                     |
| 0200 T MALIGNOS                                     |
| 1000 ENF SISTEMA RESPIRATORIO                        |
| 0700 ENF  DE ALZHEIMER                              |
| 1600 MALFORM  CONGENITAS, DEFORM  Y ANOM  CROMOSOMICAS|
| 0400 DEF  Y ANEMIAS NUTRICION                       |
| 1100 APEND , HERNIA CAV  ABDOM Y OBSTR  INTEST       |
| 1500 AFECC  PERINATALES                              |
| 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS          |
| 0900 ENF  DEL SISTEMA CIRCULATORIO                   |
| 1200 ENF  DEL HIGADO                                |
| 1400 EMBARAZO, PARTO Y PUERPERIO                     |
| 1900 SIGNOS, SINTOMAS Y AFECCIONES MAL DEFINIDAS Y DESCONOCIDAS|
| 0300 DIABETES MELLITUS                              |
| 1300 ENF  DEL SISTEMA URINARIO                       |
| 1800 DEMAS CAUSAS DEFINIDAS                          |
| 0299 C IN SITU, TUM BEN  Y COMP INCIERTO             |


Thanks to this, we will be able to make better queries related to causes of death. It is worth noting that this nomenclature corresponds to the [ICD-10](https://icd.who.int/browse10/2019/en) (in Spanish: CIE-10).

So, then i proceeded to calculate the totalS fromm 2015 to 2021 by Cause of death based on the nomenclatures:

```sql
SELECT 
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Total_2015_2021,
	FORMAT(CAST(COUNT(*) AS float) / (SELECT COUNT(cantidad) FROM [dbo].[arg_def_15_21]) * 100, 'N2') + '%' AS Porcent
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 LIKE '1100%' 
OR grupo_causa_defuncion_CIE10 LIKE '1500%'
OR grupo_causa_defuncion_CIE10 LIKE '0100%'
OR grupo_causa_defuncion_CIE10 LIKE '0900%'
OR grupo_causa_defuncion_CIE10 LIKE '1200%'
OR grupo_causa_defuncion_CIE10 LIKE '1400%'
OR grupo_causa_defuncion_CIE10 LIKE '0200%'
OR grupo_causa_defuncion_CIE10 LIKE '1900%'
OR grupo_causa_defuncion_CIE10 LIKE '0300%'
OR grupo_causa_defuncion_CIE10 LIKE '1300%'
OR grupo_causa_defuncion_CIE10 LIKE '1800%'
OR grupo_causa_defuncion_CIE10 LIKE '0299%'
OR grupo_causa_defuncion_CIE10 LIKE '0500%'
OR grupo_causa_defuncion_CIE10 LIKE '1700%'
OR grupo_causa_defuncion_CIE10 LIKE '0800%'
OR grupo_causa_defuncion_CIE10 LIKE '0600%'
OR grupo_causa_defuncion_CIE10 LIKE '1000%'
OR grupo_causa_defuncion_CIE10 LIKE '0700%'
OR grupo_causa_defuncion_CIE10 LIKE '1600%'
OR grupo_causa_defuncion_CIE10 LIKE '0400%'
GROUP BY grupo_causa_defuncion_CIE10
ORDER BY TOTAL_2015_2021 DESC;
```
| Cause                                                       | Total_2015_2021 | Porcent |
|-------------------------------------------------------------|-----------------|---------|
| 0900 Diseases of the circulatory system                      | 692307          | 27.51%  |
| 0200 Malignant neoplasms                                     | 429719          | 17.07%  |
| 1000 Diseases of the respiratory system                      | 427872          | 17.00%  |
| 0100 Certain infectious and parasitic diseases              | 236544          | 9.40%   |
| 1900 Symptoms, signs and ill-defined conditions              | 156731          | 6.23%   |
| 1700 External causes                                          | 129363          | 5.14%   |
| 1800 Other defined causes                                     | 123220          | 4.90%   |
| 1300 Diseases of the urinary system                           | 92111           | 3.66%   |
| 0300 Diabetes mellitus                                        | 65723           | 2.61%   |
| 1200 Diseases of the liver                                    | 40021           | 1.59%   |
| 1500 Pregnancy, childbirth and the puerperium                 | 21169           | 0.84%   |
| 0299 In situ neoplasms, benign neoplasms and neoplasms of uncertain behavior | 19502 | 0.77%   |
| 0800 Mental and behavioral disorders                          | 18839           | 0.75%   |
| 0500 Endocrine, nutritional and metabolic diseases            | 15147           | 0.60%   |
| 1600 Congenital malformations, deformations and chromosomal abnormalities | 14894 | 0.59%   |
| 1100 Diseases of the digestive system                         | 13125           | 0.52%   |
| 0700 Alzheimer disease                                        | 10370           | 0.41%   |
| 0400 Nutritional deficiencies and anemia                      | 6012            | 0.24%   |
| 1400 Certain conditions originating in the perinatal period   | 2122            | 0.08%   |
| 0600 Bacterial and viral infections of the central nervous system | 1993        | 0.08%   |



This table shows us the main causes of death, which is not surprising, as they are usually the most common in modern times, related to sedentary habits and poor nutrition, causes of which Argentine citizens are not exempt.




```sql
SELECT 
	grupo_etario AS Age_Group, 
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 LIKE '1100%' 
OR grupo_causa_defuncion_CIE10 LIKE '1500%'
OR grupo_causa_defuncion_CIE10 LIKE '0100%'
OR grupo_causa_defuncion_CIE10 LIKE '0900%'
OR grupo_causa_defuncion_CIE10 LIKE '1200%'
OR grupo_causa_defuncion_CIE10 LIKE '1400%'
OR grupo_causa_defuncion_CIE10 LIKE '0200%'
OR grupo_causa_defuncion_CIE10 LIKE '1900%'
OR grupo_causa_defuncion_CIE10 LIKE '0300%'
OR grupo_causa_defuncion_CIE10 LIKE '1300%'
OR grupo_causa_defuncion_CIE10 LIKE '1800%'
OR grupo_causa_defuncion_CIE10 LIKE '0299%'
OR grupo_causa_defuncion_CIE10 LIKE '0500%'
OR grupo_causa_defuncion_CIE10 LIKE '1700%'
OR grupo_causa_defuncion_CIE10 LIKE '0800%'
OR grupo_causa_defuncion_CIE10 LIKE '0600%'
OR grupo_causa_defuncion_CIE10 LIKE '1000%'
OR grupo_causa_defuncion_CIE10 LIKE '0700%'
OR grupo_causa_defuncion_CIE10 LIKE '1600%'
OR grupo_causa_defuncion_CIE10 LIKE '0400%'
GROUP BY grupo_etario, grupo_causa_defuncion_CIE10
ORDER BY grupo_etario, Total ASC;
```

__This query is too long, so I will show just a little bit of the table__, but it's important to know that we will be working based on this table and we will see the common causes of death in each age group.


| Age_Group           | Cause                                            | Total |
|---------------------|--------------------------------------------------|-------|
| 01.menor a 20 anios | 0700 ENF  DE ALZHEIMER                           |     1 |
| 01.menor a 20 anios | 0800 TRASTORNOS MENTALES Y DEL COMP               |    50 |
| 01.menor a 20 anios | 0300 DIABETES MELLITUS                            |   112 |
| 01.menor a 20 anios | 1100 APEND , HERNIA CAV  ABDOM Y OBSTR  INTEST     |   148 |
| 01.menor a 20 anios | 0400 DEF  Y ANEMIAS NUTRICION                     |   154 |
| 01.menor a 20 anios | 1400 EMBARAZO, PARTO Y PUERPERIO                   |   224 |
| 01.menor a 20 anios | 1200 ENF  DEL HIGADO                              |   239 |
| 01.menor a 20 anios | 0600 MENINGITIS                                   |   313 |
| 01.menor a 20 anios | 1300 ENF  DEL SISTEMA URINARIO                     |   407 |
| 01.menor a 20 anios | 0299 C IN SITU, TUM BEN  Y COMP INCIERTO           |   482 |
| 01.menor a 20 anios | 0500 TRAST METABOLICOS                             |   614 |
| 01.menor a 20 anios | 0900 ENF  DEL SISTEMA CIRCULATORIO                 |  1884 |
| 01.menor a 20 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS        |  2857 |
| 01.menor a 20 anios | 0200 T MALIGNOS                                    |  3776 |
| 01.menor a 20 anios | 1900 SIGNOS, SINTOMAS Y AFECCIONES MAL DEFINIDAS Y DESCONOCIDAS |  3798 |
| 01.menor a 20 anios | 1800 DEMAS CAUSAS DEFINIDAS                        |  4229 |
| 01.menor a 20 anios | 1000 ENF SISTEMA RESPIRATORIO                      |  5192 |
| 01.menor a 20 anios | 1600 MALFORM  CONGENITAS, DEFORM  Y ANOM  CROMOSOMICAS  | 13566 |
| 01.menor a 20 anios | 1700 CAUSAS EXTERNAS                               | 16411 |
| 01.menor a 20 anios | 1500 AFECC  PERINATALES                            | 21169 |
| 02.de 20 a 39 anios | 0700 ENF  DE ALZHEIMER                           |    14 |
| 02.de 20 a 39 anios | 0400 DEF  Y ANEMIAS NUTRICION                     |   117 |
| 02.de 20 a 39 anios | 0600 MENINGITIS                                   |   250 |

__Deaths by Date, region, gender, age group, cause and total__ 

(This Query was lated exported as a CSV and used in Excel for my project, that's why it seemed important to me to show it, since it reduces the original size of the table, __going from 2516784 rows to 122202 rows__, making it easy to use in Excel.)

```sql
SELECT 
	mes_anio_defuncion AS Date_id, 
	region AS Region, 
	sexo_nombre AS Gender, 
	grupo_etario AS Age_group, 
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Totals
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 LIKE '1100%' 
OR grupo_causa_defuncion_CIE10 LIKE '1500%'
OR grupo_causa_defuncion_CIE10 LIKE '0100%'
OR grupo_causa_defuncion_CIE10 LIKE '0900%'
OR grupo_causa_defuncion_CIE10 LIKE '1200%'
OR grupo_causa_defuncion_CIE10 LIKE '1400%'
OR grupo_causa_defuncion_CIE10 LIKE '0200%'
OR grupo_causa_defuncion_CIE10 LIKE '1900%'
OR grupo_causa_defuncion_CIE10 LIKE '0300%'
OR grupo_causa_defuncion_CIE10 LIKE '1300%'
OR grupo_causa_defuncion_CIE10 LIKE '1800%'
OR grupo_causa_defuncion_CIE10 LIKE '0299%'
OR grupo_causa_defuncion_CIE10 LIKE '0500%'
OR grupo_causa_defuncion_CIE10 LIKE '1700%'
OR grupo_causa_defuncion_CIE10 LIKE '0800%'
OR grupo_causa_defuncion_CIE10 LIKE '0600%'
OR grupo_causa_defuncion_CIE10 LIKE '1000%'
OR grupo_causa_defuncion_CIE10 LIKE '0700%'
OR grupo_causa_defuncion_CIE10 LIKE '1600%'
OR grupo_causa_defuncion_CIE10 LIKE '0400%'
GROUP BY sexo_nombre, region, grupo_etario, grupo_causa_defuncion_CIE10, mes_anio_defuncion
ORDER BY mes_anio_defuncion DESC; 
```

| Date_id | Region   | Gender    | Age_group            | Cause                                             | Totals |
|---------|----------|-----------|---------------------|---------------------------------------------------|--------|
| 12/2021 | 5.NOA2.  | 2.Mujeres | 03.de 40 a 49 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS       | 1      |
| 12/2021 | 3.NOA1.  | 2.Mujeres | 03.de 40 a 49 anios | 1800 DEMAS CAUSAS DEFINIDAS                      | 2      |
| 12/2021 | 4.NOA.   | 2.Mujeres | 05.de 60 a 69 anios | 1900 SIGNOS, SINTOMAS Y AFECCIONES MAL DEFINI... | 3      |
| 12/2021 | 6.Cuyo1  | 1.Varones | 04.de 50 a 59 anios | 0200 T MALIGNOS                                   | 14     |
| 12/2021 | 6.Cuyo1  | 2.Mujeres | 04.de 50 a 59 anios | 1300 ENF  DEL SISTEMA URINARIO                    | 1      |

### __The 3 most common diseases by age__

```sql
SELECT Age_group, Cause, Total
FROM (  SELECT grupo_etario AS Age_group,
        grupo_causa_defuncion_CIE10 AS Cause, 
        COUNT(cantidad) AS Total,
        ROW_NUMBER() OVER (PARTITION BY grupo_etario ORDER BY COUNT(cantidad) DESC) AS RowNum
    FROM [dbo].[arg_def_15_21]
    WHERE grupo_etario IN ('05.de 60 a 69 anios', 
	'07. de 80 anios y mas', 
	'06.de 70 a 79 anios', 
	'04.de 50 a 59 anios', 
	'03.de 40 a 49 anios', 
	'02.de 20 a 39 anios', 
	'01.menor a 20 anios')
    GROUP BY grupo_etario, grupo_causa_defuncion_CIE10
) t
WHERE RowNum <= 3
ORDER BY Age_group DESC;
```

| Age_group               | Cause                                                      | Total   |
|------------------------|------------------------------------------------------------|---------|
| 07. de 80 anios y mas   | 0900 ENF  DEL SISTEMA CIRCULATORIO                         | 340610  |
| 07. de 80 anios y mas   | 1000 ENF SISTEMA RESPIRATORIO                              | 227409  |
| 07. de 80 anios y mas   | 0200 T MALIGNOS                                            | 102124  |
| 06.de 70 a 79 anios     | 0900 ENF  DEL SISTEMA CIRCULATORIO                         | 168175  |
| 06.de 70 a 79 anios     | 0200 T MALIGNOS                                            | 119673  |
| 06.de 70 a 79 anios     | 1000 ENF SISTEMA RESPIRATORIO                              | 100716  |
| 05.de 60 a 69 anios     | 0200 T MALIGNOS                                            | 106920  |
| 05.de 60 a 69 anios     | 0900 ENF  DEL SISTEMA CIRCULATORIO                         | 103163  |
| 05.de 60 a 69 anios     | 1000 ENF SISTEMA RESPIRATORIO                              | 54674   |
| 04.de 50 a 59 anios     | 0200 T MALIGNOS                                            | 56038   |
| 04.de 50 a 59 anios     | 0900 ENF  DEL SISTEMA CIRCULATORIO                         | 46423   |
| 04.de 50 a 59 anios     | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS                | 26294   |
| 03.de 40 a 49 anios     | 0200 T MALIGNOS                                            | 24359   |
| 03.de 40 a 49 anios     | 0900 ENF  DEL SISTEMA CIRCULATORIO                         | 18677   |
| 03.de 40 a 49 anios     | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS                | 14448   |
| 02.de 20 a 39 anios     | 1700 CAUSAS EXTERNAS                                       | 45255   |
| 02.de 20 a 39 anios     | 0200 T MALIGNOS                                            | 15047   |
| 02.de 20 a 39 anios     | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS                | 9990    |
| 01.menor a 20 anios     | 1500 AFECC  PERINATALES                                    | 21169   |
| 01.menor a 20 anios     | 1700 CAUSAS EXTERNAS                                       | 16411   |
| 01.menor a 20 anios     | 1600 MALFORM  CONGENITAS, DEFORM  Y ANOM  CROMOSOMICAS     | 13566   |


## __4. Queries about covid-19__

```sql
SELECT 
	anio_def AS Date_id, 
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 = '0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS'
GROUP BY anio_def, grupo_causa_defuncion_CIE10
ORDER BY TOTAL DESC;
```


| Date_id | Cause                                         | Total |
|--------:|:----------------------------------------------|------:|
| 2021    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 99626 |
| 2020    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 66672 |
| 2019    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 14541 |
| 2017    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 14232 |
| 2018    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 14014 |
| 2016    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 13880 |
| 2015    | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS    | 13579 |

*Translation: Certain Infectious and Parasitic Diseases*

As we had anticipated, the impact of COVID-19 has been noticeable, and __we can see it in this query where deaths from this type of disease skyrocketed in 2020 and 2021.__ Therefore, the year 2021 has been the year with the most deaths, the year in which things returned to normal, to a greater or lesser extent, that is to say, without quarantine.

*Warning: I am not expressing an opinion about it, or taking a position for or against them, I am simply showing the results that the data yield.*


We checked in which months had the most death by Infectious and Parasitic Diseases

```sql
SELECT
    TOP 10
	mes_anio_defuncion AS Date_id, 
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 = '0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS'
GROUP BY grupo_causa_defuncion_CIE10, mes_anio_defuncion
ORDER BY TOTAL DESC;
```

| Date_id | Cause                                    | Total |
|---------|------------------------------------------|-------|
| 05/2021 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 20328 |
| 06/2021 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 20243 |
| 10/2020 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 14114 |
| 09/2020 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 12754 |
| 04/2021 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 12747 |
| 07/2021 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 12171 |
| 11/2020 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 9660  |
| 08/2020 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 8861  |
| 01/2021 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 7416  |
| 08/2021 | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 6817  |

__Since 2015, the top 10 months in terms of results have been in 2020-2021__. Therefore, our hypothesis was not incorrect.

### __The age groups with the most deaths by Infectious and Parasitic Diseases__

```sql
SELECT 
	anio_def AS Year, 
	grupo_etario AS Age_group, 
	grupo_causa_defuncion_CIE10 AS Cause,
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 = '0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS'
AND anio_def BETWEEN 2020 AND 2021
GROUP BY anio_def, grupo_etario, cantidad, grupo_causa_defuncion_CIE10
ORDER BY Total DESC;
```

| Year | Age_group           | Cause                                   | Total |
|------|---------------------|-----------------------------------------|-------|
| 2021 | 07. de 80 anios y mas | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 26913 |
| 2021 | 06.de 70 a 79 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 25326 |
| 2020 | 07. de 80 anios y mas | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS | 23192 |
| 2021 | 05.de 60 a 69 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 22075 |
| 2020 | 06.de 70 a 79 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 18055 |
| 2021 | 04.de 50 a 59 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 13801 |
| 2020 | 05.de 60 a 69 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 13385 |
| 2021 | 03.de 40 a 49 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 7002  |
| 2020 | 04.de 50 a 59 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 6345  |
| 2021 | 02.de 20 a 39 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 3681  |
| 2020 | 03.de 40 a 49 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 2867  |
| 2020 | 02.de 20 a 39 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 1778  |
| 2020 | 08.sin especificar | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 694   |
| 2021 | 01.menor a 20 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 464   |
| 2021 | 08.sin especificar | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 364   |
| 2020 | 01.menor a 20 anios | 0100 CIERTAS ENF INFECCIOSAS Y PARASITARIAS  | 356   |



## __5. Deaths by external cause__
Another cause of death that caught my attention was due to external causes, which presents interesting patterns.

By external causes we understand all those sudden deaths that occur when exposed to an external energy that causes death. This can be self-inflicted (suicide), interpersonal (homicide), traffic accident, etc.

#### __By age and gender__
```sql
SELECT 
	sexo_nombre AS Gender,
	grupo_etario AS Age_group,
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 = '1700 CAUSAS EXTERNAS'
AND sexo_nombre NOT LIKE '9.Sin especificar'
AND sexo_nombre NOT LIKE '3.Ignorado'
AND grupo_etario NOT LIKE '08.sin especificar'
GROUP BY sexo_nombre, grupo_causa_defuncion_CIE10, grupo_etario
ORDER BY Total DESC;
```
| Gender  | Age_group           | Cause                 | Total |
|---------|---------------------|-----------------------|-------|
| Varones | 02.de 20 a 39 anios | 1700 CAUSAS EXTERNAS   | 37836 |
| Varones | 01.menor a 20 anios | 1700 CAUSAS EXTERNAS   | 11821 |
| Varones | 03.de 40 a 49 anios | 1700 CAUSAS EXTERNAS   | 11358 |
| Varones | 04.de 50 a 59 anios | 1700 CAUSAS EXTERNAS   | 9553  |
| Varones | 05.de 60 a 69 anios | 1700 CAUSAS EXTERNAS   | 9320  |
| Mujeres | 07. de 80 anios y mas | 1700 CAUSAS EXTERNAS | 9083  |
| Varones | 06.de 70 a 79 anios | 1700 CAUSAS EXTERNAS   | 7341  |
| Mujeres | 02.de 20 a 39 anios | 1700 CAUSAS EXTERNAS   | 7262  |
| Varones | 07. de 80 anios y mas | 1700 CAUSAS EXTERNAS   | 6525  |
| Mujeres | 01.menor a 20 anios | 1700 CAUSAS EXTERNAS   | 4548  |
| Mujeres | 06.de 70 a 79 anios | 1700 CAUSAS EXTERNAS   | 3944  |
| Mujeres | 05.de 60 a 69 anios | 1700 CAUSAS EXTERNAS   | 2982  |
| Mujeres | 03.de 40 a 49 anios | 1700 CAUSAS EXTERNAS   | 2802  |
| Mujeres | 04.de 50 a 59 anios | 1700 CAUSAS EXTERNAS   | 2723  |

Here you can notice that firstly the first totals correspond to men, particularly boys, adolescents, young and young adults, and secondly deaths from external causes are the main cause of death among young and young adults.

Let's see this more closely:

### __Total by gender__
```sql
SELECT 
	sexo_nombre AS Gender,
	grupo_causa_defuncion_CIE10 AS Cause, 
	COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 = '1700 CAUSAS EXTERNAS'
GROUP BY sexo_nombre, grupo_causa_defuncion_CIE10
ORDER BY Total DESC;
```

| Gender        | Cause                | Total |
|---------------|-----------------------|--------|
| 1. Varones  | 1700 CAUSAS EXTERNAS | 95206 |
| 2. Mujeres  | 1700 CAUSAS EXTERNAS | 33640 |
| 9. Sin especificar | 1700 CAUSAS EXTERNAS | 513 |
| 3. Ignorado | 1700 CAUSAS EXTERNAS | 4 |

### __Total__
| Cause                | Total   |
| --------------------| -------|
| 1700 CAUSAS EXTERNAS | 129363 |

__Males__:
95206 / 129363 = __74%__
-
__Female__:
33640 / 129363 = __26%__


### __Comparison of males and females in the age range from 0 to 39__

```sql
SELECT 
  sexo_nombre AS Gender,
  grupo_etario AS Age_group,
  grupo_causa_defuncion_CIE10 AS Cause, 
  COUNT(cantidad) AS Total
FROM [dbo].[arg_def_15_21]
WHERE grupo_causa_defuncion_CIE10 = '1700 CAUSAS EXTERNAS'
AND (grupo_etario = '01.menor a 20 anios' OR grupo_etario = '02.de 20 a 39 anios')
AND sexo_nombre NOT LIKE '9.Sin especificar'
AND sexo_nombre NOT LIKE '3.Ignorado'
GROUP BY sexo_nombre, grupo_causa_defuncion_CIE10, grupo_etario
ORDER BY Total DESC;
```

| Gender    | Age_group           | Cause             | Total |
|-----------|---------------------|-------------------|-------|
| 1.Varones | 02.de 20 a 39 anios | 1700 CAUSAS EXTERNAS | 37836 |
| 1.Varones | 01.menor a 20 anios | 1700 CAUSAS EXTERNAS | 11821 |
| 2.Mujeres | 02.de 20 a 39 anios | 1700 CAUSAS EXTERNAS | 7262  |
| 2.Mujeres | 01.menor a 20 anios | 1700 CAUSAS EXTERNAS | 4548  |


## __6. Conclusions__

Finally, we have observed some patterns and trends that have occurred since 2015, which have led us to interesting conclusions as well as leaving us with several questions and paths to investigate, such as the cases of young male deaths from external causes. Since the dataset is only limited to the deaths that occurred and does not handle other variables, we have only focused on that, but __it is a good starting point for future research.__

In summary, these exploratory queries leave us with the following conclusions:

1. Deaths of elderly people account for 79% of deaths in Argentina. 
2. Women have a longer life expectancy than men, with deaths occurring in women over 80 years old. 
3. The impact of COVID-19 has been significant, confirming the lethality of these new diseases. 
4. The most dangerous months for health are in the middle of the year, during winter in June and July, so it is necessary to protect at-risk individuals during these months. 
5. We have seen the main causes of illness in the population and those that affect each age group the most. 
6. Deaths from external causes mainly affect young men.
