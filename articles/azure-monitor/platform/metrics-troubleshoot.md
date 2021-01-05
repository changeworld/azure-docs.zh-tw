---
title: 針對 Azure 監視器度量圖表進行疑難排解
description: 針對建立、自訂或解讀度量圖表的問題進行疑難排解
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 7c3af0865282475ded0172d18aecad1dfb61721b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814256"
---
# <a name="troubleshooting-metrics-charts"></a>對計量圖表進行疑難排解

如果您遇到在 Azure 計量瀏覽器中建立、自訂或解讀圖表的問題，請使用本文。 如果您不熟悉計量，請瞭解 [開始使用計量流覽](metrics-getting-started.md) 器和 [計量瀏覽器的先進功能](metrics-charts.md)。 您也可以查看已設定的度量圖表 [範例](metric-chart-samples.md) 。

## <a name="chart-shows-no-data"></a>圖表未顯示任何資料

在選取正確的資源和計量之後，圖表有時可能不會顯示任何資料。 此行為可能是因為下列幾個原因所造成：

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>尚未在您的訂用帳戶中註冊 Microsoft.Insights 資源提供者

必須在您的訂用帳戶中註冊 *Microsoft.Insights* 資源提供者，才能瀏覽計量。 在許多情況下都會自動註冊 (也就是說，在您設定警示規則、自訂任何資源的，或設定自動調整規則後，就會註冊)。 如果未註冊 Microsoft Insights 資源提供者，您必須遵循 [Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述的步驟來手動註冊。

**解決方案：** 開啟 [訂用帳戶]、[**資源提供者**] 索引卷 **標，然後** 確認已針對您的訂用帳戶註冊 *Microsoft。*

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>您對資源沒有足夠的存取權

在 Azure 中，計量的存取權是透過 azure [角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)來控制。 您必須是[監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader)、[監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)或[參與者](../../role-based-access-control/built-in-roles.md#contributor)的成員，才能瀏覽任何資源的計量。

**解決方案：** 確定您有足夠的許可權可供您探索度量的資源使用。

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>您的資源在選取的時間範圍內未發出計量

某些資源並不會持續發出其計量。 例如，Azure 不會對已停止的虛擬機器收集計量。 其他資源可能只會在某些條件發生時發出其計量。 例如，顯示交易處理時間的計量需要至少一個交易。 如果在選取的時間範圍內沒有任何交易，圖表自然會呈現為空白。 此外，雖然 Azure 中大部分的計量會每分鐘收集一次，但某些計量的收集頻率較低。 請參閱計量的說明文件，以取得更多與您嘗試瀏覽的計量有關的詳細資料。

**解決方案：** 將圖表的時間變更為更大的範圍。 您可以從「過去30天」開始，使用較大的時間細微性 (或依賴「自動時間細微性」選項) 。

### <a name="you-picked-a-time-range-greater-than-30-days"></a>您選擇了超過 30 天的時間範圍

[Azure 中大部分的計量會儲存 93 天](data-platform-metrics.md#retention-of-metrics)。 不過，您對任何單一圖表都只能查詢不超過 30 天的資料。 這項限制不適用於[記錄型計量](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)。

**解決方案：** 如果您看到空白的圖表，或您的圖表只顯示度量資料的一部分，請確認時間選擇器中的開始和結束日期之間的差異未超過30天的間隔。

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>所有計量值都在鎖定的 y 軸範圍外

在[鎖定圖表 y 軸的界限](metrics-charts.md#lock-boundaries-of-chart-y-axis)後，您可能會在無意中讓圖表顯示區域未顯示圖表線條。 例如，如果 y 軸鎖定於 0% 到 50% 的範圍間，而計量的常數值為 100%，則該行一律會顯示在可見的區域以外，使得圖表呈現為空白。

**解決方案：** 確認圖表的 y 軸界限不會在度量值範圍之外鎖定。 如果 y 軸界限已鎖定，您可以暫時重設其界限，以確保計量值不會落在圖表範圍以外。 對於具有 **總和**、**最小值** 和 **最大值** 彙總的圖表，建議不要使用自動細微性鎖定 y 軸範圍，因為其值將會在調整瀏覽器視窗大小或切換為不同的螢幕解析度後，隨著細微性而變更。 切換細微性可能會使圖表的顯示區域呈現為空白。

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>您正在查看來賓 OS 計量，但未啟用 Azure 診斷擴充功能

要收集 **客體 OS** 計量，必須設定 Azure 診斷擴充功能，或使用資源的 [診斷設定] 面板加以啟用。

**解決方案：** 如果 Azure 診斷延伸模組已啟用，但您仍然看不到您的計量，請遵循 [Azure 診斷擴充功能疑難排解指南》](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)中所述的步驟。 另請參閱[無法挑選來賓 OS 命名空間和計量](#cannot-pick-guest-os-namespace-and-metrics)的疑難排解步驟

## <a name="error-retrieving-data-message-on-dashboard"></a>儀表板上的「抓取資料時發生錯誤」訊息

如果用來建立儀表板的計量後續已被取代並從 Azure 中移除，就可能會發生此問題。 若要確認是否為這種情況，請開啟資源的 [ **計量** ] 索引標籤，並檢查計量選擇器中的可用計量。 如果計量未顯示，表示該計量已從 Azure 中移除。 某個計量被取代時，通常會有更好的新計量可針對資源健康情況提供類似的檢視性。

**解決方案：** 在儀表板上挑選圖表的替代度量，以更新失敗的磚。 您可以[檢閱 Azure 服務的可用計量清單](metrics-supported.md)。

## <a name="chart-shows-dashed-line"></a>圖表顯示虛線

Azure 計量圖表使用虛線樣式來表示有遺漏值 (也稱為「null 值」，) 介於兩個已知的時間細微性資料點之間。 例如，如果在時間選取器中，您選取了「1分鐘」的時間細微性，但計量是在07:26、07:27、07:29 和07:30 回報， (注意第二個和第三個資料點之間的分鐘間距) ，則虛線將會連接到07:27 和07:29，而實線將連接所有其他資料點。 當度量使用 **count** 和 **sum** 匯總時，虛線會向下舍入為零。 針對 **平均**、 **最小** 或 **最大** 匯總，虛線會連接兩個最近的已知資料點。 此外，當圖表遺漏最右側或最左側的資料時，虛線會往遺漏資料點的方向延伸。
  ![顯示圖表最右邊或最左邊的資料遺失時間的螢幕擷取畫面，此虛線會擴充至遺失資料點的方向。](./media/metrics-troubleshoot/dashed-line.png)

**解決方案：** 這是設計的行為。 這有助於識別遺漏的資料點。 折線圖是將高密度計量的趨勢視覺化的絕佳選擇，但可能難以解讀具有稀疏值的度量，特別是當相互關聯具有時間細微性的值很重要時。 虛線可讓這些圖表更容易讀取，但如果您的圖表仍然不清楚，請考慮使用不同類型的圖表來檢視您的計量。 例如，針對相同計量的散佈圖，清楚地顯示每個時間細微性，只會在有值時將點視覺化，並在遺漏值時，完全略過資料點：反白顯示 ![ [散佈圖] 功能表選項的螢幕擷取畫面。](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > 如果您仍傾向使用折線圖來呈現計量，將滑鼠移至圖表上方，在滑鼠指標所在位置反白顯示資料點，將有助於評估時間細微性。

## <a name="chart-shows-unexpected-drop-in-values"></a>圖表中的值顯示非預期的下滑

在許多情況下，您察覺到的計量值下滑來自於對圖表顯示資料的誤解。 當圖表顯示最近數分鐘的資料時，您可能會被總和或計數的下滑誤導，因為 Azure 尚未收到或處理最後的計量資料點。 視服務之不同，處理計量的延遲可能會在幾分鐘的範圍內。 針對顯示最近時間範圍具有1或5分鐘資料細微性的圖表，在過去幾分鐘內放置值會變得更明顯： ![ 顯示過去幾分鐘值的螢幕擷取畫面。](./media/metrics-troubleshoot/unexpected-dip.png)

**解決方案：** 這是設計的行為。 我們認為在收到資料時隨即加以顯示，即使在資料是 *部分* 或 *不完整* 時，都有其好處。 這麼做可讓您更快歸納出重要結論，並立即開始調查。 以顯示失敗次數的計量為例，在看到部分值 X 時，您即得知指定的分鐘數內至少有 X 次的失敗。 您可以立即開始調查問題，而不必等著查看當下這一分鐘發生的確切失敗計數，因為這可能不是那麼重要。 圖表會在我們收到整組資料後隨即更新，但屆時也可能會顯示新的不完整資料點；來自於更接近當下的分鐘數。

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>無法挑選來賓 OS 命名空間和計量

虛擬機器和虛擬機器擴展集有兩種計量：由 Azure 裝載環境收集的 **虛擬機器主機** 計量，和執行於虛擬機器上的 [監視代理程式](agents-overview.md)所收集的 **客體 OS (傳統)** 計量。 您可以藉由啟用 [Azure 診斷擴充功能](diagnostics-extension-overview.md)來安裝監視代理程式。

根據預設，客體 OS 計量會儲存在 Azure 儲存體帳戶中，這是您從資源的 [診斷設定] 索引標籤中選擇的帳戶。 如果未收集客體 OS 計量，或計量瀏覽器無法加以存取，您就只會看到 **虛擬機器主機** 計量命名空間：

![計量影像](./media/metrics-troubleshoot/vm.png)

**解決方案：** 如果您在計量瀏覽器中看不到虛擬) 命名空間和計量 **(的來賓 OS** ：

1. 請確認 [Azure 診斷擴充功能](diagnostics-extension-overview.md)已啟用，並且設定為會收集計量。
    > [!WARNING]
    > 您無法使用 [Log Analytics 代理程式](agents-overview.md#log-analytics-agent) (也稱為 Microsoft Monitoring Agent，或簡稱 "MMA") 將 **客體 OS** 傳送至儲存體帳戶中。

1. 確定已 [為您的訂](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)用帳戶註冊 **Microsoft Insights** 資源提供者。

1. 確認儲存體帳戶未受防火牆保護。 Azure 入口網站需要存取儲存體帳戶，才能取出計量資料並繪製圖表。

1. 使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 來驗證計量會流入儲存體帳戶。 如果未收集計量，請遵循 [Azure 診斷擴充功能疑難排解指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)。

## <a name="next-steps"></a>後續步驟

* [瞭解如何開始使用計量瀏覽器](metrics-getting-started.md)
* [瞭解計量瀏覽器的 advanced 功能](metrics-charts.md)
* [查看 Azure 服務的可用計量清單](metrics-supported.md)
* [查看已設定的圖表範例](metric-chart-samples.md)
