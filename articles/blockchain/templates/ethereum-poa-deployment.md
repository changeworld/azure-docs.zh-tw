---
title: 在 Azure 上部署乙太坊證明聯盟解決方案範本
description: 使用乙太坊證明聯盟解決方案，在 Azure 上部署和設定多成員的聯盟乙太坊網路
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: d1d3ad94957e791b2178b6c60d4c7debdec2b391
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283423"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>在 Azure 上部署乙太坊證明聯盟解決方案範本

您可以使用 [乙太坊證明協會預覽版 azure 解決方案範本](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) ，以最少量的 Azure 和乙太坊知識來部署、設定和管理多重成員協會證明乙太坊網路。

每個聯盟成員都可以使用解決方案範本，利用 Microsoft Azure 計算、網路和儲存體服務來布建區塊鏈網路使用量。 每個聯盟成員的網路使用量都包含一組負載平衡的驗證程式節點，應用程式或使用者可與其互動以提交乙太坊交易。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>選擇 Azure 區塊鏈解決方案

選擇使用乙太坊證明聯盟解決方案範本之前，請將您的案例與可用 Azure 區塊鏈選項的常見使用案例進行比較。

> [!IMPORTANT]
> 請考慮使用 [Azure 區塊鏈服務](../service/overview.md) 而不是 Azure 解決方案範本上的乙太坊。 Azure 區塊鏈服務是受支援的受控 Azure 服務。 同位乙太坊轉換成以團體導向的開發與維護。 如需詳細資訊，請參閱將同位檢查 [轉換乙太坊至 OPENETHEREUM DAO](https://www.parity.io/parity-ethereum-openethereum-dao/)。

選項 | 服務模型 | 常見使用案例
-------|---------------|-----------------
解決方案範本 | IaaS | 解決方案範本是 Azure Resource Manager 範本，可用來布建完整設定的區塊鏈網路拓撲。 範本會針對指定的區塊鏈網路類型，部署及設定 Microsoft Azure 計算、網路和儲存體服務。 解決方案範本是在沒有服務等級協定的情況下提供。 使用 [Microsoft 問與答頁面](/answers/topics/azure-blockchain-workbench.html)來取得支援。
[Azure 區塊鏈服務](../service/overview.md) | PaaS | Azure 區塊鏈服務 Preview 簡化了聯盟區塊鏈網路的構成、管理和治理。 針對需要 PaaS、聯盟管理或合約和交易隱私權的解決方案，使用 Azure 區塊鏈服務。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 預覽是 Azure 服務和功能的集合，目的是要協助您建立及部署區塊鏈應用程式，以與其他組織共用商務程序和資料。 使用 Azure Blockchain Workbench 來建立區塊鏈解決方案的原型，或區塊鏈應用程式概念證明。 Azure Blockchain Workbench 目前沒有服務等級合約。 使用 [Microsoft 問與答頁面](/answers/topics/azure-blockchain-workbench.html)來取得支援。

## <a name="solution-architecture"></a>方案架構

使用乙太坊解決方案範本，您可以部署單一或多個區域的單一或多個區域的乙太坊證明聯盟網路。

![部署架構](./media/ethereum-poa-deployment/deployment-architecture.png)

每個聯盟成員部署都包含：

* 執行 PoA 驗證器的虛擬機器
* 用於散發 RPC、對等互連和治理管 dapp 要求的 Azure Load Balancer
* 保護驗證器身分識別的 Azure Key Vault
* 裝載持續性網路資訊和協調租用的 Azure 儲存體
* 彙總記錄和效能統計資料的 Azure 監視器
* 允許透過私人 VNet 進行 VPN 連線的 VNet 閘道 (選擇性)

根據預設，RPC 和對等互連端點可透過公用 IP 來存取，以簡化跨訂用帳戶和雲端的連線。 針對應用層級存取控制，您可以使用同位 [的授權合約](https://wiki.parity.io/Permissioning)。 支援在 Vpn 後方部署的網路，以利用 VNet 閘道來進行跨訂用帳戶連線能力。 由於 VPN 和 VNet 部署更為複雜，因此您可能會想要在建立解決方案的原型時，從公用 IP 模型著手。

Docker 容器用於可靠性和模組化。 Azure Container Registry 用來在每個部署中裝載和提供已建立版本的映射。 容器映像包含：

* Orchestrator-產生身分識別和治理合約。 將身分識別儲存在身分識別存放區中。
* 身分識別存放區中的同位用戶端租用身分識別。 探索並連接至對等。
* EthStats 代理程式-透過 RPC 收集本機記錄和統計資料，並將資訊推送至 Azure 監視器。
* 治理管 dapp-用於與治理合約互動的 Web 介面。

### <a name="validator-nodes"></a>驗證程式節點

在權威證明通訊協定中，驗證器節點會取代傳統的礦工節點。 每個驗證程式都有唯一的乙太坊身分識別，可讓它參與封鎖建立程式。 每個聯盟成員可在五個區域間佈建兩個或更多驗證器節點，以達到異地備援的目的。 驗證器節點會與其他驗證器節點通訊，使基礎分散式總帳的狀態達到一致。 為了確保公平參與網路，每個聯盟成員都被禁止使用比網路上第一個成員更多的驗證程式。 例如，如果第一個成員部署三個驗證程式，則每個成員最多隻能有三個驗證程式。

### <a name="identity-store"></a>身分識別存放區

身分識別存放區會部署在每個成員的訂用帳戶中，安全保存所產生的乙太坊身分識別。 針對每個驗證程式，協調流程容器會產生乙太坊私密金鑰，並將其儲存在 Azure Key Vault 中。

## <a name="deploy-ethereum-consortium-network"></a>部署乙太坊聯盟網路

在此逐步解說中，讓我們假設您要建立多方乙太坊聯盟網路。 下列流程是多方部署的範例：

1. 三個成員分別使用 MetaMask 產生了以太坊帳戶
1. *成員 A* 部署乙太坊 PoA，提供其乙太坊公用位址
1. *成員 A* 將聯盟 URL 提供給*成員 B* 和*成員 C*
1. *成員 B* 和*成員 C* 提供其以太坊公用位址和*成員 A* 的聯盟 URL，部署了以太坊 PoA
1. *成員 A* 將*成員 B* 票選為管理員
1. *成員 A* 和*成員 B* 都將*成員 C* 票選為管理員

下一節會示範如何設定網路中第一個成員的使用量。

### <a name="create-resource"></a>建立資源

在 [ [Azure 入口網站](https://portal.azure.com)中，選取左上角的 [ **建立資源** ]。

選取**區塊鏈**  >  **乙太坊證明聯盟 (預覽) **。

### <a name="basics"></a>基本概念

在 [ **基本**] 下，為任何部署指定標準參數的值。

![基本概念](./media/ethereum-poa-deployment/basic-blade.png)

參數 | 說明 | 範例值
----------|-------------|--------------
建立新的網路或加入現有的網路 | 您可以建立新的聯盟網路，或加入既有的聯盟網路。 加入現有的網路需要額外的參數。 | 新建
電子郵件地址 | 當您的部署完成時，您會收到電子郵件通知，其中包含您部署的相關資訊。 | 有效的電子郵件地址
VM 使用者名稱 | 每個所部署 VM 的管理員使用者名稱 | 1-64 個英數位元
驗證類型 | 用來向虛擬機器驗證的方法。 | 密碼
密碼 | 每個所部署虛擬機器的管理員帳戶密碼。 所有 Vm 一開始都有相同的密碼。 您可以在布建之後變更密碼。 | 12-72 個字元 
訂用帳戶 | 要對其部署聯盟網路的訂用帳戶 |
資源群組| 要對其部署聯盟網路的資源群組。 | myResourceGroup
Location | 資源群組的 Azure 區域。 | 美國西部 2

選取 [確定]  。

### <a name="deployment-regions"></a>部署區域

在 [ *部署區域*] 底下，指定每個區域和位置的數目。 您最多可以部署五個區域。 第一個區域應符合 [ *基本* ] 區段中的資源群組位置。 針對開發或測試網路，您可以使用每個成員的單一區域。 針對生產環境，請在兩個或更多區域間部署，以提供高可用性。

![部署區域](./media/ethereum-poa-deployment/deployment-regions.png)

參數 | 說明 | 範例值
----------|-------------|--------------
區域數目|要部署聯盟網路的區域數目| 2
第一個區域 | 要部署聯盟網路的第一個區域 | 美國西部 2
第二個區域 | 部署聯盟網路的第二個區域。 當區域數量為二或更大時，就會顯示其他區域。 | 美國東部 2

選取 [確定]  。

### <a name="network-size-and-performance"></a>網路大小和效能

在 [ *網路大小和效能*] 底下，指定聯盟網路大小的輸入。 驗證程式節點儲存體大小會規定區塊鏈的可能大小。 您可以在部署後變更大小。

![網路大小和效能](./media/ethereum-poa-deployment/network-size-and-performance.png)

參數 | 說明 | 範例值
----------|-------------|--------------
負載平衡的驗證器節點數目 | 要布建為網路一部分的驗證器節點數目。 | 2
驗證器節點儲存體效能 | 每個已部署之驗證程式節點的受控磁片類型。 如需定價的詳細資訊，請參閱 [儲存體定價](https://azure.microsoft.com/pricing/details/managed-disks/) | 標準 SSD
驗證器節點的虛擬機器大小 | 驗證器節點所使用的虛擬機器大小。 如需定價的詳細資訊，請參閱 [虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | 標準 D2 v3

虛擬機器和儲存層會影響網路效能。  使用下表有助於選擇成本效率：

虛擬機器 SKU|儲存層|價格|Throughput|Latency
---|---|---|---|---
F1|標準 SSD|low|low|high
D2_v3|標準 SSD|中|中|中
F16s|進階 SSD|high|high|low

選取 [確定]  。

### <a name="ethereum-settings"></a>以太坊設定

在 [ *乙太坊設定*] 下，指定乙太坊相關的設定。

![以太坊設定](./media/ethereum-poa-deployment/ethereum-settings.png)

參數 | 說明 | 範例值
----------|-------------|--------------
聯盟成員識別碼 | 與參與聯盟網路的每個成員相關聯的識別碼。 它是用來設定 IP 位址空間，以避免發生衝突。 針對私人網路，成員識別碼在相同網路中的不同組織間應該是唯一的。  即使相同組織會部署到多個區域，仍然需要唯一成員識別碼。 請記下此參數的值，因為您需要與其他聯結成員共用，以確保不會發生衝突。 有效範圍是0到255。 | 0
網路識別碼 | 為要部署的聯盟以太坊網路指定網路識別碼。 每個以太坊網路都有自己的網路識別碼，1 是公用網路的識別碼。 有效範圍是5到999999999 | 10101010
管理員以太坊位址 | 用來參與 PoA 治理的乙太坊帳戶位址。 您可以使用 MetaMask 來產生乙太坊位址。 |
進階選項 | 以太坊設定的進階選項 | 啟用
使用公用 IP 部署 | 如果選取了私人 VNet，則會將網路部署在 VNet 閘道後方，並移除對等存取。 針對私人 VNet，所有成員都必須使用 VNet 閘道，連線才能相容。 | 公用 IP
封鎖天然氣限制 | 網路的開始區塊天然氣限制。 | 50000000
區塊重新封印期 (秒) | 當網路上沒有任何交易時，將建立空白區塊的頻率。 頻率越高，越快有定局，但會增加儲存成本。 | 15
交易許可權合約 | 交易授權合約的位元組程式碼。 將智慧型合約部署和執行限制為允許的乙太坊帳戶清單。 |

選取 [確定]  。

### <a name="monitoring"></a>監視

監視可讓您設定網路的記錄資源。 監視代理程式會從您的網路收集和呈現有用的計量和記錄，讓您能夠快速檢查網路健康情況或偵錯工具問題。

![Azure 監視器](./media/ethereum-poa-deployment/azure-monitor.png)

參數 | 說明 | 範例值
----------|-------------|--------------
監視 | 啟用監視的選項 | 啟用
連接至現有的 Azure 監視器記錄 | 建立新的 Azure 監視器記錄實例或加入現有實例的選項 | 新建
位置 | 部署新實例的區域 | 美國東部
現有的 log analytics 工作區識別碼 (連接到現有的 Azure 監視器記錄 = 加入現有的) |現有 Azure 監視器記錄實例的工作區識別碼||NA
現有的 log analytics 主要金鑰 (連接到現有的 Azure 監視器記錄檔 = 加入現有的) |用來連接到現有 Azure 監視器記錄實例的主要金鑰||NA

選取 [確定]  。

### <a name="summary"></a>摘要

按一下摘要以檢查指定的輸入，並執行基本的預先部署驗證。 在部署之前，您可以下載範本和參數。

選取 [ **建立** ] 以部署。

如果部署包含 VNet 閘道，部署可能需要45至50分鐘。

## <a name="deployment-output"></a>部署輸出

部署完成後，您就可以使用 Azure 入口網站來存取必要的參數。

### <a name="confirmation-email"></a>確認電子郵件

如果您在) 的 [ [基本] 區段](#basics) 中提供電子郵件 (位址，則會傳送電子郵件，其中包含部署資訊和本檔的連結。

![部署電子郵件](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>入口網站

順利完成部署並布建所有資源之後，您就可以在資源群組中查看輸出參數。

1. 在入口網站中移至您的資源群組。
1. 選取 **[總覽 > 部署**]。

    ![資源群組概觀](./media/ethereum-poa-deployment/resource-group-overview.png)

1. 選取 [區塊鏈]-[ **區塊鏈-乙太幣-...** 部署]。
1. 選取 [ **輸出** ] 區段。

    ![部署輸出](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>擴增聯盟

若要擴充您的聯盟，您必須先連接實體網路。 如果是在 VPN 後方部署，請參閱 [連接 VNet 閘道](#connecting-vnet-gateways) 將網路連線設定為新成員部署的一部分。 部署完成後，請使用 [治理管 dapp](#governance-dapp) 成為網路系統管理員。

### <a name="new-member-deployment"></a>新成員部署

讓加入的成員共用下列資訊。 您可以在部署後的電子郵件或入口網站部署輸出中找到此資訊。

* 聯盟資料 URL
* 您部署的節點數目
* VNet 閘道資源識別碼 (如果使用 VPN)

部署成員應使用相同的乙太坊證明聯盟解決方案範本，在部署其網路存在時，請使用下列指導方針：

* 選取 [加入現有的]****
* 選擇與網路上其餘成員相同數目的驗證程式節點，以確保合理表示
* 使用相同的系統管理員乙太坊位址
* 在*乙太坊設定*中使用提供的*聯盟資料 Url*
* 如果網路的其餘部分位於 VPN 後方，請在 [advanced] 區段下選取 [ **私人 VNet** ]。

### <a name="connecting-vnet-gateways"></a>連接 VNet 閘道

只有當您使用私人 VNet 進行部署時，才需要此區段。 如果您使用公用 IP 位址，則可以略過本節。

若是私人網路，則會透過 VNet 閘道連線連接不同的成員。 在成員可以加入網路並查看交易流量之前，現有的成員必須在 VPN 閘道上進行最後的設定，以接受連線。 在建立連接之前，加入成員的乙太坊節點將不會執行。 若要減少單點失敗的機率，請在聯盟中建立重複的網路連接。

新成員部署後，現有成員必須設定對新成員的 VNet 閘道連線，以完成雙向連線。 現有的成員需要：

* 連接成員的 VNet 閘道 ResourceID。 請參閱 [部署輸出](#deployment-output)。
* 共用的連接金鑰。

現有成員必須執行下列 PowerShell 指令碼，以完成連線。 您可以使用位於入口網站右上方導覽列中的 Azure Cloud Shell。

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>服務監視

您可以遵循部署電子郵件中的連結，或在部署輸出 [OMS_PORTAL_URL] 中尋找參數，找出您的 Azure 監視器入口網站。

入口網站會先顯示整體的網路統計資料和節點概觀。

![監視類別](./media/ethereum-poa-deployment/monitor-categories.png)

選取 **節點總覽**  會顯示每個節點的基礎結構統計資料。

![節點統計資料](./media/ethereum-poa-deployment/node-stats.png)

選取 [ **網路** 統計資料] 會顯示乙太坊網路統計資料。

![網路統計資料](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>範例 Kusto 查詢

您可以查詢監視記錄以調查失敗或設定閾值警示。 下列查詢是您可以在 *記錄搜尋* 工具中執行的範例：

有多個驗證程式查詢回報的清單區塊有助於找出連鎖分支。

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

取得指定之驗證程式節點平均超過5分鐘值區的平均對等計數。

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH 存取

基於安全考量，網路群組安全性規則依預設會拒絕 SSH 連接埠存取。 若要存取 PoA 網路中的虛擬機器實例，您必須將下列安全性規則變更為 [ *允許*]。

1. 移至 Azure 入口網站中已部署資源群組的 [ **總覽** ] 區段。

    ![SSH 概觀](./media/ethereum-poa-deployment/ssh-overview.png)

1. 針對您想要存取的 VM 區域選取 **網路安全性群組** 。

    ![SSH NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. 選取 [ **允許 ssh** 規則]。

    ![螢幕擷取畫面會顯示已選取 ssh 允許的總覽視窗。](./media/ethereum-poa-deployment/ssh-allow.png)

1. 將 **動作** 變更為 **允許**

    ![SSH 啟用允許](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. 選取 [儲存]。 變更可能需要幾分鐘的時間才會生效。

您可以使用您提供的系統管理員使用者名稱和密碼/SSH 金鑰，透過 SSH 從遠端連線至驗證器節點的虛擬機器。 範本部署輸出中會列出用來存取第一個驗證程式節點的 SSH 命令。 例如：

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

若要取得其他交易節點，請將埠號碼遞增1。

如果您已部署至一個以上的區域，請將命令變更為該區域中負載平衡器的 DNS 名稱或 IP 位址。 若要尋找其他區域的 DNS 名稱或 IP 位址，請尋找具有命名慣例** \* \* \* \* \* -lbpip-reg \# **的資源，並查看其 DNS 名稱和 IP 位址屬性。

## <a name="azure-traffic-manager-load-balancing"></a>Azure 流量管理員負載平衡

Azure 流量管理員可跨不同區域的多個部署路由傳入的流量，藉以縮短停機時間及改善 PoA 網路的回應能力。 內建的健康狀態檢查和自動重新路由功能，有助於確保 RPC 端點和治理管 dapp 的高可用性。 如果您已部署至多個區域，並已做好生產準備，則可利用這項功能。

使用流量管理員，透過自動容錯移轉來改善 PoA 網路的可用性。 您也可以使用流量管理員，藉由將終端使用者路由至具有最低網路延遲的 Azure 位置，來提高網路的回應速度。

如果您決定建立流量管理員設定檔，您可以使用設定檔的 DNS 名稱來存取網路。 當其他聯盟成員已新增至網路後，流量管理員也可用來在已部署的驗證器之間進行負載平衡。

### <a name="creating-a-traffic-manager-profile"></a>建立流量管理員設定檔

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取左上角的 [ **建立資源** ]。
1. 搜尋 **流量管理員設定檔**。

    ![搜尋 Azure 流量管理員](./media/ethereum-poa-deployment/traffic-manager-search.png)

    為設定檔提供一個唯一的名稱，並選取用於 PoA 部署的資源群組。

1. 選取 [ **建立** ] 以部署。

    ![建立流量管理員](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. 部署之後，請選取資源群組中的實例。 您可以在 [總覽] 索引標籤中找到存取流量管理員的 DNS 名稱。

    ![找出流量管理員 DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. 選擇 [ **端點** ] 索引標籤，然後選取 [ **新增** ] 按鈕。
1. 為端點提供一個唯一的名稱。
1. 針對 [ **目標資源類型**]，選擇 [ **公用 IP 位址**]。
1. 選擇第一個區域的負載平衡器的公用 IP 位址。

    ![路由流量管理員](./media/ethereum-poa-deployment/traffic-manager-routing.png)

為已部署網路中的每個區域重複前述步驟。 當端點處於 **啟用** 狀態之後，就會在流量管理員的 DNS 名稱自動載入和區域平衡。 您現在可以在本文的其他步驟中使用此 DNS 名稱來取代 [CONSORTIUM_DATA_URL] 參數。

## <a name="data-api"></a>資料 API

每個聯盟成員都會裝載其他成員連線至網路所需的資訊。 為了讓連線變得更容易，每個成員都會在資料 API 端點上裝載一組連接資訊。

現有的成員會在成員的部署之前提供 [CONSORTIUM_DATA_URL]。 在部署時，加入的成員將會從 JSON 介面在下列端點擷取資訊：

`<CONSORTIUM_DATA_URL>/networkinfo`

回應包含適用于將成員聯結 (創世組塊、驗證程式集合約 ABI、bootnodes) ，以及對現有成員 (驗證程式位址) 有用的資訊。 您可以使用此標準化將聯盟延伸至雲端提供者。 此 API 會傳回 JSON 格式的回應，其結構如下：

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>控管 DApp

權威證明的核心是非集中式控管。 由於授權證明依賴允許的網路授權清單來讓網路保持良好，因此請務必提供公平的機制來修改此許可權清單。 每個部署都隨附一組智慧型合約和入口網站，可供此允許清單的連鎖治理使用。 當提議的變更通過聯盟成員的多數決議後，即會執行變更。 投票可讓新的共識參與者新增或遭入侵，以透明的方式來移除，以促進誠實的網路。

治理管 dapp 是一組預先部署的 [智慧型合約](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) ，以及用來管理網路上授權單位的 web 應用程式。 授權單位會細分為系統管理員身分識別和驗證器節點。
系統管理員有能力將共識參與委派給一組驗證器節點。 管理員也可經由投票讓其他管理員加入或退出網路。

![控管 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* 非集中式**治理：** 網路授權單位中的變更是透過選取的系統管理員，透過連鎖投票來管理。
* **驗證程式委派：** 授權單位可以管理其在每個 PoA 部署中設定的驗證器節點。
* 可**審核的變更歷程記錄：** 每項變更都會記錄在區塊鏈上，提供透明度和可審核性。

### <a name="getting-started-with-governance"></a>控管入門

若要透過治理管 dapp 執行任何種類的交易，您必須使用乙太坊錢包。 最直接的方法是使用瀏覽器內錢包，例如 [MetaMask](https://metamask.io);不過，由於這些智慧型合約是部署在網路上，您也可以自動化與治理合約的互動。

安裝 MetaMask 之後，請在瀏覽器中瀏覽至「控管 DApp」。  您可以透過部署輸出中的 Azure 入口網站來尋找 URL。  如果您未安裝瀏覽器內的錢包，將無法執行任何動作;不過，您可以查看系統管理員狀態。  

### <a name="becoming-an-admin"></a>成為管理員

如果您是部署在網路上的第一個成員，則會自動成為系統管理員，而且您的同位節點會列為驗證程式。 如果您要加入網路，則必須以系統管理員的身分，在現有的系統管理員集合中以超過 50% ) 的 (來投票。 如果您選擇不成為系統管理員，您的節點仍會同步並驗證區塊鏈;不過，它們不會參與封鎖建立流程。 若要開始投票程式成為系統管理員，請選取 [ **提名** ]，然後輸入您的乙太坊位址和別名。

![提名](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>候選

選取 [ **候選** 專案] 索引標籤會顯示一組目前的候選系統管理員。  當候選項達到目前系統管理員的最新投票時，候選會提升為系統管理員。 若要在候選項上投票，請選取該資料列，然後選取 [ **投票于**]。 如果您在投票時改變主意，請選取候選，然後選取 [ **Rescind 投票**]。

![候選](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>管理員

[系統 **管理員** ] 索引標籤會顯示一組目前的系統管理員，並提供您投票的能力。  當系統管理員失去50% 以上的支援之後，就會將其移除為網路上的系統管理員。 系統管理員所擁有的任何驗證器節點都會遺失驗證程式狀態，並成為網路上的交易節點。 系統管理員可能會因為任何原因而遭到移除;不過，這是由聯盟預先同意原則。

![管理員](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>驗證程式

選取 [ **驗證** 程式] 索引標籤會顯示實例目前已部署的同位節點，以及其目前狀態 (節點類型) 。 每個聯盟成員在此清單中都有一組不同的驗證程式，因為此視圖代表目前已部署的聯盟成員。 如果實例是新部署的，而且您尚未新增驗證程式，您可以選擇 **新增驗證**程式。 新增驗證程式會自動選擇一組地區平衡的同位節點，並將它們指派給您的驗證器集合。 如果您部署的節點數目超過允許的容量，其餘的節點會成為網路上的交易節點。

指派每個驗證器的位址時，會自動透過 Azure 中的[身分識別存放區](#identity-store)指派。  如果節點停止運作，它會會讓出其身分識別，讓您的部署中的另一個節點能採用它的位置。 此程式可確保您的共識參與具有高可用性。

![驗證程式](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>聯盟名稱

任何系統管理員都可以更新聯盟名稱。  選取左上角的齒輪圖示，以更新聯盟名稱。

### <a name="account-menu"></a>帳戶功能表

在右上方，是您的乙太坊帳號別名和頭像。  如果您是系統管理員，您可以更新您的別名。

![帳戶](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>乙太坊開發<a id="tutorials"></a>

若要編譯、部署及測試智慧型合約，以下是您可以考慮用於乙太坊開發的幾個選項：
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) -以用戶端為基礎的乙太坊開發環境
* [乙太坊 Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) -瀏覽器型和本機乙太坊開發環境

### <a name="compile-deploy-and-execute-smart-contract"></a>編譯、部署和執行智慧型合約

在下列範例中，您會建立簡單的智慧型合約。 您可以使用 Truffle 來編譯智慧型合約，並將其部署至您的區塊鏈網路。 一旦部署之後，您就能透過交易呼叫智慧型合約函式。

#### <a name="prerequisites"></a>Prerequisites

* 安裝 [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)。 Truffle 和 Web3 需要 Python。 選取 [安裝] 選項，以在您的路徑中包含 Python。
* 安裝 Truffle v 5.0.5 `npm install -g truffle@v5.0.5` 。 Truffle 需要安裝數個工具，包括 [Node.js](https://nodejs.org) 和 [Git](https://git-scm.com/)。 如需詳細資訊，請參閱 [Truffle 檔](https://github.com/trufflesuite/truffle)。

### <a name="create-truffle-project"></a>建立 Truffle 專案

您必須先建立 Truffle 專案，才能編譯和部署智慧型合約。

1. 開啟命令提示字元或殼層。
1. 建立名為 `HelloWorld` 的資料夾。
1. 將目錄變更至新 `HelloWorld` 資料夾。
1. 使用命令初始化新的 Truffle 專案 `truffle init` 。

    ![建立新的 Truffle 專案](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>新增智慧型合約

在 Truffle 專案的 **合約** 子目錄中建立智慧型合約。

1. 在 `postBox.sol` Truffle 專案的 [ **合約** ] 子目錄中，建立名為的檔案。
1. 將下列的密度程式碼新增至**postBox。**

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>使用 Truffle 部署智慧型合約

Truffle projects 包含區塊鏈網路連線詳細資料的設定檔。 修改設定檔以包含網路的連接資訊。

> [!WARNING]
> 切勿透過網路傳送乙太坊私密金鑰。 請確定每個交易都已先在本機簽署，再透過網路傳送已簽署的交易。

1. [部署區塊鏈網路時，您需要使用乙太坊系統管理員帳戶](#ethereum-settings)的助記詞片語。 如果您使用 MetaMask 來建立帳戶，就可以從 MetaMask 取得助憶鍵。 選取 MetaMask 延伸模組右上方的 [系統管理員帳戶] 圖示，然後選取 [ **安全性 & 隱私權] > 設定 > 顯示種子文字**。
1. 將 Truffle 專案中的內容取代為 `truffle-config.js` 下列內容。 取代預留位置端點和助憶鍵值。

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. 由於我們使用的是 Truffle HD 錢包提供者，請使用命令在您的專案中安裝模組 `npm install truffle-hdwallet-provider --save` 。

Truffle 使用遷移腳本將智慧型合約部署至區塊鏈網路。 您需要遷移腳本來部署新的智慧型合約。

1. 加入新的遷移，以部署新的合約。 `2_deploy_contracts.js`在 Truffle 專案的 [**遷移**] 子目錄中建立檔案。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. 使用 Truffle 遷移命令部署至 PoA 網路。 在 Truffle 專案目錄的命令提示字元中，執行：

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>呼叫智慧型合約函式

現在您已部署智慧型合約，您可以傳送交易來呼叫函式。

1. 在 Truffle 專案目錄中，建立名為的新檔案 `sendtransaction.js` 。
1. 將下列內容新增至 **sendtransaction.js**。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. 使用 Truffle execute 命令執行腳本。

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![執行腳本以透過交易呼叫函數](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly (WASM) 支援

在新部署的 PoA 網路上已為您啟用 WebAssembly 支援。 它可讓您以任何能夠 transpile 為 Web 組件的語言 (Rust、C、C++) 進行智慧合約開發。 如需詳細資訊，請參閱： [WebAssembly](https://wiki.parity.io/WebAssembly-Home)的同位檢查和來自同位技術的[教學](https://github.com/paritytech/pwasm-tutorial)課程

## <a name="faq"></a>常見問題集

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>我發現網路上有許多我未傳送的交易。 這些交易來自何處？

將[個人 API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) 解除鎖定並不安全。 Bot 會接聽已解除鎖定的以太坊帳戶，並嘗試清空資金。 Bot 會假設這些帳戶含有真實的以太幣，並嘗試率先竊取餘額。 請勿在網路上啟用個人 API。 改為使用 MetaMask 或以程式設計的方式，以手動方式簽署交易。

### <a name="how-to-ssh-onto-a-vm"></a>如何透過 SSH 連線至 VM？

基於安全考量，並不會顯示 SSH 連接埠。 請遵循[本指南以啟用 SSH 連接埠](#ssh-access)。

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>如何設定稽核成員或交易節點？

交易節點是一組與網路對等互連但並未參與共識的同位檢查用戶端。 這些節點仍可用來提交以太坊交易，以及讀取智慧合約狀態。 這種機制適用于為網路上的非授權聯盟成員提供可審核性。 若要達成此目的，請依照 [成長聯盟](#growing-the-consortium)的步驟進行。

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask 交易為何十分耗時？

為了確保能夠以正確的順序接收交易，每個以太坊交易都會隨附累加的 nonce。 如果您已在不同的網路上使用 MetaMask 中的帳戶，則需要重設 nonce 值。 按一下 [設定] 圖示 (三列) 、設定、重設帳戶。 交易歷程記錄將會清除，屆時您即可重新提交交易。

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>我是否需要在 MetaMask 中指定燃料費？

乙太幣在權威證明聯盟中派不上用場。 因此，在 MetaMask 中提交交易時，不需要指定天然氣費用。

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>如果因無法佈建 Azure OMS 而導致我的部署失敗，該怎麼辦？

監視是一個選擇性功能。 在某些罕見的情況下，您的部署因為無法成功布建 Azure 監視器資源而失敗，您可以在不 Azure 監視器的情況下重新部署。

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>公用 IP 部署與私人網路部署是否相容？

否。 對等互連需要雙向通訊，因此整個網路必須是公用或私用。

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>什麼是權威證明的預期交易輸送量？

交易輸送量高度倚賴交易和網路拓撲的類型。 我們已透過跨多區域部署的網路，使用簡單交易進行平均每秒 400 個交易的基準測試。

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>如何訂閱智慧合約事件？

「以太坊權威證明」現在支援 Web 通訊端。  檢查您的部署輸出，以找出 web 通訊端 URL 和埠。

## <a name="support-and-feedback"></a>支援與意見反應

如需 Azure 區塊鏈服務消息，請瀏覽 [Azure 區塊鏈服務部落格](https://azure.microsoft.com/blog/topics/blockchain/)，即時掌握 Azure 區塊鏈服務工程小組提供的區塊鏈服務供應項目和最新資訊。

若要提供產品意見反應或要求新功能，請透過 [Azure 區塊鏈服務的意見反應論壇](https://aka.ms/blockchainuservoice) \(英文\) 張貼或票選想法。

### <a name="community-support"></a>社群支援

洽詢 Microsoft 工程師和 Azure 區塊鏈服務社群專家。

* [Microsoft 問&問題頁面](/answers/topics/azure-blockchain-workbench.html)。 區塊鏈範本的工程支援僅限於部署問題。
* [Microsoft 技術社群](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>後續步驟

如需更多 Azure 區塊鏈解決方案，請參閱 [Azure 區塊鏈檔](../index.yml)。
