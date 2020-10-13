---
title: 使用 Azure 監視器中的應用程式變更分析來尋找 web 應用程式的問題 |Microsoft Docs
description: 使用 Azure 監視器中的應用程式變更分析，針對 Azure App Service 上的即時網站上的應用程式問題進行疑難排解。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 6a5df4f6a20a9f7061f56dac507a474f7bda6100
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992877"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>在 Azure 監視器中使用應用程式變更分析 (preview) 

當即時網站問題或發生中斷時，快速判斷根本原因是很重要的。 標準監視解決方案可能會對您發出問題的警示。 它們甚至可能會指出失敗的元件。 但此警示不會一律立即說明失敗的原因。 您知道您的網站在5分鐘前完成，現在已中斷。 過去五分鐘內有哪些變更？ 這是應用程式變更分析設計來在 Azure 監視器中回答的問題。

以 [Azure Resource Graph](../../governance/resource-graph/overview.md)的能力為基礎，變更分析提供您 Azure 應用程式變更的深入解析，以增加可檢視性並減少 MTTR (平均修復) 的時間。

> [!IMPORTANT]
> 變更分析目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供。 此版本不建議用於生產工作負載。 某些功能可能不受支援，或可能具有受限的功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview"></a>概觀

變更分析會偵測各種類型的變更，從基礎結構層一直到應用程式部署。 它是訂用帳戶層級的 Azure 資源提供者，可檢查訂用帳戶中的資源變更。 變更分析提供各種診斷工具的資料，以協助使用者瞭解哪些變更可能會造成問題。

下圖說明變更分析的架構：

![變更分析如何取得變更資料，並將其提供給用戶端工具的架構圖](./media/change-analysis/overview.png)

## <a name="data-sources"></a>資料來源

Azure Resource Manager 追蹤屬性的應用程式變更分析查詢、代理式設定和來賓內的 web 應用程式變更。 此外，服務會追蹤資源相依性變更，以端對端方式診斷和監視應用程式。

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager 追蹤屬性變更

使用 [Azure Resource Graph](../../governance/resource-graph/overview.md)，變更分析會提供應用程式裝載的 Azure 資源在一段時間內的變更歷程記錄。 可以偵測到已追蹤的設定，例如受控識別、平臺 OS 升級和主機名稱。

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager 代理的設定變更

IP 設定規則、TLS 設定和延伸模組版本等設定尚未在 Azure Resource Graph 中提供，因此變更分析查詢並安全地計算這些變更，以提供應用程式中變更的詳細資料。

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Web 應用程式部署和設定中的變更 (來賓變更) 

變更分析每隔4小時就會捕獲應用程式的部署和設定狀態。 例如，它可以偵測應用程式環境變數中的變更。 此工具會計算差異，並顯示已變更的內容。 不同于 Resource Manager 變更，工具中的程式碼部署變更資訊可能無法立即使用。 若要查看變更分析中的最新變更， **請選取 [** 重新整理]。

![[立即掃描變更] 按鈕的螢幕擷取畫面](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>相依性變更

資源相依性的變更也會造成 web 應用程式中的問題。 例如，如果 web 應用程式呼叫 Redis 快取，Redis cache SKU 可能會影響 web 應用程式的效能。 若要偵測相依性中的變更，變更分析檢查 web 應用程式的 DNS 記錄。 如此一來，它會識別可能造成問題的所有應用程式元件中的變更。
目前支援下列相依性：
- Web Apps
- Azure 儲存體
- Azure SQL

## <a name="application-change-analysis-service"></a>應用程式變更分析服務

應用程式變更分析服務會計算並匯總上述資料來源中的變更資料。 它提供一組分析，讓使用者輕鬆地流覽所有資源變更，以及識別哪些變更與疑難排解或監視內容有關。
"ChangeAnalysis" 資源提供者必須向訂用帳戶註冊，才能使用 Azure Resource Manager 追蹤屬性和 proxy 設定將資料變更為可用。 當您輸入 Web 應用程式 [診斷並解決問題] 工具或顯示變更分析獨立] 索引標籤時，系統會自動註冊此資源提供者。 您的訂用帳戶不會有任何效能或成本的實現。 當您啟用 web 應用程式的變更分析 (或啟用 [診斷和解決問題] 工具) 時，對於 web 應用程式的效能影響並不會有任何費用。
針對 web 應用程式的來賓變更，在 web 應用程式中掃描程式碼檔案需要個別啟用。 如需詳細資訊，請參閱本文稍後 [的「診斷和解決問題」工具](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) 一節中的變更分析，以取得詳細資料。

