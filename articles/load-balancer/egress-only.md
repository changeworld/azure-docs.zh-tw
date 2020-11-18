---
title: 僅輸出負載平衡器組態
titleSuffix: Azure Load Balancer
description: 在本文中，瞭解如何建立具有輸出 NAT 的內部負載平衡器
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694988"
---
# <a name="outbound-only-load-balancer-configuration"></a>僅輸出負載平衡器組態

使用內部和外部標準負載平衡器的組合，為內部負載平衡器後方的 Vm 建立輸出連線能力。 

這種設定會為內部負載平衡器案例提供輸出 NAT，為您的後端集區產生「僅限輸出」設定。


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="虛擬網路 NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*圖：僅輸出負載平衡器設定*

所需的步驟如下：

1. 使用堡壘主機建立虛擬網路。
2. 建立只有私人 IP 的虛擬機器。
3. 建立內部和公用標準負載平衡器。
4. 將後端集區新增至負載平衡器，並將 VM 放入每個集區。
5. 透過堡壘主機連接到您的 VM，並：
    1. 測試輸出連線能力， 
    2. 在公用負載平衡器上設定輸出規則。
    3. 重新測試輸出連線能力。

## <a name="create-virtual-network-and-virtual-machine"></a>建立虛擬網路和虛擬機器

建立具有兩個子網的虛擬網路：

* 主要子網路
* 防禦子網

在新的虛擬網路中建立虛擬機器。

### <a name="create-the-virtual-network"></a>建立虛擬網路

1. [登入](https://portal.azure.com) Azure 入口網站。

2. 在畫面的左上方，選取 [建立資源] > [網路] > [虛擬網路]  ，或在搜尋方塊中搜尋 [虛擬網路]  。

2. 在 [建立虛擬網路] 中，在 [基本] 索引標籤中輸入或選取這項資訊：

    | **設定**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **專案詳細資料**  |                                                                 |
    | 訂用帳戶     | 選取您的 Azure 訂用帳戶                                  |
    | 資源群組   | 選取 [建立新的]  。 </br> 輸入 **myResourceGroupLB**。 </br> 選取 [確定]。 |
    | **執行個體詳細資料** |                                                                 |
    | 名稱             | 輸入 **myVNet**                                    |
    | 區域           | 選取 **美國東部 2** |

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

9. 選取 [建立]  。

### <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 在入口網站的左上角，選取 [建立資源] > [計算] > [虛擬機器]。 
   
2. 在 [建立虛擬機器] 中，輸入或選取 [基本資訊] 索引標籤中的值：

    | 設定 | 值                                          |
    |-----------------------|----------------------------------|
    | **專案詳細資料** |  |
    | 訂用帳戶 | 選取您的 Azure 訂用帳戶 |
    | 資源群組 | 選取 [myResourceGroupLB] |
    | **執行個體詳細資料** |  |
    | 虛擬機器名稱 | 輸入 myVM |
    | 區域 | 選取 **美國東部 2** |
    | 可用性選項 | 選取 [不需要基礎結構備援] |
    | 映像 | 選取 [Windows Server 2019 Datacenter] |
    | Azure Spot 執行個體 | 選取 [否] |
    | 大小 | 選擇 VM 大小或接受預設設定 |
    | **系統管理員帳戶** |  |
    | 使用者名稱 | 輸入使用者名稱 |
    | 密碼 | 輸入密碼 |
    | 確認密碼 | 再次輸入密碼 |
    | **輸入連接埠規則** |  |
    | 公用輸入連接埠 | 選取 [**允許選取的埠**] |
    | 選取輸入連接埠 | 選取 **RDP (3389)** |

3. 選取 [網路] 索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路]** 。
  
4. 在 [網路功能] 索引標籤中，選取或輸入：

    | 設定 | 值 |
    |-----|------------|
    | **網路介面** |  |
    | 虛擬網路 | **myVNet** |
    | 子網路 | **myBackendSubnet** |
    | 公用 IP | 選取 [無]。 |
    | NIC 網路安全性群組 | 選取 [無]|
    | 將此虛擬機器放在現有的負載平衡解決方案後方？ | 選取 [否] |
   
5. 選取 [管理] 索引標籤，或選取 [下一步] > [管理]。

6. 在 [管理] 索引標籤中，選取或輸入：
    
    | 設定 | 值 |
    |-|-|
    | **監視** |  |
    | 開機診斷 | 選取 [關閉] |
   
7. 選取 [檢閱 + 建立]。 
  
8. 檢閱設定，然後選取 [建立]。

## <a name="create-load-balancers-and-test-connectivity"></a>建立負載平衡器並測試連線能力

使用 Azure 入口網站來建立：

* 內部負載平衡器
* 公用 Load Balancer
 
將您建立的 VM 加入至每個 VM 的後端集區。  接著，您將設定設定，只允許來自 VM 的輸出連線能力，並在前後進行測試。

### <a name="create-internal-load-balancer"></a>建立內部負載平衡器

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取您在上一個步驟建立的 **myResourceGroupLB**。|
    | 名稱                   | 輸入 **myInternalLoadBalancer**                                   |
    | 區域         | 選取 [美國東部 2]。                                        |
    | 類型          | 選取 [內部]。                                        |
    | SKU           | 選取 [標準] |
    | 虛擬網路 | 選取您在上一個步驟中建立的 **myVNet**。 |
    | 子網路  | 選取您在上一個步驟中建立的 **myBackendSubnet**。 |
    | IP 位址指派 | 選取 [動態]。 |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   

