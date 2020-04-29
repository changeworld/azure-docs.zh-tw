---
title: Team Data Science Process 生命週期的模型化階段
description: 資料科學專案模型化階段的目標、工作和交付項目
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720464"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Process 生命週期的模型化階段

本文將概要說明與 Team Data Science Process (TDSP) 模型化階段相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

   1. **了解商務**
   2. **資料取得與認知**
   3. **模型化**
   4. **部署**
   5. **客戶接受度**

以下是 TDSP 生命週期的視覺表示法：

![TDSP 生命週期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* 判斷最適合機器學習模型的資料特徵。
* 建立最能精確預測目標的參考機器學習模型。
* 建立適用於生產環境的機器學習模型。

## <a name="how-to-do-it"></a>作法
此階段會解決三項主要工作︰

  * **特徵工程設計**：從原始資料建立資料特徵，以加速模型定型。
  * **模型定型**：藉由比較其成功的計量，找出最能精確回答問題的模型。
  * 判斷您的模型是否**適用于生產環境。**

### <a name="feature-engineering"></a>特徵設計
特徵設計包括納入、彙總和轉換未經處理的變數，以建立用於分析的特徵。 如果您想要深入了解模型驅動因子，就必須了解特徵之間的關聯方式，以及機器學習演算法要如何使用這些特徵。 

要執行此步驟，必須有創意地結合網域知識和獲取自資料瀏覽步驟的深入資訊。 特徵設計是一個尋找和納入參考變數的平衡動作，同時嘗試避免有太多不相關的變數。 參考變數可改善您的結果；不相關的變數會對模型產生不必要的雜訊。 您也必須為評分期間所取得的任何新資料產生這些功能。 因此，這些特徵的產生只會取決於評分時所能取得的資料。 

如需使用不同 Azure 資料技術時之特徵設計的技術指引，請參閱[資料科學程序中的特徵設計](create-features.md)。 

### <a name="model-training"></a>模型訓練
根據您嘗試回答的問題類型，會有許多可用的模型化演算法。 如需如何選擇演算法的指引，請參閱[如何選擇 Microsoft Azure 機器學習的演算法](../studio/algorithm-choice.md)。 雖然本文使用 Azure Machine Learning，但它提供的指引可用於任何機器學習專案。 

模型訓練程序包括下列步驟︰ 

   * 將用於模型化的**輸入資料隨機分割**成訓練資料集和測試資料集。
   * 使用訓練資料集來**建置模型**。
   * **評估**訓練和測試資料集。 使用一系列競爭的機器學習演算法，以及專為使用目前資料回答感興趣問題的各種相關微調參數 (稱為*參數掃掠*)。
   * 比較替代方法之間的成功標準，以**決定用來回答問題的「最佳」方案**。

> [!NOTE]
> **避免外洩**︰如果您納入訓練資料集以外的資料，可能會造成資料外洩，而讓模型或機器學習演算法做出誤以為良好的預測。 當資料科學家取得好得過頭的預測結果時，會讓他們感到不安的常見原因就是資料外洩。 這些相依性很難察覺。 為了避免外洩，通常會需要反覆建置分析資料集、建立模型，然後評估結果的精確度。 
> 
> 

我們透過 TDSP 提供[自動化模型和報告工具](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) \(英文\)，其可透過多個演算法及參數掃掠來執行，以產生基準模型。 它也會產生基準模型報告，摘要說明每個模型和參數組合的效能 (包括變數重要性)。 此程序也會反覆進行，因為這可促成進一步的功能設計。 

## <a name="artifacts"></a>構件
此階段所產生的成品包括︰

   * [功能集](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md)：針對模型化所開發的功能，會在**資料定義**報告的 [**功能集**] 區段中說明。 它包含產生特徵的程式碼指標，以及特徵產生方式的描述。
   * [模型報告](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md) \(英文\)：每個嘗試過的模型，都會產生一個標準的範本型報告，提供每項實驗的詳細資料。
   * **檢查點決策**：評估模型是否足以執行生產環境。 需要詢問的一些重要問題是︰
     * 在給訂測試資料的情況下，模型是否有足夠的信心來回答問題？ 
     * 您應該嘗試任何替代方法嗎？ 您應該收集其他資料、進行更多特徵設計，或實驗其他演算法嗎？

## <a name="next-steps"></a>後續步驟

以下是 TDSP 生命週期中每個步驟的連結：

   1. [了解商務](lifecycle-business-understanding.md)
   2. [資料取得與認知](lifecycle-data.md)
   3. [模型化](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客戶接受度](lifecycle-acceptance.md)

我們也會提供完整的端對端逐步解說，說明特定案例之程序中的所有步驟。 [範例逐步解說](walkthroughs.md)一文提供有連結和縮圖描述的案例清單。 這些逐步解說示範如何將雲端、內部部署工具與服務組合成工作流程或管線，以建立智慧型應用程式。 

如需如何在使用 Azure Machine Learning Studio 的 TDSP 中執行步驟的範例，請參閱[配合使用 Team Data Science Process 與 Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)。 
