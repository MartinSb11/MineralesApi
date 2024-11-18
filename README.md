# :basecamp:🐍 MineralesApi :basecamp: 🐍
Este proyecto analiza la relación entre la producción de los principales metales que se extraen en el Perú (Cobre, Plata, Zinc) y el Producto Bruto Interno (PBI) del sector de minería e hidrocarburos.
#Datos Extraídos https://estadisticas.bcrp.gob.pe/estadisticas/series/diarias/

## BCRP API manual 🤔🎆
El manual explica el uso de la creación del formato GET
> [!NOTE]
> Asegurarnos de que el código de BCRP coincida en formato temporal es decir comparar periodos anuales - anuales

## 🐍CODE🐍
> [!NOTE]
> Librerias utilizadas Pandas, Requests, matplotlib, statsmodels

          import pandas as pd
          import requests
          import statsmodels.api as sm
          import matplotlib.pyplot as plt

### Descargar los datos :accessibility:
          url_path = "https://estadisticas.bcrp.gob.pe/estadisticas/series/api/"
          serie_produccion_cobre = "RD12951DM"
          serie_pbi_mineria = "PN01760AM"
          formato = "/json"
          per = "/2003-1-1/2023-12-31"

### Construir las URLs 🔗
          url_produccion = url_path + serie_produccion_cobre + formato + per
          url_pbi_mineria = url_path + serie_pbi_mineria + formato + per

### Obtener los datos 🔗
          response_produccion = requests.get(url_produccion)
          response_pbi_mineria = requests.get(url_pbi_mineria)
          response_json_produccion = response_produccion.json()
          response_json_pbi = response_pbi_mineria.json()
          print(response_json_pbi)
          print(response_json_produccion)

### Iteracion 🔁
          periodos_produccion = response_json_produccion.get('periods', [])
          produccion_index = []
          for b in periodos_produccion:
              valoress = b.get('values', [])
              for x in valoress:
                  if x != 'n.d.':
                      produccion_index.append(float(x))
                  else:
                      produccion_index.append(None)  # Reemplazar con None (NaN en Pandas)
          

          periodos_pbi = response_json_pbi.get('periods', [])
          pbi_index = []
          for b in periodos_pbi:
              valoress = b.get('values', [])
              for x in valoress:
                  if x != 'n.d.':
                      pbi_index.append(float(x))
                  else:
                      pbi_index.append(None)  # Reemplazar con None (NaN en Pandas)
          fechas = [q['name'] for q in periodos_produccion]

### Data Frame 🖼️

          glosario = {"Fechas": fechas, "Produccion": produccion_index, "PBI": pbi_index}
          df=pd.DataFrame(glosario)
          df['Fechas'] = pd.to_datetime(df['Fechas'], errors='coerce')
          df['Produccion'] = pd.to_numeric(df['Produccion'], errors='coerce')
          df['PBI'] = pd.to_numeric(df['PBI'], errors='coerce')
          df = df.dropna(subset=['Fechas', 'Produccion', 'PBI'])
          X = df[['Produccion']]
          y = df['PBI']
          X = sm.add_constant(X)  # Añadir una constante al modelo
          modelo = sm.OLS(y, X).fit()
          predicciones = modelo.predict(X)
          print(modelo.summary())

### Gráfico 📈📊

          plt.figure(figsize=(15, 10))
          plt.scatter(df['Produccion'], df['PBI'], label='Datos Reales')
          plt.plot(df['Produccion'], predicciones, color='red', label='Línea de Regresión')
          plt.xlabel('Produccion de Cobre')
          plt.ylabel('PBI - Minero')
          plt.title('Regresión Lineal: Produccion de Cobre vs PBI - Minería e Hidrocarburos')
          plt.legend()
          plt.show()
![image](https://github.com/user-attachments/assets/5c4210d6-e489-4927-ac73-2b8ce4d53c2c)

          
### Summary OLS :godmode:
OLS Regression Results
Dep. Variable:	PBI	R-squared:	0.958
Model:	OLS	Adj. R-squared:	0.958
Method:	Least Squares	F-statistic:	3773.
Date:	Sat, 16 Nov 2024	Prob (F-statistic):	4.45e-116
Time:	18:47:09	Log-Likelihood:	-511.47
No. Observations:	168	AIC:	1027.
Df Residuals:	166	BIC:	1033.
Df Model:	1		
Covariance Type:	nonrobust		
coef	std err	t	P>|t|	[0.025	0.975]
const	53.3446	1.122	47.547	0.000	51.130	55.560
Produccion	0.0005	7.52e-06	61.429	0.000	0.000	0.000
Omnibus:	95.438	Durbin-Watson:	0.773
Prob(Omnibus):	0.000	Jarque-Bera (JB):	886.283
Skew:	-1.867	Prob(JB):	3.52e-193
Kurtosis:	13.614	Cond. No.	4.24e+05

          
## Conclusión 📑📈
La conclusión interpretativa relacionada a los modelos de regresión muestran que el cobre es el metal que muestra una relación más estrecha que los otros metales señalados (ZINC, PLATA). Además, este proyecto muestra la relación de la variabilidad mostrada en un 90%. Sin embargo, hay otros factores que motivan la variabilidad o del PBI(Minería), como las decisiones geopolíticas, demanda extranjera y suministros solicitados por las empresas mineras a cargo de la explotación del recurso señalado. 








