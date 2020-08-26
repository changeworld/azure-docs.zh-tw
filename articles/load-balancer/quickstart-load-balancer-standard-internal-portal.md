---
title: 快速入門：建立內部負載平衡器 - Azure 入口網站
titleSuffix: Azure Load Balancer
description: 本快速入門說明如何使用 Azure 入口網站建立內部負載平衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e8d11c2122a21b67620987ad9ef74efc99eeb98b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654492"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站來建立內部負載平衡器以平衡 VM 的負載

使用 Azure 入口網站建立內部負載平衡器和二部虛擬機器，以開始使用 Azure Load Balancer。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。  如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

在本節中，您會建立負載平衡器以平衡虛擬機器的負載。 

您可以建立公用負載平衡器或內部負載平衡器。 

當您建立內部負載平衡器時，會將虛擬網路設定為負載平衡器的網路。 

虛擬網路中的私人 IP 位址會設定為負載平衡器的前端 (命名為 **LoadBalancerFrontend**)。 

前端公用 IP 位址可以是**靜態**或**動態**。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和子網路。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]，或在搜尋方塊中搜尋 [虛擬網路]。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 [myResourceGroupLB] |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [西歐] |

3. 選取 [IP 位址] 索引標籤，或選取頁面底部的 [下一步：IP 位置] 按鈕。

4. 在 [IP 位址] 索引標籤中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | IPv4 位址空間 | 輸入 **10.1.0.0/16** |

5. 在 [子網路名稱] 下，選取 [預設] 字組。

6. 在 [編輯子網路] 中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | 子網路名稱 | 輸入 **myBackendSubnet** |
    | 子網路位址範圍 | 輸入 **10.1.0.0/24** |

7. 選取 [儲存]。

8. 選取 [安全性]**** 索引標籤。

9. 在 [BastionHost] 底下，選取 [啟用]。 輸入此資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名稱 | 輸入 **myBastionHost** |
    | AzureBastionSubnet 位址空間 | 輸入 **10.1.1.0/24** |
    | 公用 IP 位址 | 選取 [建立新的]。 </br> 在 [名稱] 中，輸入 myBastionIP。 </br> 選取 [確定]。 |


8. 選取 [檢閱 + 建立] 索引標籤，或選取 [檢閱 + 建立] 按鈕。

9. 選取 [建立]。

## <a name="create-load-balancer"></a>建立負載平衡器

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取您在上一個步驟建立的 **myResourceGroupLB**。|
    | 名稱                   | 輸入 **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]。                                        |
    | 類型          | 選取 [內部]。                                        |
    | SKU           | 選取 [標準] |
    | 虛擬網路 | 選取您在上一個步驟中建立的 **myVNet**。 |
    | 子網路  | 選取您在上一個步驟中建立的 **myBackendSubnet**。 |
    | IP 位址指派 | 選取 [動態]。 |
    | 可用性區域 | 選取 [區域備援] |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="建立標準內部負載平衡器" border="true":::
 
## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您將會設定：

* 後端位址集區的負載平衡器設定。
* 健康情況探查。
* 負載平衡器規則。

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

3. 保留其餘的預設值，然後選取 [確定]。

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
    | 建立隱含輸出規則 | 請選取 [否] 。

4. 保留其餘的預設值，然後選取 [確定]。

