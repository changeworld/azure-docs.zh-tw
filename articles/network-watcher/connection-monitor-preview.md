---
title: 連接監視器（預覽） |微軟文檔
description: 瞭解如何使用連接監視器（預覽）監視分散式環境中的網路通信。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599323"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>帶連接監視器的網路連接監控（預覽）

連接監視器（預覽）在 Azure 網路觀察程式中提供統一的端到端連接監視。 連接監視器（預覽）功能支援混合部署和 Azure 雲部署。 網路觀察程式提供了用於監視、診斷和查看 Azure 部署的連接相關指標的工具。

以下是連接監視器（預覽）的一些用例：

- 前端 Web 服務器 VM 與多層應用程式中的資料庫伺服器 VM 通信。 您希望檢查兩個 VM 之間的網路連接。
- 您希望美國東部區域的 VM ping 美國中部區域的 VM，並且希望比較跨區域網路延遲。
- 您在華盛頓西雅圖和弗吉尼亞州阿什本有多個本地辦公地點。 您的辦公室網站連接到 Office 365 URL。 對於 Office 365 URL 的使用者，請比較西雅圖和阿什本之間的延遲。
- 混合應用程式需要連接到 Azure 存儲終結點。 本地網站和 Azure 應用程式連接到相同的 Azure 存儲終結點。 您希望將本地網站的延遲與 Azure 應用程式的延遲進行比較。
- 您希望檢查本地設置與託管雲應用程式的 Azure VM 之間的連接。

在預覽階段，連接監視器結合了兩個功能中最好的功能：網路觀察程式[連接監視器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)功能和網路效能監視器 （NPM）[服務連接監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)功能。

以下是連接監視器（預覽）的一些優點：

* 滿足 Azure 和混合監視需求的統一直觀體驗
* 跨區域跨工作區連接監控
* 更高的探測頻率和更好的網路性能可見度
* 加快混合部署警報
* 支援基於 HTTP、TCP 和 ICMP 的連接檢查 
* 針對 Azure 和非 Azure 測試設定的指標和日誌分析支援

![顯示連接監視器如何與 Azure VM、非 Azure 主機、終結點和資料存儲位置進行交互的圖表](./media/connection-monitor-2-preview/hero-graphic.png)

要開始使用連接監視器（預覽）進行監視，請按照以下步驟操作： 

1. 安裝監視代理。
1. 在訂閱中啟用網路觀察程式。
1. 創建連接監視器。
1. 設置資料分析和警報。
1. 診斷網路中的問題。

以下各節詳細介紹了這些步驟。

## <a name="install-monitoring-agents"></a>安裝監控代理

連接監視器依賴于羽量級可執行檔來運行連接檢查。  它支援來自 Azure 環境和本地環境的連接檢查。 您使用的可執行檔取決於 VM 是託管在 Azure 上還是本地。

### <a name="agents-for-azure-virtual-machines"></a>Azure 虛擬機器的代理

要使連接監視器將 Azure VM 識別為監視源，請在它們上安裝網路觀察程式代理虛擬機器擴展。 此擴展也稱為*網路觀察程式擴展*。 Azure 虛擬機器需要擴展來觸發端到端監視和其他高級功能。 

