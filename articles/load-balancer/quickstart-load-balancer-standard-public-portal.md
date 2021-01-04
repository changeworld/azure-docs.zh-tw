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
ms.date: 10/22/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 8827171788bd83a202b3607537204c71c34f29e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511836"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立公用負載平衡器以平衡 VM 的負載

使用 Azure 入口網站建立公用負載平衡器和三部虛擬機器，以開始使用 Azure Load Balancer。

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

---

# <a name="standard-sku"></a>[**標準 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。  如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="針對快速入門所建立的標準負載平衡器資源。" border="false":::

*圖：快速入門中建立的資源。*

在本節中，您會建立負載平衡器以平衡虛擬機器的負載。 

建立公用負載平衡器時，也必須建立新的公用 IP 位址，而該 IP 位址會設定為負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend)。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]，並在文字方塊中輸入 **CreatePubLBQS-rg**。|
    | 名稱                   | 輸入 **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [標準] |
    | 公用 IP 位址 | 選取 [建立新的]。 如果您有想要使用的現有公用 IP，請選取 [使用現有]。 |
    | 公用 IP 位址名稱 | 在文字方塊中輸入 **myPublicIP**。|
    | 可用性區域 | 選取「區域備援」以建立復原性負載平衡器。 若要建立區域性負載平衡器，請從 1、2 或 3 選取特定區域 |
    | 新增公用 IPv6 位址 | 請選取 [否] 。 </br> 如需 IPv6 位址和負載平衡器的詳細資訊，請參閱[什麼是適用於 Azure 虛擬網路的 IPv6？](../virtual-network/ipv6-overview.md)  |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="建立標準負載平衡器" border="true":::
 
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
* 接聽 **連接埠 80**。
* 將負載平衡的流量導向至 **連接埠 80** 上名為 **myBackendPool** 的後端。

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
    | 閒置逾時 (分鐘) | 將滑桿移至 **15 分鐘**。 |
    | TCP 重設 | 選取 [啟用]  。 |
    | 輸出來源網路位址轉譯 (SNAT) | 選取 [(建議) 使用輸出規則，對後端集區成員提供網際網路的存取權]。 |

4. 保留其餘的預設值，然後選取 [確定]。

## <a name="create-backend-servers"></a>建立後端伺服器

在本節中，您可：

* 建立虛擬網路。
* 為負載平衡器的後端集區建立三部虛擬機器。
* 在虛擬機器上安裝 IIS，以測試負載平衡器。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和子網路。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 **CreatePubLBQS-rg** |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [西歐] |

3. 選取 [IP 位址]  索引標籤，或選取頁面底部的 [下一步：  IP 位置] 按鈕。

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

8. 選取 [安全性] 索引標籤。

9. 在 [BastionHost] 底下，選取 [啟用]。 輸入此資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名稱 | 輸入 **myBastionHost** |
    | AzureBastionSubnet 位址空間 | 輸入 **10.1.1.0/24** |
    | 公用 IP 位址 | 選取 [建立新的]。 </br> 在 [名稱] 中，輸入 myBastionIP。 </br> 選取 [確定]。 |


8. 選取 [檢閱 + 建立] 索引標籤，或選取 [檢閱 + 建立] 按鈕。

9. 選取 [建立]。

### <a name="create-virtual-machines"></a>建立虛擬機器

在本節中，您將在三個不同區域 (**區域 1**、**區域 2** 和 **區域 3**) 中建立三個 VM (**myVM1**、**myVM2** 和 **myVM3**)。 

這些 VM 會新增至先前建立之負載平衡器的後端集區。

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器]。 
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 **CreatePubLBQS-rg** |
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
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 選取 [無] |

3. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
4. 在 [網路功能] 索引標籤中，選取或輸入：

    | 設定 | 值 |
    |-|-|
    | **網路介面** |  |
    | 虛擬網路 | **myVNet** |
    | 子網路 | **myBackendSubnet** |
    | 公用 IP | 選取 [無]。 |
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

