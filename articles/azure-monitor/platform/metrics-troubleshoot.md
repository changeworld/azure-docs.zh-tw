---
title: 排除 Azure 監視器指標圖表的故障排除
description: 解決創建、自訂或解釋指標圖表時的問題
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659657"
---
# <a name="troubleshooting-metrics-charts"></a>對計量圖表進行疑難排解

如果在 Azure 指標資源管理器中遇到創建、自訂或解釋圖表的問題，請使用本文。 如果您是指標的新增功能，則瞭解如何[開始使用指標資源管理器](metrics-getting-started.md)和[指標資源管理器的高級功能](metrics-charts.md)。 您還可以查看配置的指標圖表[的示例](metric-chart-samples.md)。

## <a name="cant-find-your-resource-to-select-it"></a>找不到要選擇的資源

您按一下 [選取資源]**** 按鈕後，並未在資源選擇器對話方塊中看到您的資源。

**解決方案：** 指標資源管理器要求您在列出可用資源之前選擇訂閱和資源組。 若您未看到您的資源：

1. 請確定您已在 [訂用帳戶]**** 下拉式清單中選取正確的訂用帳戶。 如果您的訂用帳戶未列出，請按一下 [目錄 + 訂用帳戶設定]****，並新增含有資源的訂用帳戶。

1. 請確定您已選取正確的資源群組。
    > [!WARNING]
    > 為了達到最佳效能，當您第一次開啟計量瀏覽器時，[資源群組]**** 下拉式清單中不會有任何預先選取的資源群組。 您必須選擇至少一個群組，才可看到資源。

## <a name="chart-shows-no-data"></a>圖表顯示無資料

有時，圖表在選擇正確的資源和指標後可能不會顯示任何資料。 此行為可能由以下幾個原因引起：

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>尚未在您的訂用帳戶中註冊 Microsoft.Insights 資源提供者

必須在您的訂用帳戶中註冊 *Microsoft.Insights* 資源提供者，才能瀏覽計量。 在許多情況下都會自動註冊 (也就是說，在您設定警示規則、自訂任何資源的，或設定自動調整規則後，就會註冊)。 如果未註冊 Microsoft.Insights 資來源提供者，則必須按照[Azure 資來源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)中描述的步驟手動註冊它。

**解決方案：** 打開**訂閱**，**資來源提供者**選項卡，並驗證*Microsoft.Insights*是否已為您的訂閱註冊。

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>您對資源沒有足夠的存取權

