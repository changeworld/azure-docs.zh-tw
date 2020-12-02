---
title: 使用 PREDICT 來評分機器學習模型
description: 瞭解如何在專用的 SQL 集區中使用 T-sql PREDICT 函數來評分機器學習模型。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ce77a169e28e21aa37be2a49997a58ee42c93807
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510823"
---
# <a name="score-machine-learning-models-with-predict"></a>使用 PREDICT 來評分機器學習模型

專用的 SQL 集區可讓您使用熟悉的 T-sql 語言來對機器學習模型進行評分。 您可以使用 T-sql [預測](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)，讓現有的機器學習模型以歷程記錄資料進行定型，並在資料倉儲的安全界限內進行評分。 PREDICT 函數會採用 [ONNX (Open Neural Network Exchange) ](https://onnx.ai/) 模型和資料作為輸入。 這項功能消除了在資料倉儲外部移動重要資料以進行評分的步驟。 其目標是要讓資料專業人員能夠輕鬆地使用熟悉的 T-sql 介面部署機器學習服務模型，並與資料科學家完美地共同作業，並使用適合其工作的架構。

> [!NOTE]
> 無伺服器 SQL 集區目前不支援此功能。

這項功能需要在 Synapse SQL 之外定型模型。 建立模型之後，請將它載入資料倉儲中，並使用 T-sql Predict 語法來評分，以從資料中取得見解。

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>定型模型

專用的 SQL 集區需要預先定型的模型。 定型用來在專用 SQL 集區中執行預測的機器學習模型時，請記住下列因素。

- 專用的 SQL 集區只支援 ONNX 格式模型。 ONNX 是一種開放原始碼模型格式，可讓您在各種架構之間交換模型，以實現互通性。 您可以將現有的模型轉換成 ONNX 格式，方法是使用以原生方式支援的架構，或可使用轉換的套件。 例如， [sklearn-onnx](https://github.com/onnx/sklearn-onnx) package 會將 scikit-learn-學習模型轉換為 onnx。 [ONNX GitHub 存放庫](https://github.com/onnx/tutorials#converting-to-onnx-format) 提供支援的架構和範例清單。

   如果您使用 [自動化 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 進行定型，請務必將 *enable_onnx_compatible_models* 參數設為 TRUE，以產生 onnx 格式模型。 [自動 Machine Learning 筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 顯示如何使用 AUTOML 建立 ONNX 格式的機器學習模型的範例。

- 輸入資料支援下列資料類型：
    - int、Bigint、real、float
    - char、Varchar、Nvarchar

- 計分資料的格式必須與定型資料相同。 預測不支援複雜資料類型（例如多維陣列）。 因此，若要進行定型，請確定模型的每個輸入都對應到計分資料表的單一資料行，而不是傳遞包含所有輸入的單一陣列。

- 請確定模型輸入的名稱和資料類型符合新預測資料的資料行名稱和資料類型。 使用線上提供的各種開放原始碼工具來視覺化 ONNX 模型，可進一步協助進行偵錯工具。

## <a name="loading-the-model"></a>載入模型

模型會以十六進位字串的形式儲存在專用的 SQL 集區使用者資料表中。 可以在模型資料表中加入其他資料行（例如識別碼和描述），以識別模型。 使用 Varbinary (max) 作為模型資料行的資料類型。 以下是可用於儲存模型之資料表的程式碼範例：

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

一旦模型轉換為十六進位字串和指定的資料表定義之後，請使用 [COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) 或 Polybase 將模型載入專用的 SQL 集區資料表中。 下列程式碼範例會使用 Copy 命令來載入模型。

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>評分模型

在資料倉儲中載入模型和資料之後，請使用 **T-SQL PREDICT** 函數來對模型進行評分。 請確定新的輸入資料與用於建立模型的定型資料具有相同的格式。 T-sql 預測會採用兩個輸入：模型和新計分輸入資料，並產生輸出的新資料行。您可以將模型指定為變數、常值或純量 sub_query。 使用 [WITH common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true) 指定資料參數的命名結果集。

下列範例顯示使用預測函數的範例查詢。 系統會建立包含預測結果的額外資料行，其中包含名稱 *分數* 和資料類型 *float* 。 您可以使用 select 語句來顯示所有輸入資料行以及輸出預測資料行。 如需詳細資訊，請參閱 [預測 (transact-sql) ](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)。

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解 PREDICT 函數，請參閱 [預測 (transact-sql) ](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)。
