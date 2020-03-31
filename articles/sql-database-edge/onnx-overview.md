---
title: 在 Azure SQL 資料庫邊緣預覽中，使用 ONNX 進行機器學習和 AI |微軟文檔
description: Azure SQL 資料庫邊緣預覽中的機器學習支援開放神經網路交換 （ONNX） 格式的模型。 ONNX 是一種開放格式，可用於在各種機器學習框架和工具之間交換模型。
keywords: 部署 sql 資料庫邊緣
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366272"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>在 SQL 資料庫邊緣預覽中，使用 ONNX 進行機器學習和 AI

Azure SQL 資料庫邊緣預覽中的機器學習支援[開放神經網路交換 （ONNX）](https://onnx.ai/)格式的模型。 ONNX 是一種開放格式，可用於在各種[機器學習框架和工具](https://onnx.ai/supported-tools)之間交換模型。

## <a name="overview"></a>總覽

要推斷 Azure SQL 資料庫邊緣中的機器學習模型，首先需要獲取模型。 這可以是經過預先訓練的模型，也可以是使用您選擇的框架訓練的自訂模型。 Azure SQL 資料庫邊緣支援 ONNX 格式，您需要將模型轉換為此格式。 不應對模型準確性產生影響，一旦您有了 ONNX 模型，就可以在 Azure SQL 資料庫邊緣部署模型，並使用[預測 T-SQL 函數使用本機評分](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="get-onnx-models"></a>取得 ONNX 模型

要獲取 ONNX 格式的模型，：

- **模型構建服務**：Azure 機器學習和[Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)[中的自動機器學習功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)等服務支援以 ONNX 格式直接匯出已訓練的模型。

- [**轉換和/或匯出現有模型**](https://github.com/onnx/tutorials#converting-to-onnx-format)：多個培訓框架（例如[PyTorch、](https://pytorch.org/docs/stable/onnx.html)鏈條和 Caffe2）支援本機匯出功能到 ONNX，這允許您將訓練的模型保存到 ONNX 格式的特定版本。 對於不支援本機匯出的框架，有獨立的 ONNX 轉換器可安裝包，使您能夠將從不同機器學習框架訓練的模型轉換為 ONNX 格式。

     **支援的架構**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [張力流](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [學習](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    有關支援的框架和示例的完整清單，請參閱轉換為[ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。

## <a name="limitations"></a>限制

目前，並非所有 ONNX 模型都受 Azure SQL 資料庫邊緣的支援。 支援僅限於具有**數位資料類型的**模型：

- [int 和 Bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [真實和浮動](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
其他數數值型別可以使用[CAST 和 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)轉換為支援的類型。

模型輸入的結構應使模型的每個輸入對應于表中的單個列。 例如，如果使用熊貓資料框來訓練模型，則每個輸入應該是模型的單獨列。

## <a name="next-steps"></a>後續步驟

- [通過 Azure 門戶部署 SQL 資料庫邊緣](deploy-portal.md)
- [在 Azure SQL 資料庫邊緣預覽版上部署 ONNX 模型](deploy-onnx.md)
