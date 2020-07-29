---
title: 使用預測來評分機器學習模型
description: 瞭解如何使用 Synapse SQL 中的 T-sql PREDICT 函數來評分機器學習模型。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d28f37e6020e3a3165b012548868a3ec798651c2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294149"
---
# <a name="score-machine-learning-models-with-predict"></a>使用預測來評分機器學習模型

Synapse SQL 提供您使用熟悉的 T-sql 語言對機器學習模型進行評分的功能。 透過 T-sql[預測](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)，您可以讓現有的機器學習模型使用歷程記錄資料進行定型，並在資料倉儲的安全界限內進行評分。 PREDICT 函數接受[ONNX （Open Neural Network Exchange）](https://onnx.ai/)模型和資料做為輸入。 這項功能可避免在資料倉儲外部移動重要資料以進行評分的步驟。 其目標在於讓資料專業人員能夠輕鬆地使用熟悉的 T-sql 介面部署機器學習模型，並與資料科學家密切合作，以處理其工作的正確架構。

> [!NOTE]
> 這項功能目前不支援 SQL 隨選。

此功能需要在 Synapse SQL 之外訓練模型。 建立模型之後，請將其載入資料倉儲，並使用 T-sql Predict 語法對其進行評分，以從資料取得見解。

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>定型模型

Synapse SQL 需要預先定型的模型。 在訓練用來在 Synapse SQL 中執行預測的機器學習模型時，請記住下列因素。

- Synapse SQL 僅支援 ONNX 格式模型。 ONNX 是一種開放原始碼模型格式，可讓您在各種架構之間交換模型，以實現互通性。 您可以將現有的模型轉換成 ONNX 格式，方法是使用原生支援的架構，或將封裝轉換成可用的架構。 例如， [sklearn-onnx](https://github.com/onnx/sklearn-onnx) package convert scikit-learn-學習模型至 onnx。 [ONNX GitHub 存放庫](https://github.com/onnx/tutorials#converting-to-onnx-format)提供一份支援的架構和範例清單。

   如果您使用[自動化 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)進行定型，請務必將*enable_onnx_compatible_models*參數設定為 TRUE，以產生 onnx 格式模型。 [自動化 Machine Learning 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)會示範如何使用 AutoML 來建立 ONNX 格式的機器學習服務模型的範例。

- 以下是輸入資料支援的資料類型：
    - int、Bigint、real、float
    - char、Varchar、Nvarchar

- 評分資料必須與定型資料的格式相同。 PREDICT 不支援複雜資料類型（例如多維度陣列）。 因此，若要進行定型，請確定模型的每個輸入都對應到計分資料表的單一資料行，而不是傳遞包含所有輸入的單一陣列。

- 請確定模型輸入的名稱和資料類型符合新預測資料的資料行名稱和資料類型。 使用線上提供的各種開放原始碼工具將 ONNX 模型視覺化，可進一步協助進行偵錯工具。

## <a name="loading-the-model"></a>載入模型

模型會以十六進位字串的形式儲存在 Synapse SQL 使用者資料表中。 可以在模型資料表中加入其他資料行，例如識別碼和描述，以識別模型。 使用 Varbinary （max）做為模型資料行的資料類型。 以下是可用於儲存模型之資料表的程式碼範例：

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

當模型轉換成十六進位字串和指定的資料表定義之後，請使用[複製命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)或 Polybase，在 Synapse SQL 資料表中載入模型。 下列程式碼範例會使用 Copy 命令來載入模型。

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>對模型進行評分

在資料倉儲中載入模型和資料之後，請使用**T-SQL PREDICT**函數來對模型進行評分。 請確定新的輸入資料與用來建立模型的定型資料具有相同的格式。 T-sql PREDICT 會接受兩個輸入：模型和新的評分輸入資料，並產生新的資料行做為輸出。模型可以指定為變數、常值或純量 sub_query。 使用[與 common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15)來指定資料參數的命名結果集。

下列範例顯示使用預測函數的範例查詢。 系統會建立包含預測結果的額外資料行，其名稱為「*分數*」和「資料類型為*float* 」。 所有輸入資料行和輸出預測資料行都可與 select 語句一起顯示。 如需詳細資訊，請參閱[PREDICT （transact-sql）](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)。

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解 PREDICT 函數，請參閱[predict （transact-sql）](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)。
