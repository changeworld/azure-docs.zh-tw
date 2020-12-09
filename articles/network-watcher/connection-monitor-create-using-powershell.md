---
title: 建立連線監視器-PowerShell
titleSuffix: Azure Network Watcher
description: 瞭解如何使用 PowerShell 建立連線監視器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: 1d5f879ead35ef6d47b993ff833dc0b0595e3c6c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861913"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>使用 PowerShell 建立連線監視器

瞭解如何使用 Azure 網路監看員的連線監視功能，來監視您的資源之間的通訊。


## <a name="before-you-begin"></a>開始之前

在使用連線監視器建立的連線監視器中，您可以將內部部署機器和 Azure 虛擬機器 (Vm) 作為來源。 這些連線監視器也可以監視端點的連線能力。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

連接監視包含下列實體：

* 連線 **監視器資源**：區域特定的 Azure 資源。 下列所有實體都是連線監視器資源的屬性。
* **端點**：參與連接檢查的來源或目的地。 端點的範例包括 Azure Vm、內部部署代理程式、Url 和 Ip。
* **測試** 設定：測試的通訊協定特定設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率及其他參數。
* **測試群組**：包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試**：來源端點、目的地端點和測試設定的組合。 測試是可使用監視資料的最細微層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回行程時間。

    ![顯示連接監視器的圖表，定義測試群組和測試之間的關聯性。](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>建立連線監視器的步驟

使用下列命令，以使用 PowerShell 來建立連線監視器。

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>屬性的描述

* **ConnectionMonitorName**：連接監視資源的名稱。

* **SUB**：您要建立連線監視器之訂用帳戶的訂用帳戶識別碼。

* **NW**：建立連線監視器的網路監看員資源識別碼。

* **Location**：建立連線監視器的區域。

* **端點**
    * **名稱**：每個端點的唯一名稱。
    * **資源識別碼**：適用于 Azure 端點，資源識別碼指的是 vm 的 AZURE RESOURCE MANAGER 資源識別碼。 針對非 Azure 端點，資源識別碼是指連結至非 Azure 代理程式之 Log Analytics 工作區的 Azure Resource Manager 資源識別碼。
    * **位址**：僅適用于未指定資源識別碼或資源識別碼位於 Log Analytics 工作區的情況。 如果未使用資源識別碼，則可以是任何公用端點的 URL 或 IP。 如果與 Log Analytics 資源識別碼搭配使用，這會參考監視代理程式的 FQDN。
    * **篩選**：對於非 Azure 端點，請使用篩選器從連線監視資源中的 Log Analytics 工作區選取監視代理程式。 如果未設定篩選準則，屬於 Log Analytics 工作區的所有代理程式都可以用來進行監視。
        * **類型**：設定為 **代理程式位址**。
        * **位址**：設定為內部部署代理程式的 FQDN。

* **測試群組**
    * **名稱**：命名您的測試群組。
    * **來源**：選擇您稍早建立的端點。 以 azure 為基礎的來源端點需要安裝 Azure 網路監看員擴充功能;非 Azure 型來源端點需要安裝 Azure Log Analytics 代理程式。 若要為您的來源安裝代理程式，請參閱 [安裝監視代理](./connection-monitor-overview.md#install-monitoring-agents)程式。
    * **目的地**：選擇您稍早建立的端點。 您可以藉由將其指定為目的地，來監視 Azure Vm 或任何端點 (公用 IP、URL 或 FQDN) 的連線能力。 在單一測試群組中，您可以新增 Azure Vm、Office 365 Url、Dynamics 365 Url 和自訂端點。
    * **停** 用：針對測試群組指定的所有來源和目的地停用監視。

* **測試設定**
    * **名稱**：為測試設定命名。
    * **TestFrequencySec**：指定來源在您指定的通訊協定和埠上偵測目的地的頻率。 您可以選擇30秒、1分鐘、5分鐘、15分鐘或30分鐘。 來源會依據您選擇的值來測試目的地的連線能力。 例如，如果您選取30秒，則來源會在30秒內檢查目的地至少一次的連接。
    * **通訊協定**：選擇 [TCP]、[ICMP]、[HTTP] 或 [HTTPS]。 根據通訊協定，您也可以選取下列通訊協定特定設定：
        * **preferHTTPS**：指定是否要在 HTTP 上使用 HTTPS。
        * **埠**：指定您選擇的目的地埠。
        * **disableTraceRoute**：從探索拓撲和逐躍點 RTT 停止來源。 這適用于具有 TCP 或 ICMP 的測試群組。
        * **方法**：選取 (取得或張貼) 的 HTTP 要求方法。 這適用于使用 HTTP 的測試設定。
        * **路徑**：指定要附加至 URL 的路徑參數。
        * **validStatusCodes**：選擇適用的狀態碼。 如果回應碼不相符，則會顯示診斷訊息。
        * **requestHeaders**：指定傳遞至目的地的自訂要求標頭字串。
    * **成功閾值**：設定下列網路參數的閾值：
        * **checksFailedPercent**：設定當來源使用您指定的準則來檢查目的地的連接時，可能會失敗的檢查百分比。 針對 TCP 或 ICMP 通訊協定，失敗的檢查百分比可能等於封包遺失的百分比。 若為 HTTP 通訊協定，此欄位代表未收到回應的 HTTP 要求百分比。
        * **roundTripTimeMs**：設定在測試設定（以毫秒為單位）連接到目的地時，來源可花費的時間長度。

## <a name="scale-limits"></a> 調整限制

連線監視器具有下列規模限制：

* 每個區域每個訂用帳戶的連線監視器上限：100
* 每個連接監視器的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限：20

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何分析監視資料和設定警示](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)。
* 瞭解 [如何診斷網路中的問題](./connection-monitor-overview.md#diagnose-issues-in-your-network)。