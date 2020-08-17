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
ms.openlocfilehash: 0d873401d377a03581a319769604f3d976f365be
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927233"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立 Private Link 服務

Azure Private Link 服務會參考由 Private Link 管理的自有服務。 您可以提供權限給 Private Link，讓其可以存取在 Azure Standard Load Balancer 後方運作的服務或資源。 服務取用者可以從自己的虛擬網路私下存取服務。 在本快速入門中，您會了解如何使用 Azure 入口網站建立 Private Link 服務。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-an-internal-load-balancer"></a>建立內部負載平衡器

首先，建立虛擬網路。 接下來，建立要搭配 Private Link 服務使用的內部負載平衡器。

## <a name="virtual-network-and-parameters"></a>虛擬網路和參數

在本節中，您會建立虛擬網路。 您也會建立子網路，裝載可存取 Private Link 服務的負載平衡器。

在本節中，您需要使用下列資訊來取代步驟中的下列參數：

| 參數                   | 值                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 美國東部 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>建立標準負載平衡器

使用入口網站建立標準內部負載平衡器。 您所指定的名稱和 IP 位址會自動設定為負載平衡器的前端。

1. 在入口網站的左上方，選取 [建立資源] > [網路] > [負載平衡器]。

1. 在 [建立負載平衡器]**** 頁面的 [基本]**** 索引標籤中，輸入或選取下列資訊：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | **訂用帳戶**               | 選取您的訂用帳戶。    |
    | **資源群組**         | 從方塊中選取 [myResourceGroupLB]****。|
    | **名稱**                   | 輸入 **myLoadBalancer**。                                   |
    | 區域         | 選取 [美國東部 2]。                                        |
    | **型別**          | 選取 [內部]。                                        |
    | **SKU**           | 選取 [標準]。                          |
    | **虛擬網路**           | 選取 [myVNet]。                          |
    | **IP 位址指派**              | 選取 [靜態]。   |
    | **私人 IP 位址**|輸入位於您虛擬網路和子網路位址空間內的位址。 範例為 10.3.0.7。  |

1. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]

1. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。

### <a name="create-standard-load-balancer-resources"></a>建立標準負載平衡器資源

在本節中，您會設定後端位址集區和健康狀態探查的負載平衡器設定。 您也可以指定負載平衡器規則。

#### <a name="create-a-back-end-pool"></a>建立後端集區

後端位址集區會包含虛擬 NIC (已連線至負載平衡器) 的 IP 位址。 此集區可讓您將流量分散到資源。 建立名為 **myBackendPool** 後端位址集區，以包含用於平衡流量負載的資源。

1. 從最左側的功能表中選取 [所有服務]****。
1. 選取 [所有資源]****，然後從資源清單中選取 [myLoadBalancer]****。
1. 在 [設定] 底下選取 [後端集區]，然後選取 [新增]。
1. 在 [新增後端集區]**** 頁面上，輸入 **myBackEndPool** 作為後端集區的名稱，然後選取 [新增]****。

#### <a name="create-a-health-probe"></a>建立健康狀態探查

使用健康情況探查，讓負載平衡器能監視資源狀態。 健康狀態探查會根據資源對健康情況檢查的回應，以動態方式從負載平衡器輪替中新增或移除資源。

若要建立健康情況探查以監視資源的健康情況：

1. 在最左側的功能表上選取 [所有資源]****，然後從資源清單中選取 [myLoadBalancer]****。

1. 在 [設定] 底下選取 [健康狀態探查]，然後選取 [新增]。

1. 在 [新增健康情況探查]**** 頁面上，輸入或選取下列值：

   - **Name**：輸入 **myHealthProbe**。
   - **通訊協定**：選取 [TCP]。
   - **連接埠**：輸入 **80**。
   - **間隔**：輸入 **15**。 該值是探查嘗試之間的秒數。
   - **狀況不良閾值**：輸入 **2**。 這個值是將虛擬機器視為狀況不良之前，所發生的連續探查失敗次數。

1. 選取 [確定]。

#### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則可定義如何將流量分散至資源。 此規則會定義：

