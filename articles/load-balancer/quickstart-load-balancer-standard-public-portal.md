---
title: 快速入門：建立公用負載平衡器 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure 入口網站建立負載平衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: eb23f1e703c2e447c484ccb366914cb4b23c5bf7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536536"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立負載平衡器以平衡 VM 的負載

使用 Azure 入口網站建立公用負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[選項 1 (預設值)：建立負載平衡器 (標準 SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。  如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

在本節中，您會建立負載平衡器以平衡虛擬機器的負載。 

您可以建立公用負載平衡器或內部負載平衡器。 

建立公用負載平衡器時，也必須建立新的公用 IP 位址，而該 IP 位址會設定為負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend)。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]，並在文字方塊中輸入 **myResourceGroupLB**。|
    | 名稱                   | 輸入 **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [標準] |
    | 公用 IP 位址 | 選取 [建立新的]。 如果您有想要使用的現有公用 IP，請選取 [使用現有]。 |
    | 公用 IP 位址名稱 | 在文字方塊中輸入 **myPublicIP**。|
    | 可用性區域 | 選取「區域備援」以建立復原性負載平衡器。 若要建立區域性負載平衡器，請從 1、2 或 3 選取特定區域 |
    | 新增公用 IPv6 位址 | 請選取 [否] 。 </br> 如需 IPv6 位址和負載平衡器的詳細資訊，請參閱[什麼是適用於 Azure 虛擬網路的 IPv6？](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="建立標準負載平衡器" border="true":::
 
## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您將會設定：

* 後端位址集區的負載平衡器設定。
* 健康情況探查。
* 負載平衡器規則和自動輸出規則。

### <a name="create-a-backend-pool"></a>建立後端集區

後端位址集區會包含已連線至負載平衡器之虛擬 NIC 的 IP 位址。 

建立後端位址集區 **myBackendPool**，以包含用於平衡網際網路流量負載的虛擬機器。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [新增]。

3. 在 [新增後端集區] 頁面上，針對名稱輸入 **myBackEndPool** 作為後端集區的名稱，然後選取 [新增]。

### <a name="create-a-health-probe"></a>建立健康狀態探查

負載平衡器會透過健康情況探查監視應用程式的狀態。 

健康狀態探查會根據 VM 對健康情況檢查的回應，從負載平衡器輪替中新增或移除 VM。 

建立名為 myHealthProbe 的健康狀態探查，以監視 VM 的健康狀態。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [健康狀態探查]，然後選取 [新增]。
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myHealthProbe**。 |
    | 通訊協定 | 選取 [HTTP]。 |
    | 連接埠 | 輸入 **80**。|
    | 間隔 | 輸入 **15** 作為探查嘗試之間的 [間隔]  秒數。 |
    | 狀況不良臨界值 | 選取 [2] 作為 [狀況不良閾值] 的數值，或將 VM 視為狀況不良之前，必須達到的連續探查失敗次數。|
    | | |

3. 選取 [確定]。

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量。 來源和目的地連接埠會在規則中定義。 

在本節中，您將建立負載平衡器規則：

* 已命名為 **myHTTPRule**。
* 在名為 **LoadBalancerFrontEnd** 的前端中。
* 接聽**連接埠 80**。
* 將負載平衡的流量導向至**連接埠 80**上名為 **myBackendPool** 的後端。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 下選取 [負載平衡規則]，然後選取 [新增]。

3. 使用下列值來設定負載平衡規則：
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myHTTPRule**。 |
    | IP 版本 | 選取 **IPv4** |
    | 前端 IP 位址 | 選取 **LoadBalancerFrontEnd** |
    | 通訊協定 | 選取 [TCP]。 |
    | 連接埠 | 輸入 **80**。|
    | 後端連接埠 | 輸入 **80**。 |
    | 後端集區 | 選取 [myBackendPool]。|
    | 健全狀況探查 | 選取 [myHealthProbe]。 |
    | 建立隱含輸出規則 | 選取 [是]。 </br> 如需詳細資訊和進階的輸出規則組態，請參閱： </br> [Azure 中的輸出連線](load-balancer-outbound-connections.md) </br> [使用 Azure 入口網站在 Standard Load Balancer 中設定負載平衡和輸出規則](configure-load-balancer-outbound-portal.md)

4. 保留其餘的預設值，然後選取 [確定]。

## <a name="create-backend-servers"></a>建立後端伺服器

在本節中，您可：

* 建立虛擬網路。
* 為負載平衡器的後端集區建立三部虛擬機器。
* 在虛擬機器上安裝 IIS，以測試負載平衡器。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您需要使用下列資訊來取代步驟中的參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西歐      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>建立虛擬機器

公用 IP SKU 和負載平衡器 SKU 必須相符。 對於標準負載平衡器，使用後端集區中具有標準 IP 位址的 VM。 

在本節中，您將使用三個不同區域 (**區域 1**、**區域 2**和**區域 3**) 中的標準公用 IP 位址，來建立三個 VM (**myVM1**、**myVM2** 和 **myVM3**)。 

這些 VM 會新增至先前建立之負載平衡器的後端集區。

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器]。 
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 [myResourceGroupLB] |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 **myVM1** |
    | 區域 | 選取 [西歐] |
    | 可用性選項 | 選取 [可用性區域] |
    | 可用性區域 | 選取 [1] |
    | 映像 | 選取 [Windows Server 2019 Datacenter] |
    | Azure Spot 執行個體 | 選取 [否]  |
    | 大小 | 選擇 VM 大小或接受預設設定 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入使用者名稱 |
    | 密碼 | 輸入密碼 |
    | 確認密碼 | 再次輸入密碼 |

3. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
4. 在 [網路功能] 索引標籤中，選取或輸入：

    | 設定 | 值 |
    |-|-|
    | **網路介面** |  |
    | 虛擬網路 | **myVNet** |
    | 子網路 | **myBackendSubnet** |
    | 公用 IP | 接受預設值 **myVM-ip**。 </br> IP 將會自動成為區域 1 中的標準 SKU IP。 |
    | NIC 網路安全性群組 | 選取 [進階]|
    | 設定網路安全性群組 | 選取 [建立新的]。 </br> 在 [建立網路安全性群組] 的 [名稱] 中，輸入 **myNSG**。 </br> 在 [輸入規則]下，選取 [+新增輸入規則]。 </br> 在 [目的地連接埠範圍] 下，輸入 **80**。 </br> 在 [優先順序] 下，輸入 **100**。 </br> 在 [名稱] 中，輸入 **myHTTPRule** </br> 選取 [新增] </br> 選取 [確定] |
    | **負載平衡**  |
    | 要將此虛擬機器放在現有負載平衡解決方案後面嗎? | 選取 [是] |
    | **負載平衡設定** |
    | 負載平衡選項 | 選取 [Azure 負載平衡] |
    | 選取負載平衡器 | 選取 [myLoadBalancer]  |
    | 選取後端集區 | 選取 [myBackendPool] |

5. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。

6. 在 [管理] 索引標籤中，選取或輸入：
    
    | 設定 | 值 |
    |-|-|
    | **監視** |  |
    | 開機診斷 | 選取 [關閉] |
   
7. 選取 [檢閱 + 建立]。 
  
8. 檢閱設定，然後選取 [建立]。

9. 依照步驟 1 到 8，使用下列值建立兩個額外的 VM (其他所有設定則與 **myVM1** 相同)：

    | 設定 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名稱 |  **myVM2** |**myVM3**|
    | 可用性區域 | **2** |**3**|
    | 網路安全性群組 | 選取現有的 **myNSG**| 選取現有的 **myNSG**|


# <a name="option-2-create-a-load-balancer-basic-sku"></a>[選項 2：建立負載平衡器 (基本 SKU)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。  如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

在本節中，您會建立負載平衡器以平衡虛擬機器的負載。 

您可以建立公用負載平衡器或內部負載平衡器。 

