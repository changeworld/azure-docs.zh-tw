---
title: 教學課程：使用 Azure 入口網站建立跨區域基本負載平衡器
titleSuffix: Azure Load Balancer
description: 使用 Azure 入口網站部署跨區域 Azure Load Balancer 以開始本教學課程。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/24/2020
ms.openlocfilehash: 7ee203595a796529ae0aefe8b0c52a689ac29968
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762324"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站建立跨區域 Azure Load Balancer

跨區域負載平衡器可確保服務可跨多個 Azure 區域全域使用。 如果一個區域失敗，流量會路由至下一個最接近狀況良好的區域負載平衡器。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立跨區域負載平衡器。
> * 建立包含兩個區域負載平衡器的後端集區。
> * 建立負載平衡器規則。
> * 測試負載平衡器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

> [!IMPORTANT]
> 跨區域 Azure Load Balancer 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。
- 兩個 **標準** SKU Azure 負載平衡器，其後端集區部署在兩個不同的 Azure 區域中。
    - 如需針對後端集區建立區域標準負載平衡器和虛擬機器的相關資訊，請參閱[快速入門：使用 Azure 入口網站建立公用負載平衡器以平衡 VM 的負載](quickstart-load-balancer-standard-public-portal.md)。
        - 在每個區域中，將負載平衡器、虛擬機器和其他資源的名稱附加上 **R1** 和 **R2**。 

## <a name="sign-in-to-azure-portal"></a>登入 Azure 入口網站

