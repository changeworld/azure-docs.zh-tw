---
title: 計分 Vowpal Wabbit 模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用評分 Vowpal Wabbit 模組，以使用現有的定型 Vowpal Wabbit 模型來產生一組輸入資料的分數。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 02f4fe4f97d3e976675757835e3931666d1c6410
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857733"
---
# <a name="score-vowpal-wabbit-model"></a>計分 Vowpal Wabbit 模型
本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的**計分 Vowpal Wabbit**模組，以使用現有的定型 Vowpal Wabbit 模型來產生一組輸入資料的分數。  

本課程模組提供最新版本的 Vowpal Wabbit framework，版本8.8.1。 使用此模組，利用以 VW 8 版格式儲存的定型模型來評分資料。  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>如何設定分數 Vowpal Wabbit 模型

1.  將**計分 Vowpal Wabbit**模組新增至您的實驗。  
  
2.  新增定型的 Vowpal Wabbit 模型，並將它連接到左側輸入埠。 您可以使用在相同實驗中建立的定型模型，或在設計師左側流覽窗格的 [**資料集**] 分類中找出已儲存的模型。 不過，Azure Machine Learning 設計工具中必須有此模型。  
  
    > [!NOTE]
    > 僅支援 Vowpal Wabbit 8.8.1 模型;您無法連接使用其他演算法定型的已儲存模型。
  
3.  新增測試資料集，並將它連接到右側輸入埠。 如果測試資料集是包含測試資料檔的目錄，請使用**測試資料檔案的名稱來**指定測試資料檔案名稱。 如果測試資料集是單一檔案，請將**測試資料檔案的名稱**保留空白。

4. 在 [ **VW 引數**] 文字方塊中，為 Vowpal Wabbit 執行檔輸入一組有效的命令列引數。  

    如需 Azure Machine Learning 中支援和不支援哪些 Vowpal Wabbit 引數的詳細資訊，請參閱[技術](#technical-notes)提示一節。  

5.  **測試資料檔案的名稱**：輸入包含輸入資料的檔案名。 只有當測試資料集是目錄時，才會使用這個引數。

6. **指定檔案類型**：指出定型資料所使用的格式。 Vowpal Wabbit 支援這兩種輸入檔案格式：  

   - **VW**代表 Vowpal Wabbit 所使用的內部格式。 如需詳細資訊，請參閱[Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。 
   - **SVMLight**是一些其他機器學習工具所使用的格式。 

7. 如果您想要將標籤與分數一起輸出，請選取 [**包含包含標籤的額外資料行**] 選項。  

   一般來說，在處理文字資料時，Vowpal Wabbit 不需要標籤，而且只會傳回每個資料列的分數。  

8. 如果您想要將原始分數與結果一起輸出，請選取 [**包含包含原始分數的額外資料行**] 選項。  

9. 提交管線。

## <a name="results"></a>結果

定型完成後：

+ 若要以視覺化方式呈現結果，請以滑鼠右鍵按一下[評分 Vowpal Wabbit](score-vowpal-wabbit-model.md)模組的輸出。 輸出表示從0正規化到1的預測分數。 

+ 若要評估結果，輸出資料集應包含符合評估模型模組需求的特定分數資料行名稱。

  + 針對回歸工作，要評估的資料集必須有一個名為的資料行， `Regression Scored Labels` 代表評分標籤。
  + 針對二元分類工作，要評估的資料集必須具有兩個名為的資料行， `Binary Class Scored Labels` `Binary Class Scored Probabilities` 分別代表評分標籤和機率。
  + 針對多重分類工作，要評估的資料集必須有一個名為的資料行， `Multi Class Scored Labels` 代表評分標籤。

  請注意，計分 Vowpal Wabbit 模組的結果無法直接評估。 在評估之前，應該根據上述需求來修改資料集。

##  <a name="technical-notes"></a>技術說明

本章節包含執行詳細資料、秘訣和常見問題的解答。

### <a name="parameters"></a>參數

Vowpal Wabbit 有許多命令列選項可用於選擇和調整演算法。 這裡不能完整討論這些選項;我們建議您查看[Vowpal Wabbit wiki 頁面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。  

Azure Machine Learning Studio （傳統）中不支援下列參數。  

-   中指定的輸入/輸出選項[https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     模組已自動設定這些屬性。  
  
-   此外，不允許任何會產生多個輸出或接受多個輸入的選項。 其中包括 *`--cbt`* 、 *`--lda`* 和 *`--wap`* 。  
  
-   僅支援受監督的學習演算法。 這不允許下列選項： *`–active`* 、 `--rank` 等 *`--search`* 。  

除了上述所述以外的所有引數都是允許的。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 