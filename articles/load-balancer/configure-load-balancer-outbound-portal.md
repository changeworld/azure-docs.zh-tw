---
title: 使用 Azure 入口網站設定負載平衡和輸出規則
titleSuffix: Azure Load Balancer
description: 本文說明如何使用 Azure 入口網站，在 Standard Load Balancer 中設定負載平衡和輸出規則。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 2dff916bf005b307f27264ad7a17864fbba50872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367388"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>使用 Azure 入口網站在 Standard Load Balancer 中設定負載平衡和輸出規則

本文說明如何使用 Azure 入口網站在 Standard Load Balancer 中設定輸出規則。  

負載平衡器資源包含兩個前端和其相關聯的規則。 您有一個前端用於輸入流量，另一個前端用於輸出流量。  

每個前端都會參考公用 IP 位址。 在此案例中，輸入流量的公用 IP 位址與輸出流量的位址不同。   負載平衡規則只會提供輸入負載平衡。 輸出規則會控制 VM 的輸出網路位址轉譯（NAT）。  

此案例使用兩個後端集區：一個用於輸入流量，另一個用於輸出流量。 這些集區說明功能，並提供案例的彈性。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-load-balancer"></a>建立負載平衡器

在本節中，您會建立負載平衡器，以平衡虛擬機器的負載。 您可以建立公用負載平衡器或內部負載平衡器。 當您建立公用負載平衡器時，您會建立新的公用 IP 位址，並將其設定為負載平衡器的前端。 前端預設會命名為**LoadBalancerFrontEnd** 。

1. 在畫面的左上方，選取 [建立資源]**** > [網路]**** > [負載平衡器]****。
2. 在 [**建立負載平衡器**] 頁面的 [**基本**] 索引標籤中，輸入或選取下列資訊：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]****，並在文字方塊中輸入 **myResourceGroupSLB**。|
    | 名稱                   | **myLoadBalancer**                                   |
    | 區域         | 選取 [西歐]****。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [標準]。 |
    | 公用 IP 位址 | 選取 [建立新的]。 如果您有想要使用的現有公用 IP，請選取 [**使用現有**的]。  現有的公用 IP 必須是**標準**SKU。  基本公用 Ip 與**標準**SKU 負載平衡器不相容。  |
    | 公用 IP 位址名稱              | 在文字方塊中輸入 **myPublicIP**。|
    | 可用性區域 | 選取 [**區域-多餘**] 以建立可復原的 Load Balancer。 若要建立區域性 Load Balancer，請從 1、2 或 3 選取特定區域 |

3. 接受其餘設定的預設值。
4. 選取 [**審核] + [建立**]

    > [!IMPORTANT]
    > 本快速入門的其餘部分假設在上述 SKU 選取程序期間會選擇**標準** SKU。

5. 在 [檢閱 + 建立]**** 索引標籤中，選取 [建立]****。   

    ![建立標準負載平衡器](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>建立負載平衡器資源

在本節中，您會設定後端位址集區的負載平衡器設定、健康狀態探查，並指定平衡器規則。

### <a name="create-a-backend-pool"></a>建立後端集區

後端位址集區包含後端集區中虛擬 Nic 的 IP 位址。 建立後端位址集區 **myBackendPool**，以包含用於平衡網際網路流量負載的虛擬機器。

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]**** 底下選取 [後端集區]****，然後選取 [新增]****。
3. 在 [新增後端集區]**** 頁面上，針對名稱輸入 **myBackEndPool** 作為後端集區的名稱，然後選取 [新增]****。

### <a name="create-a-health-probe"></a>建立健康狀態探查

健康情況探查是用來監視應用程式的狀態。 健康情況探查會根據健康情況檢查的回應，從負載平衡器新增或移除 Vm。 建立健康狀態探查 myHealthProbe**** 以監視 VM 的健康狀態。

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]**** 底下選取 [健康狀態探查]****，然後選取 [新增]****。
    
    | 設定 | 值 |
    | ------- | ----- |
    | Name | 輸入 **myHealthProbe**。 |
    | 通訊協定 | 選取 [HTTP]****。 |
    | 連接埠 | 輸入 **80**。|
    | 間隔 | 輸入 **15** 作為探查嘗試之間的 [間隔] **** 秒數。 |
    | 狀況不良臨界值 | 選取 [2]**** 作為 [狀況不良閾值]**** 的數值，或將 VM 視為狀況不良之前，必須達到的連續探查失敗次數。|
    | | |
