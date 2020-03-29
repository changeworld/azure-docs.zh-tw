---
title: 將 Azure API 管理服務部署到多個 Azure 區域
titleSuffix: Azure API Management
description: 瞭解如何將 Azure API 管理服務執行個體部署到多個 Azure 區域。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442657"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何將 Azure API 管理服務執行個體部署到多個 Azure 區域

Azure API 管理支援多區域部署，這使 API 發行者能夠跨任意數量的受支援的 Azure 區域分發單個 Azure API 管理服務。 多區域功能有助於減少地理分佈 API 消費者感知的請求延遲，並在一個區域離線時提高服務可用性。

新的 Azure API 管理服務最初隻包含單個 Azure 區域（主區域）中的一個[單元][unit]。 可以將其他區域添加到主區域或次要區域。 API 管理閘道元件部署到每個選定的主區域和次要區域。 傳入 API 請求將自動定向到最近的區域。 如果區域離線，API 請求將自動圍繞故障區域路由到下一個最近的閘道。

> [!NOTE]
> 只有 API 管理的閘道元件部署到所有區域。 服務管理元件和開發人員門戶僅託管在主區域中。 因此，在主區域中斷的情況下，對開發人員門戶的訪問和更改配置（例如添加 API、應用策略）的能力將受到影響，直到主區域重新連線。 當主區域處於離線可用時，次要區域將繼續使用可供它們使用的最新配置為 API 流量提供服務。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>部署 API 管理服務執行個體到新區域

> [!NOTE]
> 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][create an api management service instance]。

在 Azure 入口網站中，瀏覽至「API 管理」服務執行個體的 [級別與價格]**** 頁面。

![調整索引標籤][api-management-scale-service]

若要部署新區域，請從工具列中按一下 [+加入區域]****。

![加入區域][api-management-add-region]

從下拉式清單中選取位置，然後使用滑桿設定單位數量。

![指定單位][api-management-select-location-units]

按一下 [加入]**** 以將您的選項放入 [位置] 表格中。

重複此程序，直到設定好所有位置為止，然後從工具列中按一下 [儲存]**** 來開始部署程序。

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>從區域中刪除 API 管理服務執行個體

在 Azure 入口網站中，瀏覽至「API 管理」服務執行個體的 [級別與價格]**** 頁面。

![調整索引標籤][api-management-scale-service]

針對您想要移除的位置，使用表格最右邊的 [...]**** 按鈕來開啟操作功能表。 選取 [刪除]**** 選項。

確認刪除，然後按一下 [儲存]**** 來套用變更。

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>將 API 呼叫路由至區域後端服務

Azure API 管理只有一個後端服務 URL。 即使不同區域中有多個 Azure API 管理執行個體，API 閘道仍會將要求轉送到只部署在單一區域中的相同後端服務。 在此案例中，效能的提升只在於將回應快取到要求專屬區域中的 Azure API 管理內，但在連絡全球各地的後端時，可能仍會造成高延遲。

若要完全利用您系統的地理分佈，您應該將後端服務部署在 Azure API 管理執行個體所在的區域中。 然後，藉由使用原則和 `@(context.Deployment.Region)` 屬性，您可以將流量路由至您後端的本機執行個體。

1. 瀏覽至您的 Azure API 管理執行個體，然後按一下左側功能表中的 [API]****。
2. 選取想要的 API。
3. 從 [輸入處理]**** 中的箭號下拉式清單中，按一下 [程式碼編輯器]****。

    ![API 程式碼編輯器](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. 使用 `set-backend` 結合條件式 `choose` 原則，在檔案的 `<inbound> </inbound>` 區段中建構適當的路由原則。

    例如，下列 XML 檔案將用於美國西部和東亞區域：

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> 您還可以使用[Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)在後端服務前面，將 API 呼叫定向到流量管理器，並讓它自動解析路由。

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>使用自訂路由到 API 管理區域閘道

API 管理基於[最低延遲](../traffic-manager/traffic-manager-routing-methods.md#performance)將請求路由到區域_閘道_。 儘管在 API 管理中無法覆蓋此設置，但您可以使用自己的流量管理器來使用自訂路由規則。

1. 創建您自己的[Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)。
1. 如果使用自訂域，[請使用流量管理器](../traffic-manager/traffic-manager-point-internet-domain.md)而不是 API 管理服務。
1. [在流量管理器中配置 API 管理區域終結點](../traffic-manager/traffic-manager-manage-endpoints.md)。 區域終結點遵循 的 URL 模式`https://<service-name>-<region>-01.regional.azure-api.net`，例如`https://contoso-westus2-01.regional.azure-api.net`。
1. [在流量管理器中配置 API 管理區域狀態終結點](../traffic-manager/traffic-manager-monitoring.md)。 區域狀態終結點遵循 的 URL 模式`https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`，例如`https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`。
1. 指定流量管理器的[路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