創建[VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)時，可以安裝網路觀察程式擴展。 您還可以單獨安裝、配置和排除[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)和[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的網路觀察程式擴展。

網路安全性群組 （NSG） 或防火牆的規則可能會阻止源和目標之間的通信。 連接監視器檢測到此問題，並將其作為拓撲中的診斷消息顯示。 要啟用連接監視，請確保 NSG 和防火牆規則允許在源和目標之間通過 TCP 或 ICMP 資料包。

### <a name="agents-for-on-premises-machines"></a>本地電腦的代理

要使連接監視器將本地電腦識別為監視源，請在電腦上安裝日誌分析代理。 然後啟用網路效能監視器解決方案。 這些代理連結到日誌分析工作區，因此您需要在代理開始監視之前設置工作區 ID 和主金鑰。

要安裝 Windows 電腦的日誌分析代理，請參閱[Windows 的 Azure 監視器虛擬機器擴展。](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

如果路徑包括防火牆或網路虛擬裝置 （NVA），則請確保目標是可到達的。

## <a name="enable-network-watcher-on-your-subscription"></a>在訂閱中啟用網路觀察程式

使用網路觀察程式啟用具有虛擬網路的所有訂閱。 在訂閱中創建虛擬網路時，將在虛擬網路的區域和訂閱中自動啟用網路觀察程式。 此自動啟用不會影響您的資源或產生費用。 確保訂閱上未顯式禁用網路觀察程式。 

有關詳細資訊，請參閱[啟用網路觀察程式](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="create-a-connection-monitor"></a>建立連線監視 

連接監視器定期監控通信。 它會通知您可伸易道性和延遲的變化。 您還可以檢查源代理和目標終結點之間的當前和歷史網路拓撲。

源可以是 Azure VM 或具有已安裝監視代理的本地電腦。 目標終結點可以是 Office 365 URL、動態 365 URL、自訂 URL、Azure VM 資源標識、IPv4、IPv6、FQDN 或任何功能變數名稱。

### <a name="access-connection-monitor-preview"></a>訪問連接監視器（預覽）

1. 在 Azure 門戶主頁上，轉到**網路觀察程式**。
1. 在左側，在 **"監視**"部分中，選擇**連接監視器（預覽）。**
1. 您將看到在連接監視器（預覽）中創建的所有連接監視器。 要查看在連接監視器的經典體驗中創建的連接監視器，請訪問**連接監視器**選項卡。

    ![顯示在連接監視器中創建的連接監視器的螢幕截圖（預覽）](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>建立連線監視

在連接監視器（預覽）中創建的連接監視器中，可以將本地電腦和 Azure VM 添加為源。 這些連接監視器還可以監視與端點的連接。 終結點可以位於 Azure 或任何其他 URL 或 IP 上。

連接監視器（預覽）包括以下實體：

* **連接監視器資源**= 特定于區域的 Azure 資源。 以下所有實體都是連接監視器資源的屬性。
* **終結點**= 參與連接檢查的源或目標。 終結點的示例包括 Azure VM、本地代理、URL 和 IP。
* **測試組態**– 測試特定于協定的配置。 根據您選擇的協定，您可以定義埠、閾值、測試頻率和其他參數。
* **測試組**– 包含源終結點、目標終結點和測試組態的組。 連接監視器可以包含多個測試組。
* **測試**– 源終結點、目標終結點和測試組態的組合。 測試是監視資料可用的最精細級別。 監視資料包括失敗的檢查百分比和往返時間 （RTT）。

 ![顯示連接監視器的圖表，定義測試組和測試之間的關係](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>從 Azure 門戶創建連接監視器

要從 Azure 門戶創建連接監視器，請按照以下步驟操作：

1. 在左上角的連接**監視器（預覽）** 儀表板上，選擇 **"創建**"。
1. 在 **"基本"** 選項卡上，輸入連接監視器的資訊：
   * **連接監視器名稱**= 添加連接監視器的名稱。 使用 Azure 資源的標準命名規則。
   * **訂閱**• 為連接監視器選擇訂閱。
   * **區域**= 為連接監視器選擇區域。 只能選擇在此區域中創建的源 VM。
   * **工作區配置**- 工作區保存監視資料。 您可以使用自訂工作區或預設工作區。 
       * 要使用預設工作區，請選擇"核取方塊"。 
       * 要選擇自訂工作區，請清除該核取方塊。 然後，選擇自訂工作區的訂閱和區域。 
1. 在選項卡的底部，選擇 **"下一步：測試組**"。

   ![顯示連接監視器中基礎知識選項卡的螢幕截圖](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在 **"測試組**"選項卡上，選擇 **" 測試組**"。 要設置測試組，請參閱[在連接監視器中創建測試組](#create-test-groups-in-a-connection-monitor)。 
1. 在選項卡底部，選擇 **"下一步：查看 + 創建**"以查看連接監視器。

   ![顯示"測試組"選項卡和添加測試組詳細資訊的窗格的螢幕截圖](./media/connection-monitor-2-preview/create-tg.png)

1. 在 **"審閱 + 創建**"選項卡上，在創建連接監視器之前查看基本資訊和測試組。 如果需要編輯連接監視器：
   * 要編輯基本詳細資訊，請選擇鉛筆圖示。
   * 要編輯測試組，請選擇它。

   > [!NOTE] 
   > "**審核 + 創建**"選項卡顯示連接監視器預覽階段每月的成本。 目前，"**目前成本"** 列不顯示任何費用。 當連接監視器普遍可用時，此列將顯示每月費用。 
   > 
   > 即使在連接監視器預覽階段，日誌分析引入費用也會增加。

1. 準備好創建連接監視器時，在 **"審閱 + 創建**"選項卡的底部，選擇"**創建**"。

   ![連接監視器的螢幕截圖，顯示"審閱 + 創建"選項卡](./media/connection-monitor-2-preview/review-create-cm.png)

連接監視器（預覽）在後臺創建連接監視器資源。

#### <a name="create-a-connection-monitor-by-using-armclient"></a>使用 ARMClient 創建連接監視器

使用以下代碼使用 ARMClient 創建連接監視器。

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premises agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

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

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

下面是部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>在連接監視器中創建測試組

連接監視器中的每個測試組都包括通過網路參數進行測試的源和目標。 測試檢查失敗百分比和 RTT 測試組態的百分比。

在 Azure 門戶中，要在連接監視器中創建測試組，請為以下欄位指定值：

* **禁用測試組**– 您可以選擇此欄位以禁用測試組指定的所有源和目標的監視。 預設情況下，將清除此選項。
* **名稱**= 命名測試組。
* **源**– 如果在其上安裝了代理，則可以將 Azure VM 和本地電腦指定為源。 要為源安裝代理，請參閱[安裝監視代理](#install-monitoring-agents)。
   * 要選擇 Azure 代理，請選擇 **"Azure 代理"** 選項卡。在這裡，您只看到綁定到創建連接監視器時指定的區域的 VM。 預設情況下，VM 被分組到它們所屬的訂閱中。 這些組已折疊。 
   
       可以從訂閱級別向下切入到層次結構中的其他級別：

      **訂閱** > **資源組** > **VNETs** > **子網** > VM**與代理**

      您還可以按欄位更改 **"組"** 的值，以便從任何其他級別啟動樹。 例如，如果按虛擬網路分組，您將看到層次結構中具有代理**的** > **Subnets** > VM**與代理**。

      ![連接監視器的螢幕截圖，顯示"添加源"面板和 Azure 代理選項卡](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 要選擇本地代理，請選擇 **"非 Azure 代理"** 選項卡。預設情況下，按區域將代理分組到工作區中。 所有這些工作區都配置了網路效能監視器解決方案。 
   
       如果需要將網路效能監視器添加到工作區，請從[Azure 應用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)獲取它。 有關如何添加網路效能監視器的資訊，請參閱 Azure[監視器 中的監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在 **"創建連接監視器"** 視圖中，在"**基本"** 選項卡上，選擇了預設區域。 如果更改區域，則可以從新區域中的工作區中選擇代理。 您還可以**將"分組"的值（按**欄位）更改為按子網分組。

      ![連接監視器的螢幕截圖，顯示"添加源"面板和非 Azure 代理選項卡](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 要查看所選的 Azure 和非 Azure 代理，請訪問 **"審閱**"選項卡。

      ![連接監視器的螢幕截圖，顯示"添加源"面板和"查看"選項卡](./media/connection-monitor-2-preview/review-sources.png)

   * 完成源設置後，在"**添加源**"面板的底部，選擇 **"完成**"。

* **目標**– 可以通過將其指定為目標來監視與 Azure VM 或任何終結點（公共 IP、URL 或 FQDN）的連接。 在單個測試組中，可以添加 Azure VM、Office 365 URL、動態 365 URL 和自訂終結點。

    * 要選擇 Azure VM 作為目標，請選擇**Azure VM**選項卡。預設情況下，Azure VM 被分組到訂閱層次結構中，該層次結構位於"**創建連接監視器"選項卡上"創建連接監視器"** 視圖中選擇的同一區域**Basics**中。您可以更改區域並從新選擇的區域中選擇 Azure VM。 然後，可以從訂閱級別向下切入到層次結構中的其他級別，如 Azure 代理級別。

       !["添加目標"窗格的螢幕截圖，顯示 Azure VM 選項卡](./media/connection-monitor-2-preview/add-azure-dests1.png)

       !["添加目標"窗格的螢幕截圖，顯示訂閱級別](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 要選擇終結點作為目標，請選擇 **"終結點**"選項卡。終結點清單包括 Office 365 測試 URL 和 Dynamics 365 測試 URL，按名稱分組。 除了這些終結點之外，您還可以選擇在同一連接監視器中的其他測試組中創建的終結點。 
    
        要添加新終結點，請在右上角選擇 **+ 終結點**。 然後提供終結點名稱和 URL、IP 或 FQDN。

       ![顯示在連接監視器中將終結點作為目標添加位置的螢幕截圖](./media/connection-monitor-2-preview/add-endpoints.png)

    * 要查看您選擇的 Azure VM 和終結點，請選擇"**審閱**"選項卡。
    * 完成選擇目的地後，選擇 **"完成**"。

* **測試組態**– 您可以在測試組中關聯測試組態。 Azure 門戶僅允許每個測試組一個測試組態，但可以使用 ARMClient 添加更多配置。

    * **名稱**= 命名測試組態。
    * **協定**= 選擇 TCP、ICMP 或 HTTP。 要將 HTTP 更改為 HTTPS，請選擇**HTTP**作為協定，然後選擇**443**作為埠。
        * **創建網路測試組態**— 僅當在 **"協定"** 欄位中選擇**HTTP**時，才會顯示此核取方塊。 選擇此框可創建另一個測試組態，該配置使用與配置中其他位置指定的源和目標相同的源和目標。 新創建的測試組態名為`<the name of your test configuration>_networkTestConfig`。
        * **禁用跟蹤路由**- 此欄位適用于協定為 TCP 或 ICMP 的測試組。 選擇此框可阻止源發現拓撲和逐跳 RTT。
    * **目標埠**– 您可以使用您選擇的目標埠自訂此欄位。
    * **測試頻率**– 使用此欄位選擇源在指定的協定和埠上 ping 目標的頻率。 您可以選擇 30 秒、1 分鐘、5 分鐘、15 分鐘或 30 分鐘。 源將根據您選擇的值測試到目標的連接。  例如，如果選擇 30 秒，源將在 30 秒內至少檢查一次與目標的連接。
    * **成功閾值**– 您可以設置以下網路參數的閾值：
       * **檢查失敗**– 使用指定的條件設置源檢查到目標連接時可能失敗的檢查百分比。 對於 TCP 或 ICMP 協定，失敗的檢查百分比可以等同于資料包丟失百分比。 對於 HTTP 協定，此欄位表示未收到回應的 HTTP 要求的百分比。
       * **往返時間**– 將 RTT 設置為毫秒，瞭解源通過測試組態連接到目標所花的時間。
    
       ![顯示在連接監視器中設置測試組態的位置的螢幕截圖](./media/connection-monitor-2-preview/add-test-config.png)

添加到測試組的所有源、目標和測試組態都細分為單個測試。 下面是如何分解源和目標的示例：

* 測試組：TG1
* 資料來源：3（A、B、C）
* 目的地： 2 （D， E）
* 測試組態： 2 （配置 1， 配置 2）
* 創建的測試總數： 12

| 測試編號 | 來源 | Destination | 測試組態 |
| --- | --- | --- | --- |
| 1 | A | D | 配置 1 |
| 2 | A | D | 配置 2 |
| 3 | A | E | 配置 1 |
| 4 | A | E | 配置 2 |
| 5 | B | D | 配置 1 |
| 6 | B | D | 配置 2 |
| 7 | B | E | 配置 1 |
| 8 | B | E | 配置 2 |
| 9 | C | D | 配置 1 |
| 10 | C | D | 配置 2 |
| 11 | C | E | 配置 1 |
| 12 | C | E | 配置 2 |

### <a name="scale-limits"></a> 調整限制

連接監視器具有以下比例限制：

* 每個區域每個訂閱的最大連接監視器：100
* 每個連接監視器的最大測試組：20
* 每個連接監視器的最大源和目標：100
* 每個連接監視器的最大測試組態： 
    * 20 通過 ARMClient
    * 2 通過 Azure 門戶

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析監視資料並設置警報

創建連接監視器後，源會根據測試組態檢查與目標的連接。

### <a name="checks-in-a-test"></a>簽入測試

根據您在測試組態中選擇的協定，連接監視器（預覽）對源目標對運行一系列檢查。 檢查根據您選擇的測試頻率運行。

如果使用 HTTP，則服務將計算返回回應代碼的 HTTP 回應數。 結果確定失敗的檢查的百分比。 要計算 RTT，服務測量 HTTP 調用和回應之間的時間。

如果使用 TCP 或 ICMP，該服務將計算資料包丟失百分比以確定失敗檢查的百分比。 要計算 RTT，服務測量接收所發送資料包的確認 （ACK） 所佔用的時間。 如果為網路測試啟用了跟蹤路由資料，則可以看到本地網路的逐跳丟失和延遲。

### <a name="states-of-a-test"></a>測試狀態

根據檢查返回的資料，測試可以具有以下狀態：

* **通過**= 失敗檢查和 RTT 的百分比的實際值在指定的閾值內。
* **失敗**= 失敗檢查或 RTT 超出指定閾值的百分比的實際值。 如果未指定閾值，則當失敗檢查的百分比為 100 時，測試將達到"失敗"狀態。
* **警告**– 未為失敗檢查的百分比指定條件。 如果沒有指定的條件，連接監視器（預覽）將自動分配閾值。 超過該閾值時，測試狀態將更改為警告。

### <a name="data-collection-analysis-and-alerts"></a>資料收集、分析和警報

連接監視器（預覽）收集的資料存儲在日誌分析工作區中。 創建連接監視器時設置此工作區。 

監視資料在 Azure 監視器指標中也可用。 您可以使用日誌分析根據需要保留監視資料。 預設情況下，Azure 監視器僅存儲 30 天的指標。 

您可以[對資料設置基於指標的警報](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>監視儀表板

在監視儀表板上，您將看到一個連接監視器的清單，您可以訪問訂閱、區域、時間戳記、源和目標型別。

當您從網路觀察程式轉到連接監視器（預覽）時，您可以通過：

* **連接監視器**– 為訂閱、區域、時間戳記、源和目標型別創建的所有連接監視器的清單。 此檢視為預設。
* **測試組**– 為訂閱、區域、時間戳記、源和目標型別創建的所有測試組的清單。 連接監視器不會篩選這些測試組。
* **測試**= 為訂閱、區域、時間戳記、源和目標型別運行的所有測試的清單。 這些測試不會由連接監視器或測試組篩選。

在下圖中，三個資料檢視由箭頭 1 指示。

在儀表板上，可以展開每個連接監視器以查看其測試組。 然後，您可以展開每個測試組以查看在其中運行的測試。 

您可以基於：

* **頂級篩選器**– 選擇訂閱、區域、時間戳記源和目標型別。 請參閱下圖中的框 2。
* **基於狀態的篩選器**– 按連接監視器、測試組或測試的狀態進行篩選。 請參閱下圖中的箭頭 3。
* **自訂篩選器**-**選擇"全部"** 以執行通用搜索。 要按特定實體進行搜索，請從下拉清單中選擇。 請參閱下圖中的箭頭 4。

![顯示如何篩選連接監視器、測試組和連接監視器中的測試檢視的螢幕截圖（預覽版）](./media/connection-monitor-2-preview/cm-view.png)

例如，查看源 IP 為 10.192.64.56 的連接監視器（預覽）中的所有測試：
1. 將視圖更改為 **"測試**"。
1. 在搜索欄位中，鍵入*10.192.64.56*
1. 在下拉清單中，選擇 **"源**"。

要在源 IP 為 10.192.64.56 的連接監視器（預覽）中僅顯示失敗的測試：
1. 將視圖更改為 **"測試**"。
1. 對於基於狀態的篩選器，選擇 **"失敗**"。
1. 在搜索欄位中，鍵入*10.192.64.56*
1. 在下拉清單中，選擇 **"源**"。

要在目標outlook.office365.com的連接監視器（預覽）中僅顯示失敗的測試：
1. 將視圖更改為 **"測試**"。
1. 對於基於狀態的篩選器，選擇 **"失敗**"。
1. 在搜索欄位中，輸入*outlook.office365.com*
1. 在下拉清單中，選擇 **"目的地**"。

   ![顯示經過篩選以僅顯示Outlook.Office365.com目標的失敗的測試的視圖的螢幕截圖](./media/connection-monitor-2-preview/tests-view.png)

要查看 RTT 中的趨勢以及連接監視器的失敗檢查百分比：
1. 選擇要調查的連接監視器。 預設情況下，監視資料按測試組組織。

   ![顯示連接監視器指標的螢幕截圖，由測試組顯示](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 選擇要調查的測試組。

   ![顯示選擇測試組的位置的螢幕截圖](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    根據 RTT 或失敗檢查的百分比，您將看到測試組的五大失敗測試。 對於每個測試，您都會看到未通過檢查百分比的 RTT 和趨勢線。
1. 從清單中選擇一個測試，或選擇另一個測試進行調查。 對於時間間隔和失敗檢查的百分比，您將看到閾值和實際值。 對於 RTT，您將看到閾值、平均值、最小值和最大值的值。

   ![顯示 RTT 測試結果和失敗檢查百分比的螢幕截圖](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. 更改時間間隔以查看更多資料。
1. 更改視圖以查看源、目標或測試組態。 
1. 根據失敗的測試選擇源，並調查前五個失敗的測試。 例如，選擇 **"按** > **源**進行查看"和 **"按目標查看"** > **Destinations**以調查連接監視器中的相關測試。

   ![顯示前五個失敗測試的性能指標的螢幕截圖](./media/connection-monitor-2-preview/cm-drill-select-source.png)

要查看 RTT 中的趨勢以及測試組的未通過檢查的百分比：

1. 選擇要調查的測試組。 

    預設情況下，監視資料由源、目的地和測試組態（測試）排列。 稍後，您可以將視圖從測試組更改為源、目標或測試組態。 然後選擇一個實體來調查前五個失敗的測試。 例如，將視圖更改為源和目標以調查所選連接監視器中的相關測試。
1. 選擇要調查的測試。

   ![顯示選擇測試位置的螢幕截圖](./media/connection-monitor-2-preview/tg-drill.png)

    對於時間間隔和未通過檢查的百分比，您將看到閾值和實際值。 對於 RTT，您將看到閾值、平均值、最小值和最大值的值。 您還會看到所選測試的已觸發警報。
1. 更改時間間隔以查看更多資料。

要查看 RTT 中的趨勢以及測試的失敗檢查百分比：
1. 選擇要調查的源、目標和測試組態。

    對於時間間隔和失敗檢查的百分比，您將看到閾值和實際值。 對於 RTT，您將看到閾值、平均值、最小值和最大值的值。 您還會看到所選測試的已觸發警報。

   ![顯示測試單位的螢幕截圖](./media/connection-monitor-2-preview/test-drill.png)

1. 要查看網路拓撲，請選擇**拓撲**。

   ![顯示網路拓撲選項卡的螢幕截圖](./media/connection-monitor-2-preview/test-topo.png)

1. 要查看已識別的問題，請在拓撲中選擇路徑中的任何躍點。 （這些躍點是 Azure 資源。此功能當前不適用於本地網路。

   !["拓撲"選項卡上顯示選定躍點連結的螢幕截圖](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>日誌分析中的日誌查詢

使用日誌分析創建監視資料的自訂視圖。 UI 顯示的所有資料都來自日誌分析。 您可以以對話模式分析存儲庫中的資料。 關聯來自代理運行狀況的資料或日誌分析中基於的其他解決方案。 將資料匯出到 Excel 或 Power BI，或創建可共用的連結。

#### <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

在連接監視器（預覽）體驗之前創建的連接監視器中，所有四個指標都可用：百分比探測失敗、平均往返時間、檢查失敗百分比（預覽）和往返時間（預覽）。 在連接監視器（預覽）體驗中創建的連接監視器中，資料僅適用于標記為 *（預覽）的*指標。

![顯示連接監視器中指標的螢幕截圖（預覽）](./media/connection-monitor-2-preview/monitor-metrics.png)

使用指標時，將資源類型設置為 Microsoft.網路/網路觀察器/連接監視器

| 計量 | 顯示名稱 | 單位 | 彙總類型 | 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失敗的探查百分比 | 百分比 | Average | 連接監視探測器的百分比失敗。 | 無維度 |
| AverageRoundtripMs | 平均往返時間（毫秒） | 毫秒 | Average | 用於在源和目標之間發送的連接監視探測器的平均網路 RTT。 |             無維度 |
| 檢查失敗百分比（預覽） | 檢查失敗百分比（預覽） | 百分比 | Average | 測試的失敗檢查的百分比。 | 連接監視器資源 Id <br>源位址 <br>SourceName <br>源資源 Id <br>SourceType <br>通訊協定 <br>DestinationAddress <br>DestinationName <br>目標資源Id <br>DestinationType <br>DestinationPort <br>測試組名稱 <br>測試組態名稱 <br>區域 |
| 往返時間（預覽） | 往返時間 （ms） （預覽） | 毫秒 | Average | RTT 用於在源和目標之間發送的檢查。 此值不為平均值。 | 連接監視器資源 Id <br>源位址 <br>SourceName <br>源資源 Id <br>SourceType <br>通訊協定 <br>DestinationAddress <br>DestinationName <br>目標資源Id <br>DestinationType <br>DestinationPort <br>測試組名稱 <br>測試組態名稱 <br>區域 |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure 監視器中的指標警報

要在 Azure 監視器中創建警報，請執行以下計畫：

1. 選擇您在連接監視器（預覽）中創建的連接監視器資源。
1. 確保**指標**顯示為連接監視器的信號類型。
1. 在 **"添加條件**"中 **，選擇****"檢查失敗百分比（預覽）"** 或 **"往返時間"（預覽）。**
1. 對於**信號類型**，請選擇**指標**。 例如，選擇 **"檢查失敗百分比"（預覽）。**
1. 列出了指標的所有維度。 選擇維度名稱和維度值。 例如，選擇 **"源位址**"，然後在連接監視器中輸入任何源的 IP 位址。
1. 在**警報邏輯**中，填寫以下詳細資訊：
   * **條件類型**：**靜態**。
   * **條件和****閾值**。
   * **聚合細微性和評估頻率**：連接監視器（預覽）每分鐘更新資料。
1. 在 **"操作"** 中，選擇您的行動組。
1. 提供警報詳細資訊。
1. 建立警示規則。

   ![顯示 Azure 監視器中的"創建規則區域"的螢幕截圖;突出顯示"源位址"和"源終結點名稱"](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>診斷網路中的問題

連接監視器（預覽）可説明您診斷連接監視器和網路中的問題。 您之前安裝的日誌分析代理檢測到混合網路中的問題。 網路觀察程式擴展檢測到 Azure 中的問題。 

您可以在網路拓撲中查看 Azure 網路中的問題。

對於源為本地 VM 的網路，可以檢測到以下問題：

* 要求逾時。
* 未通過 DNS 解析的終結點 = 臨時或持久性。 URL 無效。
* 未找到主機。
* 源無法連接到目標。 目標無法通過ICMP到達。
* 證書相關問題： 
    * 驗證代理所需的用戶端憑證。 
    * 無法訪問證書重定位清單。 
    * 終結點的主機名稱與證書的主題或主題備用名稱不匹配。 
    * 根憑證在源的本地電腦受信任的憑證授權單位存儲中丟失。 
    * SSL 憑證已過期、無效、吊銷或不相容。

對於源為 Azure VM 的網路，可以檢測到以下問題：

* 代理問題：
    * 代理已停止。
    * DNS 解析失敗。
    * 在目標埠上沒有應用程式或攔截器。
    * 無法打開通訊端。
* VM 狀態問題： 
    * 啟動中
    * 停止中
    * 已停止
    * 正在解除配置
    * 已解除配置
    * 重 啟
    * 未分配
* 缺少 ARP 表條目。
* 由於本地防火牆問題或 NSG 規則，流量被阻止。
* 虛擬網路閘道問題： 
    * 缺少路由。
    * 兩個閘道之間的隧道已中斷連線或丟失。
    * 隧道沒有找到第二個閘道。
    * 未找到對等資訊。
* 在微軟邊緣中缺少路由。
* 由於系統路由或 UDR 而停止流量。
* 閘道連接上未啟用 BGP。
* DIP 探頭位於負載平衡器處。
