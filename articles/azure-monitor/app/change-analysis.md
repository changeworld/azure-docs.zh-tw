---
title: 在 Azure 監視器中使用應用程式變更分析來尋找 web 應用程式的問題 |Microsoft Docs
description: 在 Azure 監視器中使用應用程式變更分析，針對 Azure App Service 上的即時網站上的應用程式問題進行疑難排解。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: d53097c7884b9908cd3a2c7f21dc059ed9d00c39
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540157"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>在 Azure 監視器中使用應用程式變更分析（預覽）

當發生即時網站問題或中斷時，快速判斷根本原因是很重要的。 標準監視解決方案可能會警告您發生問題。 他們甚至可能會指出哪個元件失敗。 但此警示不一定會立即說明失敗的原因。 您知道您的網站在五分鐘前就已正常運作，現在已中斷。 過去五分鐘內有哪些變更？ 這是應用程式變更分析設計來在 Azure 監視器中回答的問題。

藉由[Azure Resource Graph](../../governance/resource-graph/overview.md)的威力，變更分析可讓您深入瞭解 Azure 應用程式變更，以增加可檢視性並減少 MTTR （平均修復時間）。

> [!IMPORTANT]
> 變更分析目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供。 不建議將此版本用於生產工作負載。 某些功能可能不受支援，或可能有限制的功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概觀

變更分析會偵測各種類型的變更，從基礎結構層到應用程式部署都是如此。 這是訂用帳戶層級的 Azure 資源提供者，可檢查訂用帳戶中的資源變更。 變更分析會針對各種診斷工具提供資料，以協助使用者瞭解哪些變更可能會造成問題。

下圖說明變更分析的架構：

![變更分析如何取得變更資料，並將其提供給用戶端工具的架構圖](./media/change-analysis/overview.png)

## <a name="data-sources"></a>資料來源

應用程式變更分析查詢，適用于 Azure Resource Manager 追蹤的屬性、proxy 設定和來賓內的 web 應用程式變更。 此外，服務會追蹤資源相依性變更，以診斷和監視應用程式端對端。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager 追蹤的屬性變更

變更分析會使用[Azure Resource Graph](../../governance/resource-graph/overview.md)，提供裝載應用程式的 Azure 資源在一段時間內變更的歷程記錄。 可以偵測受追蹤的設定，例如受控識別、平臺作業系統升級和主機名稱。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxy 設定變更

Azure Resource Graph 中尚無法使用 IP 設定規則、TLS 設定和延伸模組版本等設定，因此請變更分析查詢並安全地計算這些變更，以在應用程式中變更的內容提供更多詳細資料。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 應用程式部署和設定的變更（來賓內變更）

變更分析會每隔4小時捕獲應用程式的部署和設定狀態。 例如，它可以偵測應用程式環境變數中的變更。 此工具會計算差異，並顯示已變更的內容。 不同于 Resource Manager 變更，工具中可能不會立即提供程式碼部署變更資訊。 若要查看變更分析的最新變更，**請選取 [** 重新整理]。

