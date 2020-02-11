---
title: 快速入門 - 使用 Azure 入口網站建立 Private Link 服務
titlesuffix: Azure Private Link
description: 在本快速入門中了解如何使用 Azure 入口網站建立 Private Link 服務
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026201"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立 Private Link 服務

Azure Private Link 服務是由 Azure Private Link 所支援的自有服務參考。 可以啟用在 Azure Load Balancer 後方運作的服務或資源，以進行 Private Link 存取。 服務取用者可以從自己的 VNet 私下存取服務。 在本快速入門中，您會了解如何使用 Azure 入口網站建立私人連結服務。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-an-internal-load-balancer"></a>建立內部負載平衡器

首先，建立虛擬網路，然後建立要搭配 Azure Private Link 服務使用的內部負載平衡器。

### <a name="create-the-virtual-network"></a>建立虛擬網路

在本節中，您將建立虛擬網路和子網路，以裝載用來存取 Private Link 服務的負載平衡器。


1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [虛擬網路]  。
   
1. 在 [建立虛擬網路]  窗格中，輸入或選取下列值：
   
   - **Name**：輸入 MyVNet  。
   - **資源群組**：選取 [新建]  ，然後輸入 MyResourceGroupLB  ，然後選取 [確定]  。 
   - [子網路]   > [名稱]  ：輸入 MyBackendSubnet  。
   
