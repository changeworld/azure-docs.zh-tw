---
title: 使用 ONNX 在 Azure SQL Edge 上部署 ML 模型
description: 在此 Azure SQL Edge 教學課程三部曲的第三部分中，您會在 SQL Edge 上執行 ONNX 機器學習模型，以預測鐵礦中的雜質含量。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: abd1bc9b53c303d094d74683da0d6e3fad4d715f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887465"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>使用 ONNX 在 Azure SQL Edge 上部署 ML 模型 

在此教學課程三部曲的第三部分中，若要在 Azure SQL Edge 中預測鐵礦中的雜質含量，您必須：

1. 使用 Azure Data Studio 連線到 Azure SQL Edge 執行個體中的 SQL Database。
2. 使用 Azure SQL Edge 中的 ONNX 來預測鐵礦中的雜質含量。

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>連線到 Azure SQL Edge 執行個體中的 SQL Database

1. 開啟 Azure Data Studio。

2. 在**歡迎使用**索引標籤中，使用下列詳細資料開始新的連線：

   |_欄位_|_ReplTest1_|
   |-------|-------|
   |連線類型| Microsoft SQL Server|
   |伺服器|在此示範中建立的 VM 中提到的公用 IP 位址|
   |使用者名稱|sa|
   |密碼|建立 Azure SQL Edge 執行個體時所使用的強式密碼|
   |資料庫|預設|
   |伺服器群組|預設|
   |名稱 (選用)|提供選用的名稱|

3. 按一下 [連接] 

4. 在**檔案**區段中開啟新的筆記本，或使用鍵盤快速鍵 Alt + Windows + N。 

5. 將核心設定為 Python 3。

## <a name="predict-iron-ore-impurities-with-onnx"></a>使用 ONNX 來預測鐵礦中的雜質含量

在 Azure Data Studio 筆記本中輸入下列 Python 程式碼並執行。

1. 首先，安裝和匯入所需的套件。

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. 定義迴歸實驗的 Azure AutoML 工作區和 AutoML 實驗組態。

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. 將資料集匯入 Panda 框架。 為了訓練模型，請使用 Kaggle 的[挖礦流程品質預測](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process)訓練資料集。 下載資料檔案，並儲存在您的開發電腦本機上。 您將使用此資料來預測濃縮鐵質中的雜質含量。

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. 分析資料以找出任何偏態。 在此過程中，查看資料框架中每個資料行的分佈和扭曲資訊。

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. 檢查並修正資料中的偏態層級。

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. 使用預測功能檢查其他功能的相互關聯。 如果相互關聯不高，請移除這些功能。

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. 啟動 AzureML 實驗，以尋找並定型最佳演算法。 在此情況下，您會測試所有的迴歸演算法，其中主要計量為標準化均方根誤差 (NRMSE)。 如需詳細資訊，請參閱 [Azure ML 實驗的主要計量](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric)。 下列程式碼會在本機執行 ML 實驗。

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. 在 Azure SQL Edge 資料庫中載入模型以進行本機計分。

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. 最後，使用 Azure SQL Edge 模型，利用定型的模型來執行預測。

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. 使用 Python，針對鐵質進量、時間日期和二氧化硅進量建立二氧化硅預測量的百分比圖表。

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>後續步驟

如需在 Azure SQL Edge 中使用 ONNX 模型的詳細資訊，請參閱[在 SQL Edge 中使用 ONNX 的機器學習和 AI](onnx-overview.md)。