## <a name="visualizations-for-application-change-analysis"></a>應用程式變更分析的視覺效果

### <a name="standalone-ui"></a>獨立 UI

在 Azure 監視器中，有一個獨立的窗格可讓變更分析透過應用程式相依性和資源的深入解析來查看所有變更。

在 Azure 入口網站的搜尋列中搜尋變更分析，以啟動體驗。

![在 Azure 入口網站中搜尋變更分析的螢幕擷取畫面](./media/change-analysis/search-change-analysis.png)

所選訂用帳戶下的所有資源都會顯示過去24小時內的變更。 為了針對頁面載入效能進行優化，服務一次會顯示10個資源。 按一下 [下一頁] 以查看更多資源。 我們正在努力移除這項限制。

![Azure 入口網站中的變更分析分頁的螢幕擷取畫面](./media/change-analysis/change-analysis-standalone-blade.png)

按一下資源以查看其所有變更。 如有需要，請向下切入變更，以查看 json 格式化的變更詳細資料和深入解析。

![變更詳細資料的螢幕擷取畫面](./media/change-analysis/change-details.png)

如有任何意見反應，請使用分頁或電子郵件中的 [傳送意見反應] 按鈕 changeanalysisteam@microsoft.com 。

![變更分析分頁中意見反應按鈕的螢幕擷取畫面](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Web 應用程式診斷和解決問題

在 Azure 監視器中，變更分析也內建于自助 **診斷和解決問題** 體驗中。 從 App Service 應用程式的 [ **總覽** ] 頁面存取此體驗。

![[總覽] 按鈕和 [診斷並解決問題] 按鈕的螢幕擷取畫面](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>診斷和解決問題工具中的應用程式變更分析

應用程式變更分析是 Web 應用程式中的獨立偵測器，可診斷並解決問題工具。 它也會在 **應用程式** 當機和 **Web 應用程式關閉偵測器**時進行匯總。 當您輸入診斷和解決問題工具時，將會自動註冊 **ChangeAnalysis** 資源提供者。 遵循這些指示來啟用 web 應用程式的來賓變更追蹤。

1. 選取 [ **可用性] 和 [效能**]。

    ![[可用性和效能] 疑難排解選項的螢幕擷取畫面](./media/change-analysis/availability-and-performance.png)

2. 選取 [ **應用程式變更**]。 **應用程式**損毀中也提供此功能。

   ![[應用程式當機] 按鈕的螢幕擷取畫面](./media/change-analysis/application-changes.png)

3. 此連結會導向至 web 應用程式範圍內的應用程式變更 Analysis UI。 如果未啟用 web 應用程式的「來賓變更追蹤」，請遵循橫幅來取得檔案和應用程式設定變更。

   ![[應用程式當機] 選項的螢幕擷取畫面](./media/change-analysis/enable-changeanalysis.png)

4. 開啟 **變更分析** ，然後選取 [ **儲存**]。 此工具會顯示 App Service 方案下的所有 web 應用程式。 您可以使用 [方案層級] 參數，為方案下的所有 web 應用程式開啟變更分析。

    ![「啟用變更分析」使用者介面的螢幕擷取畫面](./media/change-analysis/change-analysis-on.png)

5. 變更資料也可在 [選取 **Web 應用程式關閉** ] 和 [ **應用程式** 當機] 偵測器中使用。 您會看到一個圖表，其中摘要說明一段時間內的變更類型，以及這些變更的詳細資料。 依預設，會顯示過去24小時內的變更，以協助解決立即發生的問題。

     ![變更差異視圖的螢幕擷取畫面](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>虛擬機器診斷和解決問題

移至虛擬機器的 [診斷並解決問題] 工具。  移至 [ **疑難排解工具**]，向下流覽頁面並選取 [ **分析最近的變更** ]，以查看虛擬機器上的變更。

![VM 診斷和解決問題的螢幕擷取畫面](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![疑難排解工具中的變更分析器](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>活動記錄變更歷程記錄
活動記錄中的 [View change history](../platform/activity-log.md#view-change-history) 功能會呼叫應用程式變更分析 service 後端，以取得與作業相關聯的變更。 **變更** 用來直接呼叫 [Azure Resource Graph](../../governance/resource-graph/overview.md) 的歷程記錄，但會將後端交換以呼叫應用程式變更分析因此，傳回的變更將包含來自 [Azure Resource Graph](../../governance/resource-graph/overview.md)的資源層級變更、來自 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)的資源屬性，以及來自 PaaS 服務（例如應用程式服務 web 應用程式）的來賓變更。 為了讓應用程式變更分析服務能夠掃描使用者訂用帳戶中的變更，必須註冊資源提供者。 當您第一次進入 [ **變更歷程記錄** ] 索引標籤時，此工具會自動開始註冊 **ChangeAnalysis** 資源提供者。 註冊之後， **Azure Resource Graph** 的變更將會立即可用，並涵蓋過去14天。 當訂用帳戶上架之後，其他來源的變更將會在 ~ 4 小時後提供使用。

![活動記錄變更歷程記錄整合](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>VM Insights 整合
啟用 [VM 深入](../insights/vminsights-overview.md) 解析的使用者可以查看其虛擬機器中的變更，這些變更可能會造成計量圖表（例如 CPU 或記憶體）中的任何尖峰，並想知道造成它的原因。 變更資料已整合至 VM Insights 側導覽列。 使用者可以查看 VM 中是否發生任何變更，並按一下 [ **調查變更** ] 以查看應用程式變更分析獨立 UI 中的變更詳細資料。

[![VM insights 整合](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)



## <a name="enable-change-analysis-at-scale"></a>啟用大規模的變更分析

如果您的訂用帳戶包含許多 web 應用程式，在 web 應用程式層級啟用服務將會沒有效率。 執行下列腳本，以啟用您訂用帳戶中的所有 web 應用程式。

先決條件：

- PowerShell Az 模組。 依照[安裝 Azure PowerShell 課程模組的](/powershell/azure/install-az-ps)指示操作

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

## <a name="troubleshoot"></a>疑難排解

### <a name="having-trouble-registering-microsoftchange-analysis-resource-provider-from-change-history-tab"></a>註冊 Microsoft 時發生問題。請從變更歷程記錄索引標籤變更分析資源提供者
如果這是您第一次在與應用程式變更分析整合之後查看變更歷程記錄，您會看到它自動註冊資源提供者 **ChangeAnalysis**。 在罕見的情況下，可能會因為下列原因而失敗：

- **您沒有足夠的許可權可註冊 ChangeAnalysis 資源提供者**。 此錯誤訊息表示您在目前訂用帳戶中的角色沒有相關聯的 **Microsoft 支援/註冊/動作** 範圍。 如果您不是訂用帳戶的擁有者，而且透過同事取得共用存取權限，就可能發生這種情況。 亦即，查看資源群組的存取權。 若要修正此問題，您可以聯繫訂用帳戶的擁有者，註冊 **ChangeAnalysis** 資源提供者。 這可以透過訂用帳戶在 Azure 入口網站中完成 **|資源提供者** ，並 ```Microsoft.ChangeAnalysis``` 在 UI 中搜尋和註冊，或透過 Azure PowerShell 或 Azure CLI。

    透過 PowerShell 註冊資源提供者： 
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **無法註冊 ChangeAnalysis 資源提供者**。 這則訊息表示 UI 傳送要求以註冊資源提供者時，會立即失敗，而且與許可權問題無關。 可能是暫時性的網際網路連線問題。 請嘗試重新整理頁面，並檢查您的網際網路連線。 如果錯誤持續發生，請聯絡 changeanalysishelp@microsoft.com

- **這所花費的時間超出預期**。 這則訊息表示註冊所花費的時間超過2分鐘。 這是不尋常的，但不一定表示發生錯誤。 您可以前往 [ **訂閱] |** 要檢查 **Microsoft ChangeAnalysis** 資源提供者註冊狀態的資源提供者。 您可以嘗試使用 UI 來取消註冊、重新註冊或重新整理，以查看是否有説明。 如果問題持續發生，請洽詢 changeanalysishelp@microsoft.com 支援人員。
    ![針對 RP 註冊花費太長時間進行疑難排解](./media/change-analysis/troubleshoot-registration-taking-too-long.png)



## <a name="next-steps"></a>接下來的步驟

- 啟用 [Azure App 服務應用程式](azure-web-apps.md)的 Application Insights。
- 針對 [AZURE VM 和 azure 虛擬機器擴展集 IIS 裝載的應用程式](azure-vm-vmss-apps.md)啟用 Application Insights。
- 深入瞭解可協助您變更分析的 [Azure Resource Graph](../../governance/resource-graph/overview.md)。