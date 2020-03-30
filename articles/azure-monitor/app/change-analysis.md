---
title: 在 Azure 監視器中使用應用程式更改分析查找 Web 應用問題 |微軟文檔
description: 在 Azure 監視器中使用應用程式更改分析，解決 Azure 應用服務上即時網站上的應用程式問題。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348739"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>在 Azure 監視器中使用應用程式更改分析（預覽）

當發生即時網站問題或中斷時，快速確定根本原因至關重要。 標準監視解決方案可能會提醒您問題。 它們甚至可能指示哪個元件出現故障。 但是，此警報並不總是立即解釋故障的原因。 你知道你的網站五分鐘前就成功了，現在它壞了。 過去五分鐘有什麼變化？ 這是應用程式更改分析旨在在 Azure 監視器中回答的問題。

基於[Azure 資源圖](https://docs.microsoft.com/azure/governance/resource-graph/overview)的強大功能，更改分析可深入瞭解 Azure 應用程式更改，以提高可觀察性並減少 MTTR（平均修復時間）。

> [!IMPORTANT]
> 更改分析當前處於預覽狀態。 此預覽版本在沒有服務等級協定的情況下提供。 不建議此版本用於生產工作負載。 某些功能可能不受支援或功能受限。 有關詳細資訊，請參閱[Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>總覽

更改分析可檢測各種類型的更改，從基礎結構層一直檢測到應用程式部署。 它是一個訂閱級 Azure 資來源提供者，它檢查訂閱中的資源更改。 更改分析為各種診斷工具提供資料，以説明使用者瞭解哪些更改可能導致問題。

下圖說明瞭更改分析的體系結構：

![更改分析如何獲取更改資料並將其提供給用戶端工具的體系結構圖](./media/change-analysis/overview.png)

目前，更改分析已集成到應用服務 Web 應用中的**診斷和解決問題**中，並在 Azure 門戶中作為獨立選項卡提供。
請參閱 *"查看 Azure 部分中所有資源的查看更改*"，以訪問更改分析邊欄選項卡和*Web 應用功能功能的更改分析*部分，以便在本文後面的 Web 應用門戶中使用它。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure 資源管理器跟蹤屬性更改

使用[Azure 資源圖](https://docs.microsoft.com/azure/governance/resource-graph/overview)，更改分析提供了承載應用程式的 Azure 資源隨時間變化的歷史記錄。 可以檢測到已跟蹤的設置，如託管標識、平臺作業系統升級和主機名稱。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure 資源管理器隨用設置更改
ARG 中尚不可用的 IP 配置規則、TLS 設置和擴展版本等設置尚不可用，因此更改分析查詢並安全地計算這些更改，以便在應用中更改的內容中提供更多詳細資訊。 此資訊在 Azure 資源圖中尚不可用，但將很快可用。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 應用部署和配置的更改（訪客中更改）

更改分析捕獲應用程式每 4 小時一次的部署和配置狀態。 例如，它可以檢測應用程式環境變數中的更改。 該工具計算差異並呈現已更改的內容。 與資源管理器更改不同，代碼部署更改資訊可能無法立即在工具中使用。 要查看更改分析中的最新更改，請選擇 **"立即掃描更改**"。

!["立即掃描更改"按鈕的螢幕截圖](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依賴項更改

對資源依賴項的更改還可能導致 Web 應用中的問題。 例如，如果 Web 應用調用 Redis 緩存，則 Redis 緩存 SKU 可能會影響 Web 應用性能。 要檢測依賴項中的更改，更改分析將檢查 Web 應用的 DNS 記錄。 通過這種方式，它標識可能導致問題的所有應用元件中的更改。
目前支援以下依賴項：
- Web Apps
- Azure 儲存體
- Azure SQL

### <a name="enablement"></a>啟用
"Microsoft.更改分析"資來源提供者需要註冊 Azure 資源管理器跟蹤屬性的訂閱，並隨近設置更改資料以可用。 當您輸入 Web 應用診斷和解決問題工具或啟動"更改分析獨立"選項卡時，此資來源提供者將自動註冊。 它沒有任何訂閱的性能和成本實現。 當您為 Web 應用啟用更改分析（或在診斷和解決問題工具中啟用）時，它將對 Web 應用的性能影響忽略不計，並且沒有計費成本。
對於 Web 應用的來賓更改，掃描 Web 應用中的代碼檔需要單獨的啟用。 有關詳細資訊，請參閱本文後面的["診斷和解決問題"工具部分中的啟用更改分析](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool)，瞭解更多詳細資訊。


## <a name="viewing-changes-for-all-resources-in-azure"></a>查看 Azure 中所有資源的更改
在 Azure 監視器中，有一個用於更改分析的獨立邊欄選項卡，用於查看具有見解和應用程式依賴項資源的所有更改。

在 Azure 門戶的搜索欄中搜索更改分析以啟動邊欄。

![Azure 門戶中搜索更改分析的螢幕截圖](./media/change-analysis/search-change-analysis.png)

選擇資源組和資源以開始查看更改。

![Azure 門戶中更改分析邊欄選項卡的螢幕截圖](./media/change-analysis/change-analysis-standalone-blade.png)

您可以看到託管應用程式的見解和相關依賴項資源。 此視圖設計為以應用程式為中心，供開發人員解決問題。

當前支援的資源包括：
- 虛擬機器
- 虛擬機器擴展集
- Azure 網路資源
- 具有來賓檔跟蹤和環境變數更改的 Web 應用

對於任何回饋，請使用邊欄選項卡或電子郵件中changeanalysisteam@microsoft.com的發送回饋按鈕。

![更改分析邊欄選項卡中回饋按鈕的螢幕截圖](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Web 應用功能的更改分析

在 Azure 監視器中，更改分析也內置於自助服務**診斷和解決問題**體驗中。 從應用服務應用程式的 **"概述"** 頁面訪問此體驗。

!["概述"按鈕和"診斷和解決問題"按鈕的螢幕截圖](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>在診斷和解決問題工具中啟用更改分析

1. 選擇**可用性和性能**。

    !["可用性和性能"故障排除選項的螢幕截圖](./media/change-analysis/availability-and-performance.png)

1. 選擇**應用程式更改**。 並不是說該功能在**應用程式崩潰**中也可用。

   !["應用程式崩潰"按鈕的螢幕截圖](./media/change-analysis/application-changes.png)

1. 要啟用更改分析，請選擇"**立即啟用**"。

   !["應用程式崩潰"選項的螢幕截圖](./media/change-analysis/enable-changeanalysis.png)

1. 打開 **"更改分析**"並選擇 **"保存**"。 該工具在應用服務方案下顯示所有 Web 應用。 您可以使用計畫級別開關為計畫下的所有 Web 應用打開"更改分析"。

    !["啟用更改分析"使用者介面的螢幕截圖](./media/change-analysis/change-analysis-on.png)


1. 要訪問更改分析，請選擇 **"診斷並解決問題** > **可用性和性能** > **應用程式崩潰**"。 您將看到一個圖表，其中總結了隨時間變化的類型以及有關這些更改的詳細資訊。 預設情況下，將顯示過去 24 小時的更改，以説明解決眼前的問題。

     ![更改差異視圖的螢幕截圖](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>大規模啟用變更分析

如果您的訂閱包含許多 Web 應用，則在 Web 應用級別啟用該服務將效率低下。 運行以下腳本以啟用訂閱中的所有 Web 應用。

先決條件：
* 電源殼 Az 模組。 在[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)時按照說明操作

執行下列指令碼：

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>後續步驟

- 為 Azure[應用服務應用](azure-web-apps.md)啟用應用程式見解。
- 為 Azure [VM 和 Azure 虛擬機器縮放集 IIS 託管的應用程式](azure-vm-vmss-apps.md)啟用應用程式見解。
- 瞭解有關[Azure 資源圖](https://docs.microsoft.com/azure/governance/resource-graph/overview)（ ） 的介紹，這有助於提供更改分析。