4. 選取 [確定]****。

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則
負載平衡器規則用來定義如何將流量分散至 VM。 

您可以定義：
 - 連入流量的前端 IP 設定。
 - 要接收流量的後端 IP 集區。
 - 所需的來源和目的地埠。 

在下一節中，您將建立：
 - 用於接聽埠80的負載平衡器規則**myHTTPRule** 。
 - 前端**LoadBalancerFrontEnd**。
 - 後端位址集區**myBackEndPool**也使用埠80。 

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 **myLoadBalancer**。
2. 在 [設定]**** 下選取 [負載平衡規則]****，然後選取 [新增]****。
3. 使用下列值來設定負載平衡規則：
    
    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myHTTPRule**。 |
    | 通訊協定 | 選取 [TCP]****。 |
    | 連接埠 | 輸入 **80**。|
    | 後端連接埠 | 輸入 **80**。 |
    | 後端集區 | 選取 [myBackendPool]****。|
    | 健全狀況探查 | 選取 [myHealthProbe]****。 |
    | 建立隱含輸出規則 | 選取 [否]****。 我們會使用專用的公用 IP，在稍後的區段中建立輸出規則。 |
4. 保留其餘的預設值，然後選取 [確定]****。

## <a name="create-outbound-rule-configuration"></a>建立輸出規則設定
負載平衡器輸出規則會在後端集區中設定 Vm 的輸出 SNAT。 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>建立輸出公用 IP 位址和前端

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [**設定**] 底下，選取 [**前端 IP**設定]，然後選取 [**新增**]。

3. 使用這些值來設定輸出的前端 IP 設定：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入**LoadBalancerFrontEndOutbound**。 |
    | IP 版本 | 選取 [IPv4]****。 |
    | IP 類型 | 選取 [ **IP 位址**]。|
    | 公用 IP 位址 | 選取 [建立新的]。 在 [**新增公用 IP 位址**] 中，輸入**myPublicIPOutbound**。  選取 [確定]。 |

4. 選取 [新增]。

### <a name="create-an-outbound-backend-pool"></a>建立輸出後端集區

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [設定]**** 底下選取 [後端集區]****，然後選取 [新增]****。

3. 在 [**新增後端集**區] 頁面上，針對 [名稱] 輸入**myBackendPoolOutbound**，做為後端集區的名稱，然後選取 [**新增**]。

### <a name="create-outbound-rule"></a>建立輸出規則

1. 選取左側功能表中的 [所有服務]****、選取 [所有資源]****，然後從資源清單中選取 **myLoadBalancer**。

2. 在 [**設定**] 底下，選取 [**輸出規則**]，然後選取 [**新增**]。

3. 使用這些值來設定輸出規則：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入**myOutboundRule**。 |
    | 前端 IP 位址 | 選取 [ **LoadBalancerFrontEndOutbound**]。 |
    | 閒置逾時 (分鐘) | 將滑杆移至 * * 15 分鐘。|
    | TCP 重設 | 選取 [啟用]。|
    | 後端集區 | 選取**myBackendPoolOutbound** |
    | 埠配置-> 埠配置 | 選取 **[手動選擇輸出埠數目**] |
    | 輸出埠-> 選擇依據 | 選取**每個實例的埠** |
    | 輸出埠-每個實例 > 埠 | 輸入**10000**。 |

4. 選取 [新增]。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 選取包含負載平衡器的資源群組**myResourceGroupSLB** ，然後選取 [**刪除**]。

## <a name="next-steps"></a>後續步驟

本文內容：
 - 您已建立標準負載平衡器。
 - 已設定輸入和輸出負載平衡器流量規則。
 - 已針對後端集區中的 Vm 設定健康情況探查。 

若要深入瞭解，請繼續進行[Azure Load Balancer 的教學](tutorial-load-balancer-standard-public-zone-redundant-portal.md)課程。
