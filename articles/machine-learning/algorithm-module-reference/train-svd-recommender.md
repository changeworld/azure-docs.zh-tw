---
title: 定型 SVD 推薦：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [定型 SVD 推薦] 模組，利用 SVD 演算法來定型貝氏推薦。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477403"
---
# <a name="train-svd-recommender"></a>訓練 SVD 推薦

本文說明如何在 Azure Machine Learning 設計工具（預覽）中使用 [定型 SVD 推薦] 模組。 使用此模組來根據單一值分解（SVD）演算法來定型建議模型。  

[定型 SVD 推薦] 模組會讀取使用者-專案-評等三合一的資料集。 它會傳回定型的 SVD 推薦。 接著，您可以使用[計分 SVD 推薦](score-svd-recommender.md)模組，透過定型的模型來預測評等或產生建議。  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>深入瞭解建議模型和 SVD 推薦  

建議系統的主要目的是建議系統*使用者*的一個或多個*專案*。 專案的範例可能是電影、餐廳、書籍或歌曲。 使用者可能是個人、人員群組或具有專案喜好設定的其他實體。  

推薦系統的主要方法有兩種： 

+ 以**內容為基礎**的方法會利用使用者和專案的功能。 使用者可以透過「年齡」和「性別」等屬性來描述。 專案可以由作者和製造商等屬性來描述。 您可以在社交配對網站上找到以內容為基礎的建議系統的典型範例。 
+ 共同作業**篩選**只會使用使用者和專案的識別碼。 它會從使用者提供給專案的評等（稀疏）矩陣，取得有關這些實體的隱含資訊。 我們可以從已分級的專案和其他已分級相同專案的使用者，瞭解使用者。  

SVD 推薦會使用使用者的識別碼和專案，以及使用者提供給專案的評等矩陣。 這是共同作業的*推薦*。 

如需有關 SVD 推薦的詳細資訊，請參閱相關的研究論文：[推薦系統的矩陣分解技術](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)。


## <a name="how-to-configure-train-svd-recommender"></a>如何設定定型 SVD 推薦  

### <a name="prepare-data"></a>準備資料

使用模組之前，您的輸入資料必須是建議模型所預期的格式。 需要使用者-專案-評等三合一的訓練資料集。

+ 第一個資料行包含使用者識別碼。
+ 第二個數據行包含專案識別碼。
+ 第三個數據行包含使用者-專案組的評等。 評等值必須是數數值型別。  

Azure Machine Learning 設計工具中的**電影分級**資料集（[選取**資料集**] 和 [**範例**]）會示範預期的格式：

![電影分級](media/module/movie-ratings-dataset.png)

從這個範例中，您可以看到單一使用者已分級數個電影。 

### <a name="train-the-model"></a>將模型定型

1.  將訓練 SVD 推薦模組新增至您在設計工具中的管線，並將其連接至定型資料。  
   
2.  針對 [**因素數目**]，指定要與推薦搭配使用的因素數目。  
    
    每個因素都會測量使用者與專案相關的數量。 因數數目也是潛在因數空間的維度。 隨著使用者和專案數目的增加，最好能設定更多的因素。 但是，如果數位太大，效能可能會下降。
    
3.  **建議的演算法**反復專案數目會指出演算法應該處理輸入資料的次數。 這個數位愈高，預測就愈精確。 不過，較高的數位表示訓練速度較慢。 預設值是 30。

4.  針對 [**學習速率**]，輸入介於0.0 和2.0 之間的數位，以定義學習的步驟大小。

    學習速率會決定每個反復專案的步驟大小。 如果步驟大小太大，您可能會下限最佳的解決方案。 如果步驟大小太小，訓練會花更長的時間來尋找最佳解決方案。 
  
5.  提交管線。  


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 
