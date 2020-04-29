---
title: Azure SQL Database Edge 預覽版中的機器學習服務和 AI 搭配 ONNX |Microsoft Docs
description: Azure SQL Database Edge 預覽版中的機器學習支援 Open Neural Network Exchange （ONNX）格式的模型。 ONNX 是一種開放格式，可讓您用來在各種機器學習架構和工具之間交換模型。
keywords: 部署 sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366272"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL Database Edge 預覽版中的機器學習服務和 AI 搭配 ONNX

Azure SQL Database Edge 預覽版中的機器學習支援[Open Neural Network Exchange （ONNX）](https://onnx.ai/)格式的模型。 ONNX 是一種開放格式，可讓您用來在各種[機器學習架構和工具](https://onnx.ai/supported-tools)之間交換模型。

## <a name="overview"></a>總覽

若要在 Azure SQL Database Edge 中推斷機器學習模型，您需要先取得模型。 這可以是預先定型的模型，或是以您選擇的架構定型的自訂模型。 Azure SQL Database Edge 支援 ONNX 格式，而且您必須將模型轉換成此格式。 對模型的精確度應該不會有任何影響，而且一旦擁有 ONNX 模型，您就可以在 Azure SQL Database 邊緣部署模型，並搭配[預測 t-sql 函數使用原生計分](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="get-onnx-models"></a>取得 ONNX 模型

若要取得 ONNX 格式的模型：

- **模型建立服務**： Azure Machine Learning 和[Azure 自訂視覺服務](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)[中自動化 Machine Learning 功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)的服務，支援以 ONNX 格式直接匯出定型的模型。

- [**轉換和/或匯出現有模型**](https://github.com/onnx/tutorials#converting-to-onnx-format)：數個定型架構（例如[PyTorch](https://pytorch.org/docs/stable/onnx.html)、Chainer 和 CAFFE2）支援對 ONNX 的原生匯出功能，可讓您將定型的模型儲存至特定版本的 ONNX 格式。 對於不支援原生匯出的架構，有獨立的 ONNX 轉換器可安裝套件，可讓您將從不同機器學習架構定型的模型轉換成 ONNX 格式。

     **支援的架構**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    如需支援的架構和範例的完整清單，請參閱[轉換為 ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。

## <a name="limitations"></a>限制

目前，Azure SQL Database Edge 並不支援所有的 ONNX 模型。 支援僅限於具有**數值資料類型**的模型：

- [int 和 Bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real 和 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
您可以使用[CAST 和 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)，將其他數數值型別轉換成支援的類型。

模型輸入應該是結構化的，讓模型的每個輸入都對應到資料表中的單一資料行。 例如，如果您使用 pandas 資料框架來定型模型，則每個輸入都應該是模型的個別資料行。

## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Database Edge](deploy-portal.md)
- [在 Azure SQL Database Edge 預覽上部署 ONNX 模型](deploy-onnx.md)
