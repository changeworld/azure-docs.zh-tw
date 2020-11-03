---
title: 教學課程：使用 Azure 流量管理員設定優先順序流量路由
description: 本教學課程將說明如何在流量管理員中設定優先順序流量路由方法
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208051"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>教學課程：在流量管理員中設定優先順序流量路由方法

本教學課程說明如何使用 Azure 流量管理員，透過優先順序路由方法將使用者流量路由傳送至特定端點。 在此路由方法中，您將定義每個進入流量管理員設定檔組態的端點順序。 來自使用者的流量會依照其列出的順序路由傳送至端點。 當您想要設定服務容錯移轉時，這個路由方法很有用。 主要端點會取得優先順序號碼 '1'，並將為所有傳入的要求提供服務。 而較低優先順序的端點則會作為備份。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> - 建立具有優先順序路由的流量管理員設定檔。
> - 新增端點。
> - 設定端點的優先順序。
> - 使用流量管理員設定檔。
> - 刪除流量管理員設定檔。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="to-configure-the-priority-traffic-routing-method"></a>設定優先順序流量路由方法
1. 從瀏覽器登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取左側的 [+建立資源]。 搜尋 [流量管理員設定檔] ，然後選取 [建立]。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="建立流量管理員優先順序設定檔":::

1. 在 [建立流量管理員設定檔] 頁面中，定義下列設定：

    | 設定         | 值                                              |
    | ---             | ---                                                |
    | 名稱            | 提供設定檔的名稱。 此名稱必須是 trafficmanager.net 區域內的唯一名稱。 若要存取流量管理員設定檔，您可以使用 DNS 名稱 `<profilename>.trafficmanager.net`。 |    
    | 路由方法  | 選取 [優先順序]。 |
    | 訂用帳戶    | 選取您的訂用帳戶。 |
    | 資源群組   | 使用現有的資源群組，或建立新的資源群組來放置此設定檔。 如果您選擇建立新的資源群組，請使用 [資源群組位置]  下拉式清單來指定資源群組的位置。 這項設定是指資源群組的位置，完全不影響將部署到全球的流量管理員設定檔。 |

1. 選取 [建立] 以部署您的流量管理員設定檔。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="建立流量管理員設定檔優先順序":::

## <a name="add-endpoints"></a>新增端點

1. 從清單中選取流量管理員設定檔。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="流量管理員設定檔清單":::

1. 在 [設定] 底下選取 [端點]，然後選取 [+ 新增] 以新增端點。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="流量管理員新增端點":::

1. 選取或輸入下列設定： 

    | 設定                | 值                                              |
    | ---                    | ---                                                |
    | 類型                   | 選取端點類型。 |    
    | Name                   | 提供名稱以識別此端點。 |
    | 目標資源類型   | 選取目標的資源類型。 |
    | 目標資源        | 從清單中選取資源。 |
    | 優先順序               | 為此端點提供優先順序號碼。 1 是最高優先順序。 |


1. 選取 [新增] 以新增端點。 重複步驟 2 和 3 以新增其他端點。 請記得設定適當的優先順序號碼。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="新增優先順序 1 端點":::

1. 在 [端點] 頁面上，檢閱端點的優先順序。 當您選取 [優先順序] 流量路由方法時，所選端點的順序便很重要。 確認端點的優先順序。  主要端點會位於最上方。 請重複檢查其顯示順序。 所有要求都將路由傳送到第一個端點，且如果流量管理員偵測到端點狀況不良，流量會自動容錯移轉到下一個端點。 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="優先順序端點的清單":::

1. 若要變更端點優先順序，請選取端點、變更優先順序值，然後選取 [儲存] 以儲存端點設定。

## <a name="use-the-traffic-manager-profile"></a>使用流量管理員設定檔

1.  在入口網站的搜尋列中，搜尋您在上一節建立的 **流量管理員設定檔** 名稱，然後在顯示的結果中選取流量管理員設定檔。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="搜尋流量管理員設定檔":::

1.  [流量管理員設定檔] 概觀頁面會顯示新建流量管理員設定檔的 DNS 名稱。 這可由任何用戶端使用 (例如使用網頁瀏覽器進行瀏覽)，以路由傳送至由路由類型所決定的正確端點。 在此情況下，所有的要求都會路由傳送到第一個端點，且如果流量管理員偵測到端點狀況不良，流量會自動容錯移轉到下一個端點。

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="流量管理員 DNS 名稱":::

1. 在流量管理員設定檔運作後，您可以編輯授權 DNS 伺服器上的 DNS 記錄，以將您的公司網域名稱指向流量管理員網域名稱。

## <a name="clean-up-resources"></a>清除資源

如果您不再需要流量管理員設定檔，請找出設定檔，然後選取 [刪除設定檔]。

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="刪除流量管理員優先順序設定檔":::

## <a name="next-steps"></a>後續步驟

若要深入了解優先順序路由方法，請參閱：

> [!div class="nextstepaction"]
> [優先順序路由方法](traffic-manager-routing-methods.md#priority-traffic-routing-method)
