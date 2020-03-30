---
title: 列車 SVD 推薦者：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用訓練 SVD 推薦器模組，使用 SVD 演算法訓練貝葉斯推薦者。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477403"
---
# <a name="train-svd-recommender"></a>訓練 SVD 推薦

本文介紹如何在 Azure 機器學習設計器（預覽）中使用訓練 SVD 推薦器模組。 使用此模組可以基於單值分解 （SVD） 演算法訓練推薦模型。  

訓練 SVD 推薦器模組讀取使用者專案評級三重資料集。 它返回經過培訓的 SVD 推薦人。 然後，您可以使用經過訓練的模型來預測評分或生成建議，使用 Score [SVD 推薦器](score-svd-recommender.md)模組。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>更多關於推薦模型和SVD推薦者  

推薦系統的主要目標是向系統*使用者*推薦一個或多個*專案*。 專案的示例可以是電影、餐廳、書籍或歌曲。 使用者可以是人員、一組人或具有專案首選項的另一個實體。  

推薦人系統有兩種主要方法： 

+ **基於內容**的方法使用使用者和專案的功能。 使用者可以按年齡和性別等屬性進行描述。 可以通過作者和製造商等屬性來描述專案。 您可以在社交匹配網站上找到基於內容的建議系統的典型示例。 
+ **協作篩選**僅使用使用者和項的識別碼。 它從使用者提供給項的評級（稀疏）矩陣中獲取有關這些實體的隱式資訊。 我們可以從使用者評級的專案和對相同專案進行評級的其他使用者中瞭解使用者。  

SVD 推薦人使用使用者和項的識別碼，以及使用者為項提供的評級矩陣。 這是一個*協作推薦者*。 

有關 SVD 推薦者的詳細資訊，請參閱相關研究論文：[推薦器系統的矩陣分解技術](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何配置列車 SVD 推薦器  

### <a name="prepare-data"></a>準備資料

在使用模組之前，輸入資料必須採用建議模型期望的格式。 需要一組使用者專案評級三重的培訓資料集。

+ 第一列包含使用者識別碼。
+ 第二列包含項識別碼。
+ 第三列包含使用者項對的評級。 評級值必須為數字類型。  

Azure 機器學習設計器中**的影片分級**資料集（選擇**資料集**，然後**示例**）演示了預期的格式：

![電影分級](media/module/movie-ratings-dataset.png)

通過此示例，您可以看到單個使用者對多部電影進行了評級。 

### <a name="train-the-model"></a>將模型定型

1.  將訓練 SVD 推薦器模組添加到設計器中的管道中，並將其連接到訓練資料。  
   
2.  對於**因數數**，指定與推薦人一起使用的因數數。  
    
    每個因數都測量使用者與專案相關的程度。 因數的數量也是潛在因數空間的維度。 隨著使用者和專案數量的增加，最好設置更多的因素。 但是，如果數位太大，性能可能會下降。
    
3.  **建議演算法反覆運算次數**指示演算法應處理輸入資料的次數。 這個數位越高，預測越準確。 但是，數位越大，意味著訓練速度變慢。 預設值是 30。

4.  對於**學習速率**，輸入定義學習步驟大小的介於 0.0 和 2.0 之間的數位。

    學習速率確定每次反覆運算中步驟的大小。 如果步長過大，則可能會超過最佳解決方案。 如果步長太小，則訓練需要更長的時間才能找到最佳解決方案。 
  
5.  提交管道。  


## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