[登入](https://preview.portal.azure.com) Azure 預覽入口網站。

## <a name="create-cross-region-load-balancer"></a>建立跨區域負載平衡器

在本節中，您將建立跨區域的負載平衡器和公用 IP 位址。

1. 在畫面的左上方，選取 [建立資源] > [網路] > [負載平衡器]，或在搜尋方塊中搜尋 [負載平衡器]。

2. 在 [建立負載平衡器] 頁面的 [基本] 索引標籤中，輸入或選取下列資訊： 

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶               | 選取您的訂用帳戶。    |    
    | 資源群組         | 選取 [新建]，並在文字方塊中輸入 **CreateCRLBTutorial-rg**。|
    | 名稱                   | 輸入 **myLoadBalancer-CR**                                   |
    | 區域         | 選取 [美國西部]。                                        |
    | 類型          | 選取 [公用]。                                        |
    | SKU           | 選取 [標準] |
    | 層           | 選取 [全域] |
    | 公用 IP 位址 | 選取 [建立新的]。|
    | 公用 IP 位址名稱 | 在文字方塊中輸入 **myPublicIP-CR**。|
    | 路由喜好設定| 選取 [Microsoft 網路] |

    > [!NOTE]
    > 跨區域負載平衡器只能部署在下列的主要區域中：**美國東部 2、美國西部、西歐、東南亞、美國中部、北歐、亞太地區**。 如需詳細資訊，請參閱 **https://aka.ms/homeregionforglb** \(英文\)。


3. 接受其餘設定的預設值，然後選取 [檢閱 + 建立]。

4. 在 [檢閱 + 建立] 索引標籤中，選取 [建立]。   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="建立跨區域負載平衡器" border="true":::

## <a name="create-backend-pool"></a>建立後端集區

在本節中，您會在跨區域負載平衡器的後端集區中新增兩個區域標準負載平衡器。

> [!IMPORTANT]
> 若要完成這些步驟，請確定已在您的訂用帳戶中部署兩個具有後端集區的區域負載平衡器。  如需詳細資訊，請參閱 **[快速入門：使用 Azure 入口網站建立公用負載平衡器以平衡 VM 的負載](quickstart-load-balancer-standard-public-portal.md)** 。

建立後端位址集區 **myBackendPool-CR**，以包含區域性標準負載平衡器。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer-CR**。

2. 在 [設定] 底下選取 [後端集區]，然後選取 [新增]。

3. 在 **新增後端集區** 頁面上，針對名稱輸入 **myBackendPool-CR**。

4. 選取 [新增]。

4. 選取 [myBackendPool-CR]。

5. 在 **負載平衡器** 下，選取 [負載平衡器] 下的下拉方塊。

5. 選取 [myLoadBalancer-R1] 或區域 1 中的負載平衡器名稱。

6. 選取 [前端 IP 組態] 下的下拉核取方塊。 選擇 **LoadBalancerFrontEnd**。

7. 重複步驟 4-6 以新增 **myLoadBalancer-R2**。

8. 選取 [新增]。

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="將區域性負載平衡器新增至後端集區" border="true":::

## <a name="create-a-health-probe"></a>建立健康狀態探查

在本節中，您將建立健全狀態探查來建立負載平衡規則：

* 具名 **myHealthProbe**。
* 通訊協定 **TCP**。
* 間隔 **5** 秒。
* **兩個** 失敗的狀況不良閾值。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer-CR**。

2. 在 [設定] 下，選取 [健康狀態探查]。

3. 使用這些值來設定健全狀態探查：

    | 設定 | 值 |
    | ------- | ----- |
    | 名稱 | 輸入 **myHealthProbe**。 |
    | 通訊協定 | 選取 [TCP]。 |
    | 連接埠 | 輸入 **80**。 |
    | 間隔 | 輸入 **5**。 |
    | 狀況不良臨界值 | 輸入 **2**。 |

4. 選取 [確定]。

    > [!NOTE]
    > 跨區域負載平衡器有內建的健全狀態探查。 此探查是用來建立負載平衡規則的預留位置。  如需詳細資訊，請參閱 **[跨區域負載平衡器的限制](cross-region-overview.md#limitations)** 。

## <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

在本節中，您將建立負載平衡器規則：

* 已命名為 **myHTTPRule**。
* 在名為 **LoadBalancerFrontEnd** 的前端中。
* 接聽 **連接埠 80**。
* 將負載平衡的流量導向至 **連接埠 80** 上名為 **myBackendPool-CR** 的後端。

    > [!NOTE]
    > 前端連接埠必須符合後端集區中區域負載平衡器的後端連接埠和前端連接埠。

1. 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後從資源清單中選取 **myLoadBalancer-CR**。

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
    | 閒置逾時 (分鐘) | 將滑桿移至 **15**。 |
    | TCP 重設 | 選取 [啟用]  。 |

4. 保留其餘的預設值，然後選取 [確定]。

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="建立負載平衡器規則" border="true":::

## <a name="test-the-load-balancer"></a>測試負載平衡器

在本節中，您將測試跨區域負載平衡器。 您會在 Web 瀏覽器中連線到公用 IP 位址。  您將停止其中一個區域性負載平衡器後端集區中的虛擬機器，並觀察容錯移轉的狀態。

1. 在 [概觀] 畫面上尋找負載平衡器的公用 IP 位址。 選取左側功能表中的 [所有服務]、選取 [所有資源]，然後選取 [myPublicIP-CR]。

2. 將公用 IP 位址複製並貼到您瀏覽器的網址列。 IIS Web 伺服器的預設頁面會顯示在瀏覽器上。

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="測試負載平衡器" border="true":::

3. 停止其中一個區域性負載平衡器後端集區中的虛擬機器。

4. 重新整理網頁瀏覽器，並觀察與其他區域負載平衡器連線的容錯移轉狀態。

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="在容錯移轉之後測試負載平衡器" border="true":::

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、負載平衡器和所有相關資源。 

若要這樣做，請選取包含資源的資源群組 **CreateCRLBTutorial-rg**，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

* 已建立跨區域負載平衡器。
* 已在跨區域負載平衡器的後端集區中新增了區域性負載平衡器。
* 建立了負載平衡規則。
* 測試了負載平衡器。

如需跨區域負載平衡器的詳細資訊，請參閱[跨區域負載平衡器 (預覽)](cross-region-overview.md)。


請前往下一篇文章以了解如何：
> [!div class="nextstepaction"]
> [跨越多個可用性區域為 VM 進行負載平衡](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