![[立即掃描變更] 按鈕的螢幕擷取畫面](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>相依性變更

變更資源相依性也會導致 web 應用程式發生問題。 例如，如果 web 應用程式呼叫 Redis 快取，Redis cache SKU 可能會影響 web 應用程式的效能。 若要偵測相依性中的變更，變更分析會檢查 web 應用程式的 DNS 記錄。 如此一來，它會識別可能造成問題的所有應用程式元件中的變更。
目前支援下列相依性：
- Web Apps
- Azure 儲存體
- Azure SQL

## <a name="application-change-analysis-service"></a>應用程式變更分析服務

應用程式變更分析服務會計算並匯總前述資料來源中的變更資料。 它提供一組分析，讓使用者輕鬆流覽所有資源變更，並識別哪些變更與疑難排解或監視內容有關。
"ChangeAnalysis" 資源提供者必須向訂用帳戶註冊，才能使用 Azure Resource Manager 追蹤的屬性，而 proxy 設定會將資料變更為可供使用。 當您輸入 Web 應用程式 [診斷並解決問題] 工具或顯示 [變更分析] [獨立] 索引標籤時，此資源提供者會自動註冊。 您的訂用帳戶沒有任何效能或成本的實現。 當您啟用 web 應用程式的變更分析（或啟用 [診斷並解決問題] 工具）時，對 web 應用程式的效能影響會不明顯，而且不會產生任何費用。
若是 web 應用程式的來賓變更，在 web 應用程式中掃描程式碼檔案時，需要個別的啟用。 如需詳細資訊，請參閱本文稍後[的診斷和解決問題工具](#application-change-analysis-in-the-diagnose-and-solve-problems-tool)一節中的變更分析。

## <a name="visualizations-for-application-change-analysis"></a>應用程式變更分析的視覺效果

### <a name="standalone-ui"></a>獨立 UI

在 Azure 監視器中，有一個用於變更分析的獨立窗格，可使用應用程式相依性和資源的深入解析來查看所有變更。

在 Azure 入口網站的搜尋列中搜尋變更分析，以啟動體驗。

![在 Azure 入口網站中搜尋變更分析的螢幕擷取畫面](./media/change-analysis/search-change-analysis.png)

所選訂用帳戶下的所有資源都會顯示過去24小時內的變更。 為了優化頁面載入效能，服務會一次顯示10個資源。 按一下 [下一頁] 以查看更多資源。 我們正努力移除這項限制。

![Azure 入口網站中 [變更分析] 分頁的螢幕擷取畫面](./media/change-analysis/change-analysis-standalone-blade.png)

按一下資源以查看其所有變更。 如有需要，向下切入變更以查看 json 格式化的變更詳細資料和深入解析。

![變更詳細資料的螢幕擷取畫面](./media/change-analysis/change-details.png)

如需任何意見反應，請使用分頁或電子郵件中的 [傳送意見反應] 按鈕 changeanalysisteam@microsoft.com 。

![[變更分析] 分頁中 [意見反應] 按鈕的螢幕擷取畫面](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Web 應用程式診斷並解決問題

在 Azure 監視器中，[變更分析] 也內建于自助**診斷和解決問題**體驗。 從您 App Service 應用程式的 [**總覽**] 頁面存取這項體驗。

![[總覽] 按鈕和 [診斷並解決問題] 按鈕的螢幕擷取畫面](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>[診斷並解決問題] 工具中的應用程式變更分析

應用程式變更分析是 Web 應用程式中的獨立偵測器，用於診斷和解決問題工具。 它也會在**應用程式**當機和**Web 應用程式關閉偵測器**中匯總。 當您輸入 [診斷並解決問題] 工具時， **ChangeAnalysis**資源提供者會自動註冊。 請遵循這些指示來啟用 web 應用程式的來賓變更追蹤。

1. 選取 [**可用性和效能**]。

    ![[可用性和效能] 疑難排解選項的螢幕擷取畫面](./media/change-analysis/availability-and-performance.png)

2. 選取 [**應用程式變更**]。 **應用程式**損毀也提供此功能。

   ![[應用程式當機] 按鈕的螢幕擷取畫面](./media/change-analysis/application-changes.png)

3. 若要啟用變更分析，請選取 [**立即啟用**]。

   ![[應用程式當機] 選項的螢幕擷取畫面](./media/change-analysis/enable-changeanalysis.png)

4. 開啟 [**變更分析**]，然後選取 [**儲存**]。 此工具會顯示 App Service 方案下的所有 web 應用程式。 您可以使用 [方案層級] 切換，針對方案下的所有 web 應用程式開啟變更分析。

    ![[啟用變更分析] 使用者介面的螢幕擷取畫面](./media/change-analysis/change-analysis-on.png)

5. 若要存取變更分析，請選取 [**診斷並解決問題**  >  **可用性和效能**  >  **應用程式**當機]。 您會看到一個圖表，其中匯總一段時間的變更類型，以及這些變更的詳細資料。 根據預設，會顯示過去24小時內的變更，以協助您立即遇到問題。

     ![變更差異視圖的螢幕擷取畫面](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>啟用大規模的變更分析

如果您的訂用帳戶包含多個 web 應用程式，則在 web 應用程式層級上啟用服務會沒有效率。 執行下列腳本，以啟用您訂用帳戶中的所有 web 應用程式。

先決條件：

- PowerShell Az 模組。 請遵循[安裝 Azure PowerShell 模組中的](/powershell/azure/install-az-ps?view=azps-2.6.0)指示

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

### <a name="virtual-machine-diagnose-and-solve-problems"></a>虛擬機器診斷並解決問題

移至 [診斷並解決虛擬機器的問題] 工具。  移至 [**疑難排解工具**]，流覽頁面並選取 [**分析最近的變更**]，以查看虛擬機器上的變更。

![VM 診斷和解決問題的螢幕擷取畫面](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![VM 診斷和解決問題的螢幕擷取畫面](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>後續步驟

- 啟用[Azure App Services 應用程式](azure-web-apps.md)的 Application Insights。
- 啟用[AZURE VM 和 azure 虛擬機器擴展集 IIS 託管應用程式](azure-vm-vmss-apps.md)的 Application Insights。
- 深入瞭解[Azure Resource Graph](../../governance/resource-graph/overview.md)，其可協助您進行電源變更分析。
