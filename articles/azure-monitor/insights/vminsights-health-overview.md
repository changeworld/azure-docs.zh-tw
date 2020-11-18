---
title: '適用於 VM 的 Azure 監視器來賓健康情況 (預覽) '
description: 概要說明適用於 VM 的 Azure 監視器中的健康情況功能，包括如何查看虛擬機器的健康情況，以及在虛擬機器變成狀況不良時接收警示。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686821"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>適用於 VM 的 Azure 監視器來賓健康情況 (預覽) 
適用於 VM 的 Azure 監視器來賓健康狀態可讓您根據一組從客體作業系統定期取樣的效能測量，來查看虛擬機器的健康情況。 您可以快速檢查訂用帳戶或資源群組中所有虛擬機器的健康狀態、向下切入特定虛擬機器的詳細健全狀況，或在虛擬機器變成狀況不良時主動通知您。 

## <a name="enable-virtual-machine-health"></a>啟用虛擬機器健康情況
如需啟用來賓健康情況功能和將虛擬機器上線的詳細資訊，請參閱 [啟用適用於 VM 的 Azure 監視器來賓健全狀況 (preview) ](vminsights-health-enable.md) 。

## <a name="pricing"></a>定價
來賓健康情況功能沒有直接成本，但在 Log Analytics 工作區中內嵌和儲存健全狀況狀態資料會產生費用。 所有資料都會儲存在 *HealthStateChangeEvent* 資料表中。 如需定價模式和成本的詳細資訊，請參閱 [使用 Azure 監視器記錄來管理使用量和成本](../platform/manage-cost-storage.md) 。

## <a name="view-virtual-machine-health"></a>查看虛擬機器健康情況
[**開始**] 頁面中的 [**來賓 VM 健康** 情況] 欄可讓您快速查看特定訂用帳戶或資源群組中每個虛擬機器的健全狀況。 每個虛擬機器的目前健康狀態會顯示，而每個群組的圖示會顯示該群組中目前處於每個狀態的虛擬機器數目。

[![具有來賓 VM 健康情況的開始使用頁面](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>監視器
按一下虛擬機器的健全狀況狀態，以查看每個監視的詳細狀態。 每個監視器都會測量特定元件的健康情況。 虛擬機器的整體健全狀況是由其個別監視的健全狀況所決定。 

![監視範例](media/vminsights-health-overview/monitors.png)

下表列出每個虛擬機器目前可用的匯總和單位監視。 

| 監視器 | 類型 | 描述 |
|:---|:---|:---|
| CPU 使用率 | 單位 | 處理器使用率百分比。 |
| 檔案系統 | Aggregate | 匯總 Linux VM 上所有檔案系統的健康情況。 |
| 檔案系統  | Aggregate | Linux VM 上每個個別檔案系統的健康情況。 監視器的名稱是檔案系統的名稱。 |
| 可用空間 | 單位 | 檔案系統上的可用空間百分比。 |
| 邏輯磁碟 | Aggregate | 匯總 Windows VM 上所有邏輯磁片的健全狀況。 |
| 邏輯磁碟  | Aggregate | Windows VM 上每個個別邏輯磁片的健全狀況。 監視器的名稱是磁片的名稱。 |
| 記憶體 | Aggregate | 匯總 VM 上記憶體的健康情況。 |
| 可用的記憶體 | 單位 | VM 上的可用 mb 數。 |

## <a name="health-states"></a>健康狀態
每個監視器每分鐘都會取樣代理程式上的值，並將取樣值與每個健全狀況狀態的準則進行比較。 如果特定狀態的準則為 true，則監視器會設為該狀態。 如果沒有任何準則成立，則監視器會設為狀況良好的狀態。 從代理程式傳送資料到每三分鐘 Azure 監視器，或在狀態變更時立即傳送。

每個監視器都有一個回顧視窗，會分析在該時間內收集的任何樣本。 例如，監視器可能會有240秒的回顧視窗，尋找至少2個取樣值之間的最大值，但不超過最後3個。 雖然值是以固定速率取樣，但如果代理程式作業中有任何中斷情形，則在特定視窗中取樣的數位可能會稍有不同。

監視每個都有下表中的潛在健全狀況狀態，而且在任何指定時間都只會有一個。 當監視初始化時，它會以狀況良好的狀態啟動。

| 健全狀況狀態 | 描述 |
|:---|:---|
| Healthy  | 監視器目前不超過警告或重大閾值。 |
| 警告  | 監視器已超過警告臨界值 (如果已定義) 。 |
| Critical | 如果定義) ，監視已超過 [重大臨界值] (。 |
| 未知  | 收集的資料不足以判斷健全狀況狀態。 |
| Disabled | 目前已停用此監視器。 |
| 無     | 監視器剛開始，尚未評估或受監視的物件已不存在。 |



有兩種類型的監視器，如下表所示。

| 監視器 | 描述 |
|:---|:---|
| 單位監視 | 測量資源或應用程式的某些層面。 這可能是檢查效能計數器以判斷資源的效能或可用性。 |
| 彙總監視器 | 將多個監視器分組，以提供單一匯總的健全狀況狀態。 匯總監視可包含一或多個單位監視和其他匯總監視。 |


  
### <a name="health-rollup-policy"></a>健全狀況匯總套件原則
虛擬機器的健全狀況狀態取決於其每個單位和匯總監視的健全狀況匯總套件。 每個匯總監視都會使用最糟的狀態健康情況匯總原則，其中匯總監視的狀態會與最差健全狀況狀態的子監視狀態相符。  

在下列範例中， **可用空間** 監視器處於重大狀態，而這些角色最多可達到其實例的匯總，然後到 **檔案系統**。 即使 **CPU 使用率** 處於警告狀態，虛擬機器仍會設定為重大，因為這是其下的最差狀態。 如果可用空間恢復狀況良好狀態，則虛擬機器將會變更為警告狀態，因為 CPU 使用率會變成處於最差狀態的監視器。

![健康情況彙總範例](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>監視詳細資料
選取監視以查看其詳細資料，其中包括下列索引標籤。

**總覽** 提供監視的描述、上次評估的時間，以及用來判斷目前健全狀況狀態的取樣值。 樣本數目可能會根據監視的定義和值的變動程度而有所不同。

[![監視詳細資料總覽](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

歷程 **記錄** 會列出監視器狀態變更的歷程記錄。 您可以展開任何狀態變更，以查看評估的值以判斷健全狀況狀態。 按一下 [已套用的 **視圖** 設定]，即可在健全狀況狀態變更時，查看監視的設定。



[![監視詳細資料歷程記錄](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>設定
查看並修改所選取 VM 的監視設定。 如需詳細資訊，請參閱 [適用於 VM 的 Azure 監視器來賓健康情況中設定監視 (預覽) ](vminsights-health-enable.md) 。

[![監視詳細資料設定](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>後續步驟

- [在適用於 VM 的 Azure 監視器中啟用來賓健全狀況，並將代理程式上架。](vminsights-health-enable.md)
- [使用 Azure 入口網站設定監視。](vminsights-health-configure.md)
- [使用資料收集規則設定監視。](vminsights-health-configure-dcr.md)