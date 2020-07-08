---
title: Azure 流量分析常見問題集 | Microsoft Docs
description: 取得一些關於「流量分析」常見問題的解答。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 87b4f0573fbcc73573c508a7f8e39acadcfa05af
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056458"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>流量分析常見問題集

本文在同一處收集許多關於 Azure 網路監看員流量分析的常見問題。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>使用流量分析的必要條件有哪些？

流量分析需要下列必要條件：

- 已啟用網路監看員的訂用帳戶。
- 已針對您要監視的 NSG 啟用網路安全性群組 (NSG) 流量記錄。
- 用來儲存原始流量記錄的 Azure 儲存體帳戶。
- 具有讀取和寫入權限的 Azure Log Analytics 工作區。

您的帳戶必須符合下列其中一項才能啟用流量分析：

- 您的帳戶必須擁有訂用帳戶範圍中下列任一個角色型存取控制 (RBAC) 角色：擁有者、參與者、讀者或網路參與者。
- 如果您的帳戶未指派給其中一個之前列出的角色，則必須指派給一個自訂角色，且該角色已獲得下列訂用帳戶層級的動作。
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
檢查指派給訂用帳戶使用者的角色：

1. 使用**Disconnect-azaccount 登**入 Azure。 

2. 使用 [ **get-azsubscription**] 選取所需的訂用帳戶。 

3. 若要列出指派給指定使用者的所有角色，請使用**new-azroleassignment-SignInName [使用者電子郵件]-IncludeClassicAdministrators**。 

如果看不到任何輸出，請聯絡個別訂用帳戶管理員，取得執行命令的權限。 如需詳細資料，請參閱[使用 Azure PowerShell 管理角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)。


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>在哪些 Azure 區域中可使用流量分析？

您可以在下列任何支援的區域中使用 NSG 的流量分析：
- 加拿大中部
- 美國中西部
- 美國東部
- 美國東部 2
- 美國中北部
- 美國中南部
- 美國中部
- 美國西部
- 美國西部 2
- 法國中部
- 西歐
- 北歐
- 巴西南部
- 英國西部
- 英國南部
- 澳大利亞東部
- 澳大利亞東南部 
- 東亞
- 東南亞
- 南韓中部
- 印度中部
- 印度南部
- 日本東部
- 日本西部
- US Gov 維吉尼亞州
- 中國東部 2

Log Analytics 工作區必須存在於下列區域：
- 加拿大中部
- 美國中西部
- 美國東部
- 美國東部 2
- 美國中北部
- 美國中南部
- 美國中部
- 美國西部
- 美國西部 2
- 法國中部
- 西歐
- 北歐
- 英國西部
- 英國南部
- 澳大利亞東部
- 澳大利亞東南部
- 東亞
- 東南亞 
- 南韓中部
- 印度中部
- 日本東部
- US Gov 維吉尼亞州
- 中國東部 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>啟用了流量記錄的 NSG 是否可與我的工作區位於不同的區域中？

是，這些 NSG 可與您的 Log Analytics 工作區位於不同的區域。

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>可以在單一工作區內設定多個 NSG 嗎？

是。

## <a name="can-i-use-an-existing-workspace"></a>我可以使用現有的工作區嗎？

是。 如果您選取現有的工作區，請確定該工作區已遷移至新的查詢語言。 如果您不想升級工作區，則需要建立新的工作區。 如需新查詢語言的詳細資訊，請參閱[Azure 監視器記錄升級至新的記錄搜尋](../log-analytics/log-analytics-log-search-upgrade.md)。

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>是否可以讓我的 Azure 儲存體帳戶位於某個訂用帳戶，而讓我的 Log Analytics 工作區位於不同的訂用帳戶中？

是，您的 Azure 儲存體帳戶可以位於某個訂用帳戶，而您的 Log Analytics 工作區可以位於不同的訂用帳戶中。

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>可以在不同的訂用帳戶中儲存原始記錄嗎？

是。 您可以將 NSG 流量記錄設定為要傳送至位於不同訂用帳戶中的儲存體帳戶，前提是您擁有適當的許可權，而且儲存體帳戶位於與 NSG 相同的區域中。 NSG 和目的地儲存體帳戶也必須共用相同的 Azure Active Directory 租使用者。

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>因為發生「找不到」錯誤而無法設定 NSG 進行流量分析，該怎麼辦？

請選取支援的地區。 如果您選取不受支援的區域，就會收到「找不到」錯誤。 本文稍早已列出支援的地區。

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>NSG 流量記錄頁面下方顯示「無法載入」狀態，該怎麼辦？

