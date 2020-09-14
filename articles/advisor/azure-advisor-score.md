---
title: 使用 Advisor 分數將 Azure 工作負載優化
description: 使用 Advisor 分數充分運用 Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056229"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>使用 Advisor 分數將 Azure 工作負載優化

## <a name="introduction-to-advisor-score"></a>Advisor 分數簡介

Azure Advisor 為您的工作負載提供最佳做法建議。 這些建議是個人化且可採取動作的，可協助您：
* 改善工作負載的狀態，並將您的 Azure 部署優化
* 遵循最佳作法主動預防最重要的問題
* 針對[Microsoft Azure 架構良好架構](https://docs.microsoft.com/azure/architecture/framework/)的五個要素來評定您的 Azure 工作負載

建議程式 **分數** 是 advisor 的核心功能，可協助您有效且有效率地達成這些目標。 

若要充分利用 Azure，請務必瞭解您在工作負載優化旅程的哪個階段，哪些服務/資源的使用方式和不是。 此外，您也會想要瞭解如何根據建議來設定動作的優先順序，以最大化結果。 追蹤和報告您在此優化旅程中所做的進展也很重要。 有了 **Advisor 分數**，您就可以使用新的遊戲化體驗輕鬆完成這些工作。 作為您的個人化雲端顧問，Azure Advisor 持續評估您的使用量遙測和資源設定，以檢查產業的最佳作法。 Advisor 接著會將其結果匯總成單一分數，讓您在採取必要的步驟來建立可靠、安全且符合成本效益的解決方案時，一眼就能看出。 Advisor 分數是由整體分數所組成，可進一步細分為五個分類分數，每個類別的 Azure Advisor，代表架構良好架構的五個支柱。 您可以透過每日、每週和每月的趨勢來查看您的整體分數和類別分數，並設定基準測試以協助您達成目標，藉此追蹤您所進行的進度。 

 ![Advisor 分數體驗](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>如何使用 Advisor 分數
Advisor 會以百分比顯示 Advisor 類別的整體 Advisor 分數和細目。 在任何類別中，分數為100%，表示 Advisor 評估的所有資源都遵循 Advisor 建議的最佳做法。 在色譜的另一端，分數為0% 表示任何由 Advisor 評定的資源都不遵循 Advisor 的建議。 使用這些分數粒紋，您可以輕鬆達成以下 flow：
* **Advisor 分數** 可協助您根據 Advisor 分數來基準工作負載/訂用帳戶的進行。 您也可以查看歷程記錄趨勢，以瞭解您的趨勢。
* **Advisor 類別分數** 可協助您瞭解哪些類別需要更多注意，並可協助您進行優先順序
* 每個建議的**可能分數增加**，以協助您設定每個類別的補救動作優先順序

您分數的每個建議的比重會清楚地顯示在 Azure 入口網站的 [總覽] 頁面上。 您可以藉由採用最佳作法來增加分數，也可以優先採用最大 **可能分數提高** 的建議，讓您的時間達到最快速的進度。  

![Advisor 分數影響](./media/advisor-score-2.png)

由於 Advisor 的評分方法會將額外的權數套用至具有長期建議的昂貴資源，因此您可以先修復具有最高零售成本的資源，以進行最多的進度。 如果任何建議程式建議與個別資源無關，您可以關閉這些建議以將其從分數計算中排除，並將意見反應傳送給 Advisor，以改善其建議。 

## <a name="how-is-advisor-score-calculated"></a>Advisor 分數的計算方式為何？
Advisor 會以百分比顯示您的類別分數和整體建議程式分數。 在任何類別中，分數為100% 表示您所有的資源（ *由 advisor 評估*）都會遵循 advisor 建議的最佳做法。 在色譜的另一端，0% 的分數表示沒有任何資源（由 Advisor 評估）遵循 Advisor 的建議。 
**這五個類別的每一個都有最高的可能分數100。** 您的整體 Advisor 分數會計算為每個適用類別分數的總和，除以所有適用類別中最高潛在分數的總和。 針對大部分的訂用帳戶，這表示 Advisor 會將每個類別的分數加上除以500。 不過， **只有當您使用由 Advisor 評估的資源時，才會計算每個類別分數。**

### <a name="scoring-methodology"></a>評分方法： 
您可以利用四個步驟來匯總 Advisor 分數的計算：
1. Advisor 會計算 **受影響資源的每日零售成本**，也就是您訂用帳戶上至少有一個建議程式的資源。
2. Advisor 會計算 **評估資源的每日零售成本**，也就是 Advisor 所監視的資源，不論是否有任何建議。 
3. 針對每個建議類型，Advisor 會計算 **狀況良好的資源比率**，也就是受影響資源的成本除以評定資源的成本。
4. Advisor 會在每個類別中對狀況良好的資源比例套用三個額外的權數：
* 具有較大影響的建議會加權比影響較低的建議。
* 具有長期建議的資源將會依據您的分數計算更多費用。
* 您在 Advisor 中關閉的資源會完全從分數計算中移除。 
    
Advisor 會在 Advisor 類別層級套用此模型， (安全性使用 [安全分數](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) 模型) ，為每個類別提供美國建議程式分數，並讓簡單的平均值產生最終的 Advisor 分數。


## <a name="advisor-score-faq"></a>Advisor 分數常見問題
* **重新整理分數的頻率為何？**
您的分數每天至少會重新整理一次。 
* **我是否需要查看建議程式中的建議，以取得分數的點數？**
不會。 您的分數會反映您是否採用 Advisor 建議的最佳作法，即使您從未在 Advisor 中查看這些建議並主動採用最佳作法也是如此。  
* **Advisor 如何計算訂用帳戶上每日資源的零售成本？**
Advisor 會使用您在 Azure.com 定價頁面上發佈的 *隨用隨付* 費率，而不會反映出任何適用的折扣，並乘以最後一天分配資源的使用量數量。 省略資源成本計算的折扣可讓 Advisor 分數可在折扣可能不同的訂用帳戶、租使用者及註冊之間進行比較。 
* **如果建議不相關，該怎麼辦？**
如果您關閉 Advisor 的建議，則會在您的分數計算中省略。 解除建議也有助於 Advisor 改善建議的品質。
* **為什麼分數變更？** 如果您採用 Advisor 建議的最佳作法來修復受影響的資源，您的分數可能會變更。 如果您或訂用帳戶上具有許可權的任何人已修改或建立新的資源，您可能也會看到分數波動，因為您的分數是根據成本受影響資源的比率（相對於所有資源的總成本）。
* **我的分數是否取決於我在 Azure 上花費多少時間？**
不會。 分數是設計來控制訂用帳戶和服務混合的大小。 
* **評分方法是否會區分生產和開發測試工作負載？**
否，目前不適用，但如果這些資源用於開發和測試，您就可以解除個別資源的建議，而不適用建議。
* **我可以比較訂用帳戶與100資源之間的分數，以及具有100000資源的訂用帳戶嗎？**
評分方法是設計用來控制訂用帳戶和服務混合上的資源數目，因此具有較少資源的訂用帳戶可擁有比具有更多資源的訂用帳戶更高或更低的分數。 

## <a name="how-to-access-advisor"></a>如何存取 Advisor
Advisor 分數在 Azure 入口網站中處於公開預覽狀態。 您必須移至 [Advisor] 區段，而且您會在左側導覽中找到 [Advisor 分數] 作為第二個功能表項目。 

![Advisor 分數進入點](./media/advisor-score-3.png)

## <a name="next-steps"></a>接下來的步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
