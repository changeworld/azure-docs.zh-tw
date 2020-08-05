---
title: 建立連線監視器預覽-ARMClient
titleSuffix: Azure Network Watcher
description: 瞭解如何使用 ARMClient 建立連線監視器 (預覽) 。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567897"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>使用 ARMClient 建立連接監視器 (預覽) 

瞭解如何 (預覽) 建立連線監視器，以使用 ARMClient 監視資源之間的通訊。 它支援混合式和 Azure 雲端部署。

## <a name="before-you-begin"></a>開始之前 

在您于連線監視器中建立的連線監視器 (預覽) 中，您可以將內部部署機器和 Azure Vm 新增為來源。 這些連接監視器也可以監視端點的連線。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

連線監視器 (預覽) 包含下列實體：

* 連線**監視資源**–區域特定的 Azure 資源。 下列所有實體都是連接監視器資源的屬性。
* **端點**–參與連線性檢查的來源或目的地。 端點的範例包括 Azure Vm、內部部署代理程式、Url 和 Ip。
* **測試**設定–適用于測試的通訊協定專屬設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率和其他參數。
* **測試群組**–包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試**–來源端點、目的地端點和測試設定的組合。 測試是可用的監視資料最細微的層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回時間。

    ![圖表顯示連接監視器，定義測試群組與測試之間的關聯性](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>使用範例 ARM 範本建立的步驟

使用下列程式碼，使用 ARMClient 建立連線監視器。

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

以下是部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>屬性的描述

* connectionMonitorName-連線監視資源的名稱

* 您想要建立連線監視之訂用帳戶的子訂用帳戶識別碼

* 網路監看員將建立 CM 的資源識別碼 

* 位置-將在其中建立連線監視的區域

* 端點
    * 名稱–每個端點的唯一名稱
    * resourceId –針對 Azure 端點，資源識別碼指的是虛擬機器的 Azure Resource Manager (ARM) 資源識別碼。針對非 Azure 端點，資源識別碼是指連結至非 Azure 代理程式之 Log Analytics 工作區的 ARM 資源識別碼。
    * 位址-僅適用于未指定資源識別碼或資源識別碼為 Log Analytics 工作區的情況。 如果與 Log Analytics 資源識別碼搭配使用，這會參考可用於監視之代理程式的 FQDN。 如果在沒有資源識別碼的情況下使用，則可以是任何公用端點的 URL 或 IP。
    * 篩選–針對非 Azure 端點，使用 [篩選] 從 Log Analytics 工作區中選取代理程式，以用於監視連線監視器資源。 如果未設定篩選，則屬於 Log Analytics 工作區的所有代理程式都可以用於監視
        * 類型–將類型設定為「代理程式位址」
        * 位址–將位址設定為內部部署代理程式的 FQDN

* 測試群組
    * 名稱-將您的測試群組命名為。
    * Testconfigurations.java-根據要連接至目的地端點的來源端點來測試設定
    * 來源-從上方建立的端點選擇。 以 azure 為基礎的來源端點必須已安裝 Azure 網路監看員延伸模組，且以 nonAzure 為基礎的來源端點必須 haveAzure 已安裝的 Log Analytics 代理程式。 若要為您的來源安裝代理程式，請參閱[安裝監視代理](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)程式。
    * 目的地-從上方建立的端點選擇。 您可以藉由將 Azure Vm 或任何端點的連線指定為目的地，來監視其 (的公用 IP、URL 或 FQDN) 。 在單一測試群組中，您可以新增 Azure Vm、Office 365 Url、Dynamics 365 Url 和自訂端點。
    * 停用-使用此欄位可針對測試群組指定的所有來源和目的地停用監視。

* 測試組態
    * 名稱-測試設定的名稱。
    * testFrequencySec-指定來源將在您指定的通訊協定和埠上 ping 目的地的頻率。 您可以選擇30秒、1分鐘、5分鐘、15分鐘或30分鐘。 來源會根據您所選擇的值，測試目的地的連線能力。 例如，如果您選取30秒，則來源至少會在30秒的期間內檢查目的地的連線能力。
    * 通訊協定-您可以選擇 [TCP]、[ICMP]、[HTTP 或 HTTPS]。 視通訊協定而定，您可以執行一些通訊協定特定的
        * preferHTTPS-指定是否要使用 HTTPS over HTTP
        * 埠-指定您選擇的目的地埠。
        * disableTraceRoute-這適用于通訊協定為 TCP 或 ICMP 的測試群組。 它會阻止來源探索拓撲和逐躍點的 RTT。
    * successThreshold-您可以設定下列網路參數的閾值：
        * checksFailedPercent-設定當來源使用您所指定的準則來檢查目的地的連線時，可能會失敗的檢查百分比。 針對 TCP 或 ICMP 通訊協定，失敗檢查的百分比可以等同于至封包遺失的百分比。 若為 HTTP 通訊協定，此欄位代表未收到回應的 HTTP 要求百分比。
        * roundTripTimeMs-以毫秒為單位設定 RTT，讓來源在測試設定上連接到目的地所需的時間。

## <a name="scale-limits"></a>調整限制

連接監視器具有下列調整限制：

* 每個訂用帳戶每個區域的連線監視器上限：100
* 每個連線監視的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限：20個 via ARMClient

## <a name="next-steps"></a>後續步驟

* 瞭解[如何分析監視資料及設定警示](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* 瞭解[如何診斷網路中的問題](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
