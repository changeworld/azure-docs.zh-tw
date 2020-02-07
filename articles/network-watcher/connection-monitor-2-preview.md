---
title: 連接監視器（預覽） |Microsoft Docs
description: 瞭解如何使用連接監視器（預覽）來監視分散式環境中的網路通訊
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dc705fbd17a12ee001e1e8de15b49e841f08b81
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049349"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>使用連線監視器進行整合連線監視（預覽）

連線監視器（預覽）可在 Azure 網路監看員中，為混合式和 Azure 雲端部署提供整合的端對端連線監視功能。 Azure 網路監看員提供工具來監視、診斷及查看 Azure 部署的連線相關計量。

主要使用案例：

- 您的前端網頁伺服器 VM 會與多層式應用程式中的資料庫伺服器 VM 通訊。 您想要檢查兩個 Vm 之間的網路連線。
- 您想要讓美國東部區域中的 Vm ping 美國中部區域的 Vm，並比較跨區域的網路延遲
- 您在西雅圖之類的城市中有多個內部部署辦公室網站。 連接到 Office 365 Url。 您想要比較使用來自西雅圖和阿什本之 Office 365 Url 的使用者所遇到的延遲。
- 您已設定混合式應用程式，其需要 Azure 儲存體端點的連線能力。 您想要比較內部部署網站與 Azure 應用程式之間的延遲，兩者都連接到相同的 Azure 儲存體端點。
- 您想要檢查從裝載雲端應用程式的 Azure Vm 到內部部署的連線能力。

在此預覽階段中，解決方案結合了兩個主要功能，網路監看員的連線[監視器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)和網路效能監控（NPM）的服務連線[能力監視](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)。

反映

* 適用于 Azure 和混合式監視需求的統一、直覺體驗
* 跨區域，跨工作區連線能力監視
* 更高的探查頻率和更佳的網路效能可見度
* 更快的混合式部署警示
* 支援 HTTP、TCP 和 ICMP 型連線能力檢查
* Azure 和非 Azure 測試設定的計量和 Log Analytics 支援

![連線監視器](./media/connection-monitor-2-preview/hero-graphic.png)

遵循以下所述的步驟，使用連線監視器開始監視（預覽）

## <a name="step-1-install-monitoring-agents"></a>步驟1：安裝監視代理程式

連線監視會依賴輕量可執行檔來執行連線能力檢查。  我們支援來自 Azure 和內部部署環境的連線能力檢查。 要使用的特定可執行檔取決於您的 VM 是裝載于 Azure 或內部部署環境。

### <a name="agents-for-azure-virtual-machines"></a>適用于 Azure 虛擬機器的代理程式