>[!NOTE]
>後端集區中的虛擬機器將不會有使用此設定的輸出網際網路連線。 </br> 如需有關提供輸出連線的詳細資訊，請參閱： </br> **[Azure 中的輸出連線](load-balancer-outbound-connections.md)**</br> 提供連線的選項： </br> **[僅輸出負載平衡器組態](egress-only.md)** </br> **[什麼是虛擬網路 NAT？](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

## <a name="create-backend-servers"></a>建立後端伺服器

在本節中，您可：

* 為負載平衡器的後端集區建立二部虛擬機器。
* 在虛擬機器上安裝 IIS，以測試負載平衡器。

### <a name="create-virtual-machines"></a>建立虛擬機器

在本節中，您將使用二個不同區域 (**區域 1**和**區域 2** ) 中的標準公用 IP 位址，來建立二個 VM (**myVM1** 和 **myVM2** )。 

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
    | Azure Spot 執行個體 | 選取 [否] |
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
    | 設定網路安全性群組 | 選取 [建立新的]。 </br> 在 [建立網路安全性群組] 的 [名稱] 中，輸入 **myNSG**。 </br> 選取 [確定] |
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

9. 依照步驟 1 到 8，使用下列值建立一個額外的 VM (其他所有設定則與 **myVM1** 相同)：

    | 設定 | VM 2|
    | ------- | ----- |
    | 名稱 |  **myVM2** |
    | 可用性區域 | **2** |
    | 網路安全性群組 | 選取現有的 **myNSG**|


# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。  如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

在本節中，您會建立負載平衡器以平衡虛擬機器的負載。 

您可以建立公用負載平衡器或內部負載平衡器。 

當您建立內部負載平衡器時，會將虛擬網路設定為負載平衡器的網路。 

虛擬網路中的私人 IP 位址會設定為負載平衡器的前端 (命名為 **LoadBalancerFrontend**)。 

前端公用 IP 位址可以是**靜態**或**動態**。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和子網路。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]，或在搜尋方塊中搜尋 [虛擬網路]。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 [myResourceGroupLB] |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [西歐] |

3. 選取 [IP 位址] 索引標籤，或選取頁面底部的 [下一步：IP 位置] 按鈕。

4. 在 [IP 位址] 索引標籤中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | IPv4 位址空間 | 輸入 **10.1.0.0/16** |

5. 在 [子網路名稱] 下，選取 [預設] 字組。

6. 在 [編輯子網路] 中，輸入這項資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | 子網路名稱 | 輸入 **myBackendSubnet** |
    | 子網路位址範圍 | 輸入 **10.1.0.0/24** |

7. 選取 [儲存]。

8. 選取 [安全性]**** 索引標籤。

9. 在 [BastionHost] 底下，選取 [啟用]。 輸入此資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名稱 | 輸入 **myBastionHost** |
    | AzureBastionSubnet 位址空間 | 輸入 **10.1.1.0/24** |
    | 公用 IP 位址 | 選取 [建立新的]。 </br> 在 [名稱] 中，輸入 myBastionIP。 </br> 選取 [確定]。 |


8. 選取 [檢閱 + 建立] 索引標籤，或選取 [檢閱 + 建立] 按鈕。

9. 選取 [建立]。

## <a name="create-load-balancer"></a>建立負載平衡器

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取您在上一個步驟建立的 **myResourceGroupLB**。|
    | 名稱                   | 輸入 **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]。                                        |
    | 類型          | 選取 [內部]。                                        |
    | SKU           | 選取 [基本] |
    | 虛擬網路 | 選取您在上一個步驟中建立的 **myVNet**。 |
    | 子網路  | 選取您在上一個步驟中建立的 **myBackendSubnet**。 |
    | IP 位址指派 | 選取 [動態]。 |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="建立標準內部負載平衡器" border="true":::

## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您將會設定：

* 後端位址集區的負載平衡器設定。
* 健康情況探查。
* 負載平衡器規則。

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

4. 選取 [新增]。

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

* 為負載平衡器的後端集區建立二部虛擬機器。
* 建立虛擬機器的可用性設定組。
* 在虛擬機器上安裝 IIS，以測試負載平衡器。

### <a name="create-virtual-machines"></a>建立虛擬機器

公用 IP SKU 和負載平衡器 SKU 必須相符。 對於標準負載平衡器，使用後端集區中具有基本 IP 位址的 VM。 

在本節中，您將使用基本的公用 IP 位址來建立二個 VM (**myVM1** 和 **myVM2**)。  

這二個 VM 將會新增至名為 **myAvailabilitySet** 的可用性設定組中。

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
    | Azure Spot 執行個體 | 選取 [否] |
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
    | 公用 IP | 選取 [無] |
    | NIC 網路安全性群組 | 選取 [進階]|
    | 設定網路安全性群組 | 選取 [建立新的]。 </br> 在 [建立網路安全性群組] 的 [名稱] 中，輸入 **myNSG**。 </br> 選取 [確定] |
    | **負載平衡**  |
    | 要將此虛擬機器放在現有負載平衡解決方案後面嗎? | 選取 [否] |
 
5. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。

6. 在 [管理] 索引標籤中，選取或輸入：
    
    | 設定 | 值 |
    |-|-|
    | **監視** |  |
    | 開機診斷 | 選取 [關閉] |

7. 選取 [檢閱 + 建立]。 
  
8. 檢閱設定，然後選取 [建立]。

9. 依照步驟 1 到 8，使用下列值建立一個額外的 VM (其他所有設定則與 **myVM1** 相同)：

    | 設定 | VM 2 |
    | ------- | ----- |
    | 名稱 |  **myVM2** |
    | 可用性設定組| 選取 [MyAvailabilitySet] |
    | 網路安全性群組 | 選取現有的 **myNSG**|

### <a name="add-virtual-machines-to-the-backend-pool"></a>將虛擬機器新增至後端集區

在先前步驟中建立的 VM 必須新增至 **myLoadBalancer** 的後端集區。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [myBackendPool]。

3. 在 [關聯性] 中，選取 [虛擬機器]。

4. 在 [虛擬機器] 區段中，選取 [+新增]。

5. 選取 **myVM1** 和 **myVM2** 旁的方塊。

6. 選取 [新增]。

7. 選取 [儲存]。
---

## <a name="create-test-virtual-machine"></a>建立測試虛擬機器

在本節中，您將建立名為 **myTestVM** 的 VM。  此 VM 將用來測試負載平衡器組態。

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器]。 
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 [myResourceGroupLB] |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 **myTestVM** |
    | 區域 | 選取 [西歐] |
    | 可用性選項 | 選取 [不需要基礎結構備援] |
    | 映像 | 選取 [Windows Server 2019 Datacenter] |
    | Azure Spot 執行個體 | 選取 [否] |
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
    | 公用 IP | 選取 [無]。 |
    | NIC 網路安全性群組 | 選取 [進階]|
    | 設定網路安全性群組 | 選取您在上一個步驟中建立的 **MyNSG**。|
    
5. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。

6. 在 [管理] 索引標籤中，選取或輸入：
    
    | 設定 | 值 |
    |-|-|
    | **監視** |  |
    | 開機診斷 | 選取 [關閉] |
   
7. 選取 [檢閱 + 建立]。 
  
8. 檢閱設定，然後選取 [建立]。

## <a name="install-iis"></a>安裝 IIS

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **myResourceGroupLB** 資源群組中的 [myVM1]。

2. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

4. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

5. 選取 [連接]。

6. 在伺服器桌面上，瀏覽至 [Windows 系統管理工具] > [Windows PowerShell]。

7. 在 PowerShell 視窗中執行下列命令，以便：

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
8. 關閉 **myVM1** 的 Bastion 工作階段。

9. 重複步驟 1 到 6，在 myVM2 上安裝 IIS 和更新的 iisstart.htm 檔案。


## <a name="test-the-load-balancer"></a>測試負載平衡器

1. 在 [概觀] 畫面上尋找負載平衡器的私人 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myLoadBalancer]。

2. 在 **myLoadBalancer** 的**概觀**中，記下或複製**私人 IP 位址** 旁的位址。

3. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **myResourceGroupLB** 資源群組中的 [myTestVM]。

4. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

6. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

7. 在 **myTestVM** 上開啟 **Internet Explorer**。

8. 在瀏覽器的網址列中，輸入上一個步驟中的 IP 位址。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="建立標準內部負載平衡器" border="true":::
   
若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 若要這樣做，請選取包含資源的資源群組 **myResourceGroupSLB**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已：

* 建立 Azure 標準或基本內部 Load Balancer
* 將 2 個 VM 連接至負載平衡器。
* 設定負載平衡器流量規則、健康情況探查，並測試負載平衡器。 

如需 Azure Load Balancer 的詳細資訊，請繼續參閱[什麼是 Azure Load Balancer？](load-balancer-overview.md)和 [Load Balancer 常見問題集](load-balancer-faqs.md)。

* 深入了解 [Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。
* 深入了解 [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)。
