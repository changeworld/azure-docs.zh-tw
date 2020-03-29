---
title: 在 Azure 上部署以地庭防水功能聯合體解決方案範本
description: 使用以地庭機構證明聯合體解決方案在 Azure 上部署和配置多成員聯合公司以非坊網路
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387452"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>在 Azure 上部署以地庭驗證授權聯合體解決方案範本

您可以使用[以天地驗證機構聯盟預覽 Azure 解決方案範本](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum)，以最少的 Azure 和以天圖知識部署、配置和管理多成員聯合體重定機構以地庭網路。

每個聯合體成員可以使用解決方案範本使用 Microsoft Azure 計算、網路和存儲服務來預配區塊鏈網路佔用空間。 每個聯聯成員的網路佔用空間由一組負載平衡驗證器節點組成，應用程式或使用者可以與之交互以提交以非定器事務。

## <a name="choose-an-azure-blockchain-solution"></a>選擇 Azure 區塊鏈解決方案

在選擇使用以非坊驗證權威聯合體解決方案範本之前，請將您的方案與可用 Azure 區塊鏈選項的常見用例進行比較。

選項 | 服務模型 | 常見用例
-------|---------------|-----------------
解決方案範本 | IaaS | 解決方案範本是 Azure 資源管理器範本，可用於預配完全配置的區塊鏈網路拓撲。 這些範本為給定的區塊鏈網路類型部署和配置 Microsoft Azure 計算、網路和存儲服務。
[Azure 區塊鏈服務](../service/overview.md) | PaaS | Azure 區塊鏈服務預覽簡化了聯合體區塊鏈網路的形成、管理和治理。 對於需要 PaaS、聯合體管理或合同和事務隱私的解決方案，請使用 Azure 區塊鏈服務。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 預覽是 Azure 服務和功能的集合，目的是要協助您建立及部署區塊鏈應用程式，以與其他組織共用商務程序和資料。 使用 Azure 區塊鏈工作臺原型設計區塊鏈解決方案或區塊鏈應用程式概念驗證。

## <a name="solution-architecture"></a>方案架構

使用以非坊解決方案範本，可以部署基於單個或多區域的多成員以地庭驗證權威聯盟網路。

![部署架構](./media/ethereum-poa-deployment/deployment-architecture.png)

每個聯盟成員部署都包含：

* 執行 PoA 驗證器的虛擬機器
* 用於分發 RPC、對等互連和治理 DApp 請求的 Azure 負載等化器
* 保護驗證器身分識別的 Azure Key Vault
* 裝載持續性網路資訊和協調租用的 Azure 儲存體
* 彙總記錄和效能統計資料的 Azure 監視器
* 允許透過私人 VNet 進行 VPN 連線的 VNet 閘道 (選擇性)