為了讓流量記錄正常運作，您必須註冊 Microsoft.Insights 提供者。 如果您不確定自己是否已為訂用帳戶註冊 Microsoft.Insights 提供者，請取代下列命令中的 *xxxxx-xxxxx-xxxxxx-xxxx*，然後從 PowerShell 執行下列命令：

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>我已設定解決方案。 為什麼在儀表板上看不到任何項目？

第一次時，儀表板最久可能需要 30 分鐘才會出現。 解決方案必須先彙總足夠資料以衍生有意義的見解， 然後才能產生報告。 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>收到下列訊息時該怎麼辦：「在選取的時間間隔內，我們無法在此工作區中找到任何資料。 請嘗試變更時間間隔，或選取不同的工作區。」？

請嘗試下列選項：
- 在上方列中變更時間間隔。
- 在上方列中選取不同的 Log Analytics 工作區。
- 如果是最近才啟用的話，請嘗試於 30 分鐘過後再存取流量分析。
    
如果問題仍然存在，請在[使用者心聲論壇](https://feedback.azure.com/forums/217313-networking?category_id=195844) \(英文\) 中提出疑慮。

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>收到下列訊息時該怎麼辦：「第一次分析您的 NSG 流量記錄。 此程序可能需要 20-30 分鐘才能完成。 請在一段時間過後再回來查看。 2) 如果上述步驟無效，且您的工作區屬於免費的 SKU，請在這裡檢查您的工作區使用量以進行配額驗證，或請參閱常見問題集以取得進一步資訊。」？

您會看到此訊息的原因可能如下：
- 流量分析最近才啟用，而且可能尚未彙總到足夠資料以衍生有意義的見解。
- 您使用的是免費版本的 Log Analytics 工作區，而且超過配額限制。 您可能需要使用容量較大的工作區。
    
