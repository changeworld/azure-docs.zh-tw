---
title: 定型 SVD 推薦：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [定型 SVD 推薦] 模組，利用 SVD 演算法將貝氏推薦定型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a5740e851fbd8f7ba82e179f7e5299d6c7090596
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890234"
---
# <a name="train-svd-recommender"></a>訓練 SVD 推薦

本文描述如何在 Azure Machine Learning 設計師中使用「定型 SVD 推薦模組。 您可以使用此模組，根據單一值分解 (SVD) 演算法來訓練建議模型。  

定型 SVD 推薦模組會讀取使用者-專案-評等三合一的資料集。 它會傳回已定型的 SVD 推薦。 然後，您可以使用 [計分 SVD 推薦](score-svd-recommender.md) 模組，使用定型的模型來預測評等或產生建議。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>深入瞭解建議模型和 SVD 推薦  

建議系統的主要目的是建議系統的*使用者*使用一或多個*專案*。 專案的範例可能是電影、餐廳、書籍或歌曲。 使用者可能是個人、人員群組或具有專案喜好設定的其他實體。  

推薦系統有兩種主要方法： 

+ 以 **內容為基礎** 的方法會針對使用者和專案使用功能。 使用者可以透過年齡和性別等屬性來描述。 專案可以依作者和製造商等屬性來描述。 您可以在社交配對網站上找到以內容為基礎的建議系統一般範例。 
+ 共同作業**篩選**只會使用使用者和專案的識別碼。 它會從使用者指定給專案的 (稀疏) 矩陣中取得有關這些實體的隱含資訊。 我們可以從已評分的專案，以及其他已評等相同專案的使用者瞭解使用者。  

SVD 推薦會使用使用者和專案的識別碼，以及使用者指定給專案的評等矩陣。 它是共同作業的 *推薦*。 

如需 SVD 推薦的詳細資訊，請參閱相關的研究白皮書： [推薦系統的矩陣分解技術](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何設定定型 SVD 推薦  

### <a name="prepare-data"></a>準備資料

使用模組之前，您的輸入資料必須採用建議模型所預期的格式。 需要使用者-專案-評等三合一的訓練資料集。

+ 第一個資料行包含使用者識別碼。
+ 第二個數據行包含專案識別碼。
+ 第三個數據行包含使用者-專案組的評等。 評等值必須是數數值型別。  

Azure Machine Learning 設計工具中的 **電影分級** 資料集 (選取 **資料集** ，然後再) **範例** 示範預期的格式：

![電影分級](media/module/movie-ratings-dataset.png)

從這個範例中，您可以看到單一使用者已分級數部電影。 

### <a name="train-the-model"></a>將模型定型

1.  在設計工具中將 [定型 SVD 推薦] 模組新增至您的管線，並將其連接到定型資料。  
   
2.  針對 [ **因素數目**]，指定要與推薦搭配使用的因素數目。  
    
    每個因素會測量使用者與專案之間的關聯量。 因素的數目也是潛在因數空間的維度。 隨著使用者和專案數目的增加，最好是設定較大量的因素。 但如果數位太大，可能會降低效能。
    
3.  **建議演算法反覆運算次數** 會指出演算法應該處理輸入資料的次數。 此數位越大，預測就越準確。 不過，較高的數位表示定型速度較慢。 預設值是 30。

4.  若為 **學習速率**，請輸入介於0.0 到2.0 之間的數位，以定義學習的步驟大小。

    學習速率會決定每個反復專案的步驟大小。 如果步驟的大小太大，您可能會超過最佳的解決方案。 如果步驟的大小太小，定型需要較長的時間才能找出最佳的解決方案。 
  
5.  提交管線。  


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