建立公用負載平衡器時，也必須建立新的公用 IP 位址，而該 IP 位址會設定為負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend)。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]，並在文字方塊中輸入 **myResourceGroupLB**。|
    | 名稱                   | 輸入 **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [基本] |
    | 公用 IP 位址 | 選取 [建立新的]。 如果您有想要使用的現有公用 IP，請選取 [使用現有]。 |
    | 公用 IP 位址名稱 | 在文字方塊中輸入 **myPublicIP**。|
    | 指派 | 選取 [動態]。 |
    | 新增公用 IPv6 位址 | 請選取 [否] 。 </br> 如需 IPv6 位址和負載平衡器的詳細資訊，請參閱[什麼是適用於 Azure 虛擬網路的 IPv6？](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="建立基本負載平衡器" border="true":::

## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您將會設定：

* 建立虛擬網路。
* 後端位址集區的負載平衡器設定。
* 健康情況探查。
* 負載平衡器規則。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您需要使用下列資訊來取代步驟中的參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西歐      |
| **\<IPv4-address-space>**   | 10.1.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-backend-pool"></a>建立後端集區

後端位址集區會包含已連線至負載平衡器之虛擬 NIC 的 IP 位址。 

建立後端位址集區 **myBackendPool**，以包含用於平衡網際網路流量負載的虛擬機器。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [新增]。

3. 在 [新增後端集區] 頁面上，輸入或選取：
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myBackendPool**。 |
    | 虛擬網路 | 選取 [myVNet]。 |
    | 與下列產生關聯： | 選取 [虛擬機器] |

### <a name="create-a-health-probe"></a>建立健康狀態探查

負載平衡器會透過健康情況探查監視應用程式的狀態。 

健康狀態探查會根據 VM 對健康情況檢查的回應，從負載平衡器輪替中新增或移除 VM。 

建立名為 myHealthProbe 的健康狀態探查，以監視 VM 的健康狀態。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [健康狀態探查]，然後選取 [新增]。
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myHealthProbe**。 |
    | 通訊協定 | 選取 [HTTP]。 |
    | 連接埠 | 輸入 **80**。|
    | Path | 輸入 **/** |
    | 間隔 | 輸入 **15** 作為探查嘗試之間的 [間隔]  秒數。 |
    | 狀況不良臨界值 | 選取 [2] 作為 [狀況不良閾值] 的數值，或將 VM 視為狀況不良之前，必須達到的連續探查失敗次數。|

3. 選取 [確定]。

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量。 來源和目的地連接埠會在規則中定義。 

在本節中，您將建立負載平衡器規則：

* 已命名為 **myHTTPRule**。
* 在名為 **LoadBalancerFrontEnd** 的前端中。
* 接聽**連接埠 80**。
* 將負載平衡的流量導向至**連接埠 80**上名為 **myBackendPool** 的後端。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 下選取 [負載平衡規則]，然後選取 [新增]。

3. 使用下列值來設定負載平衡規則：
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myHTTPRule**。 |
    | IP 版本 | 選取 **IPv4** |
    | 前端 IP 位址 | 選取 **LoadBalancerFrontEnd** |
    | 通訊協定 | 選取 [TCP]。 |
    | 連接埠 | 輸入 **80**。|
    | 後端連接埠 | 輸入 **80**。 |
    | 後端集區 | 選取 [myBackendPool]。|
    | 健全狀況探查 | 選取 [myHealthProbe]。 |
 
4. 保留其餘的預設值，然後選取 [確定]。

## <a name="create-backend-servers"></a>建立後端伺服器

在本節中，您可：

* 為負載平衡器的後端集區建立三部虛擬機器。
* 建立虛擬機器的可用性設定組。
* 在虛擬機器上安裝 IIS，以測試負載平衡器。

### <a name="create-virtual-machines"></a>建立虛擬機器

公用 IP SKU 和負載平衡器 SKU 必須相符。 對於標準負載平衡器，使用後端集區中具有基本 IP 位址的 VM。 

在本節中，您將使用基本的公用 IP 位址來建立三個 VM (**myVM1**、**myVM2**和 **myVM3**)。  

這三個 VM 將會新增至名為 **myAvailabilitySet** 的可用性設定組中。

這些 VM 會新增至先前建立之負載平衡器的後端集區。

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器]。 
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 [myResourceGroupLB] |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 **myVM1** |
    | 區域 | 選取 [西歐] |
    | 可用性選項 | 選取 [可用性設定組] |
    | 可用性設定組 | 選取 [建立新的]。 </br> 在 [名稱] 中輸入 **myAvailabilitySet**。 </br> 選取 [確定] |
    | 映像 | **Windows Server 2019 Datacenter** |
    | Azure Spot 執行個體 | 選取 [否]  |
    | 大小 | 選擇 VM 大小或接受預設設定 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入使用者名稱 |
    | 密碼 | 輸入密碼 |
    | 確認密碼 | 再次輸入密碼 |

3. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
4. 在 [網路功能] 索引標籤中，選取或輸入：

    | 設定 | 值 |
    |-|-|
    | **網路介面** |  |
    | 虛擬網路 | 選取 [myVNet] |
    | 子網路 | 選取 [myBackendSubnet]。 |
    | 公用 IP | 選取 [建立新的] </br> 在名稱中輸入 **myVM-ip**。 </br> 選取 [確定] |
    | NIC 網路安全性群組 | 選取 [進階]|
    | 設定網路安全性群組 | 選取 [建立新的]。 </br> 在 [建立網路安全性群組] 的 [名稱] 中，輸入 **myNSG**。 </br> 在 [輸入規則]下，選取 [+新增輸入規則]。 </br> 在 [目的地連接埠範圍] 下，輸入 **80**。 </br> 在 [優先順序] 下，輸入 **100**。 </br> 在 [名稱] 中，輸入 **myHTTPRule** </br> 選取 [新增] </br> 選取 [確定] |
    | **負載平衡**  |
    | 要將此虛擬機器放在現有負載平衡解決方案後面嗎? | 選取 [否]  |
 
5. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。

6. 在 [管理] 索引標籤中，選取或輸入：
    | 設定 | 值 |
    |-|-|
    | **監視** | |
    | 開機診斷 | 選取 [關閉] |

7. 選取 [檢閱 + 建立]。 
  
8. 檢閱設定，然後選取 [建立]。

9. 依照步驟 1 到 8，使用下列值建立兩個額外的 VM (其他所有設定則與 **myVM1** 相同)：

    | 設定 | VM 2| VM 3|
    | ------- | ----- |---|
    | 名稱 |  **myVM2** |**myVM3**|
    | 可用性設定組| 選取 [MyAvailabilitySet] | 選取 [MyAvailabilitySet]|
    | 網路安全性群組 | 選取現有的 **myNSG**| 選取現有的 **myNSG**|
---

### <a name="install-iis"></a>安裝 IIS

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **myResourceGroupLB** 資源群組中的 [myVM1]。

2. 在 [概觀] 頁面上，選取 [連線]，以下載 VM 的 RDP 檔案。

3. 開啟 RDP 檔案。

4. 使用您在此 VM 建立期間提供的認證登入 VM。

5. 在伺服器桌面上，瀏覽至 [Windows 系統管理工具]>[Windows PowerShell]。

6. 在 PowerShell 視窗中執行下列命令，以便：

    * 安裝 IIS 伺服器
    * 移除預設的 iisstart.htm 檔案
    * 新增顯示 VM 名稱的新 iisstart.htm 檔案：

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
7. 使用 myVM1 關閉 RDP 工作階段。

8. 重複步驟 1 到 6，在 myVM2 和 myVM3 上安裝 IIS 和更新的 iisstart.htm 檔案。

## <a name="test-the-load-balancer"></a>測試負載平衡器

1. 在 [概觀] 畫面上尋找負載平衡器的公用 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myPublicIP]。

2. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

   ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 若要這樣做，請選取包含資源的資源群組 **myResourceGroupSLB**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已：

* 建立 Azure 標準或基本 Load Balancer
* 將 3 個 VM 連接至負載平衡器。
* 設定負載平衡器流量規則、健康情況探查，並測試負載平衡器。 

如需 Azure Load Balancer 的詳細資訊，請繼續參閱[什麼是 Azure Load Balancer？](load-balancer-overview.md)和 [Load Balancer 常見問題集](load-balancer-faqs.md)。

深入了解 [Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。
