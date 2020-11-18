---
title: 建立連線監視器-ARMClient
titleSuffix: Azure Network Watcher
description: 瞭解如何使用 ARMClient 建立連線監視器。
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
ms.openlocfilehash: 929a2feeb53e8903d675644dcb72b422eceb2858
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699095"
---
# <a name="create-a-connection-monitor-using-the-armclient"></a>使用 ARMClient 建立連線監視

瞭解如何建立連線監視器，以使用 ARMClient 監視資源之間的通訊。 它支援混合式和 Azure 雲端部署。

## <a name="before-you-begin"></a>開始之前 

在您于連線監視器中建立的連線監視器中，您可以新增內部部署機器和 Azure Vm 作為來源。 這些連線監視器也可以監視端點的連線能力。 端點可以位於 Azure 或任何其他 URL 或 IP 上。

連接監視包含下列實體：

* 連線 **監視器資源**–區域特定的 Azure 資源。 下列所有實體都是連線監視器資源的屬性。
* **端點** –參與連線能力檢查的來源或目的地。 端點的範例包括 Azure Vm、內部部署代理程式、Url 和 Ip。
* **測試** 設定–適用于測試的通訊協定特定設定。 根據您選擇的通訊協定，您可以定義埠、閾值、測試頻率及其他參數。
* **測試群組** –包含來源端點、目的地端點和測試設定的群組。 連接監視器可以包含一個以上的測試群組。
* **測試** –來源端點、目的地端點和測試設定的組合。 測試是可使用監視資料的最細微層級。 監視資料包含失敗的檢查百分比，以及 (RTT) 的來回行程時間。

    ![顯示連接監視器、定義測試群組與測試之間關聯性的圖表](./media/connection-monitor-2-preview/cm-tg-2.png)

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

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
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
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

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

* connectionMonitorName-連接監視資源的名稱

* 您要建立連線監視器之訂用帳戶的子訂用帳戶識別碼

* NW-將在其中建立 CM 的網路監看員資源識別碼 

* 將建立連線監視器的位置區域

* 端點
    * 名稱-每個端點的唯一名稱
    * resourceId –針對 Azure 端點，資源識別碼指的是虛擬機器的 Azure Resource Manager 資源識別碼。針對非 Azure 端點，資源識別碼是指連結至非 Azure 代理程式之 Log Analytics 工作區的 Azure Resource Manager 資源識別碼。
    * 位址-只有在未指定任何資源識別碼或資源識別碼為 Log Analytics 工作區時適用。 如果與 Log Analytics 資源識別碼搭配使用，這會參考可用於監視的代理程式 FQDN。 如果在沒有資源識別碼的情況下使用，則可以是任何公用端點的 URL 或 IP。
    * 篩選-針對非 Azure 端點，請使用篩選器從 Log Analytics 工作區選取將用於監視連線監視資源的代理程式。 如果未設定篩選準則，屬於 Log Analytics 工作區的所有代理程式都可以用於監視
        * 類型–將類型設定為 [代理程式位址]
        * 位址-將位址設定為內部部署代理程式的 FQDN

* 測試群組
    * 命名：命名您的測試群組。
    * testConfigurations-根據連接到目的地端點的來源端點來測試設定
    * 來源-選擇上方所建立的端點。 以 azure 為基礎的來源端點需要安裝 Azure 網路監看員延伸模組，且 nonAzure 型來源端點需要 haveAzure 已安裝的 Log Analytics 代理程式。 若要為您的來源安裝代理程式，請參閱 [安裝監視代理](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)程式。
    * 目的地-選擇上方所建立的端點。 您可以藉由將其指定為目的地，來監視 Azure Vm 或任何端點 (公用 IP、URL 或 FQDN) 的連線能力。 在單一測試群組中，您可以新增 Azure Vm、Office 365 Url、Dynamics 365 Url 和自訂端點。
    * 停用-使用此欄位可針對測試群組指定的所有來源和目的地停用監視。

* 測試組態
    * 名稱-測試設定的名稱。
    * testFrequencySec-指定來源將在您指定的通訊協定和埠上偵測目的地的頻率。 您可以選擇30秒、1分鐘、5分鐘、15分鐘或30分鐘。 來源會依據您選擇的值，測試目的地的連線能力。 例如，如果您選取30秒，則來源會在30秒內檢查目的地至少一次的連接。
    * 通訊協定-您可以選擇 TCP、ICMP、HTTP 或 HTTPS。 根據通訊協定而定，您可以進行一些特定的通訊協定特定的
    
        * preferHTTPS-指定當使用的埠不是80或443時，是否要使用 HTTPS over HTTP
        * 埠-指定您選擇的目的地埠。
        * disableTraceRoute-這適用于其通訊協定為 TCP 或 ICMP 的測試設定。 它會從探索拓撲和逐躍點的 RTT 停止來源。
        * 方法-這適用于其通訊協定為 HTTP 的測試設定。 選取 HTTP 要求方法--GET 或 POST
        * 路徑-指定要附加至 URL 的路徑參數
        * validStatusCodes-選擇適用的狀態碼。 如果回應碼與這份清單不符，您將會收到診斷訊息
        * requestHeaders-指定將會傳遞至目的地的自訂要求標頭字串
        
    * successThreshold-您可以設定下列網路參數的閾值：
        * checksFailedPercent-設定當來源使用您指定的準則來檢查目的地的連接時，可能會失敗的檢查百分比。 針對 TCP 或 ICMP 通訊協定，失敗的檢查百分比可等同于為封包遺失的百分比。 若為 HTTP 通訊協定，此欄位代表未收到回應的 HTTP 要求百分比。
        * roundTripTimeMs：設定以毫秒為單位的 RTT （以毫秒為單位），以供來源透過測試設定連接到目的地所需的時間。

## <a name="scale-limits"></a> 調整限制

連線監視器具有下列規模限制：

* 每個區域每個訂用帳戶的連線監視器上限：100
* 每個連接監視器的測試群組上限：20
* 每個連線監視的來源和目的地上限：100
* 每個連線監視的測試設定上限： 20 via ARMClient

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何分析監視資料和設定警示](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* 瞭解 [如何診斷網路中的問題](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