## <a name="create-outbound-rule-configuration"></a>建立輸出規則設定
負載平衡器輸出規則會在後端集區中設定 VM 的輸出 SNAT。 

如需輸出連線詳細資訊，請參閱 [Azure 中的輸出連線](load-balancer-outbound-connections.md)。

### <a name="create-outbound-rule"></a>建立輸出規則

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 下選取 [輸出規則]，然後選取 [新增]。

3. 使用這些值來設定輸出規則：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myOutboundRule**。 |
    | 前端 IP 位址 | 選取 [建立新的]。 </br> 在 [名稱]中，輸入 **LoadBalancerFrontEndOutbound**。 </br> 選取 [IP 位址] 或 [IP 前置詞]。 </br> 選取 [公用 IP 位址] 或 [公用 IP 前置詞] 底下的 [建立新的]。 </br> 針對 [名稱]，輸入 **myPublicIPOutbound** 或 **myPublicIPPrefixOutbound**。 </br> 選取 [新增]。|
    | 閒置逾時 (分鐘) | 將滑桿移至 **15 分鐘**。|
    | TCP 重設 | 選取 [啟用] 。|
    | 後端集區 | 選取 [建立新的]。 </br> 在 [名稱] 中輸入 **myBackendPoolOutbound**。 </br> 選取 [新增]。 |
    | 連接埠配置 -> 連接埠配置 | 選取 [手動選擇輸出連接埠數目] |
    | 輸出連接埠 -> 選擇依據 | 選取 [每個執行個體的連接埠數] |
    | 輸出連接埠 -> 每個執行個體的連接埠數 | 輸入 **10000**。 |

4. 選取 [新增]。

### <a name="add-virtual-machines-to-outbound-pool"></a>將虛擬機器新增至輸出集區

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [後端集區]。

3. 選取 **myBackendPoolOutbound**。

4. 在 [虛擬網路] 中，選取 [myVNet]。

5. 在 [虛擬機器] 中，選取 [新增]。

6. 勾選 **myVM1**、**myVM2** 和 **myVM3** 旁的方塊。 

7. 選取 [新增]。

8. 選取 [儲存]。

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>建議對生產環境工作負載使用標準 SKU 負載平衡器。  如需 SKU 的詳細資訊，請參閱 **[Azure Load Balancer 的標準 SKU](skus.md)** 。

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="快速入門中建立的基本負載平衡器資源。" border="false":::

*圖：快速入門中建立的資源。*

在本節中，您會建立負載平衡器以平衡虛擬機器的負載。 

建立公用負載平衡器時，也必須建立新的公用 IP 位址，而該 IP 位址會設定為負載平衡器的前端 (預設的名稱為 LoadBalancerFrontend)。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]，並在文字方塊中輸入 **CreatePubLBQS-rg**。|
    | 名稱                   | 輸入 **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [基本] |
    | 公用 IP 位址 | 選取 [建立新的]。 如果您有想要使用的現有公用 IP，請選取 [使用現有]。 |
    | 公用 IP 位址名稱 | 在文字方塊中輸入 **myPublicIP**。|
    | 指派 | 選取 [動態]。 |
    | 新增公用 IPv6 位址 | 請選取 [否] 。 </br> 如需 IPv6 位址和負載平衡器的詳細資訊，請參閱[什麼是適用於 Azure 虛擬網路的 IPv6？](../virtual-network/ipv6-overview.md)  |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="建立基本負載平衡器" border="true":::

## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您將會設定：

* 建立虛擬網路。
* 後端位址集區的負載平衡器設定。
* 健康情況探查。
* 負載平衡器規則。

## <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您會建立虛擬網路和子網路。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 **CreatePubLBQS-rg** |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 [西歐] |

3. 選取 [IP 位址]  索引標籤，或選取頁面底部的 [下一步：  IP 位置] 按鈕。

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

8. 選取 [安全性] 索引標籤。

