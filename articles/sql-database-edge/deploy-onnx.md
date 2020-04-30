---
title: 在 SQL Database Edge 預覽中使用 ONNX 部署和進行預測
description: 瞭解如何將模型定型、將它轉換成 ONNX、將它部署到 Azure SQL Database Edge 預覽，然後使用上傳的 ONNX 模型對資料執行原生預測。
keywords: 部署 sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 04/23/2020
ms.openlocfilehash: aa2bf5473bf5bd76cfdad39310ce793ab3921652
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129283"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-in-sql-database-edge-preview"></a>在 SQL Database Edge 預覽中使用 ONNX 模型部署和進行預測

在本快速入門中，您將瞭解如何將模型定型、將它轉換成 ONNX、將它部署到 Azure SQL Database Edge 預覽，然後使用上傳的 ONNX 模型對資料執行原生預測。 如需詳細資訊，請參閱[SQL Database Edge 預覽中的機器學習服務和 AI 搭配 ONNX](onnx-overview.md)。

本快速入門是以**scikit-learn**為基礎，學習並使用[波士頓機架資料集](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)。

## <a name="before-you-begin"></a>開始之前

* 如果您尚未部署 Azure SQL Database Edge 模組，請遵循[使用 Azure 入口網站部署 SQL Database Edge 預覽](deploy-portal.md)的步驟。

* 安裝[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)。

* 開啟 Azure Data Studio，並遵循下列步驟來安裝本快速入門所需的套件：

    1. 開啟連線到 Python 3 核心的[新筆記本](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks)。 
    1. 按一下 [**管理封裝**]，然後在 [**新增**] 下搜尋**scikit-learn-瞭解**，並安裝 scikit-learn-學習套件。 
    1. 此外，請安裝**setuptools**、 **numpy**、 **onnxmltools**、 **onnxruntime**、 **skl2onnx**、 **pyodbc**和**sqlalchemy**套件。
    
* 針對以下每個腳本部分，在 Azure Data Studio 筆記本的資料格中輸入它，然後執行儲存格。

## <a name="train-a-pipeline"></a>訓練管線

將資料集分割成使用特徵來預測房屋的中間值。

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**輸出**：

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

建立管線來定型 LinearRegression 模型。 您也可以使用其他回歸模型。

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

檢查模型的精確度，然後計算 R2 分數和 mean 平方誤差。

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**輸出**：

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>將模型轉換成 ONNX

將資料類型轉換成支援的 SQL 資料類型。 其他資料框架也會需要此轉換。

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

使用`skl2onnx`，將 LinearRegression 模型轉換成 ONNX 格式，並將它儲存在本機。

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>測試 ONNX 模型

將模型轉換成 ONNX 格式之後，請對模型進行評分，以顯示效能降低的情況。

> [!NOTE]
> ONNX 執行時間會使用浮點數，而不是加倍，因此可能會有小差異。

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**輸出**：

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>插入 ONNX 模型

在資料庫`models` `onnx`的資料表中，將模型儲存在 Azure SQL Database 邊緣。 在 [連接字串] 中，指定 [**伺服器位址**]、[使用者**名稱**] 和 [**密碼**]。

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>載入資料

將資料載入 Azure SQL Database 邊緣。

首先，建立兩個數據表、**功能**和**目標**，以儲存波士頓機架資料集的子集。

* 「**功能**」包含所有用來預測目標的資料（中間值）。 
* **目標**包含資料集內每筆記錄的中間值。 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

最後，使用`sqlalchemy` ，分別將`x_train`和`y_train` pandas 資料框架插入資料表`features`和`target`中。 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

現在您可以在資料庫中查看資料。

## <a name="run-predict-using-the-onnx-model"></a>使用 ONNX 模型執行 PREDICT

透過 Azure SQL Database Edge 中的模型，使用上傳的 ONNX 模型，對資料執行原生預測。

> [!NOTE]
> 將 [筆記本核心] 變更為 [SQL]，以執行其餘的儲存格。

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [onnx].[dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input) WITH (variable1 FLOAT) AS p
```

## <a name="next-steps"></a>後續步驟

* [使用 SQL Database Edge 中的 ONNX Machine Learning 和 AI](onnx-overview.md)