在 Azure 中，計量的存取權由[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md) 所控制。 您必須是[監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader)、[監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)或[參與者](../../role-based-access-control/built-in-roles.md#contributor)的成員，才能瀏覽任何資源的計量。

**解決方案：** 確保您對從中流覽指標的資源具有足夠的許可權。

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>您的資源在選取的時間範圍內未發出計量

某些資源並不會持續發出其計量。 例如，Azure 不會對已停止的虛擬機器收集計量。 其他資源可能只會在某些條件發生時發出其計量。 例如，顯示交易處理時間的計量需要至少一個交易。 如果在選取的時間範圍內沒有任何交易，圖表自然會呈現為空白。 此外，雖然 Azure 中大部分的計量會每分鐘收集一次，但某些計量的收集頻率較低。 請參閱計量的說明文件，以取得更多與您嘗試瀏覽的計量有關的詳細資料。

**解決方案：** 將圖表的時間更改為更寬的範圍。 您可以使用更大的時間細微性（或依賴于"自動時間細微性"選項）從"最近 30 天"開始。

### <a name="you-picked-a-time-range-greater-than-30-days"></a>您選擇了超過 30 天的時間範圍

[Azure 中大部分的計量會儲存 93 天](data-platform-metrics.md#retention-of-metrics)。 不過，您對任何單一圖表都只能查詢不超過 30 天的資料。 這項限制不適用於[記錄型計量](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)。

**解決方案：** 如果看到空白圖表或圖表僅顯示部分指標資料，請驗證時間選取器中的開始日期和結束日期之間的差異是否超過 30 天間隔。

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>所有計量值都在鎖定的 y 軸範圍外

在[鎖定圖表 y 軸的界限](metrics-charts.md#lock-boundaries-of-chart-y-axis)後，您可能會在無意中讓圖表顯示區域未顯示圖表線條。 例如，如果 y 軸鎖定於 0% 到 50% 的範圍間，而計量的常數值為 100%，則該行一律會顯示在可見的區域以外，使得圖表呈現為空白。

**解決方案：** 驗證圖表的 y 軸邊界未鎖定在指標值範圍之外。 如果 y 軸界限已鎖定，您可以暫時重設其界限，以確保計量值不會落在圖表範圍以外。 對於具有**總和**、**最小值**和**最大值**彙總的圖表，建議不要使用自動細微性鎖定 y 軸範圍，因為其值將會在調整瀏覽器視窗大小或切換為不同的螢幕解析度後，隨著細微性而變更。 切換細微性可能會使圖表的顯示區域呈現為空白。

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>您正在查看客體作業系統指標，但沒有啟用 Azure 診斷擴展

要收集**客體 OS** 計量，必須設定 Azure 診斷擴充功能，或使用資源的 [診斷設定]**** 面板加以啟用。

**解決方案：** 如果啟用了 Azure 診斷擴展，但仍無法查看指標，請按照[Azure 診斷擴展故障排除指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)中概述的步驟操作。 另請參閱["無法選擇來賓 OS 命名空間和指標"的](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)故障排除步驟

## <a name="error-retrieving-data-message-on-dashboard"></a>儀表板上的"錯誤檢索資料"消息

如果用來建立儀表板的計量後續已被取代並從 Azure 中移除，就可能會發生此問題。 若要確認是否就是此狀況，請開啟資源的 [計量]**** 索引標籤，並檢查計量選擇器中的可用計量。 如果計量未顯示，表示該計量已從 Azure 中移除。 某個計量被取代時，通常會有更好的新計量可針對資源健康情況提供類似的檢視性。

**解決方案：** 通過在儀表板上為圖表選擇替代指標來更新失敗的磁貼。 您可以[檢閱 Azure 服務的可用計量清單](metrics-supported.md)。

## <a name="chart-shows-dashed-line"></a>圖表顯示虛線

Azure 指標圖表使用虛線樣式來指示兩個已知時間細微性資料點之間缺少值（也稱為"空值"）。 例如，如果在時間選擇器中選取了"1 分鐘"時間細微性，但指標報告時間為 07：26、07：27、07：29 和 07：30（注意第二個和第三個資料點之間的一分鐘間隔），則虛線將連接 07：27 和 07：29，而實線將連接所有其他資料點。 當指標使用**計數****和總和**聚合時，虛線將降至零。 對於**平均**、**最小**或**最大**聚合，虛線連接兩個最近的已知資料點。 此外，當圖表遺漏最右側或最左側的資料時，虛線會往遺漏資料點的方向延伸。
  ![計量影像](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**解決方案：** 此行為是設計。 這有助於識別遺漏的資料點。 折線圖是視覺化高密度指標趨勢的優越選擇，但對於具有稀疏值的指標可能難以解釋，尤其是在將值與時粒進行關聯時。 虛線可讓這些圖表更容易讀取，但如果您的圖表仍然不清楚，請考慮使用不同類型的圖表來檢視您的計量。 例如，同一指標的分散繪圖圖表僅在存在值時視覺化點，並在缺少值時完全跳過資料點，從而清楚地顯示每次顆粒： ![](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > 如果您仍傾向使用折線圖來呈現計量，將滑鼠移至圖表上方，在滑鼠指標所在位置反白顯示資料點，將有助於評估時間細微性。

## <a name="chart-shows-unexpected-drop-in-values"></a>圖表顯示值意外下降

在許多情況下，您察覺到的計量值下滑來自於對圖表顯示資料的誤解。 當圖表顯示最近數分鐘的資料時，您可能會被總和或計數的下滑誤導，因為 Azure 尚未收到或處理最後的計量資料點。 視服務之不同，處理計量的延遲可能會在幾分鐘的範圍內。 對於顯示最近時間範圍、細微性為 1 或 5 分鐘的圖表的圖表，在最後幾分鐘內的值下降變得更加明顯：![公制圖像](./media/metrics-troubleshoot/drop-in-values.png)

**解決方案：** 此行為是設計。 我們認為在收到資料時隨即加以顯示，即使在資料是*部分*或*不完整*時，都有其好處。 這麼做可讓您更快歸納出重要結論，並立即開始調查。 以顯示失敗次數的計量為例，在看到部分值 X 時，您即得知指定的分鐘數內至少有 X 次的失敗。 您可以立即開始調查問題，而不必等著查看當下這一分鐘發生的確切失敗計數，因為這可能不是那麼重要。 圖表會在我們收到整組資料後隨即更新，但屆時也可能會顯示新的不完整資料點；來自於更接近當下的分鐘數。

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>無法選擇客體作業系統命名空間和指標

虛擬機器和虛擬機器規模集有兩類指標：由 Azure 託管環境收集的**虛擬機器主機**指標和在虛擬機器上運行的[監視代理](agents-overview.md)收集的**來賓 OS（經典）** 指標。 您可以藉由啟用 [Azure 診斷擴充功能](diagnostics-extension-overview.md)來安裝監視代理程式。

根據預設，客體 OS 計量會儲存在 Azure 儲存體帳戶中，這是您從資源的 [診斷設定]**** 索引標籤中選擇的帳戶。 如果未收集客體 OS 計量，或計量瀏覽器無法加以存取，您就只會看到**虛擬機器主機**計量命名空間：

![計量影像](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**解決方案：** 如果在指標資源管理器中看不到**來賓 OS（經典）** 命名空間和指標：

1. 請確認 [Azure 診斷擴充功能](diagnostics-extension-overview.md)已啟用，並且設定為會收集計量。
    > [!WARNING]
    > 您無法使用 [Log Analytics 代理程式](agents-overview.md#log-analytics-agent) (也稱為 Microsoft Monitoring Agent，或簡稱 "MMA") 將**客體 OS** 傳送至儲存體帳戶中。

1. 確保為[您的訂閱註冊](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)**了 Microsoft.Insights**資源供應商。

1. 確認儲存體帳戶未受防火牆保護。 Azure 入口網站需要存取儲存體帳戶，才能取出計量資料並繪製圖表。

1. 使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)驗證計量會傳輸到儲存體帳戶。 如果未收集計量，請遵循 [Azure 診斷擴充功能疑難排解指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)。

## <a name="next-steps"></a>後續步驟

* [瞭解有關使用公制資源管理器入門](metrics-getting-started.md)
* [瞭解公制資源管理器的高級功能](metrics-charts.md)
* [查看 Azure 服務的可用計量清單](metrics-supported.md)
* [查看已設定的圖表範例](metric-chart-samples.md)
