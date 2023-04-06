# Argentina Deaths from 2015-2021
__Author__: Esteban R.

## Disclaimer
This dataset contains information on deaths that occurred from 2015 to 2019. __The purpose of using this data is solely for educational and scientific research purposes__. We want to emphasize that we fully respect the privacy of the deceased individuals and their families, and we will treat this data with the utmost respect and care.

We want to make it clear that __the data presented in this project does not identify any individual person, and it has been anonymized to ensure their privacy__. We want to reiterate our commitment to preserving the dignity and respect of those who have passed away.

## About the project
This project was carried out as part of a final __capstone project in the Data Analyst course__, provided by Coursera in collaboration with Google. The purpose of the project is to apply all the skills learned during the course within the ask, prepare, process, analyze, share, and act methodology

Duration: 1 week.

## Method
The public dataset provided by the Argentine Ministry of Health available on the [Argentine public data website was used](https://www.datos.gob.ar/dataset/salud-defunciones-generales-mensuales-ocurridas-registradas-republica-argentina/archivo/salud_41691186-72d2-4c3b-a353-7e93fd50a7ed).

The tools used for data cleaning, extraction, preparation, and analysis were:

- __SQL server__: Due to the size of the dataset, which consists of more than 2 million data points, queries were used to extract, sort, and filter the information in order to reduce its size and allow it to be used in an environment such as Excel.

- __Excel__: Data cleaning was performed using Excel, as well as preliminary analysis using pivot tables and charts. The table created was later used in Power BI

- __R__: Like SQL, the R programming language was used to filter, sort, and validate the data to ensure its accuracy.

## Details about the dataset and project
- The nomenclature for causes of death corresponds to those defined in the ICD-10 by the WHO and followed by the Argentine Ministry of Health.

### Structure of the Data - Metadada: 
- Format: csv.
- Data size: 308 MB
- Author: Ministerio de Salud de la Nación
- Summary: Number of Monthly General Deaths occurred and registered in the Argentine Republic.
- Last Update: 2023/03/14
- Download date: 2023/03/22
- Structure:  
  1. Columns: 11 
  -Name columns: region, jurisdiccion, mes_anio_defuncion, mes_def, anio_def, sexo_id, sexo_nombre, grupo_etario, grupo_causa_defuncion_CIE10, cod_causa_muerte_CIE10, cantidad

  2. Rows: 2516784. Each row correspond to a death for an specific indivdual

