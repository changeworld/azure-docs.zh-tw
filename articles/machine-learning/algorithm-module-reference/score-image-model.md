---
title: 使用評分影像模型模組
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用評分影像模型模組，以使用定型的影像模型來產生預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b949603b3e6ee51311f9c54f3e1326217f00c82d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039105"
---
# <a name="score-image-model"></a>映像計分模型

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

使用此模組，在輸入影像資料上使用定型的影像模型來產生預測。

## <a name="how-to-configure-score-image-model"></a>如何設定分數影像模型

1. 將**評分影像模型**模組新增至您的管線。

2. 附加定型的影像模型和包含輸入影像資料的資料集。 

    資料的類型應該是 ImageDirectory。 如需如何取得映射目錄的詳細資訊，請參閱[轉換成映射目錄](convert-to-image-directory.md)模組。 輸入資料集的架構通常也應該符合用來定型模型之資料的架構。

3. 提交管線。

## <a name="results"></a>結果

使用[分數影像模型](score-image-model.md)產生一組分數之後，若要產生一組用來評估模型精確度（效能）的計量，您可以將此模組和評分資料集連接到[評估模型](evaluate-model.md)。 

### <a name="publish-scores-as-a-web-service"></a>將分數發佈為 web 服務

計分的常見用法是將輸出當做預測性 web 服務的一部分傳回。 如需詳細資訊，請參閱[本教學](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy)課程，瞭解如何在 Azure Machine Learning 設計工具中根據管線部署即時端點。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
