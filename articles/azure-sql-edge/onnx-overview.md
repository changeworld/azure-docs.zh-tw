---
title: Azure SQL Edge (預覽) 中採用 ONNX 的機器學習和 AI
description: Azure SQL Edge (預覽) 中的機器學習服務支援 Open Neural Network Exchange (ONNX) 格式的模型。 ONNX 是一種開放格式，您可以用此格式在各種機器學習架構和工具之間交換模型。
keywords: 部署 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 2db6c728ac35c6fc2f1fee1a602725371e448104
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368051"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>SQL Edge (預覽) 中採用 ONNX 的機器學習和 AI

Azure SQL Edge (預覽) 中的機器學習服務支援 [Open Neural Network Exchange (ONNX)](https://onnx.ai/) 格式的模型。 ONNX 是一種開放格式，您可以用此格式在各種[機器學習架構和工具](https://onnx.ai/supported-tools)之間交換模型。

## <a name="overview"></a>概觀

若要在 Azure SQL Edge 中推斷機器學習模型，您必須先取得模型。 這可以是預先定型的模型，或是以您所選架構定型的自訂模型。 Azure SQL Edge 支援 ONNX 格式，而且您必須將模型轉換成此格式。 這對模型的精確度應該不會有任何影響，而且一旦您擁有 ONNX 模型，您就可以在 Azure SQL Edge 中部署模型，並[搭配 PREDICT T-SQL 函式使用原生評分](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="get-onnx-models"></a>取得 ONNX 模型

若要取得 ONNX 格式的模型：

- **模型建立服務**：[Azure Machine Learning 中的自動化機器學習功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)及 [Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)等服務都支援以 ONNX 格式直接匯出定型的模型。

- [**轉換和/或匯出現有模型**](https://github.com/onnx/tutorials#converting-to-onnx-format)：多個訓練架構 (例如 [PyTorch](https://pytorch.org/docs/stable/onnx.html)、Chainer 和 Caffe2) 都支援 ONNX 的原生匯出功能，讓您可以將定型的模型儲存至特定版本的 ONNX 格式。 對於不支援原生匯出的架構，則有獨立的 ONNX 轉換器安裝套件，讓您可以將已定型的模型從不同機器學習架構轉換成 ONNX 格式。

     **支援的架構**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    如需支援架構的完整清單和範例，請參閱[轉換成 ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。

## <a name="limitations"></a>限制

目前，Azure SQL Edge 並未支援所有的 ONNX 模型。 僅支援**數值資料類型**的模型：

- [int 和 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real 和 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
您可以使用 [CAST 和 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)，將其他數數值類型轉換成支援的類型。

模型輸入應已結構化，如此一來，模型的每個輸入才可對應到資料表中的單一資料行。 例如，如果您使用 Pandas 資料框架來定型模型，則每個輸入都應該是模型的個別資料行。

## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Edge](deploy-portal.md)
- [在 Azure SQL Edge (預覽) 上部署 ONNX 模型](deploy-onnx.md)