根據預設，RPC 和對等互連端點可透過公用 IP 來存取，以簡化跨訂用帳戶和雲端的連線。 對於應用程式級訪問控制項，可以使用[同位的許可權協定](https://wiki.parity.io/Permissioning)。 支援 VPN 後部署的網路，這些網路利用 VNet 閘道進行跨訂閱連接。 由於 VPN 和 VNet 部署更為複雜，因此在原型設計解決方案時，您可能希望從公共 IP 模型開始。

Docker 容器用於可靠性和模組化。 Azure 容器註冊表用於承載和提供版本映射，作為每個部署的一部分。 容器映像包含：

* 協調器 - 生成標識和治理協定。 在標識存儲中存儲標識。
* 同位用戶端 - 從標識存儲租賃標識。 發現並連接到對等方。
* EthStats 代理 - 通過 RPC 收集本地日誌和統計資訊，並將資訊推送到 Azure 監視器。
* 治理 DApp - 用於與治理協定交互的 Web 介面。

### <a name="validator-nodes"></a>驗證器節點

在權威證明通訊協定中，驗證器節點會取代傳統的礦工節點。 每個驗證器都有一個唯一的乙太坊標識，允許它參與塊創建過程。 每個聯盟成員可在五個區域間佈建兩個或更多驗證器節點，以達到異地備援的目的。 驗證器節點會與其他驗證器節點通訊，使基礎分散式總帳的狀態達到一致。 為確保網路公平參與，每個聯營成員不得使用比網路上的第一個成員更多的驗證器。 例如，如果第一個成員部署三個驗證器，則每個成員最多隻能有三個驗證器。

### <a name="identity-store"></a>身分識別存放區

標識存儲部署在每個成員的訂閱中，該訂閱安全地保存生成的以地庭標識。 對於每個驗證器，業務流程容器生成乙太坊私密金鑰並將其存儲在 Azure 金鑰保存庫中。

## <a name="deploy-ethereum-consortium-network"></a>部署以地庭聯合網路

在本演練中，假設您正在創建一個多方以地圖姆聯合網路。 以是多黨部署的示例：

1. 三個成員分別使用 MetaMask 產生了以太坊帳戶
1. *成員 A*部署以地庭 PoA，提供以地庭公共位址
1. *成員 A* 將聯盟 URL 提供給*成員 B* 和*成員 C*
1. *成員 B* 和*成員 C* 提供其以太坊公用位址和*成員 A* 的聯盟 URL，部署了以太坊 PoA
1. *成員 A* 將*成員 B* 票選為管理員
1. *成員 A* 和*成員 B* 都將*成員 C* 票選為管理員

下一節將介紹如何配置網路中的第一個成員的佔用空間。

### <a name="create-resource"></a>建立資源

在[Azure 門戶](https://portal.azure.com)中，選擇在左上角**創建資源**。

選擇**區塊鏈** > **以非庭權威證明聯盟（預覽）。**

### <a name="basics"></a>基本概念

在 **"基本"** 下，為任何部署指定標準參數的值。

![基本概念](./media/ethereum-poa-deployment/basic-blade.png)

參數 | 描述 | 範例值
----------|-------------|--------------
創建新網路或加入現有網路 | 您可以創建新的聯合體網路或加入預先存在的聯合體網路。 加入現有網路需要額外的參數。 | 新建
電子郵件地址 | 部署完成有關部署的資訊後，您將收到電子郵件通知。 | 有效的電子郵件地址
VM 使用者名稱 | 每個所部署 VM 的管理員使用者名稱 | 1-64 字母數位字元
驗證類型 | 用來向虛擬機器驗證的方法。 | 密碼
密碼 | 每個所部署虛擬機器的管理員帳戶密碼。 所有 VM 最初具有相同的密碼。 您可以在預配後更改密碼。 | 12-72 個字元 
訂用帳戶 | 要對其部署聯盟網路的訂用帳戶 |
資源群組| 要對其部署聯盟網路的資源群組。 | myResourceGroup
Location | 資源群組的 Azure 區域。 | 美國西部 2

選取 [確定]****。

### <a name="deployment-regions"></a>部署區域

在 *"部署區域"* 下，指定每個區域的區數和位置數。 最多可以在五個區域中部署。 第一個區域應與 *"基本"* 部分的資源組位置匹配。 對於開發或測試網路，每個成員可以使用單個區域。 對於生產，跨兩個或多個區域進行部署，以便獲得高可用性。

![部署區域](./media/ethereum-poa-deployment/deployment-regions.png)

參數 | 描述 | 範例值
----------|-------------|--------------
區域數目|要部署聯盟網路的區域數目| 2
第一個區域 | 要部署聯盟網路的第一個區域 | 美國西部 2
第二個區域 | 第二個區域部署聯合體網路。 當區域數為 2 或更大時，其他區域可見。 | 美國東部 2

選取 [確定]****。

### <a name="network-size-and-performance"></a>網路大小和效能

在 *"網路大小和性能*"下，指定聯合體網路大小的輸入。 驗證器節點存儲大小決定了區塊鏈的潛在大小。 部署後可以更改大小。

![網路大小和效能](./media/ethereum-poa-deployment/network-size-and-performance.png)

參數 | 描述 | 範例值
----------|-------------|--------------
負載平衡的驗證器節點數目 | 作為網路的一部分預配的驗證器節點數。 | 2
驗證器節點儲存體效能 | 每個已部署的驗證器節點的託管磁片類型。 有關定價的詳細資訊，請參閱[存儲定價](https://azure.microsoft.com/pricing/details/managed-disks/) | 標準 SSD
驗證器節點的虛擬機器大小 | 驗證器節點所使用的虛擬機器大小。 有關定價的詳細資訊，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | 標準 D2 v3

虛擬機器和存儲層會影響網路性能。  使用下表説明選擇成本效益：

虛擬機器 SKU|儲存層|Price|Throughput|Latency
---|---|---|---|---
F1|標準 SSD|low|low|high
D2_v3|標準 SSD|中|中|中
F16s|進階 SSD|high|high|low

選取 [確定]****。

### <a name="ethereum-settings"></a>以太坊設定

在*以地庭設置*下，指定與以非坊相關的配置設置。

![以太坊設定](./media/ethereum-poa-deployment/ethereum-settings.png)

參數 | 描述 | 範例值
----------|-------------|--------------
聯盟成員識別碼 | 與參與聯合網路的每個成員關聯的 ID。 它用於配置 IP 位址空間以避免衝突。 對於私人網路絡，成員 ID 應在同一網路中的不同組織中是唯一的。  即使相同組織會部署到多個區域，仍然需要唯一成員識別碼。 記下此參數的值，因為您需要與其他聯接成員共用它，以確保沒有衝突。 有效範圍為 0 到 255。 | 0
網路識別碼 | 為要部署的聯盟以太坊網路指定網路識別碼。 每個以太坊網路都有自己的網路識別碼，1 是公用網路的識別碼。 有效範圍為 5 到 999，999，999 | 10101010
管理員以太坊位址 | 用於參與 PoA 治理的以裡瑟姆帳戶位址。 您可以使用 MetaMask 生成乙太坊位址。 |
進階選項 | 以太坊設定的進階選項 | 啟用
使用公共 IP 進行部署 | 如果選擇了專用 VNet，則網路將部署在 VNet 閘道後面，並刪除對等訪問。 對於專用 VNet，所有成員必須使用 VNet 閘道才能相容連接。 | 公用 IP
塊氣體限制 | 網路的起始塊氣體限制。 | 50000000
區塊重新封印期 (秒) | 當網路上沒有任何交易時，將建立空白區塊的頻率。 頻率越高，越快有定局，但會增加儲存成本。 | 15
交易許可權合同 | 交易授權合約的位元組程式碼。 將智慧合同部署和執行限制為允許的以地庭帳戶清單。 |

選取 [確定]****。

### <a name="monitoring"></a>監視

監視允許您為網路設定日誌資源。 監視代理從網路收集和顯示有用的指標和日誌，從而能夠快速檢查網路運行狀況或調試問題。

![Azure 監視器](./media/ethereum-poa-deployment/azure-monitor.png)

參數 | 描述 | 範例值
----------|-------------|--------------
監視 | 啟用監視的選項 | 啟用
連接到現有的 Azure 監視器日誌 | 創建新 Azure 監視器日誌實例或聯接現有實例的選項 | 新建
Location | 部署新實例的區域 | 美國東部
現有日誌分析工作區 ID（連接到現有 Azure 監視器日誌 + 加入現有日誌）|現有 Azure 監視器日誌實例的工作區 ID||NA
現有日誌分析主鍵（連接到現有 Azure 監視器日誌 + 加入現有）|用於連接到現有 Azure 監視器日誌實例的主鍵||NA

選取 [確定]****。

### <a name="summary"></a>總結

按一下摘要以查看指定的輸入並運行基本部署前驗證。 在部署之前，可以下載範本和參數。

選擇 **"創建**"以進行部署。

如果部署包括 VNet 閘道，則部署可能需要 45 到 50 分鐘。

## <a name="deployment-output"></a>部署輸出

部署完成後，可以使用 Azure 門戶訪問必要的參數。

### <a name="confirmation-email"></a>確認電子郵件

如果您提供電子郵件地址（[基礎知識部分](#basics)），將發送電子郵件，其中包含部署資訊和指向本文檔的連結。

![部署電子郵件](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>入口網站

成功完成部署並預配所有資源後，您可以查看資源組中的輸出參數。

1. 轉到門戶中的資源組。
1. 選擇 **"部署>概述**"。

    ![資源群組概觀](./media/ethereum-poa-deployment/resource-group-overview.png)

1. 選擇**微軟-azure-區塊鏈.azure-區塊鏈-ether-...** 部署。
1. 選擇"**輸出"** 部分。

    ![部署輸出](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>擴增聯盟

若要擴充您的聯盟，您必須先連接實體網路。 如果部署在 VPN 後面，請參閱連接[VNet 閘道](#connecting-vnet-gateways)的部分，將網路連接配置為新成員部署的一部分。 部署完成後，使用[治理 DApp](#governance-dapp)成為網路系統管理員。

### <a name="new-member-deployment"></a>新成員部署

讓加入的成員共用下列資訊。 這些資訊在部署後電子郵件或閘戶部署輸出中找到。

* 聯盟資料 URL
* 部署的節點數
* VNet 閘道資源識別碼 (如果使用 VPN)

部署成員在使用以下指南部署其網路狀態時，應使用相同的以非坊防水機構聯盟解決方案範本：

* 選取 [加入現有的]****
* 選擇與網路上的其他成員相同的驗證器節點數，以確保公平表示
* 使用相同的管理員以地圖姆位址
* 在*以以坊設置*中使用提供的*聯盟資料 URL*
* 如果網路的其餘部分位於 VPN 後面，請在高級部分下選擇**專用 VNet**

### <a name="connecting-vnet-gateways"></a>連接 VNet 閘道

僅當使用專用 VNet 部署時，才需要此部分。 如果您使用的是公共 IP 位址，則可以跳過此部分。

對於私人網路絡，不同的成員通過 VNet 閘道連接連接。 在成員可以加入網路並查看事務流量之前，現有成員必須在其 VPN 閘道上執行最終配置才能接受連接。 在建立連接之前，聯接成員的以天間節點不會運行。 要減少單點故障的可能性，請在聯合體中創建冗余網路連接。

新成員部署後，現有成員必須設定對新成員的 VNet 閘道連線，以完成雙向連線。 現有成員需要：

* 連接成員的 VNet 閘道資源識別碼。 請參閱[部署輸出](#deployment-output)。
* 共用連接金鑰。

現有成員必須執行下列 PowerShell 指令碼，以完成連線。 您可以使用位於門戶右上角巡覽列中的 Azure 雲外殼。

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

您可以按照部署電子郵件中的連結或在部署輸出 [OMS_PORTAL_URL] 中查找參數來查找 Azure 監視器門戶。

入口網站會先顯示整體的網路統計資料和節點概觀。

![監控類別](./media/ethereum-poa-deployment/monitor-categories.png)

選擇**節點概述**可顯示每個節點基礎結構統計資訊。

![節點統計資訊](./media/ethereum-poa-deployment/node-stats.png)

選擇**網路統計資訊**將顯示以地圖姆網路統計資訊。

![網路統計資訊](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>庫斯托查詢示例

您可以查詢監視日誌以調查故障或設置閾值警報。 以下查詢是您可以在*日誌搜索*工具中運行的示例：

由多個驗證器查詢報告的清單塊可用於説明查找鏈叉。

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

獲取指定驗證器節點的平均對等計數，平均超過 5 分鐘存儲桶。

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

基於安全考量，網路群組安全性規則依預設會拒絕 SSH 連接埠存取。 要訪問 PoA 網路中的虛擬機器實例，您需要將以下安全規則更改為 *"允許*"。

1. 轉到 Azure 門戶中已部署的資源組的 **"概述"** 部分。

    ![SSH 概觀](./media/ethereum-poa-deployment/ssh-overview.png)

1. 為要訪問的 VM 區域選擇**網路安全性群組**。

    ![SSH NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. 選擇**允許-ssh**規則。

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. 更改**操作**以**允許**

    ![SSH 啟用允許](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. 選取 [儲存]****。 更改可能需要幾分鐘才能應用。

您可以使用提供的管理員使用者名和密碼/SSH 金鑰，通過 SSH 遠端連線到驗證器節點的虛擬機器。 範本部署輸出中列出了訪問第一個驗證器節點的 SSH 命令。 例如：

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

要獲取其他事務節點，將埠號增加一個。

如果部署到多個區域，則將命令更改為該區域中負載等化器的 DNS 名稱或 IP 位址。 要查找其他區域的 DNS 名稱或 IP 位址，查找具有命名約定**\*\*\*\*\*-lbpip-reg\#** 的資源並查看其 DNS 名稱和 IP 位址屬性。

## <a name="azure-traffic-manager-load-balancing"></a>Azure 流量管理員負載平衡

Azure 流量管理員可跨不同區域的多個部署路由傳入的流量，藉以縮短停機時間及改善 PoA 網路的回應能力。 內置運行狀況檢查和自動重新路由有助於確保 RPC 終結點和治理 DApp 的高可用性。 如果您已部署至多個區域，並已做好生產準備，則可利用這項功能。

使用流量管理器通過自動容錯移轉提高 PoA 網路可用性。 您還可以使用流量管理器通過將最終使用者路由到網路延遲最低的 Azure 位置來增加網路回應能力。

如果您決定建立流量管理員設定檔，您可以使用設定檔的 DNS 名稱來存取網路。 當其他聯盟成員已新增至網路後，流量管理員也可用來在已部署的驗證器之間進行負載平衡。

### <a name="creating-a-traffic-manager-profile"></a>建立流量管理員設定檔

1. 在[Azure 門戶](https://portal.azure.com)中，選擇在左上角**創建資源**。
1. 搜索**流量管理器設定檔**。

    ![搜索 Azure 流量管理器](./media/ethereum-poa-deployment/traffic-manager-search.png)

    為設定檔指定唯一名稱，並選擇用於 PoA 部署的資源組。

1. 選擇 **"創建**"以進行部署。

    ![創建流量管理器](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. 部署後，在資源組中選擇實例。 訪問流量管理器的 DNS 名稱可在"概述"選項卡中找到。

    ![找出流量管理員 DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. 選擇 **"端點"** 選項卡並選擇"**添加**"按鈕。
1. 為端點提供一個唯一的名稱。
1. 對於**目標資源類型**，請選擇**公共 IP 位址**。
1. 選擇第一個區域負載等化器的公共 IP 位址。

    ![路由流量管理員](./media/ethereum-poa-deployment/traffic-manager-routing.png)

為已部署網路中的每個區域重複前述步驟。 終結點處於**啟用**狀態後，在流量管理器的 DNS 名稱下自動載入和平衡區域。 現在，您可以在本文的其他步驟中使用此 DNS 名稱代替 [CONSORTIUM_DATA_URL] 參數。

## <a name="data-api"></a>資料 API

每個聯盟成員都會裝載其他成員連線至網路所需的資訊。 為了便於連接，每個成員在資料 API 終結點上承載一組連接資訊。

現有成員在成員部署之前提供 [CONSORTIUM_DATA_URL] 。 在部署時，加入的成員將會從 JSON 介面在下列端點擷取資訊：

`<CONSORTIUM_DATA_URL>/networkinfo`

回應包含可用於加入成員的資訊（Genesis 塊、驗證器集協定 ABI、引導節點）以及對現有成員有用的資訊（驗證器位址）。 您可以使用此標準化將聯合體擴展到雲供應商。 此 API 返回具有以下結構的 JSON 格式化回應：

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

權威證明的核心是非集中式控管。 由於授權證明依賴于允許的網路頒發機構清單來保持網路正常運行，因此提供一個公平的機制來修改此許可權清單非常重要。 每個部署都附帶一組智慧合同和門戶，用於此允許清單的鏈上治理。 當提議的變更通過聯盟成員的多數決議後，即會執行變更。 投票允許以透明的方式刪除新的共識參與者或妥協的參與者，從而鼓勵誠實的網路。

治理 DApp 是一組預先部署的[智慧合約](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts)和一個 Web 應用程式，用於管理網路上的權威機構。 許可權被分解為管理員身份和驗證器節點。
管理員有權將協商一致參與委託給一組驗證器節點。 管理員也可經由投票讓其他管理員加入或退出網路。

![控管 DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **分散治理：** 網路許可權的更改由選定的管理員通過鏈式投票進行管理。
* **驗證器委派：** 許可權可以管理在每個 PoA 部署中設置的驗證器節點。
* **可審核更改歷史記錄：** 每個更改都記錄在區塊鏈上，提供透明度和可審計性。

### <a name="getting-started-with-governance"></a>控管入門

要通過治理 DApp 執行任何類型的事務，您需要使用以裡瑟姆錢包。 最直接的方法是使用瀏覽器內錢包，如[MetaMask;](https://metamask.io)但是，由於這些智慧合約部署在網路上，您還可以自動與治理協定進行交互。

安裝 MetaMask 之後，請在瀏覽器中瀏覽至「控管 DApp」。  您可以在部署輸出中通過 Azure 門戶找到 URL。  如果您沒有安裝瀏覽器內錢包，您將無法執行任何操作;因此，您將無法執行任何操作。但是，您可以查看管理員狀態。  

### <a name="becoming-an-admin"></a>成為管理員

如果您是部署在網路上的第一個成員，則會自動成為管理員，並且同位節點將列為驗證程式。 如果您要加入網路，則需要以管理員身份獲得多數（大於 50%） 的投票。現有管理集。 如果您選擇不成為管理員，您的節點仍同步並驗證區塊鏈;但是，他們不參與塊創建過程。 要開始投票過程以成為管理員，請選擇 **"提名"** 並輸入您的以子坊位址和別名。

![提名](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>候選

選擇"**候選"** 選項卡將顯示當前一組候選管理員。  一旦候選人獲得當前管理員的多數投票，該候選人將升任管理員。 要對候選人進行投票，請選擇該行並選擇 **"在 中投票**"。 如果您在投票中改變主意，請選擇該候選人並選擇 **"撤銷"投票**。

![候選](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>管理員

"**管理員"** 選項卡顯示當前管理員集，並使您能夠投反對票。  一旦管理員失去超過 50% 的支援，他們將作為網路上的管理員刪除。 管理員擁有的任何驗證器節點都會失去驗證器狀態，並成為網路上的事務節點。 管理員可能由於任何原因被移除;然而，由財團事先就政策達成一致。

![管理員](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>驗證程式

選擇**驗證器**選項卡將顯示實例的當前已部署同位節點及其目前狀態（節點類型）。 每個聯營成員在此清單中都有一組不同的驗證器，因為此視圖表示當前部署的聯營成員。 如果實例是新部署的，但您尚未添加驗證器，則可以選擇**添加驗證器**。 添加驗證程式會自動選擇區域平衡的同位節點集，並將其分配給驗證器集。 如果部署的節點數超過允許的容量，則其餘節點將成為網路上的事務節點。

指派每個驗證器的位址時，會自動透過 Azure 中的[身分識別存放區](#identity-store)指派。  如果節點出現故障，它將放棄其標識，從而允許部署中的另一個節點佔據其位置。 此過程可確保您的共識參與獲得高支援。

![驗證程式](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>聯盟名稱

任何管理員可以更新聯合體名稱。  選擇左上角的齒輪圖示以更新聯合體名稱。

### <a name="account-menu"></a>帳戶功能表

在右上角，是您的以非坊帳號別名和標識。  如果您是管理員，則可以更新別名。

![帳戶](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>以地庭開發<a id="tutorials"></a>

要編譯、部署和測試智慧合約，以下是您可以考慮的以裡瑟姆開發的幾個選項：
* [特魯弗爾套房](https://www.trufflesuite.com/docs/truffle/overview)- 基於客戶的以地庭開發環境
* [以非庭混音](https://remix-ide.readthedocs.io/en/latest/index.html )- 基於瀏覽器和本地以地開發環境

### <a name="compile-deploy-and-execute-smart-contract"></a>編譯、部署和執行智慧合同

在下面的示例中，您將創建一個簡單的智慧合約。 您可以使用 Truffle 編譯智慧合約並將其部署到區塊鏈網路。 部署後，通過事務調用智慧合約函數。

#### <a name="prerequisites"></a>Prerequisites

* 安裝 [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)。 特魯弗爾和Web3需要Python。 選擇安裝選項以在路徑中包含 Python。
* 安裝特魯夫拉夫 v5.0.5 `npm install -g truffle@v5.0.5`。 Truffle 需要安裝數個工具，包括 [Node.js](https://nodejs.org) 和 [Git](https://git-scm.com/)。 有關詳細資訊，請參閱[特魯弗爾文檔](https://github.com/trufflesuite/truffle)。

### <a name="create-truffle-project"></a>建立 Truffle 專案

在編譯和部署智慧合約之前，需要創建 Truffle 專案。

1. 開啟命令提示字元或殼層。
1. 建立名為 `HelloWorld` 的資料夾。
1. 將目錄更改為新`HelloWorld`資料夾。
1. 使用 命令`truffle init`初始化新的特魯弗爾專案。

    ![創建新的特魯弗爾專案](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>添加智慧合約

在特魯弗爾專案**的合同**子目錄中創建智慧合約。

1. 在 Truffle 專案`postBox.sol`**的合同**子目錄中的命名中創建檔。
1. 將以下實體代碼添加到**postBox.sol**。

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

### <a name="deploy-smart-contract-using-truffle"></a>使用特魯弗爾部署智慧合約

Truffle 專案包含一個用於區塊鏈網路連接詳細資訊的設定檔。 修改設定檔以包括網路的連接資訊。

> [!WARNING]
> 切勿通過網路發送以地庭私密金鑰。 請確定每個交易都已先在本機簽署，再透過網路傳送已簽署的交易。

1. 您需要[在部署區塊鏈網路時使用的以地庭管理員帳戶](#ethereum-settings)的助記符短語。 如果使用 MetaMask 創建帳戶，則可以從 MetaMask 檢索助記符。 選擇 MetaMask 擴展右上角的管理員帳戶圖示，然後選擇 **"設置>安全&隱私>顯示種子詞**"。
1. 將特魯弗爾專案中`truffle-config.js`的內容替換為以下內容。 替換預留位置終結點和助記符值。

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

1. 由於我們使用特魯弗爾高清錢包供應商，請使用 命令`npm install truffle-hdwallet-provider --save`在您的專案中安裝模組。

Truffle 使用遷移腳本將智慧合約部署到區塊鏈網路。 您需要一個遷移腳本來部署新的智慧合約。

1. 添加新遷移以部署新合同。 在特魯`2_deploy_contracts.js`弗爾專案的**遷移**子目錄中創建檔。

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. 使用 Truffle 遷移命令部署到 PoA 網路。 在特魯弗爾專案目錄中的命令提示符下，運行：

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>調用智慧合約功能

現在，智慧合約已部署，您可以發送事務來調用函數。

1. 在 Truffle 專案目錄中，創建名為`sendtransaction.js`的新檔。
1. 添加以下內容以**發送事務.js**。

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

1. 使用特魯弗爾執行命令執行腳本。

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![通過事務執行腳本以調用函數](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>WebAssembly (WASM) 支援

在新部署的 PoA 網路上已為您啟用 WebAssembly 支援。 它可讓您以任何能夠 transpile 為 Web 組件的語言 (Rust、C、C++) 進行智慧合約開發。 有關詳細資訊，請參閱：[同位技術](https://github.com/paritytech/pwasm-tutorial)Web 組裝和教程的[奇偶校](https://wiki.parity.io/WebAssembly-Home)驗

## <a name="faq"></a>常見問題集

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>我注意到網路上有許多事務我沒有發送。 這些交易來自何處？

將[個人 API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html) 解除鎖定並不安全。 Bot 會接聽已解除鎖定的以太坊帳戶，並嘗試清空資金。 Bot 會假設這些帳戶含有真實的以太幣，並嘗試率先竊取餘額。 請勿在網路上啟用個人 API。 而是使用 MetaMask 之類的錢包手動或以程式設計方式對交易進行預簽名。

### <a name="how-to-ssh-onto-a-vm"></a>如何透過 SSH 連線至 VM？

基於安全考量，並不會顯示 SSH 連接埠。 請遵循[本指南以啟用 SSH 連接埠](#ssh-access)。

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>如何設定稽核成員或交易節點？

事務節點是一組同位用戶端，與網路對等，但不參與共識。 這些節點仍可用來提交以太坊交易，以及讀取智慧合約狀態。 該機制適用于向網路上的非權威聯合體成員提供可審計性。 為此，請按照["擴大聯盟"](#growing-the-consortium)的步驟進行。

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask 交易為何十分耗時？

為了確保能夠以正確的順序接收交易，每個以太坊交易都會隨附累加的 nonce。 如果您在不同網路上使用了 MetaMask 中的帳戶，則需要重置 nonce 值。 按一下設置圖示（三欄）、設置、重置帳戶。 交易歷程記錄將會清除，屆時您即可重新提交交易。

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>我是否需要在 MetaMask 中指定燃料費？

乙太幣在權威證明聯盟中派不上用場。 因此，在 MetaMask 中提交交易時，無需指定天然氣費用。

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>如果因無法佈建 Azure OMS 而導致我的部署失敗，該怎麼辦？

監視是一個選擇性功能。 在某些極少數情況下，部署由於無法成功預配 Azure 監視器資源而失敗，則可以在沒有 Azure 監視器的情況下重新部署。

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>公用 IP 部署與私人網路部署是否相容？

否。 對等互連需要雙向通信，因此整個網路必須是公共的或私有的。

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>什麼是權威證明的預期交易輸送量？

交易輸送量高度倚賴交易和網路拓撲的類型。 我們已透過跨多區域部署的網路，使用簡單交易進行平均每秒 400 個交易的基準測試。

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>如何訂閱智慧合約事件？

「以太坊權威證明」現在支援 Web 通訊端。  檢查部署輸出以查找 Web 通訊端 URL 和埠。

## <a name="next-steps"></a>後續步驟

有關更多 Azure 區塊鏈解決方案，請參閱[Azure 區塊鏈文檔](https://docs.microsoft.com/azure/blockchain/)。
