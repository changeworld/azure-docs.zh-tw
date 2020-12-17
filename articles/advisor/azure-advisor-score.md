---
title: 使用 Advisor 分數將 Azure 工作負載優化
description: 使用 Azure Advisor 分數以充分運用 Azure。
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630117"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>使用 Advisor 分數將 Azure 工作負載優化

## <a name="introduction-to-advisor-score"></a>Advisor 分數簡介

Azure Advisor 為您的工作負載提供最佳做法建議。 這些建議是個人化且可採取動作的，可協助您：

* 改善工作負載的狀態，並將您的 Azure 部署優化。
* 遵循最佳作法主動預防最重要的問題。
* 針對 [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)的五個要素來評定您的 Azure 工作負載。

Advisor 分數是 Advisor 的核心功能，可協助您有效且有效率地達成這些目標。

若要充分利用 Azure，請務必瞭解您在工作負載優化旅程的哪個階段。 您必須知道哪些服務或資源的使用方式很好，哪些不是。 此外，您也會想要瞭解如何根據建議來設定動作的優先順序，以最大化結果。

追蹤和報告您在此優化旅程中所做的進展也很重要。 有了 Advisor 分數，您就可以使用新的遊戲化體驗輕鬆完成這些工作。

作為您的個人化雲端顧問，Azure Advisor 持續評估您的使用量遙測和資源設定，以檢查產業的最佳作法。 Advisor 接著會將其結果匯總成單一分數。 有了這個分數，如果您採取的步驟是建立可靠、安全且符合成本效益的解決方案，您就可以一目了然。

Advisor 分數是由整體分陣列成，可進一步細分為五個類別分數。 每個 Advisor 類別的一個分數都代表 Well-Architected Framework 的五個支柱。