1. 選取 [建立]  。

   ![建立虛擬網路](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>建立標準負載平衡器

使用入口網站建立標準內部負載平衡器。 您所建立的名稱和 IP 位址會自動設定為負載平衡器的前端。

1. 在入口網站的左上方，選取 [建立資源]   > [網路]   > [負載平衡器]  。
   
2. 在 [建立負載平衡器]  頁面的 [基本資料]  中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [檢閱 + 建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 在下拉式方塊中選取 [MyResourceGroupLB]  。|
    | 名稱                   | *myLoadBalancer*                                   |
    | 區域         | 選取 [美國東部 2]  。                                        |
    | 類型          | 選取 [內部]  。                                        |
    | SKU           | 選取 [標準]  。                          |
    | 虛擬網路           | 選取 [MyVNet]  。                          |    
    | IP 位址指派              | 選取 [靜態]  。   |
    | 私人 IP 位址|輸入位於您虛擬網路和子網路的位址空間內的位址，例如 *10.3.0.7*。  |

3. 在 [檢閱 + 建立]  索引標籤中，按一下 [建立]  。 
   

### <a name="create-standard-load-balancer-resources"></a>建立標準負載平衡器資源

在本節中，您會設定後端位址集區和健康狀態探查的負載平衡器設定，並指定負載平衡器規則。

#### <a name="create-a-backend-pool"></a>建立後端集區

為了將流量分散至您的資源，後端位址集區包含已連線至負載平衡器之虛擬 (NIC) 的 IP 位址。 建立後端位址集區 *myBackendPool*，以包含用於平衡流量負載的資源。

1. 選取左側功能表中的 [所有服務]  、選取 [所有資源]  ，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]  底下選取 [後端集區]  ，然後選取 [新增]  。
3. 在 [新增後端集區]  頁面上，針對名稱輸入 *myBackEndPool* 作為後端集區的名稱，然後選取 [新增]  。



#### <a name="create-a-health-probe"></a>建立健康狀態探查

若要讓負載平衡器能監視資源狀態，請使用健康情況探查。 健康狀態探查會根據資源對健康情況檢查的回應，以動態方式從負載平衡器輪替中新增或移除資源。 

**若要建立健康情況探查以監視資源的健康情況：**

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  底下選取 [健康狀態探查]  ，然後選取 [新增]  。
   
1. 在 [新增健康情況探查]  頁面上，輸入或選取下列值：
   
   - **Name**：輸入 MyHealthProbe  。
   - **通訊協定**：下拉並選取 [TCP]  。 
   - **連接埠**：輸入 80  。 
   - **間隔**：輸入 15  。 間隔是探查嘗試之間的秒數。
   - **狀況不良閾值**：輸入 2  。 這個值是將 VM 視為狀況不良之前，所發生的連續探查失敗次數。
   
1. 選取 [確定]  。

#### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則可定義如何將流量分散至資源。 此規則會定義連入流量的前端 IP 組態、用來接收流量的後端 IP 集區，以及所需的來源和目的地連接埠。 

名為 **MyLoadBalancerRule** 的負載平衡器規則會接聽前端 **LoadBalancerFrontEnd** 中的連接埠 80。 此規則會將網路流量傳送到後端位址集區 **MyBackendPool**，也在連接埠 80 上。 

**若要建立負載平衡器規則：**

1. 選取左側功能表上的 [所有資源]  ，然後從資源清單中選取 [MyLoadBalancer]  。
   
1. 在 [設定]  下選取 [負載平衡規則]  ，然後選取 [新增]  。
   
1. 在 [新增負載平衡規則]  頁面上，輸入或選取下列值 (如果尚未存在)：
   
   - **Name**：輸入 MyLoadBalancerRule  。
   - **前端 IP 位址：** 輸入 LoadBalancerFrontEnd  (如果部存在)。
   - **通訊協定**：選取 [TCP]  。
   - **連接埠**：輸入 80  。
   - **後端連接埠**：輸入 80  。
   - **後端集區**：選取 [MyBackendPool]  。
   - **健康情況探查**：選取 [MyHealthProbe]  。 
   
1. 選取 [確定]  。
   
## <a name="create-a-private-link-service"></a>建立私人連結服務

在本節中，您將建立標準負載平衡器後方的私用連結服務。

1. 在 Azure 入口網站的畫面左上角，選取 [建立資源]   > [網路]   > [Private Link 中心 (預覽)]  。 您也可以透過入口網站搜尋來搜尋 Private Link。

2. 在 [私人連結中心 - 概觀]  中，選取 [公開您自己的服務以便其他人連線]  選項上的 [開始]。

3. 在 [建立私人連結服務 - 基本資料]  中，輸入或選取這項資訊：

    | 設定           | 值                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **專案詳細資料：**  |                                                                              |
    | 訂用帳戶      | 選取您的訂用帳戶                                                     |
    | 資源群組    | 選取 [MyResourceGroupLB]  。                                                     |
    | **執行個體詳細資料：** |                                                                              |
    | 名稱              | 輸入 [myPrivateLinkService]  。 |
    | 區域            | 選取 [美國東部 2]  。                                                         |

4. 完成時，選取 [下一步:  輸出設定]。

5. 在 [建立私人連結服務 - 輸出設定]  中，輸入或選取這項資訊：


    | 設定                           | 值                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | 負載平衡器                     | 選取 [MyLoadBalancer]  。                                                           |
    | 負載平衡器前端 IP 位址 | 選取 MyLoadBalancer 的前端 IP 位址                                |
    | 來源 NAT 虛擬網路        | 選取 [myVNET]  。                                                                   |
    | 來源 NAT 子網路                 | 選取 [myBackendSubnet]  。                                                          |
    | 啟用 TCP Proxy v2               | 根據您的應用程式是否需要 TCP Proxy v2 標頭，選取 [是] / [否]。 |
    | 私人 IP 位址設定       | 為每個 NAT IP 設定配置方法和 IP 位址                  |

6. 完成時，選取 [下一步:  存取安全性]。

7. 在 [建立私人連結服務 - 存取安全性]  中，輸入或選取這項資訊：

    | 設定                                     | 值                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | 可見性                                  | 選擇 [僅限角色型存取控制]  。         |
  
8. 完成時，選取 [下一步:  標籤]，然後選取 [檢閱 + 建立]  ，或選擇頁面頂端的 [檢閱 + 建立標籤]  。

9. 檢閱您的資訊，然後選取 [建立]  。

## <a name="clean-up-resources"></a>清除資源 
當您使用完私用連結服務時，請刪除資源群組以清除本快速入門中使用的資源：

1. 在入口網站頂端的 [搜尋]  方塊中輸入 *myResourceGroupLB*，然後從搜尋結果中選取 [myResourceGroupLB]  。 
2. 選取 [刪除資源群組]  。 
3. 針對 [輸入資源群組名稱]  輸入 myResourceGroup，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立內部 Azure 負載平衡器和私人連結服務。 若要深入了解如何建立私人端點，請參閱[使用 Azure 入口網站建立私人端點](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)。
