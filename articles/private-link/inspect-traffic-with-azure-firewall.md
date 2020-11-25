---
title: 使用 Azure 防火牆來檢查以私人端點為目的地的流量
titleSuffix: Azure Private Link
description: 瞭解如何使用 Azure 防火牆來檢查以私人端點為目的地的流量。
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 5cbfd90ca65a1fb75c9cbe5602ac2a69741e378f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "96017231"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>使用 Azure 防火牆來檢查以私人端點為目的地的流量

Azure 私人端點是 Azure Private Link 的基本組建區塊。 私人端點可讓您在虛擬網路中部署的 Azure 資源與私人連結資源進行私密通訊。

私人端點可讓資源存取在虛擬網路中部署的私人連結服務。 透過虛擬網路對等互連和內部部署網路連線來存取私人端點，可延伸連線能力。

您可能需要檢查或封鎖從用戶端到透過私人端點公開之服務的流量。 使用 [Azure 防火牆](../firewall/overview.md) 或協力廠商網路虛擬裝置完成這項檢查。

適用下列限制：

* 網路安全性群組 (Nsg) 不會套用至私人端點
*  (UDR) 不會套用至私人端點的使用者定義路由
* 單一路由表可以附加至子網
* 路由表最多可支援400個路由

Azure 防火牆會使用下列其中一種方式來篩選流量：

