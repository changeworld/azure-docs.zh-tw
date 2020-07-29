---
title: 評估推薦：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [評估推薦] 模組來評估推薦模型預測的精確度。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76312255"
---
# <a name="evaluate-recommender"></a>評估推薦

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的 [評估推薦] 模組。 其目標是要測量建議模型所做預測的精確度。 藉由使用此課程模組，您可以評估不同類型的建議：  
  
-   針對使用者和專案所預測的評等    
-   建議使用者使用的專案  
  
當您使用建議模型建立預測時，會針對每個支援的預測類型傳回稍微不同的結果。 [評估推薦] 模組會從評分資料集的資料行格式會推算預測種類。 例如，評分資料集可能包含：

- 使用者-專案-評等三合一
- 使用者及其建議的專案

此模組也會根據所進行的預測類型，套用適當的效能計量。 

  
## <a name="how-to-configure-evaluate-recommender"></a>如何設定評估推薦

[評估推薦] 模組會使用包含對應「基礎事實」資料的建議模型來比較預測輸出。 例如，[計分 SVD 推薦](score-svd-recommender.md)模組會產生評分的資料集，您可以使用 [評估] 推薦來分析。

### <a name="requirements"></a>規格需求

評估推薦需要下列資料集做為輸入。 
  
#### <a name="test-dataset"></a>測試資料集

測試資料集包含以使用者-專案-評等三合一形式呈現的「真實」資料。  

#### <a name="scored-dataset"></a>計分的資料集

評分資料集包含建議模型產生的預測。  
  
第二個資料集中的資料行取決於您在計分程式期間所執行的預測類型。 例如，評分資料集可能包含下列其中一項：

- 使用者可能為專案提供的使用者、專案和評等
- 建議的使用者和專案清單 

### <a name="metrics"></a>計量

模型的效能計量是根據輸入的類型產生。 下列各節提供詳細資料。

## <a name="evaluate-predicted-ratings"></a>評估預測的評等  

當您評估預測的評等時，評分資料集（評估推薦的第二個輸入）必須包含符合下列需求的使用者-專案-評等三合一：
  
-   資料集的第一個資料行包含使用者識別碼。    
-   第二個數據行包含專案識別碼。  
-   第三個數據行包含對應的使用者-專案評等。  
  
> [!IMPORTANT] 
> 若要成功評估，資料行名稱必須 `User` 分別是、 `Item` 和 `Rating` 。  
  
[評估推薦] 會比較「基礎」資料集的評等與評分資料集的預測評等。 然後，它會計算平均絕對錯誤（MAE）和根 mean 平方誤差（RMSE）。



## <a name="evaluate-item-recommendations"></a>評估專案建議

當您評估專案建議時，請使用評分資料集，其中包含每個使用者的建議專案：
  
-   資料集的第一個資料行必須包含使用者識別碼。    
-   所有後續的資料行都應該包含對應的建議專案識別碼，並依專案與使用者的相關程度排序。 

在您連接此資料集之前，建議您先排序資料集，以便先產生最相關的專案。  

> [!IMPORTANT] 
> 若要讓 [評估推薦] 能夠正常執行，資料行名稱必須是 `User` 、 `Item 1` 、等等 `Item 2` `Item 3` 。  
  
[評估推薦] 會計算平均正規化折扣累積增益（NDCG），並在輸出資料集中傳回。  
  
因為不可能知道建議專案的實際「誠實」，所以 [評估推薦] 會使用測試資料集中的使用者-專案評等，做為 NDCG 的計算增益。 若要評估，推薦評分模組必須只針對具有「真」分級（在測試資料集中）的專案產生建議。  
  

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