您可以透過每日、每週和每月的趨勢來查看您的整體分數和分類分數，以追蹤您在一段時間內所做的進度。 您也可以設定基準測試來協助您達成目標。

 ![顯示 Advisor 分數頁面的螢幕擷取畫面。](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>解讀 Advisor 分數

Advisor 會顯示您的整體 Advisor 分數和 Advisor 類別的細目（以百分比表示）。 在任何類別中，分數為100%，表示 Advisor 評估的所有資源都遵循 Advisor 建議的最佳做法。 在色譜的另一端，分數為0% 表示任何由 Advisor 評定的資源都不遵循 Advisor 的建議。 使用這些分數粒紋，您可以輕鬆地達成下列流程：

* **Advisor 分數** 可協助您根據 Advisor 分數來基準工作負載或訂用帳戶的執行情況。 您也可以查看歷程記錄趨勢，以瞭解您的趨勢。
* 每個建議的 **分數依類別**，告訴您哪些未處理的建議將會改善您的分數。 這些值會反映建議的權數和預測的簡化。 這些因素有助於確保您能在時間發揮最大的價值。 它們也可協助您進行優先順序。
* 每個建議的 **類別分數影響** 可協助您設定每個類別的補救動作優先順序。

每個建議對您分類分數的貢獻會清楚地顯示在 Azure 入口網站的 [ **Advisor 分數** ] 頁面上。 您可以依 [ **潛在分數增加** ] 資料行中所列的百分比點來增加每個類別分數。 此值會反映類別內建議的權數，以及預測的簡化作業，以解決可能最簡單的工作。 著重于具有最大分數影響的建議可協助您使用時間進行最多的進度。

![顯示 Advisor 分數影響的螢幕擷取畫面。](./media/advisor-score-2.png)

如果任何 Advisor 建議與個別資源無關，您可以延期或關閉這些建議。 下次重新整理時，將會從分數計算中排除它們。 Advisor 也會使用此輸入做為改善模型的其他意見反應。

## <a name="how-is-an-advisor-score-calculated"></a>Advisor 分數的計算方式為何？

Advisor 會以百分比顯示您的類別分數和整體建議程式分數。 在任何類別中，分數為100% 表示您所有的資源（ *由 advisor 評估*）都會遵循 advisor 建議的最佳做法。 在色譜的另一端，0% 的分數表示任何您的資源（由 Advisor 評估）都會遵循 Advisor 建議。

**這五個類別的每一個都有最高的可能分數100。** 您的整體 Advisor 分數會計算為每個適用類別分數的總和，除以所有適用類別中最高潛在分數的總和。 針對大部分的訂用帳戶，這表示 Advisor 會將每個類別的分數加上除以500。 但是 *，只有當您使用由 Advisor 評估的資源時，才會計算每個類別分數*。

### <a name="advisor-score-calculation-example"></a>Advisor 分數計算範例

* **單一訂用帳戶分數：** 此範例是訂用帳戶的所有 Advisor 類別分數的簡單平均值。 如果 Advisor 類別分數為- **Cost** = 73、 **可靠性** = 85、 **卓越的營運** = 77 和 **效能** = 100，建議程式分數會是 (73 + 85 + 77 + 100) / (4x100) = 0.84% 或84%。
* **多個** 訂用帳戶分數：選取多個訂用帳戶時，所產生的整體 Advisor 分數為加權匯總類別分數。 在這裡，每個 Advisor 類別分數都會依據訂用帳戶所取用的資源進行匯總。 當 Advisor 有加權匯總分類分數時，Advisor 會進行簡單的平均計算，以提供您訂用帳戶的整體分數。

### <a name="scoring-methodology"></a>計分方法

您可以利用四個步驟來匯總 Advisor 分數的計算：

1. Advisor 會計算 *受影響資源的零售成本*。 這些資源是您訂用帳戶中至少有一個建議程式的資源。
1. Advisor 會計算 *評估資源的零售成本*。 這些資源是由 Advisor 監視的資源，不論是否有任何建議。
1. 針對每個建議類型，Advisor 會計算 *狀況良好的資源比例*。 這項比例是受影響資源的零售成本除以評定資源的零售成本。
1. Advisor 會在每個類別中對狀況良好的資源比例套用三個額外的權數：

   * 具有較大影響的建議會加權比影響較低的建議。
   * 具有長期建議的資源將會依據您的分數計算更多費用。
   * 您在 Advisor 中延期或關閉的資源會完全從分數計算中移除。

Advisor 會在 Advisor 類別層級套用此模型，以提供每個類別的 Advisor 分數。 **安全性** 使用 [安全的分數](../security-center/secure-score-security-controls.md#introduction-to-secure-score) 模型。 簡單的平均會產生最終的 Advisor 分數。

## <a name="advisor-score-faqs"></a>Advisor 分數常見問題

### <a name="how-often-is-my-score-refreshed"></a>重新整理分數的頻率為何？

您的分數每天至少會重新整理一次。

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>為什麼某些建議在類別分數影響資料行中有空白的 "-" 值？

Advisor 不會立即在評分模型中包含新的建議或建議，以及最新的變更。 在簡短的評估期之後（通常是幾周），分數就會包含在內。

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>為什麼成本分數對某些建議的影響較高，即使它們的可能節省成本較低？

您的成本分數反映了您可能從使用量過低的資源節省的 **成本** ，以及預測這些建議的簡化工作。 例如，額外的權數會套用至已閒置較長時間的受影響資源，即使可能的節省成本也較低。

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>為什麼我沒有一或多個類別或訂用帳戶的分數？

Advisor 只會針對擁有 Advisor 評估資源的類別和訂閱產生分數。

### <a name="what-if-a-recommendation-isnt-relevant"></a>如果建議不相關，該怎麼辦？

如果您關閉 Advisor 的建議，則會在您的分數計算中省略。 解除建議也有助於 Advisor 改善建議的品質。

### <a name="why-did-my-score-change"></a>為什麼分數變更？

如果您採用 Advisor 建議的最佳作法來修復受影響的資源，您的分數可能會變更。 如果您或您的訂用帳戶上具有許可權的任何人已修改或建立新資源，您可能也會看到分數波動。 分數是根據成本影響資源的比率，相對於所有資源的總成本。

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Advisor 如何計算訂用帳戶上資源的零售成本？

Advisor 會使用隨用隨付費率發佈的 [Azure 定價](https://azure.microsoft.com/pricing/)。 這些費率不會反映任何適用的折扣。 然後，費率會乘以資源最後一天的使用量數量。 省略資源成本計算的折扣可讓 Advisor 分數可與折扣可能不同的訂用帳戶、租使用者及註冊進行比較。

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>我是否需要查看建議程式中的建議，以取得分數的點數？

不會。 您的分數會反映您是否採用 Advisor 建議的最佳作法，即使您主動採用這些最佳作法，也永遠不會在 Advisor 中看到建議。

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>評分方法是否會區分生產和開發測試工作負載？

否，目前不適用。 但是，如果這些資源用於開發和測試，您可以解除個別資源的建議，而不適用這些建議。

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>我可以比較訂用帳戶與100資源之間的分數，以及具有100000資源的訂用帳戶嗎？

評分方法是設計來控制訂用帳戶和服務混合上的資源數目。 具有較少資源的訂用帳戶，其分數可能會比訂用帳戶擁有更多資源。

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>當我在分數影響資料行中看到「即將推出」時，這是什麼意思？

這則訊息表示建議是新的，而且我們正努力將它帶入 Advisor 分數模型。 在分數計算中考慮這個新的建議之後，您會看到建議的分數影響值。

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>我的分數是否取決於我在 Azure 上花費多少時間？

不會。 您的分數不一定是您花費多少時間的反映。 不必要的費用會導致 **成本** 較低的分數。

## <a name="access-advisor-score"></a>存取 Advisor 分數

Advisor 分數在 Azure 入口網站中處於公開預覽狀態。 在左窗格的 [ **advisor** ] 區段下，參閱 **advisor 分數**。

![顯示 Advisor 分數進入點的螢幕擷取畫面。](./media/advisor-score-3.png)

## <a name="next-steps"></a>下一步

如需 Advisor 建議的詳細資訊，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
