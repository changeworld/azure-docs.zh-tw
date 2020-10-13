---
title: 使用 Azure 入口網站建立具有 HTTP 到 HTTPS 重新導向的 Front Door
description: 瞭解如何使用 Azure 入口網站來建立 Front Door，並將流量從 HTTP 重新導向至 HTTPS。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626609"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 入口網站建立具有 HTTP 到 HTTPS 重新導向的 Front Door

您可以使用 Azure 入口網站來建立具有 TLS 終止憑證的 [Front Door](quickstart-create-front-door.md) 。 路由規則可用來將 HTTP 流量重新導向至 HTTPS。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用現有的 Web 應用程式資源建立 Front Door

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

1. 選取 Azure 入口網站左上角的 [ **建立資源** ]。

1. 使用搜尋列搜尋 **Front Door** ，一旦找到資源類型，請選取 [ **建立**]。

1. 選擇 *訂* 用帳戶，然後使用現有的資源群組或建立一個新的群組。 選取 **[下一步** ] 以進入 [設定] 索引標籤。

    > [!NOTE]
    > 在 UI 中要求的位置僅適用于資源群組。 您的 Front Door 設定將會部署到所有 [Azure Front Door 的 POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="設定新 Front Door 的基本概念":::

1. Front Door 的設定會以三個步驟進行：新增預設前端主機、在後端集區中新增後端，然後建立路由規則來對應前端主機的路由行為。 選取 **+** _前端主機_ 上的 ' ' 圖示，以建立前端主機。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="設定新 Front Door 的基本概念":::

1. 為您的 Front Door 輸入預設前端主機的全域唯一名稱。 選取 [ **新增** ] 繼續進行下一個步驟。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="設定新 Front Door 的基本概念":::

### <a name="create-backend-pool"></a>建立後端集區

1. 選取 **+** _後端_ 集區上的 ' ' 圖示來建立後端集區。 提供後端集區的名稱，然後選取 [ **新增後端**]。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="設定新 Front Door 的基本概念":::

1. 選取後端主機類型作為 _App service_。 選取您的 web 應用程式裝載所在的訂用帳戶，然後從 **後端主機名稱**的下拉式清單中選取特定的 web 應用程式。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="設定新 Front Door 的基本概念":::

1. 選取 [ **新增** ] 以儲存後端，然後選取 [ **新增** ] 以儲存後端集區設定。 

## <a name="create-http-to-https-redirect-rule"></a>建立 HTTP 至 HTTPS 的重新導向規則

1. 選取 **+** *路由規則* 上的 ' ' 圖示來建立路由。 提供路由的名稱，例如 ' HttpToHttpsRedirect '，然後將 [ *接受的通訊協定* ] 欄位設定為 [ **僅限 HTTP**]。 確定已選取適當的 *前端/網域* 。  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="設定新 Front Door 的基本概念":::

1. 在 [ *路由詳細資料* ] 區段下，將 *路由類型* 設定為 [重新 **導向**]。 確定 [重新 *導向類型* ] 設定為 **找到 (302) ** 並將 [重新 *導向通訊協定* ] 設定為 [ **僅 HTTPS**]。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="設定新 Front Door 的基本概念":::

1. 選取 [ **新增** ] 以儲存 HTTP 到 HTTPS 重新導向的路由規則。

## <a name="create-forwarding-rule"></a>建立轉送規則

1. 新增另一個路由規則來處理 HTTPS 流量。 選取 [ **+** 簽署 *路由規則* ]，並提供路由的名稱，例如 ' DefaultForwardingRoute '。 然後，將 [ *接受的通訊協定* ] 欄位設定為 [ **僅 HTTPS**]。 確定已選取適當的 *前端/網域* 。

1. 在 [路由詳細資料] 區段中，將 *路由類型* 設定為 **轉寄**。 確定已選取正確的後端集區，且 *轉送通訊協定* 設定為 [ **僅 HTTPS**]。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="設定新 Front Door 的基本概念" border="false":::

1. 選取 [ **新增** ] 以儲存要求轉送的路由規則。

1. 選取 [ **審核** ] 和 [建立] **，然後建立您的 Front Door**設定檔。 一旦建立資源，請移至該資源。

## <a name="next-steps"></a>後續步驟

- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
- 深入瞭解 [Front Door 上的 URL 重新導向](front-door-url-redirect.md)。
