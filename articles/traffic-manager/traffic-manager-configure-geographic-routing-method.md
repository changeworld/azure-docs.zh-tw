---
title: 教學課程：使用 Azure 流量管理員設定地理流量路由
description: 本教學課程將說明如何使用 Azure 流量管理員設定地理流量路由方法
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188661"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>教學課程：使用流量管理員設定地理流量路由方法

地理流量路由方法可讓您根據要求的來源地理位置，將流量導向特定的端點位置。 本教學課程示範如何使用這個路由方法來建立流量管理員設定檔，並設定端點以接收來自特定地理位置的流量。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 建立具有地理路由的流量管理員設定檔。
> - 設定巢狀端點。
> - 使用流量管理員設定檔。
> - 刪除流量管理員設定檔。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取左側的 [+建立資源]。 搜尋 [流量管理員設定檔] ，然後選取 [建立]。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="建立流量管理員設定檔":::

1. 在 [建立流量管理員設定檔] 頁面中，定義下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 名稱            | 提供設定檔的名稱。 此名稱必須是 trafficmanager.net 區域內的唯一名稱。 若要存取流量管理員設定檔，您可以使用 DNS 名稱 `<profilename>.trafficmanager.net`。 |    
    | 路由方法  | 選取 [地理]。 |
    | 訂用帳戶    | 選取您的訂用帳戶。 |
    | 資源群組   | 使用現有的資源群組，或建立新的資源群組來放置此設定檔。 如果您選擇建立新的資源群組，請使用 [資源群組位置]  下拉式清單來指定資源群組的位置。 這項設定是指資源群組的位置，完全不影響將部署到全球的流量管理員設定檔。 |

1. 選取 [建立] 以部署您的流量管理員設定檔。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="建立流量管理員設定檔頁面":::

## <a name="add-endpoints"></a>新增端點

1. 從清單中選取流量管理員設定檔。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="流量管理員地理清單":::

1. 在 [設定] 底下選取 [端點]，然後選取 [+ 新增] 以新增端點。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="新增端點":::

1. 選取或輸入下列設定： 

    | 設定                | 值                                              |
    | ---                    | ---                                                |
    | 類型                   | 選取端點類型。 針對生產環境中使用的地理路由設定檔，我們強烈建議使用巢狀端點類型，而且包含的子設定檔中有多個端點。 如需詳細資訊，請參閱[地理流量路由方法的常見問題集](traffic-manager-FAQs.md)。 |    
    | Name                   | 提供名稱以識別此端點。 |
    | 目標資源類型   | 選取目標的資源類型。 |
    | 目標資源        | 從清單中選取資源。 |

    > [!Note]
    > 此頁面上的某些欄位，取決於您要新增的端點類型︰
    > 1. 如果您要新增 Azure 端點，請根據您想要將流量導向的資源，選取 [目標資源類型] 和 [目標]
    > 1. 如果您要新增 **外部** 端點，請提供 [完整網域名稱 (FQDN)] 端點。
    > 1. 如果您要新增 **巢狀端點**，請選取對應至您想要使用之子設定檔的 [目標資源]，並指定 [子端點計數下限]。

1. 在 [地區對應] 區段中，使用下拉式清單新增區域，表示您想要將這些區域的流量傳送至此端點。 至少必須新增一個區域。 您可以對應多個區域。

1. 針對您想要在此設定檔下新增的所有端點，重複最後一個步驟，然後選取 [儲存]。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="新增流量管理員端點":::

## <a name="use-the-traffic-manager-profile"></a>使用流量管理員設定檔

1.  在入口網站的搜尋列中，搜尋您在上一節建立的 **流量管理員設定檔** 名稱，然後在顯示的結果中選取流量管理員設定檔。
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="搜尋流量管理員設定檔":::

1. [流量管理員設定檔]  會顯示新建立之流量管理員設定檔的 DNS 名稱。 此名稱可由任何用戶端使用 (例如使用網頁瀏覽器進行瀏覽)，以路由傳送至由路由類型所決定的正確端點。 透過地理路由，流量管理員可查看連入要求的來源 IP，並判斷其來源區域。 如果該區域對應至端點，流量會路由傳送至該處。 如果此區域未對應至端點，流量管理員會傳回 NODATA 查詢回應。

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="地理流量管理員的概觀":::

## <a name="clean-up-resources"></a>清除資源

如果您不再需要流量管理員設定檔，請找出設定檔，然後選取 [刪除設定檔]。

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="刪除流量管理員設定檔":::

## <a name="next-steps"></a>後續步驟

若要深入了解地理路由方法，請參閱：

> [!div class="nextstepaction"]
> [地理流量路由方法](traffic-manager-routing-methods.md#geographic)
