---
title: 評估推薦者：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用評估推薦器模組來評估推薦者模型預測的準確性。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312255"
---
# <a name="evaluate-recommender"></a>評估推薦

本文介紹如何在 Azure 機器學習設計器（預覽）中使用評估推薦器模組。 目標是測量推薦模型所做的預測的準確性。 通過使用此模組，您可以評估不同類型的建議：  
  
-   預測使用者和專案的評級    
-   建議使用者的專案  
  
使用建議模型創建預測時，將為每個受支援的預測類型返回略有不同的結果。 評估推薦器模組從評分資料集的列格式推斷預測類型。 例如，評分的資料集可能包含：

- 使用者專案評級三倍
- 使用者及其推薦專案

該模組還根據所做預測的類型應用適當的性能指標。 

  
## <a name="how-to-configure-evaluate-recommender"></a>如何配置評估推薦器

評估推薦器模組使用推薦模型與相應的"地面真相"資料比較預測輸出。 例如，"評分[SVD 推薦器"](score-svd-recommender.md)模組生成評分資料集，您可以使用評估推薦器進行分析。

### <a name="requirements"></a>需求

評估推薦器需要以下資料集作為輸入。 
  
#### <a name="test-dataset"></a>測試資料集

測試資料集以使用者專案評級三重的形式包含"地面真相"資料。  

#### <a name="scored-dataset"></a>計分的資料集

評分資料集包含建議模型生成的預測。  
  
此第二個資料集中的列取決於您在評分過程中執行的預測類型。 例如，評分的資料集可能包含以下任一內容：

- 使用者、專案和使用者可能為該專案提供分級
- 為其推薦的使用者和專案的清單 

### <a name="metrics"></a>計量

模型的性能指標基於輸入類型生成。 以下各節詳細介紹了詳情。

## <a name="evaluate-predicted-ratings"></a>評估預測評級  

評估預測評級時，評分資料集（評估推薦人的第二個輸入）必須包含滿足這些要求的使用者項評級三倍：
  
-   資料集的第一列包含使用者識別碼。    
-   第二列包含項識別碼。  
-   第三列包含相應的使用者專案評級。  
  
> [!IMPORTANT] 
> 要使評估成功，列名稱必須分別為`User``Item`和 。 `Rating`  
  
評估推薦人將"地面真相"資料集中的評分與評分資料集的預測評級進行比較。 然後計算平均絕對誤差 （MAE） 和根均方誤差 （RMSE）。



## <a name="evaluate-item-recommendations"></a>評估專案建議

評估專案建議時，請使用包含每個使用者推薦專案的評分資料集：
  
-   資料集的第一列必須包含使用者識別碼。    
-   所有後續列都應包含相應的建議項識別碼，該識別碼按項與使用者的相關性排序。 

在連接此資料集之前，我們建議您對資料集進行排序，以便最相關的項先于此。  

> [!IMPORTANT] 
> 要使評估推薦者正常工作，列名稱必須為`User`、、`Item 2``Item 3``Item 1`依此類推。  
  
評估推薦者計算平均正常化貼現累積收益 （NDCG），並在輸出資料集中返回。  
  
由於不可能知道建議項的實際"地面真相"，因此評估推薦者使用測試資料集中的使用者專案評級作為 NDCG 計算的收益。 要進行評估，推薦人評分模組必須僅針對具有"地面真相"評級的專案（在測試資料集中）生成建議。  
  

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
