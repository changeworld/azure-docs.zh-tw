---
title: 使用 ONNX 部署和進行預測
titleSuffix: SQL machine learning
description: 瞭解如何將模型定型、將其轉換為 ONNX、將它部署至 Azure SQL Edge 或 Azure SQL 受控執行個體 (preview) ，然後使用上傳的 ONNX 模型，對資料執行原生預測。
keywords: 部署 SQL Edge
ms.prod: sql
ms.technology: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 07/14/2020
ms.openlocfilehash: 5a1e0b12179070dc11e838004c4b27cf04b5396b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298900"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model"></a>使用 ONNX 模型部署及進行預測

在本快速入門中，您將瞭解如何將模型定型、將其轉換為 ONNX、將它部署至 [AZURE Sql Edge](onnx-overview.md) 或 [azure sql 受控執行個體 (preview) ](../azure-sql/managed-instance/machine-learning-services-overview.md)，然後使用上傳的 ONNX 模型來執行原生預測資料。

本快速入門以 **scikit-learn** 為基礎，並使用 [Boston Housing 資料集](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)。

## <a name="before-you-begin"></a>開始之前

* 如果您使用的是 Azure SQL Edge，且您尚未部署 Azure SQL Edge 模組，請遵循 [使用 Azure 入口網站部署 SQL Edge](deploy-portal.md)的步驟。

* 安裝 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)。

* 安裝本快速入門所需的 Python 套件：

  1. 開啟連線至 Python 3 核心的[新筆記本](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks)。 
  1. 按一下 [**管理封裝**]
  1. 在 [ **已安裝** ] 索引標籤中，于已安裝的套件清單中尋找下列 Python 套件。 如果未安裝任何這些封裝，請選取 [ **新增** ] 索引標籤，搜尋封裝，然後按一下 [ **安裝**]。
     - **scikit-learn**
     - **numpy**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **setuptools**
     - **skl2onnx**
     - **sqlalchemy**

* 針對以下每個指令碼部分，將其輸入至 Azure Data Studio 筆記本中的資料格，然後執行該資料格。

## <a name="train-a-pipeline"></a>定型管線

分割資料集，以使用特徵來預測房屋的中間值。

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

建立用來定型 LinearRegression 模型的管線。 您也可以使用其他迴歸模型。

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

檢查模型的精確度，然後計算 R2 分數和均方差。

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

使用 `skl2onnx`，將 LinearRegression 模型轉換成 ONNX 格式，並將其儲存在本機。

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>測試 ONNX 模型

將模型轉換成 ONNX 格式之後，請對模型進行評分，以顯示效能略為降低或不受影響的情況。

> [!NOTE]
> ONNX 執行階段會使用浮點數 (而非雙精度浮點數)，因此可能會略有差異。

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

在 Azure SQL Edge 或 Azure SQL 受控執行個體中，將模型儲存在 `models` 資料庫的資料表中 `onnx` 。 在連接字串中，指定**伺服器位址**、**使用者名稱**和**密碼**。

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

將資料載入 SQL。

首先，建立 **features** 和 **target** 這兩個資料表，以儲存 Boston Housing 資料集的子集。

* **Features** 包含用來預測目標中間值的所有資料。 
* **Target** 包含資料集內每筆記錄的中間值。 

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

最後，使用 `sqlalchemy` 將 `x_train` 和 `y_train` 的 pandas 資料框架分別插入資料表 `features` 和 `target` 中。 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

現在，您可以檢視資料庫中的資料。

## <a name="run-predict-using-the-onnx-model"></a>使用 ONNX 模型執行 PREDICT

使用 SQL 中的模型，使用上傳的 ONNX 模型，對資料執行原生預測。

> [!NOTE]
> 將筆記本核心變更為 SQL，以執行其餘的資料格。

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
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>後續步驟

* [SQL Edge 中採用 ONNX 的機器學習和 AI](onnx-overview.md)
* [Azure SQL 中的 Machine Learning 服務受控執行個體 (preview) ](../azure-sql/managed-instance/machine-learning-services-overview.md)