- 傳入流量的前端 IP 設定。
- 接收流量的後端 IP 集區。
- 所需的來源和目的地連接埠。

名為 **myLoadBalancerRule** 的負載平衡器規則會接聽 **LoadBalancerFrontEnd** 前端中的連接埠 80。 此規則會將網路流量傳送到相同連接埠 80 上的 **myBackendPool** 後端位址集區。

若要建立負載平衡器規則：

1. 在最左側的功能表上選取 [所有資源]****，然後從資源清單中選取 [myLoadBalancer]****。

1. 在 [設定]**** 下選取 [負載平衡規則]****，然後選取 [新增]****。

1. 在 [新增負載平衡規則]**** 頁面上，輸入或選取下列值 (如果尚未存在)：

   - **Name**：輸入 **myLoadBalancerRule**。
   - **前端 IP 位址：** 輸入**LoadBalancerFrontEnd**。
   - **通訊協定**：選取 [TCP]。
   - **連接埠**：輸入 **80**。
   - **後端連接埠**：輸入 **80**。
   - **後端集區**：選取 [myBackendPool]。
   - **健康情況探查**：選取 [myHealthProbe]。 

1. 選取 [確定]。

## <a name="create-a-private-link-service"></a>建立 Private Link 服務

在本節中，您將建立標準負載平衡器後方的 Private Link 服務。

1. 在 Azure 入口網站的頁面左上角，選取 [建立資源] > [網路] > [Private Link 中心 (預覽)]。 您也可以使用入口網站的搜尋方塊來搜尋 Private Link。

1. 在 [Private Link 中心 - 概觀] > [公開您自己的服務以便其他人連線] 中，選取 [開始]。

1. 在 [建立私人連結服務 - 基本資料]**** 底下，輸入或選取這項資訊：

    | 設定           | 值                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | 專案詳細資料：  |                                                                              |
    | **訂用帳戶**      | 選取您的訂用帳戶。                                                     |
    | 資源群組    | 選取 [myResourceGroupLB]。                                                    |
    | 執行個體詳細資料： |                                                                              |
    | **名稱**              | 輸入 **myPrivateLinkService**。 |
    | 區域            | 選取 [美國東部 2]。                                                        |

1. 完成時，選取 [下一步:輸出設定]。

1. 在 [建立私人連結服務 - 輸出設定]**** 底下，輸入或選取這項資訊：

    | 設定                           | 值                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **負載平衡器**                     | 選取 [myLoadBalancer]。                                                           |
    | **負載平衡器前端 IP 位址** | 選取 **myLoadBalancer** 的前端 IP 位址。                                |
    | **來源 NAT 虛擬網路**        | 選取 [myVNet]。                                                                   |
    | **來源 NAT 子網路**                 | 選取 [myBackendSubnet]。                                                          |
    | **啟用 TCP Proxy v2**               | 根據您的應用程式是否需要 TCP Proxy v2 標頭，選取 [是]**** 或 [否]****。 |
    | **私人 IP 位址設定**       | 為每個 NAT IP 設定配置方法和 IP 位址。                  |

1. 完成時，選取 [下一步:存取安全性]。

1. 在 [建立私人連結服務 - 存取安全性]**** 底下，選取 [可見度]****，然後選擇 [僅限角色型存取控制]****。
  
1. 請選取 [下一步：標記] > [檢閱 + 建立]，或選擇頁面頂端的 [檢閱 + 建立]。

1. 檢閱您的資訊，然後選取 [建立]****。

## <a name="clean-up-resources"></a>清除資源

當您使用完 Private Link 服務時，請刪除資源群組以清除本快速入門中使用的資源。

1. 在入口網站頂端的搜尋方塊中輸入 **myResourceGroupLB**，然後從搜尋結果中選取 [myResourceGroupLB]****。
1. 選取 [刪除資源群組]。
1. 在 [輸入資源群組名稱]**** 中，輸入 **myResourceGroup**。
1. 選取 [刪除] 。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立內部 Azure 負載平衡器和 Private Link 服務。 您也可以[了解如何使用 Azure 入口網站建立私人端點](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)。