9. 在 [BastionHost] 底下，選取 [啟用]。 輸入此資訊：

    | 設定            | 值                      |
    |--------------------|----------------------------|
    | Bastion 名稱 | 輸入 **myBastionHost** |
    | AzureBastionSubnet 位址空間 | 輸入 **10.1.1.0/24** |
    | 公用 IP 位址 | 選取 [建立新的]。 </br> 在 [名稱] 中，輸入 myBastionIP。 </br> 選取 [確定]。 |


8. 選取 [檢閱 + 建立] 索引標籤，或選取 [檢閱 + 建立] 按鈕。

9. 選取 [建立]。
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
* 接聽 **連接埠 80**。
* 將負載平衡的流量導向至 **連接埠 80** 上名為 **myBackendPool** 的後端。

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
    | 閒置逾時 (分鐘) | 將滑桿移至 **15 分鐘**。 |
 
4. 保留其餘的預設值，然後選取 [確定]。

## <a name="create-backend-servers"></a>建立後端伺服器

在本節中，您可：

* 為負載平衡器的後端集區建立三部虛擬機器。
* 建立虛擬機器的可用性設定組。
* 在虛擬機器上安裝 IIS，以測試負載平衡器。

### <a name="create-virtual-machines"></a>建立虛擬機器

在本節中，您將使用基本的公用 IP 位址來建立三個 VM (**myVM1**、**myVM2** 和 **myVM3**)。  

這三個 VM 將會新增至名為 **myAvailabilitySet** 的可用性設定組中。

這些 VM 會新增至先前建立之負載平衡器的後端集區。

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器]。 
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 **CreatePubLBQS-rg** |
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
    | 設定網路安全性群組 | 選取 [建立新的]。 </br> 在 [建立網路安全性群組] 的 [名稱] 中，輸入 **myNSG**。 </br> 在 [輸入規則]下，選取 [+新增輸入規則]。 </br> 在 [目的地連接埠範圍] 下，輸入 **80**。 </br> 在 [優先順序] 下，輸入 **100**。 </br> 在 [名稱] 中，輸入 **myHTTPRule** </br> 選取 [新增] </br> 選取 [確定] |
    | **負載平衡**  |
    | 要將此虛擬機器放在現有負載平衡解決方案後面嗎? | 選取 [否] |
 
5. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。

6. 在 [管理] 索引標籤中，選取或輸入：
    
    | 設定 | 值 |
    |---|---|
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

### <a name="add-virtual-machines-to-the-backend-pool"></a>將虛擬機器新增至後端集區

在先前步驟中建立的 VM 必須新增至 **myLoadBalancer** 的後端集區。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [myBackendPool]。

3. 在 [關聯性] 中，選取 [虛擬機器]。

4. 在 [虛擬機器] 區段中，選取 [+新增]。

5. 選取 **myVM1**、**myVM2** 和 **myVM3** 旁的方塊。

6. 選取 [新增]。

7. 選取 [儲存]。

---

## <a name="install-iis"></a>安裝 IIS

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單選取 **CreatePubLBQS-rg** 資源群組中的 [myVM1]。

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

9. 重複步驟 1 到 6，在 myVM2 和 myVM3 上安裝 IIS 和更新的 iisstart.htm 檔案。

## <a name="test-the-load-balancer"></a>測試負載平衡器

1. 在 [概觀] 畫面上尋找負載平衡器的公用 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myPublicIP]。

2. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

   ![IIS Web 伺服器](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

若要讓負載平衡器將流量分散到這三個 VM，您可以為每個 VM 的 IIS Web 伺服器自訂預設頁面，然後從用戶端機器強制重新整理您的網頁瀏覽器。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 若要這樣做，請選取包含資源的資源群組 **CreatePubLBQS-rg**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已：

* 建立 Azure 標準或基本 Load Balancer
* 將 3 個 VM 連接至負載平衡器。
* 設定負載平衡器流量規則、健康情況探查，並測試負載平衡器。 

若要深入了解 Azure Load Balancer，請繼續：
> [!div class="nextstepaction"]
> [什麼是 Azure Load Balancer？](load-balancer-overview.md)