若要讓連線監視器將您的 Azure Vm 辨識為來源進行監視，您必須在其上安裝網路監看員代理程式虛擬機器擴充功能（也稱為網路監看員擴充功能）。 網路監看員代理程式延伸模組是在 Azure 虛擬機器上觸發端對端監視和其他先進功能的需求。 您可以[建立 VM，並](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[在其上](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)安裝網路監看員延伸模組。  您也可以分別安裝、設定和疑難排解適用于[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)和[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的網路監看員延伸模組。

如果 NSG 或防火牆規則封鎖來源與目的地之間的通訊，連線監視器會偵測到問題，並將其顯示為拓撲中的診斷訊息。 若要啟用連線監視，請確定 NSG 和防火牆規則允許來源與目的地之間的 TCP 或 ICMP 封包。

### <a name="agents-for-on-premise-machines"></a>內部部署機器的代理程式

若要讓連線監視器將您的內部部署機器辨識為監視的來源，您必須在電腦上安裝 Log Analytics 代理程式，並啟用網路效能監視解決方案。 這些代理程式會連結至 Log Analytics 工作區，並需要設定工作區識別碼和主要金鑰，才能開始進行監視。

若要安裝適用于 Windows 機器的 Log Analytics 代理程式，請遵循[此連結](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)中所述的指示

如果路徑中有防火牆或虛擬網路設備（NVA），請確定可連線到目的地。

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>步驟2：在您的訂用帳戶上啟用網路監看員

具有 VNET 的所有訂用帳戶都已啟用網路監看員。 當您在訂用帳戶中建立虛擬網路時，將會自動在該虛擬網路的區域和訂用帳戶中啟用網路監看員。 自動啟用網路監看員不會對您的資源或相關費用造成任何影響。 確定您的訂用帳戶未明確停用網路監看員。 如需詳細資訊，請參閱[Enable 網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="step-3-create-connection-monitor"></a>步驟3：建立連線監視 

連線_監視器_會定期監視通訊，並通知您來源代理程式與目的地端點之間的可連線性、延遲和網路拓撲變更。 來源可以是已安裝監視代理程式的 Azure Vm 或內部部署機器。 目的地端點可以是 Office 365 Url、Dynamics 365 Url、自訂 Url、Azure VM 資源識別碼、IPv4、IPv6、FQDN 或任何功能變數名稱。

### <a name="accessing-connection-monitor-preview"></a>存取連接監視器（預覽）

1. 從 Azure 入口網站首頁中，流覽網路監看員
2. 在網路監看員的左窗格中，按一下 [監視] 區段中的 [連接監視器（預覽）] 索引標籤。
3. 您可以看到使用連線監視器（預覽）體驗所建立的所有連線監視器。 所有使用 [連線監視器的傳統體驗] 索引標籤建立的連線監視器，都會顯示在 [連線監視器] 索引標籤中。

    ![建立連線監視](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>建立連線監視

使用連線監視器（預覽）建立的連線監視器可讓您同時新增內部部署和 Azure Vm 做為來源，並監視端點的連線能力，以跨越 Azure 或任何其他 URL/IP。

以下是連接監視器中的實體：

* 連線監視資源–區域特定的 Azure 資源。 以下所述的所有實體都是連接監視器資源的屬性。
* 端點–參與連線性檢查的所有來源和目的地都會被稱為端點。 端點的範例-Azure Vm、內部部署代理程式、Url、Ip
* 測試設定–每個測試設定都是特定通訊協定。 根據選擇的通訊協定，您可以定義埠、閾值、測試頻率和其他參數
* 測試群組–每個測試群組都包含來源端點、目的地端點和測試設定。 每個連接監視器可以包含一個以上的測試群組
* 測試–來源端點、目的地端點和測試設定的組合進行一項測試。 測試是監視資料（檢查失敗% 和 RTT）可用的最低層級

 ![建立連線監視](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>從入口網站

若要建立連線監視器，請遵循下面所述的步驟：

1. 在 [連接監視器（預覽）] 儀表板中，按一下左上角的 [建立]。
2. 在 [基本] 索引標籤中，輸入連接監視的資訊
   1. 連線監視名稱–連線監視器的名稱。 Azure 資源的標準命名規則適用于這裡。
   2. 訂用帳戶–選擇連線監視的訂用帳戶。
   3. 區域–選擇連接監視資源的區域。 您只能選取在此區域中建立的來源 Vm。
   4. 工作區設定-您可以使用連接監視器所建立的預設工作區，按一下預設核取方塊來儲存監視資料。 若要選擇自訂工作區，請取消核取此方塊。 選擇 [訂用帳戶] 和 [區域] 以選取工作區，這會保留您的監視資料。
   5. 按一下 [下一步：測試群組] 以加入測試群組

      ![建立連線監視](./media/connection-monitor-2-preview/create-cm-basics.png)

3. 在 [測試群組] 索引標籤中，按一下 [+ 測試群組] 以加入測試群組。 使用 [_連接監視器] 中的 [建立測試群組_] 來加入測試群組。 按一下 [審查 + 建立] 以查看您的連線監視器。

   ![建立連線監視](./media/connection-monitor-2-preview/create-tg.png)

4. 在 [審查 + 建立] 索引標籤中，建立連線監視器之前，請先查看基本資訊和測試群組。 若要從 [審查 + 建立] 視圖編輯連線監視器：
   1. 若要編輯基本的詳細資料，請使用影像2中 box 1 所指定的鉛筆圖示
   2. 若要編輯個別的測試群組，請按一下您想要編輯的測試群組，以便在編輯模式中開啟測試群組。
   3. 目前的成本/月表示預覽期間的成本。 使用「連線監視器」目前不會收費，因此此資料行會顯示零。 實際成本/月表示在正式運作後將收取的價格。 請注意，即使在預覽期間，log analytics 的內嵌費用仍適用。

5. 在 [審查 + 建立] 索引標籤中，按一下 [建立] 按鈕以建立連線監視器。

   ![建立連線監視](./media/connection-monitor-2-preview/review-create-cm.png)

6.  連線監視（預覽）會在背景中建立連線監視器資源。

#### <a name="from-armclient"></a>從 Armclient

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

address: '\&lt;FQDN of your on-premise agent'

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

部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>在連接監視器中建立測試群組

「連線監視器」中的每個測試群組都包含來源和目的地，可在檢查失敗的網路參數和測試設定的 RTT 上進行測試。

#### <a name="from-portal"></a>從入口網站

若要在連線監視器中建立測試群組，請指定以下所述欄位的值：

1. 停用測試群組–核取此欄位將會停用監視測試群組中指定的所有來源和目的地。 根據預設，您會看到此選項未核取。
2. 名稱–測試群組的名稱
3. 來源–如果代理程式已安裝在其中，您可以將 Azure Vm 和內部部署機器指定為來源。 請參閱步驟1，為您的來源安裝特定的代理程式。
   1. 按一下 [Azure 代理程式] 索引標籤以選取 [Azure 代理程式]。 您只會看到列出的 Vm 已系結至您在建立連線監視器時所指定的區域。 根據預設，Vm 會分組成其所屬的訂用帳戶，而群組則會折迭。 您可以向下切入至階層中的其他層級：

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      您也可以變更 [群組依據] 欄位的值，從任何其他層級啟動樹狀結構。 例如： Group by – VNET 會在階層 Vnet 中顯示具有代理程式的 Vm-\&gt;子網-\&gt;具有代理程式的 Vm。

      ![新增來源](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. 按一下 [非 Azure 代理程式] 索引標籤，選取 [內部部署代理程式]。 根據預設，您會看到代理程式分組成一個區域中的工作區。只會列出已設定網路效能監控解決方案的工作區。 從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) 將網路效能監視器解決方案新增至您的 OMS 工作區。 您也可以使用[從方案庫新增 Azure 監視器解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)中所述的程式。根據預設，您會在 [建立連線監視器] 視圖的 [基本資訊] 索引標籤中看到選取的區域。 您可以變更區域，並從新選取的區域中選擇 [工作區中的代理程式]。 您也可以將 [群組依據] 欄位的值變更為 [群組于子網]。

      ![非 Azure 來源](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. 按一下 [審查]，以查看選取的 Azure 和非 Azure 代理程式。

      ![審查來源](./media/connection-monitor-2-preview/review-sources.png)

   4. 當您完成選取來源之後，請按一下 [完成]。

4. 目的地–您可以藉由將 Azure Vm 或任何端點（公用 IP、URL、FQDN）指定為目的地，來監視其連線能力。 在單一測試群組中，您可以新增 Azure Vm、O365 Url、D365 移轉 Url 或自訂端點。

   1. 按一下 [Azure Vm] 索引標籤，選取 [Azure Vm] 作為 [目的地]。 根據預設，您會在 [建立連線監視器] 視圖的 [基本資訊] 索引標籤中所選取的相同區域中，看到 Azure Vm 群組為訂用帳戶階層。 您可以變更區域，並從新選取的區域中選擇 [Azure Vm]。 您可以從訂用帳戶層級向下切入至階層中的其他層級，例如 Azure 代理程式。

      ![新增目的地](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![新增目的地2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. 按一下 [端點] 索引標籤，選取 [Azure Vm] 作為 [目的地]。 端點清單將會填入 O365 和 D365 移轉測試 Url，並依名稱分組。  您也可以在相同的連線監視器中選擇在其他測試群組中建立的端點。 若要新增端點，請按一下螢幕右上角的 [+ 端點]，並提供端點 URL/IP/FQDN 和名稱

      ![新增端點](./media/connection-monitor-2-preview/add-endpoints.png)

   3. 按一下 [審查]，以查看選取的 Azure 和非 Azure 代理程式。
   4. 當您完成選取來源之後，請按一下 [完成]。

5. 測試設定–您可以在指定的測試群組中建立任意數目的測試設定。 入口網站會針對每個測試群組將它限制為一個測試設定，但使用 Armclient 來新增更多。
   1. 名稱–測試設定的名稱
   2. 通訊協定–您可以選擇 [TCP]、[ICMP] 或 [HTTP]。 若要將 HTTP 變更為 HTTPS，請選取 [HTTP 身分通訊協定] 和 [443] 作為埠
   3. 建立網路測試設定-只有當您在 [通訊協定] 欄位中選取 [HTTP] 時，才會看到此核取方塊。 啟用此欄位，以使用步驟3和4透過 TCP/IP 通訊協定所指定的相同來源和目的地，建立另一個測試設定。 新建立的測試設定名稱為 "\&lt; 在5中指定的名稱。\&gt;\_networkTestConfig」
   4. 停用追蹤路由–此欄位將適用于具有 TCP 或 ICMP 作為通訊協定的測試群組。  請檢查此欄位，以防止來源探索拓撲和逐躍點的來回行程時間。
   5. 目的地埠–您可以自訂此欄位以放在您選擇的目的地埠中。
   6. 測試頻率–此欄位會決定來源在上述通訊協定和埠上 ping 目的地的頻率。 您可以選擇30秒、1分鐘、5分鐘、15分鐘和30分鐘。 來源會根據您選擇的值，測試目的地的連線能力。  例如，如果您選取30秒，則來源至少會在30秒的期間內檢查目的地的連線能力。
   7. 健康情況閾值–您可以在下面所述的網路參數上設定閾值
      1. 檢查失敗的百分比-當來源透過上述指定的準則來檢查目的地的連線時，檢查失敗的百分比。 針對 TCP/ICMP 通訊協定，% 中的檢查失敗可能會等同于到封包遺失%。 針對 HTTP 通訊協定，此欄位代表未收到回應的 HTTP 要求數目。
      2. RTT （以毫秒為單位）–當來源透過上述指定的測試設定連線到目的地時的來回時間（以毫秒為單位）。

      ![新增 TG](./media/connection-monitor-2-preview/add-test-config.png)

所有新增至測試群組並已指定測試設定的來源和目的地，都會細分為個別的測試。 例如：

* 測試群組： TG1
* 來源：3（A、B、C）
* 目的地：2（D，E）
* 測試設定：2（Config 1、Config 2）
* 已建立的測試：總計 = 12

| **測試編號** | **Source** | **目的地** | **測試設定名稱** |
| --- | --- | --- | --- |
| 1 | A | D | 設定1 |
| 2 | A | D | Config 2 |
| 3 | A | E | 設定1 |
| 4 | A | E | Config 2 |
| 5 | B | D | 設定1 |
| 6 | B | D | Config 2 |
| 7 | B | E | 設定1 |
| 8 | B | E | Config 2 |
| 9 | C | D | 設定1 |
| 10 | C | D | Config 2 |
| 11 | C | E | 設定1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>調整限制

* 每個區域每個訂用帳戶的連線監視數目上限–100
* 每個連線監視的最大測試群組數目-20
* 最大 # 個來源 + 每個連線的目的地監視–100
* 每個連線監視的測試設定數目上限–20透過 Armclient。 2透過入口網站。

## <a name="step-4--data-analysis-and-alerts"></a>步驟4：資料分析和警示

建立連線監視器之後，來源會根據指定的測試設定來檢查目的地的連線能力。

### <a name="checks-in-a-test"></a>測試中的簽入

根據使用者在測試設定中選取的通訊協定，連線監視器（預覽）會在所選的測試頻率執行一系列的來源目的地配對檢查。

如果選取 [HTTP]，服務會計算傳迴響應碼以判斷檢查失敗% 的 HTTP 回應數目。  為了計算 RTT，我們測量接收 HTTP 呼叫回應所花費的時間。

如果選取 [TCP] 或 [ICMP]，服務會計算封包% 來判斷檢查失敗%。 為了計算 RTT，我們測量接收傳送之封包的 ACK 所花費的時間。 如果您已啟用網路測試的追蹤路由資料，您可以看到內部部署網路的逐躍點遺失和延遲。

### <a name="states-of-a-test"></a>測試的狀態

根據測試中的檢查所傳回的資料，每個測試都可以有下列狀態：

* Pass = 當檢查的實際值失敗%，而 RTT 在指定的閾值內
* Fail = 當檢查的實際值失敗時，% 或 RTT 跨指定的閾值。 如果未指定任何閾值，當檢查失敗% = 100% 時，就會將測試標示為失敗
* 警告–未指定檢查失敗的條件%。 在這種情況下，連線監視器（預覽）會使用自動設定準則做為臨界值，而當該閾值違反時，測試的狀態會設定為「警告」

### <a name="data-collection-analysis-and-alerts"></a>資料收集、分析和警示

連接監視器（預覽）所收集的所有資料都會儲存在連線監視器建立時所設定的 Log Analytics 工作區中。 Azure 監視器計量也提供監視資料。 您可以視需要使用 Log Analytics 來保留您的監視資料，但 Azure 監視器預設會儲存30天的計量 **。** 接著，您可以[針對資料設定](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)計量型警示。

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>監視連接監視解決方案中的儀表板

您會看到您的連線監視器清單，您可以存取指定的訂用帳戶、區域、時間戳記、來源及目的地類型的選項。

當您從網路監看員服務流覽至 [連接監視器（預覽）] 時，您可以選擇 [**查看依據**]：

* 連線監視（預設）–針對所選訂用帳戶、區域、時間戳記、來源及目的地類型所建立的所有連線監視器清單
* 測試群組–針對所選訂用帳戶、區域、時間戳記、來源及目的地類型所建立的所有測試群組清單。 這些測試群組不會在連接監視器上進行篩選
* 測試–針對所選訂閱、區域、時間戳記、來源及目的地類型執行的所有測試清單。 這些測試不會在連接監視器或測試群組上進行篩選。

您可以將每個連接監視器展開至測試群組，並將每個測試群組擴充到儀表板中執行的各種個別測試。 在下圖中標示為 [1]。

您可以根據下列內容篩選這份清單：

* 最上層篩選-訂用帳戶、區域、時間戳記來源及目的地類型。 在下圖中標示為 [2]。
* 以狀態為基礎的篩選-第二層篩選連接監視/測試群組/測試的狀態。 在下圖中標示為 [3]。
* 搜尋欄位–選擇 [全部] 以進行一般搜尋。 若要在任何特定實體上進行搜尋，請使用下拉式清單縮小搜尋結果的範圍。 在下圖中標示為 [4]。

![篩選測試](./media/connection-monitor-2-preview/cm-view.png)

例如：

1. 若要查看所有連線監視器（預覽）中的所有測試，其中來源 IP = 10.192.64.56：
   1. 將視圖變更為「測試」
   2. 搜尋已歸檔 = 10.192.64.56
   3. 使用 [值] 旁的下拉式清單選取 [來源]
2. 僅篩選出所有連線監視器（預覽）中的失敗測試，其中來源 IP = 10.192.64.56
   1. 將視圖變更為「測試」
   2. 從以狀態為基礎的篩選中選取 [失敗]。
   3. 搜尋欄位 = 10.192.64.56
   4. 使用 [值] 旁的下拉式清單選取 [來源]
3. 僅篩選出目的地為 outlook.office365.com 的所有連線監視器（預覽）中的失敗測試
   1. 將視圖變更為「測試」
   2. 從以狀態為基礎的篩選中選取 [失敗]。
   3. 搜尋欄位 = outlook.office365.com
   4. 使用 [值] 旁的下拉式清單選取 [目的地]

   ![失敗的測試](./media/connection-monitor-2-preview/tests-view.png)

若要查看失敗的% 和 RTT 檢查的趨勢：

1. 連線監視器
   1. 按一下您想要詳細調查的連線監視器
   2. 根據預設，您將會藉由「測試群組」來查看監視資料

      ![查看度量依據](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. 選擇您想要詳細調查的測試群組

      ![計量（依 TG）](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. 針對您在上一個步驟中選擇的測試群組，您會看到前5項失敗的測試檢查失敗的% 或 RTT 毫秒。 針對每個測試，您會看到失敗的檢查趨勢線% 和 RTT 毫秒
   5. 從上述清單中選取測試，或選擇另一個要詳細調查的測試。
   6. 針對選取的時間間隔，針對 [檢查失敗%]，您會看到閾值和實際值。 針對 RTT 毫秒，您會看到 [閾值]、[平均]、[最小] 和 [最大值]。

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. 變更時間間隔以查看更多資料
  8. 您可以在步驟 b 中變更此視圖，並選擇依來源、目的地或測試設定來進行查看。 然後根據失敗的測試選擇來源，並調查前5個失敗的測試。  例如：選擇 [View by：來源] 和 [目的地]，以調查在選取的連線監視器中該組合之間執行的所有測試。

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. 測試群組
   1. 按一下您要詳細調查的測試群組
   2. 根據預設，您會依「來源 + 目的地 + 測試設定（測試）」來查看監視資料

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. 選擇您想要詳細調查的測試
   4. 針對選取的時間間隔，針對 [檢查失敗%]，您會看到閾值和實際值。 針對 RTT 毫秒，您會看到 [閾值]、[平均]、[最小] 和 [最大值]。 您也會看到所選測試特定的已引發警示。
   5. 變更時間間隔以查看更多資料
   6. 您可以在步驟 b 中變更此視圖，並選擇依來源、目的地或測試設定來進行查看。 然後選擇一個實體來調查前5個失敗的測試。  例如：選擇 [View by：來源] 和 [目的地]，以調查在選取的連線監視器中該組合之間執行的所有測試。

3. 測試
   1. 按一下您想要詳細調查的來源 + 目的地 + 測試設定
   2. 針對選取的時間間隔，針對 [檢查失敗%]，您會看到閾值和實際值。 針對 RTT 毫秒，您會看到 [閾值]、[平均]、[最小] 和 [最大值]。 您也會看到所選測試特定的已引發警示。

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. 您也可以按一下 [拓撲]，以查看任何時間點的網路拓撲。

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. 您可以按一下 [Azure 網路] 的任何連結躍點，以查看連線監視器所識別的問題。 這項功能目前不適用於內部部署網路。

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Azure 監視器 Log Analytics 中的記錄查詢

使用 Log Analytics 來建立監視資料的自訂視圖。 UI 中顯示的所有資料都會從 Log Analytics 填入。 您可以對存放庫中的資料執行互動式分析，並將來自不同來源（例如代理程式健全狀況和其他 Log Analytics 型應用程式）的資料相互關聯。 您也可以將資料匯出至 Excel、Power BI 或可共用的連結。

#### <a name="metrics-in-azure-monitor"></a>Azure 監視器中的計量

對於在連線監視器（預覽）體驗之前建立的連線監視器，會提供所有4個計量。 對於透過連線監視器（預覽）體驗所建立的連線監視器，資料僅適用于以 "（Preview）" 標記的計量。

資源類型-Microsoft. Network/networkWatchers/connectionMonitors

| 計量 | 計量顯示名稱 | 單位 | 彙總類型 | 描述 | 維度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失敗的探查百分比 | 百分比 | Average | 失敗的連線能力監視探查百分比 | 無維度 |
| AverageRoundtripMs | 平均來回時間（毫秒） | 毫秒 | Average | 在來源與目的地之間所傳送連線能力監視探查的平均網路來回時間 (毫秒) |             無維度 |
| ChecksFailedPercent （預覽） | % 檢查失敗（預覽） | 百分比 | Average | 測試的檢查失敗% | * ConnectionMonitorResourceId <br> * SourceAddress <br> * 已的 <br> * SourceResourceId <br> * SourceType <br> * 通訊協定 <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * 區域 |
| RoundTripTimeMs （預覽） | 來回時間（毫秒）（預覽） | 毫秒 | Average | 在來源與目的地之間傳送之檢查的來回時間（毫秒）。 此值不是平均 | * ConnectionMonitorResourceId <br> * SourceAddress <br> * 已的 <br> * SourceResourceId <br> * SourceType <br> * 通訊協定 <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * 區域 |

 ![監視計量](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Azure 監視器中的計量警示

建立警示：

1. 選擇使用連接監視器建立的連線監視器資源（預覽）
2. 確定 [計量] 顯示為上一個步驟中所選取資源的 [信號類型]
3. 在 [新增條件] 中，選擇 [信號名稱] 為 [ChecksFailedPercent （預覽）] 或 [RoundTripTimeMs （預覽）]，並將 [信號類型] 例如：選擇 ChecksFailedPercent （預覽）
4. 所有適用于每個計量的維度都會列出。  選擇維度名稱和維度值。 例如：選擇 [來源位址]，並提供任何與步驟1中所選連線監視器資源相關的來源 IP 位址
5. 在 [警示邏輯] 中，選擇：
   1. 條件類型-靜態
   2. 條件和閾值
   3. 匯總細微性和評估頻率–連線監視器（預覽）每隔1分鐘會更新資料。
6.  在 [動作] 中，選擇您的動作群組
7. 提供警示詳細資料
8. 建立警示規則

   ![警示](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>步驟5：診斷網路中的問題

連線監視可協助您診斷與連線監視器資源和網路中對應的問題。 您在步驟1中安裝的 Log Analytics 代理程式會偵測到您混合式網路中的問題，且網路監看員延伸模組將會偵測到 Azure 中的問題。  混合式網路中的問題將會顯示在 [診斷] 頁面中，且 Azure 網路中的問題將會顯示在網路拓撲中。

針對具有內部部署 Vm 作為來源的網路，我們偵測到：

* 要求逾時
* DNS 未解析的端點–暫時性或持續性。 URL 無效。
* 找不到任何主機。
* 來源無法連接到目的地。 無法透過 ICMP 到達目標。
* 憑證相關問題-需要用戶端憑證才能驗證代理程式、無法存取憑證重新配置清單、端點的主機名稱不符合憑證的主體或主體替代名稱、來源的本機電腦信任的憑證授權單位單位存放區中遺失的根憑證、SSL 憑證已過期/無效/已撤銷、不相容

針對具有 Azure Vm 的網路是來源，我們偵測到：

* 代理程式問題–代理程式已停止，DNS 解析失敗，沒有在目的地埠上接聽的應用程式/接聽程式，無法開啟通訊端
* VM 狀態問題–啟動、停止、停止、解除配置、取消配置、重新開機、未配置
* 缺少 ARP 資料表專案
* 因為本機防火牆問題而封鎖的流量，NSG 規則
* VNET 閘道-遺失路由，兩個閘道之間的通道已中斷連線或遺失，或通道未找到第二個閘道，找不到任何對等資訊
* MS Edge 中缺少路由。
* 流量因系統路由或 UDR 而停止
* 未在閘道連線上啟用 BGP
* DIP 探查會在 Load Balancer