如果問題仍然存在，請在[使用者心聲論壇](https://feedback.azure.com/forums/217313-networking?category_id=195844) \(英文\) 中提出疑慮。
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>收到下列訊息時該怎麼辦：「看起來我們有資源資料 (拓撲)，但沒有任何流量資訊。 於此同時，請按一下這裡以查看資源資料，並參閱常見問題集以取得進一步資訊。」？

您會在儀表板上看到資源資訊；不過，不會有任何流程相關的統計資料。 資源之間沒有通訊流量，所以可能不會有資料。 請稍候 60 分鐘，然後重新檢查狀態。 如果問題仍然存在，而且您確定資源之間有通訊流量，請在[使用者心聲論壇](https://feedback.azure.com/forums/217313-networking?category_id=195844) (英文) 中提出疑慮。

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>我可以使用 PowerShell 或 Azure Resource Manager 範本或用戶端來設定流量分析嗎？

您可以使用 Windows PowerShell 6.2.1 以上版本設定流量分析。 若要使用 Set Cmdlet 來設定特定 NSG 的流量記錄和流量分析，請參閱[AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog)。 若要取得特定 NSG 的流量記錄和流量分析狀態，請參閱[AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus)。

目前您無法使用 Azure Resource Manager 範本來設定流量分析。

若要使用 Azure Resource Manager 用戶端設定流量分析，請參閱以下範例。

**設定 cmdlet 範例：**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**取得 cmdlet 範例：**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>流量分析的價格為何？

流量分析採計量付費。 計量是以服務處理的流量記錄資料來進行，並將所產生的加強型記錄儲存在 Log Analytics 工作區中。 

例如，根據[定價方案](https://azure.microsoft.com/pricing/details/network-watcher/)，以美國中西部區域為例，如果儲存在儲存體帳戶且由流量分析處理的流量記錄資料為 10 GB，而內嵌於 Log Analytics 工作區的加強型記錄為 1 GB，則適用費用為：10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>流量分析處理資料的頻率為何？

請參閱流量分析架構和資料匯總檔中的[資料匯總一節](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation)

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>流量分析如何判斷 IP 是惡意的？ 

流量分析會依賴 Microsoft 內部威脅情報系統，將 IP 視為惡意。 這些系統會運用各種遙測來源，例如 Microsoft 產品和服務、Microsoft 數位犯罪單位（DCU）、Microsoft Security Response Center （MSRC）和外部摘要，並在其上建立許多智慧。 這其中一些資料是 Microsoft Internal。 如果已知的 IP 標示為惡意，請提出支援票證以瞭解詳細資料。

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>如何設定流量分析資料的警示？

流量分析不具有警示的內建支援。 不過，因為流量分析資料儲存在 Log Analytics 中，所以您可以撰寫自訂查詢並對其設定警示。 步驟
- 您可以在流量分析中將簡短連結用於 Log Analytics。 
- 使用[這裡記載的架構](traffic-analytics-schema.md)來撰寫您的查詢 
- 按一下 [新增警示規則] 以建立警示
- 請參閱[記錄警示檔](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)以建立警示

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>如何? 檢查哪些 Vm 會接收大部分的內部部署流量？

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  針對 Ip：

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

針對時間，請使用格式： yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>如何? 檢查我的 Vm 從內部部署機器收到的流量標準差嗎？

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


針對 Ip：

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>如何? 檢查 IP 組與 NSG 規則之間可連線（或封鎖）哪些埠？

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>如何在地理地圖檢視中使用鍵盤來瀏覽？

地理地圖頁面包含兩大區段：
    
- **橫幅**：地理地圖頂端的橫幅會提供按鈕來選取流量發佈篩選器（例如，部署、來自國家/地區的流量，以及惡意的流量）。 當您選取按鈕時，個別篩選條件便會套用至地圖。 例如，如果您選取 [使用中] 按鈕，地圖就會將部署中的使用中資料中心醒目提示。
- **Map**：在橫幅底下，[地圖] 區段會顯示 Azure 資料中心與國家/地區之間的流量分配。
    
### <a name="keyboard-navigation-on-the-banner"></a>橫幅上的鍵盤瀏覽
    
- 根據預設，系統會在地理地圖頁面的橫幅上選擇「Azure DC」篩選條件。
- 若要移至另一個篩選條件，可使用 `Tab` 或 `Right arrow` 鍵。 若要反向移動，可使用 `Shift+Tab` 或 `Left arrow` 鍵。 順向瀏覽是由左到右，再由上到下。
- 按 `Enter` 或 `Down` 方向鍵可套用選取的篩選條件。 根據選取的篩選條件和部署，系統會在 [地圖] 區段下將一或多個節點醒目提示。
- 若要切換橫幅和地圖，請按 `Ctrl+F6`。
        
### <a name="keyboard-navigation-on-the-map"></a>地圖上的鍵盤瀏覽
    
- 選取橫幅上的任何篩選條件並按下 `Ctrl+F6` 後，焦點就會移至地圖檢視中的其中一個醒目提示節點 (**Azure 資料中心**或**國家/區域**)。
- 若要移至地圖上其他醒目提示節點，請使用 `Tab` 或 `Right arrow` 鍵順向移動。 若要反向移動，請使用 `Shift+Tab` 或 `Left arrow` 鍵。
- 若要選取地圖中的任何醒目提示節點，請使用 `Enter` 或 `Down arrow` 鍵。
- 一選取任何這類節點後，焦點就會移至節點的 [資訊工具方塊]****。 根據預設，焦點會移至 [資訊工具方塊]**** 上已關閉的按鈕。 若要進一步地在 [方塊]**** 檢視內移動，請分別使用 `Right arrow` 和 `Left arrow` 鍵來順向和反向移動。 按 `Enter` 的效果等同於在 [資訊工具方塊]**** 內選取焦點按鈕的效果。
- 若您在焦點於 [資訊工具方塊]**** 時按 `Tab`，焦點就會移至與所選節點位於相同大陸的端點。 使用 `Right arrow` 和 `Left arrow` 鍵可在這些端點之間移動。
- 若要移動至其他流量端點或大陸叢集，請使用 `Tab` 進行順向移動，使用 `Shift+Tab` 進行反向移動。
- 一旦焦點位於 [大陸叢集]**** 時，請使用 `Enter` 或 `Down` 方向鍵來醒目提示大陸叢集內的端點。 若要在端點和大陸叢集資訊方塊上的關閉按鈕之間移動，請分別使用 `Right arrow` 或 `Left arrow` 鍵來進行順向和反向移動。 在任何端點上，您都可以使用 `Shift+L` 來切換到從選取節點到端點的連接線。 您可以再按一次 `Shift+L`，即可移至所選的端點。
        
### <a name="keyboard-navigation-at-any-stage"></a>各階段的鍵盤瀏覽
    
- `Esc` 可將展開的選取範圍摺疊。
- `Up-arrow` 鍵可執行與 `Esc` 相同的動作。 `Down arrow` 鍵可執行與 `Enter` 相同的動作。
- 使用 `Shift+Plus` 可放大，使用 `Shift+Minus` 可縮小。

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>如何在虛擬網路拓撲檢視中使用鍵盤來瀏覽？

虛擬網路拓樸頁面包含兩個主要區段：
    
- **橫幅**：虛擬網路拓撲上方的橫幅會提供各種按鈕，可選取流量發佈篩選器 (例如連線的虛擬網路、 中斷連線的虛擬網路及公用 IP)。 當您選取按鈕時，個別篩選條件便會套用至拓撲。 例如，如果您選取 [使用中] 按鈕，拓撲會將部署中的使用中虛擬網路醒目提示。
- **拓樸**：橫幅下方的 [拓撲] 區段會顯示虛擬網路的流量分配。
    
### <a name="keyboard-navigation-on-the-banner"></a>橫幅上的鍵盤瀏覽
    
- 根據預設，系統會在虛擬網路拓樸頁面的橫幅上選擇「已連線的 Vnet」篩選條件。
- 若要移至另一個篩選條件，可使用 `Tab` 鍵順向移動。 若要反向移動，請使用 `Shift+Tab` 鍵。 順向瀏覽是由左到右，再由上到下。
- 按 `Enter` 可套用選取的篩選條件。 根據選取的篩選條件和部署，系統會在 [拓撲] 區段下將一或多個節點 (虛擬網路) 醒目提示。
- 若要切換橫幅和拓撲，請按 `Ctrl+F6`。
        
### <a name="keyboard-navigation-on-the-topology"></a>拓撲上的鍵盤瀏覽
    
- 選取橫幅上的任何篩選條件並按下 `Ctrl+F6` 後，焦點就會移至拓撲檢視中的其中一個醒目提示節點 (**VNet**)。
- 若要移至拓撲檢視上其他醒目提示節點，請使用 `Shift+Right arrow` 鍵順向移動。 
- 在醒目提示的節點上，焦點就會移至節點的 [資訊工具方塊]****。 根據預設，焦點會移至 [資訊工具方塊]**** 上的 [更多詳細資料]**** 按鈕。 若要進一步地在 [方塊]**** 檢視內移動，請分別使用 `Right arrow` 和 `Left arrow` 鍵來順向和反向移動。 按 `Enter` 的效果等同於在 [資訊工具方塊]**** 內選取焦點按鈕的效果。
- 選取任何這類節點時，可以按 `Shift+Left arrow` 鍵逐一前往其所有連線。 焦點會移至該連線的 [資訊工具方塊]****。 任何時候只要再按一次 `Shift+Right arrow`，都可以將焦點轉移回該節點。
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>如何在子網路拓撲檢視中使用鍵盤來瀏覽？

虛擬子網路拓樸頁面包含兩個主要區段：
    
- **橫幅**：虛擬子網路拓撲上方的橫幅會提供各種按鈕，可選取流量發佈篩選器 (例如使用中、媒體及閘道子網路)。 當您選取按鈕時，個別篩選條件便會套用至拓撲。 例如，如果您選取 [使用中] 按鈕，拓撲會醒目提示部署中的使用中虛擬子網路。
- **拓樸**：橫幅下方的 [拓撲] 區段會顯示虛擬子網路的流量分配。
    
### <a name="keyboard-navigation-on-the-banner"></a>橫幅上的鍵盤瀏覽
    
- 根據預設，系統會在虛擬子網路拓樸頁面的橫幅上選擇「子網路」篩選條件。
- 若要移至另一個篩選條件，可使用 `Tab` 鍵順向移動。 若要反向移動，請使用 `Shift+Tab` 鍵。 順向瀏覽是由左到右，再由上到下。
- 按 `Enter` 可套用選取的篩選條件。 根據選取的篩選條件和部署，系統會在 [拓撲] 區段下將一或多個節點 (子網路) 醒目提示。
- 若要切換橫幅和拓撲，請按 `Ctrl+F6`。
        
### <a name="keyboard-navigation-on-the-topology"></a>拓撲上的鍵盤瀏覽
    
- 選取橫幅上的任何篩選條件並按下 `Ctrl+F6` 後，焦點就會移至拓撲檢視中的其中一個醒目提示節點 (**子網路**)。
- 若要移至拓撲檢視上其他醒目提示節點，請使用 `Shift+Right arrow` 鍵順向移動。 
- 在醒目提示的節點上，焦點就會移至節點的 [資訊工具方塊]****。 根據預設，焦點會移至 [資訊工具方塊]**** 上的 [更多詳細資料]**** 按鈕。 若要進一步地在 [方塊]**** 檢視內移動，請分別使用 `Right arrow` 和 `Left arrow` 鍵來順向和反向移動。 按 `Enter` 的效果等同於在 [資訊工具方塊]**** 內選取焦點按鈕的效果。
- 選取任何這類節點時，可以按 `Shift+Left arrow` 鍵逐一前往其所有連線。 焦點會移至該連線的 [資訊工具方塊]****。 任何時候只要再按一次 `Shift+Right arrow`，都可以將焦點轉移回該節點。    