### <a name="create-public-load-balancer"></a>建立公用負載平衡器

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]，並在文字方塊中輸入 **myResourceGroupLB**。|
    | 名稱                   | 輸入 **myPublicLoadBalancer**                                   |
    | 區域         | 選取 [美國東部 2]。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [標準] |
    | 公用 IP 位址 | 選取 [建立新的]  。 |
    | 公用 IP 位址名稱 | 在文字方塊中輸入 **myFrontendIP** 。|
    | 可用性區域 | 選取 [區域備援] |
    | 新增公用 IPv6 位址 | 請選取 [否] 。 |

3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   
   
### <a name="create-internal-backend-address-pool"></a>建立內部後端位址集區

建立後端位址集區 **myInternalBackendPool**：

1. 在左側功能表中選取 [ **所有服務** ]，選取 [ **所有資源**]，然後從 [資源] 清單中選取 [ **myInternalLoadBalancer** ]。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [新增]。

3. 在 [ **新增後端集** 區] 頁面上，針對 [名稱] 輸入 **myInternalBackendPool**，作為您後端集區的名稱。
 
4. 在 [虛擬網路] 中，選取 [myVNet]。

5. 在 [ **虛擬機器**] 下，選取 [ **新增** ] 並選擇 [ **myVM**]。
 
6. 選取 [ **新增**]。

### <a name="create-public-backend-address-pool"></a>建立公用後端位址集區

建立後端位址集區 **myPublicBackendPool**：

1. 在左側功能表中選取 [ **所有服務** ]，選取 [ **所有資源**]，然後從 [資源] 清單中選取 [ **myPublicLoadBalancer** ]。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [新增]。

3. 在 [ **新增後端集** 區] 頁面上，針對 [名稱] 輸入 **myPublicBackendPool**，作為您後端集區的名稱。

4. 在 [虛擬網路] 中，選取 [myVNet]。
 
5. 在 [ **虛擬機器**] 下，選取 [ **新增** ] 並選擇 [ **myVM**]。
 
6. 選取 [ **新增**]。

### <a name="test-connectivity-before-outbound-rule"></a>在輸出規則之前測試連線能力

1. 在左側功能表中選取 [**所有服務**]，選取 [**所有資源**]，然後從 [資源] 清單中選取位於 **MyResourceGroupLB** 資源群組中的 **myVM** 。

2. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

4. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

5. 選取 [連接]。

6. 開啟 Internet Explorer。

7. **https://whatsmyip.org** 在網址列中輸入。

8. 連接應該會失敗。 根據預設，標準公用負載平衡器 [不允許未定義輸出規則的輸出流量](load-balancer-overview.md#securebydefault)。
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>建立公用負載平衡器輸出規則

1. 在左側功能表中選取 [ **所有服務** ]，選取 [ **所有資源**]，然後從 [資源] 清單中選取 [ **myPublicLoadBalancer** ]。

2. 在 [設定] 下選取 [輸出規則]，然後選取 [新增]。

3. 使用這些值來設定輸出規則：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myOutboundRule**。 |
    | 前端 IP 位址 | 選取 [ **LoadBalancerFrontEnd**]。|
    | 閒置逾時 (分鐘) | 將滑桿移至 **15 分鐘**。|
    | TCP 重設 | 選取 [啟用] 。|
    | 後端集區 | 選取 [ **myPublicBackendPool**]。| |
    | 連接埠配置 -> 連接埠配置 | 選取 **[使用預設的輸出埠數目**] |

4. 選取 [新增]  。

### <a name="test-connectivity-after-outbound-rule"></a>在輸出規則之後測試連線能力

1. 在左側功能表中選取 [**所有服務**]，選取 [**所有資源**]，然後從 [資源] 清單中選取位於 **MyResourceGroupLB** 資源群組中的 **myVM** 。

2. 在 [概觀] 頁面上，選取 [連線]，然後選 [Bastion]。

4. 輸入在 VM 建立期間輸入的使用者名稱和密碼。

5. 選取 [連接]。

6. 開啟 Internet Explorer。

7. **https://whatsmyip.org** 在網址列中輸入。

8. 連接應該會成功。

9. 顯示的 IP 位址應該是 **myPublicLoadBalancer** 的前端 ip 位址。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組、負載平衡器、VM 和所有相關資源。 

若要這樣做，請選取資源群組 **myResourceGroupLB** ，然後選取 [ **刪除**]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用公用和內部負載平衡器的組合建立「僅限輸出」設定。  

此設定可讓您將傳入的內部流量負載平衡至後端集區，同時仍能防止任何公用輸入連線。

- 瞭解 [Azure Load Balancer](load-balancer-overview.md)。
- 瞭解 [Azure 中的輸出](load-balancer-outbound-connections.md)連線。
- 負載平衡器 [常見問題](load-balancer-faqs.md)。
- 瞭解[Azure](../bastion/bastion-overview.md)防禦