* TCP 和 UDP 通訊協定[網路規則中的 FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP、HTTPS 和 MSSQL[應用程式規則中的 FQDN](../firewall/features.md#application-fqdn-filtering-rules) 。 

透過私人端點公開的大部分服務都會使用 HTTPS。 使用 Azure SQL 時，建議使用透過網路規則的應用程式規則。

> [!NOTE]
> 只有 [Proxy 模式](../azure-sql/database/connectivity-architecture.md#connection-policy)才支援 SQL FQDN 篩選 (連接埠 1433)。 相較于重新 *導向*， **Proxy** 模式可能會產生更多延遲。 如果您想要繼續使用重新導向模式（這是在 Azure 中連線用戶端的預設值），您可以在防火牆網路規則中使用 FQDN 篩選存取權。

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>案例1：中樞和輪輻架構-私人端點專用的虛擬網路

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="私人端點專用的虛擬網路" border="true":::

此案例是最可擴充的架構，可使用私人端點私下連接至多個 Azure 服務。 會建立路由，指向部署私人端點的網路位址空間。 這項設定可降低系統管理的額外負荷，並防止超過400個路由的限制。

如果虛擬網路已對等互連，從用戶端虛擬網路到中樞虛擬網路中的 Azure 防火牆的連線將會產生費用。

如需與對等互連虛擬網路連線相關之費用的詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/private-link/) 頁面的常見問題一節。

>[!NOTE]
> 您可以使用任何協力廠商 NVA 或 Azure 防火牆網路規則（而非應用程式規則）來執行此案例。

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>案例2：中樞和輪輻架構-私人端點和虛擬機器的共用虛擬網路

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="在相同虛擬網路中的私人端點和虛擬機器" border="true":::

此案例會在下列情況下實行：

* 私人端點不可能有專用的虛擬網路

* 使用私人端點時，只會在虛擬網路中公開一些服務

虛擬機器將會有/32 系統路由指向每個私人端點。 每個私人端點的一個路由都會設定為透過 Azure 防火牆路由傳送流量。 

維護路由表的管理額外負荷，會隨著服務在虛擬網路中的公開而增加。 達到路由限制的可能性也會增加。

根據您的整體架構，您可能會遇到400路由限制。 建議您盡可能使用案例1。

如果虛擬網路已對等互連，從用戶端虛擬網路到中樞虛擬網路中的 Azure 防火牆的連線將會產生費用。

如需與對等互連虛擬網路連線相關之費用的詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/private-link/) 頁面的常見問題一節。

>[!NOTE]
> 您可以使用任何協力廠商 NVA 或 Azure 防火牆網路規則（而非應用程式規則）來執行此案例。

## <a name="scenario-3-single-virtual-network"></a>案例3：單一虛擬網路

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="單一虛擬網路" border="true":::

有一些可執行檔限制：無法遷移至中樞和輪輻架構。 與案例2相同的考慮也適用。 在此案例中，虛擬網路對等互連費用並不適用。

>[!NOTE]
> 如果您想要使用協力廠商 NVA 或 Azure 防火牆來執行此案例，則需要網路規則，而不是應用程式規則，以將目標設為私人端點的 SNAT 流量。 否則，虛擬機器和私人端點之間的通訊將會失敗。

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>案例4：內部部署到私人端點的流量

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="私人端點的內部部署流量" border="true":::

如果您已使用下列任一項設定與內部部署網路的連線，則可執行此架構： 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [站對站 VPN](..\vpn-gateway\vpn-gateway-howto-site-to-site-resource-manager-portal.md) 

如果您的安全性需求要求用戶端流量通過私人端點公開的服務，以透過安全性應用裝置路由傳送，請部署此案例。

與上述案例2相同的考慮也適用。 在此案例中，不會有虛擬網路對等互連費用。 如需如何設定 DNS 伺服器以允許內部部署工作負載存取私人端點的詳細資訊，請參閱 [使用 DNS 轉寄站的內部部署工作負載](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)。

>[!NOTE]
> 如果您想要使用協力廠商 NVA 或 Azure 防火牆來執行此案例，則需要網路規則，而不是應用程式規則，以將目標設為私人端點的 SNAT 流量。 否則，虛擬機器和私人端點之間的通訊將會失敗。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。
* Log Analytics 工作區。  

請參閱， [在 Azure 入口網站中建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md) ，以建立工作區（如果您的訂用帳戶中沒有該工作區的話）。


## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vm"></a>建立 VM

在本節中，您將建立虛擬網路和子網，以裝載用來存取私人連結資源的 VM。 Azure SQL database 可作為範例服務。

## <a name="virtual-networks-and-parameters"></a>虛擬網路和參數

建立三個虛擬網路及其對應的子網，以：

* 包含用來限制 VM 與私人端點之間通訊的 Azure 防火牆。
* 裝載用來存取 private link 資源的 VM。
* 裝載私人端點。

使用下列資訊來取代步驟中的下列參數：

### <a name="azure-firewall-network"></a>Azure 防火牆網路
| 參數                   | 值                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | 美國中南部      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>虛擬機器網路
| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | 美國中南部      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>私人端點網路
| 參數                   | 值                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | 美國中南部      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. 重複步驟1到9來建立虛擬網路，以裝載虛擬機器和私人端點資源。

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 在 Azure 入口網站畫面的左上方，選取 [**建立資源**  >  **計算**  >  **虛擬機器**]。

2. 在 [建立虛擬機器 - 基本] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此資源群組。  |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 **myVM**。 |
    | 區域 | 選取 **(us) 美國中南部**。 |
    | 可用性選項 | 保留預設值 [不需要基礎結構備援]。 |
    | 映像 | 選取 [ **Ubuntu Server 18.04 LTS-Gen1**]。 |
    | 大小 | 選取 **Standard_B2s**。 |
    | **系統管理員帳戶** |  |
    | 驗證類型 | 選取 [密碼]。 |
    | 使用者名稱 | 輸入您選擇的使用者名稱。 |
    | 密碼 | 輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 確認密碼 | 再次輸入密碼。 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 選取 [無]。 |
    |||

3. 完成時，選取 [下一步:  磁碟]。

4. 在 [建立虛擬機器 - 磁碟]，保留預設值並選取 [下一步：網路功能]。

5. 在 [建立虛擬機器 - 網路] 中，選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 選取 [ **myVMVNet**]。  |
    | 子網路 | 選取 **VMSubnet (10.1.0.0/24)**。|
    | 公用 IP | 保留預設值 [(new) myVm-ip]。 |
    | 公用輸入連接埠 | 選取 [允許選取的連接埠]。 |
    | 選取輸入連接埠 | 選取 [ **SSH**]。|
    ||

6. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

7. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

## <a name="deploy-the-firewall"></a>部署防火牆

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。

2. 在搜尋方塊中輸入 **firewall**，然後按 **Enter**。

3. 選取 [防火牆]，然後選取 [建立]。

4. 在 [建立防火牆] 頁面上，使用下表來設定防火牆：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。  |
    | **執行個體詳細資料** |  |
    | 名稱 | 輸入 **myAzureFirewall**。 |
    | 區域 | 選取 [ **美國中南部**]。 |
    | 可用性區域 | 保留預設值 [無]。 |
    | 選擇虛擬網路    |    選取 [ **使用現有** 的]。    |
    | 虛擬網路    |    選取 [ **myAzFwVNet**]。    |
    | 公用 IP 位址    |    選取 [ **加入新** 的]，然後在 [名稱] 中輸入 **myFirewall-ip**。    |
    | 強制通道    | 將預設值保留為 **停用**。    |
    |||
5. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

6. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

## <a name="enable-firewall-logs"></a>啟用防火牆記錄

在本節中，您會啟用防火牆上的記錄。

1. 在 [Azure 入口網站中，選取左側功能表中的 [ **所有資源** ]。

2. 選取資源清單中的 [防火牆] **myAzureFirewall** 。

3. 在 [防火牆設定] 的 [**監視**] 底下，選取 [**診斷設定**]

4. 選取 [診斷設定] 中的 [ **+ 新增診斷設定** ]。

5. 在 [ **診斷] 設定** 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 診斷設定名稱 | 輸入 **myDiagSetting**。 |
    | 類別目錄詳細資料 | |
    | log | 選取 [ **>azurefirewallapplicationrule** ] 和 [ **AzureFirewallNetworkRule**]。 |
    | 目的地詳細資料 | 選取 [傳送至 Log Analytics]。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | Log Analytics 工作區 | 選取 Log Analytics 工作區。 |

6. 選取 [儲存]。

## <a name="create-azure-sql-database"></a>建立 Azure SQL 資料庫

在本節中，您會建立私用 SQL Database。

1. 在 Azure 入口網站畫面的左上方，選取 [**建立資源**  >  **資料庫**]  >  **SQL Database**。

2. 在 [ **建立 SQL Database-基本**] 中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 您已在上一節中建立此資源群組。|
    | **資料庫詳細資料** |  |
    | 資料庫名稱  | 輸入 mydatabase。  |
    | 伺服器 | 選取 [ **建立新** 的]，然後輸入下列資訊。    |
    | 伺服器名稱 | 輸入 **mydbserver**。 如果採用此名稱，請輸入唯一的名稱。   |
    | 伺服器管理員登入 | 輸入您選擇的名稱。 |
    | 密碼    |    輸入您選擇的密碼。    |
    | 確認密碼 | 再次輸入密碼    |
    | Location    | 選取 **(us) 美國中南部**。    |
    | 想要使用 SQL 彈性集區    | 保留預設值 [否]。 |
    | 計算 + 儲存體 | 保留預設的 **一般用途第5代，2虛擬核心，32 GB 的儲存體**。 |
    |||

3. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

4. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

## <a name="create-private-endpoint"></a>建立私人端點

在本節中，您會在上一節中建立 Azure SQL 資料庫的私人端點。

1. 在 [Azure 入口網站中，選取左側功能表中的 [ **所有資源** ]。

2. 在服務清單中選取 [Azure SQL server] **mydbserver** 。  如果您使用不同的伺服器名稱，請選擇該名稱。

3. 在 [伺服器設定] 中，選取 [**安全性**] 下的 [**私人端點** 連線]。

4. 選取 [+私人端點]。

5. 在 [ **建立私人端點**] 中，于 [ **基本** ] 索引標籤中輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。 |
    | **執行個體詳細資料** | |
    | 名稱 | 輸入 **SQLPrivateEndpoint**。 |
    | 區域 | 選取 **(us) 美國中南部。** |

6. 選取 [ **資源** ] 索引標籤，或選取頁面底部的 **[下一步：資源** ]。

7. 在 [ **資源** ] 索引標籤中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 連線方法 | 選取 [連線到我目錄中的 Azure 資源]。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源類型 | 選取 [Microsoft.Sql/servers]。 |
    | 資源 | 選取 **mydbserver** 或您在上一個步驟中建立的伺服器名稱。
    | 目標子資源 | 選取 [ **sqlServer**]。 |

8. 選取 [ **設定] 索引標籤，或** 選取頁面底部的 **[下一步：** 設定]。

9. **在 [設定**] 索引標籤中，輸入或選取這項資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | **網路功能** | |
    | 虛擬網路 | 選取 [ **myPEVnet**]。 |
    | 子網路 | 選取 [ **PrivateEndpointSubnet**]。 |
    | **私人 DNS 整合** | |
    | 與私人 DNS 區域整合 | 選取 [是]。 |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 私人 DNS 區域 | 保留預設 **privatelink.database.windows.net**。 |

10. 選取頁面底部的 [ **審核 + 建立** ] 索引標籤或選取 [ **審核 + 建立** ]。

11. 選取 [建立]。

12. 建立端點之後，請選取 [**安全性**] 下的 [**防火牆和虛擬網路**]。

13. 在 [ **防火牆與虛擬網路**] 中，選取 [ **是** ]，以 **允許 Azure 服務和資源存取此伺服器**。

14. 選取 [儲存]。

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>使用虛擬網路對等互連來連接虛擬網路

在本節中，我們會使用對等互連將虛擬網路 **myVMVNet** 和 **MyPEVNet** 連線至 **myAzFwVNet** 。 **MyVMVNet** 和 **myPEVNet** 之間不會有直接連線。

1. 在入口網站的搜尋列中，輸入 **myAzFwVNet**。

2. 在 [**設定**] 功能表下選取 **對等互連**，然後選取 [ **+ 新增**]。

3. 在 [ **新增對等互連** ] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 從 myAzFwVNet 到遠端虛擬網路的對等互連名稱 | 輸入 **myAzFwVNet 至 myVMVNet**。 |
    | **對等詳細資料** |  |
    | 虛擬網路部署模型  | 保留預設 **Resource Manager**。  |
    | 我知道我的資源識別碼 | 請勿核取。    |
    | 訂用帳戶 | 選取您的訂用帳戶。    |
    | 虛擬網路 | 選取 [ **myVMVNet**]。 |
    | 從遠端虛擬網路到 myAzFwVNet 的對等互連名稱    |    輸入 **myVMVNet 至 myAzFwVNet**。    |
    | **設定** | |
    | **設定虛擬網路存取設定** | |
    | 允許從 myAzFwVNet 到遠端虛擬網路的虛擬網路存取 | 保留預設值 [啟用]。    |
    | 允許從遠端虛擬網路到 myAzFwVNet 的虛擬網路存取    | 保留預設值 [啟用]。    |
    | **設定轉送的流量設定** | |
    | 允許從遠端虛擬網路轉送到 myAzFwVNet 的流量    | 選取 [啟用]。 |
    | 允許從 myAzFwVNet 轉送到遠端虛擬網路的流量 | 選取 [啟用]。 |
    | **設定閘道傳輸設定** | |
    | 允許閘道傳輸 | 保持未核取狀態 |
    |||

4. 選取 [確定]。

5. 選取 [+ 新增]  。

6. 在 [ **新增對等互連** ] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 從 myAzFwVNet 到遠端虛擬網路的對等互連名稱 | 輸入 **myAzFwVNet 至 myPEVNet**。 |
    | **對等詳細資料** |  |
    | 虛擬網路部署模型  | 保留預設 **Resource Manager**。  |
    | 我知道我的資源識別碼 | 請勿核取。    |
    | 訂用帳戶 | 選取您的訂用帳戶。    |
    | 虛擬網路 | 選取 [ **myPEVNet**]。 |
    | 從遠端虛擬網路到 myAzFwVNet 的對等互連名稱    |    輸入 **myPEVNet 至 myAzFwVNet**。    |
    | **設定** | |
    | **設定虛擬網路存取設定** | |
    | 允許從 myAzFwVNet 到遠端虛擬網路的虛擬網路存取 | 保留預設值 [啟用]。    |
    | 允許從遠端虛擬網路到 myAzFwVNet 的虛擬網路存取    | 保留預設值 [啟用]。    |
    | **設定轉送的流量設定** | |
    | 允許從遠端虛擬網路轉送到 myAzFwVNet 的流量    | 選取 [啟用]。 |
    | 允許從 myAzFwVNet 轉送到遠端虛擬網路的流量 | 選取 [啟用]。 |
    | **設定閘道傳輸設定** | |
    | 允許閘道傳輸 | 保持未核取狀態 |

7. 選取 [確定]。

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>將虛擬網路連結至私人 DNS 區域

在本節中，我們會將虛擬網路 **myVMVNet** 和 **myAzFwVNet** 連結到 **privatelink.database.windows.net** 私人 DNS 區域。 建立私人端點時，會建立此區域。 

VM 和防火牆需要有連結，才能將資料庫的 FQDN 解析為其私人端點位址。 建立私人端點時，會自動連結虛擬網路 **myPEVNet** 。

>[!NOTE]
>如果您未將 VM 和防火牆虛擬網路連結至私人 DNS 區域，則 VM 和防火牆仍能解析 SQL Server FQDN。 它們會解析為其公用 IP 位址。

1. 在入口網站的搜尋列中，輸入 **privatelink。**

2. 在搜尋結果中選取 [ **privatelink.database.windows.net** ]。

3. 在 [**設定**] 底下選取 [**虛擬網路] 連結**。

4. 選取 [+ 新增]

5. 在 [ **新增虛擬網路連結** ] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 連結名稱 | 輸入 **myVMVNet 的連結**。 |
    | **虛擬網路詳細資料** |  |
    | 我知道虛擬網路的資源識別碼  | 請勿核取。  |
    | 訂用帳戶 | 選取您的訂用帳戶。    |
    | 虛擬網路 | 選取 [ **myVMVNet**]。 |
    | **CONFIGURATION** | |
    | 啟用自動註冊 | 請勿核取。    |


6. 選取 [確定]。

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>使用 Azure 防火牆中的 SQL FQDN 來設定應用程式規則

在本節中，設定應用程式規則以允許 SQL Server **mydbserver.database.windows.net** 的 **myVM** 和私人端點之間的通訊。 

此規則可讓您透過在先前步驟中建立的防火牆進行通訊。

1. 在入口網站的搜尋列中，輸入 **myAzureFirewall**。

2. 在搜尋結果中選取 [ **myAzureFirewall** ]。

3. 在 [ **myAzureFirewall** 總覽] 的 [**設定**] 下選取 [**規則**]。

4. 選取 [應用程式規則集合] 索引標籤。

5. 選取 [ **+ 新增應用程式規則集合**]。

6. 在 [ **新增應用程式規則集合** ] 中，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **SQLPrivateEndpoint**。 |
    | 優先順序 | 輸入 **100**。 |
    | 動作 | 輸入 [ **允許**]。 |
    | **規則** |  |
    | **FQDN 標記** | |
    | 名稱  | 保留空白。  |
    | 來源類型 | 保留預設 **IP 位址**。    |
    | 來源 | 保留空白。 |
    | FQDN 標記 | 保留選取預設值 **0**。 |
    | **目標 Fqdn** | |
    | 名稱 | 輸入 **SQLPrivateEndpoint**。    |
    | 來源類型 | 保留預設 **IP 位址**。 |
    | 來源 | 輸入 **10.1.0.0/16**。 |
    | 通訊協定：埠 | 輸入 **mssql： 1433**。 |
    | 目標 Fqdn | 輸入 **mydbserver.database.windows.net**。 |
    |||

7. 選取 [新增]  。

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>透過 Azure 防火牆在虛擬機器和私人端點之間路由傳送流量

我們未直接在虛擬網路 **myVMVNet** 與 **myPEVNet** 之間建立虛擬網路對等互連。 虛擬機器 **myVM** 沒有所建立私人端點的路由。 

在本節中，我們將建立具有自訂路由的路由表。 

此路由會透過 Azure 防火牆，將流量從 **myVM** 子網傳送到虛擬網路 **myPEVNet** 的位址空間。

1. 在 Azure 入口網站功能表上，或從 [首頁] 頁面，選取 [建立資源]。

2. 在搜尋方塊中輸入 **路由表** ，然後按 **enter** 鍵。

3. 選取 [ **路由表** ]，然後選取 [ **建立**]。

4. 在 [ **建立路由表** ] 頁面上，使用下表來設定路由表：

    | 設定 | 值 |
    | ------- | ----- |
    | **專案詳細資料** | |
    | 訂用帳戶 | 選取您的訂用帳戶。 |
    | 資源群組 | 選取 **myResourceGroup**。  |
    | **執行個體詳細資料** |  |
    | 區域 | 選取 [ **美國中南部**]。 |
    | 名稱 | 輸入 **VMsubnet 至 AzureFirewall**。 |
    | 傳播閘道路由 | 請選取 [否] 。 |

5. 選取 [檢閱 + 建立]。 您會移至 [檢閱 + 建立] 頁面，其中 Azure 會驗證您的設定。

6. 當您看到 [驗證成功] 訊息時，請選取 [建立]。

7. 部署完成後，請選取 [ **移至資源**]。

8. 在 [**設定**] 底下選取 [**路由**]。

9. 選取 [+ 新增]  。

10. 在 [ **新增路由** ] 頁面上，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 路由名稱 | 輸入 **myVMsubnet 至 privateendpoint**。 |
    | 位址首碼 | 輸入 **10.2.0.0/16**。  |
    | 下一個躍點類型 | 選取 [ **虛擬裝置**]。 |
    | 下一個躍點位址 | 輸入 **10.0.0.4**。 |

11. 選取 [確定]。

12. 在 [**設定**] 底下選取 [**子網**]。

13. 選取 [+ 關聯]。

14. 在 [ **關聯子網** ] 頁面上，輸入或選取下列資訊：

    | 設定 | 值 |
    | ------- | ----- |
    | 虛擬網路 | 選取 [ **myVMVNet**]。 |
    | 子網路 | 選取 [ **VMSubnet**]。  |

15. 選取 [確定]。

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>從用戶端電腦連接到虛擬機器

從網際網路連線至 VM：myVm，如下所示：

1. 在入口網站的搜尋列中，輸入 **myVm-ip**。

2. 在搜尋結果中選取 [ **myVM-ip** ]。

3. 複製或寫下 [ **IP 位址**] 下的值。

4. 如果您是使用 Windows 10，請使用 PowerShell 執行下列命令。 針對其他 Windows 用戶端版本，請使用 SSH 用戶端，例如 [Putty](https://www.putty.org/)：

* 以您在建立 VM 時所輸入的系統管理員使用者名稱取代 **username** 。

* 使用上一個步驟中的 IP 位址來取代 **IPaddress** 。

    ```bash
    ssh username@IPaddress
    ```

5. 輸入您在建立 **myVm** 時所定義的密碼

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>從虛擬機器私下存取 SQL Server

在本節中，您會使用私人端點私下連接到 SQL Database。

1. 輸入 `nslookup mydbserver.database.windows.net`
    
    您會收到類似下面的訊息：

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. 安裝 [SQL Server 命令列工具](/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools)。

3. 執行下列命令以連接到 SQL Server。 使用您在先前步驟中建立 SQL Server 時定義的伺服器管理員和密碼。

* **\<ServerAdmin>** 將取代為您在 SQL server 建立期間所輸入的管理員使用者名稱。

* **\<YourPassword>** 將取代為您在 SQL server 建立期間所輸入的系統管理員密碼。

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. 成功登入時，會顯示 SQL 命令提示字元。 輸入 **exit** 以結束 **sqlcmd** 工具。

5. 輸入 **exit** 以關閉 **myVM** 的連線。

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>驗證 Azure 防火牆記錄中的流量

1. 在 [Azure 入口網站中，選取 [ **所有資源** ]，然後選取您的 Log Analytics 工作區。

2. 在 [Log Analytics 工作區] 頁面中選取 **[一般**] 底下的 [**記錄**]。

3. 選取藍色 **開始** 按鈕。

4. 在 [**範例查詢**] 視窗中，選取 [**所有查詢**] 下的 [**防火牆**]。

5. 選取 [**應用程式規則記錄資料**] 下的 [**執行**] 按鈕。

6. 在記錄查詢輸出中，確認 **mydbserver.database.windows.net** 列在 [ **FQDN** ] 底下，而 [ **SQLPrivateEndpoint** ] 列在 [ **RuleCollection**] 之下。

## <a name="clean-up-resources"></a>清除資源

當您使用完資源時，請刪除資源群組及其包含的所有資源：

1. 在入口網站頂端的 [搜尋] 方塊中輸入 **myResourceGroup**，然後從搜尋結果中選取 [myResourceGroup]。

1. 選取 [刪除資源群組]。

1. 針對 [輸入資源群組名稱] 輸入 **myResourceGroup**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本文中，您已探索不同的案例，可用來限制使用 Azure 防火牆的虛擬機器和私人端點之間的流量。 

您已連線至 VM，並透過 Azure 防火牆使用 private link 安全地向資料庫通訊。

若要深入瞭解私人端點，請參閱 [什麼是 Azure 私人端點？](private-endpoint-overview.md)。