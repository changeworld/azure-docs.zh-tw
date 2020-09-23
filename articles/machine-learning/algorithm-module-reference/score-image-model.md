---
title: 使用計分影像模型模組
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「評分影像模型」模組，利用定型的影像模型來產生預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 88ca997e2d22283babf582b10d9b0eeb7de122c0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905191"
---
# <a name="score-image-model"></a>映像計分模型

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組，在輸入影像資料上使用定型的影像模型來產生預測。

## <a name="how-to-configure-score-image-model"></a>如何設定計分影像模型

1. 將 **評分影像模型** 模組新增至您的管線。

2. 附加已定型的影像模型和包含輸入影像資料的資料集。 

    資料的類型應該是 ImageDirectory。 如需如何取得映射目錄的詳細資訊，請參閱 [轉換成映射目錄](convert-to-image-directory.md) 模組。 輸入資料集的架構通常也應該符合用來定型模型之資料的架構。

3. 提交管線。

## <a name="results"></a>結果

使用 [計分影像模型](score-image-model.md)產生一組分數之後，若要產生一組用來評估模型精確度 (效能) 的計量，您可以連接此模組和計分資料集來 [評估模型](evaluate-model.md)。 

### <a name="publish-scores-as-a-web-service"></a>將分數發佈為 web 服務

計分的常見用法是在預測性 web 服務中傳回輸出。 如需詳細資訊，請參閱 [本教學](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) 課程，以瞭解如何在 Azure Machine Learning 表設計工具中根據管線部署即時端點。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
