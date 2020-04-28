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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442657"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何將 Azure API 管理服務執行個體部署到多個 Azure 區域

Azure API 管理支援多區域部署，可讓 API 發行者跨任意數目的支援 Azure 區域散發單一 Azure API 管理服務。 多重區域功能有助於減少地理位置分散的 API 取用者所觀察到的要求延遲，並在某個區域離線時改善服務可用性。

新的 Azure API 管理服務一開始只會在單一 Azure 區域（主要區域）中包含一個[單位][unit]。 您可以將其他區域新增至主要或次要區域。 API 管理閘道元件會部署到每個選取的主要和次要區域。 傳入的 API 要求會自動導向至最接近的區域。 如果區域離線，API 要求將會自動路由至失敗的區域，直到下一個最接近的閘道。

> [!NOTE]
> 只有 API 管理的閘道元件會部署到所有區域。 服務管理元件和開發人員入口網站僅裝載于主要區域。 因此，在主要區域中斷的情況下，存取開發人員入口網站和變更設定的能力（例如新增 Api、套用原則）將會受到影響，直到主要區域恢復上線為止。 當主要區域離線時，次要區域將會繼續使用最新的可用設定來提供 API 流量。

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
> 您也可以使用[Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)來處理後端服務，將 API 呼叫導向至流量管理員，並讓它自動解析路由。

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>使用 API 管理區域閘道的自訂路由

API 管理會根據[最低延遲](../traffic-manager/traffic-manager-routing-methods.md#performance)，將要求路由傳送至區域_閘道_。 雖然無法在 API 管理中覆寫這項設定，但您可以使用自己的流量管理員搭配自訂路由規則。

1. 建立您自己的[Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)。
1. 如果您使用的是自訂網域，請[將其用於流量管理員](../traffic-manager/traffic-manager-point-internet-domain.md)，而不是 API 管理服務。
1. [在流量管理員中設定 API 管理區域端點](../traffic-manager/traffic-manager-manage-endpoints.md)。 例如`https://<service-name>-<region>-01.regional.azure-api.net` `https://contoso-westus2-01.regional.azure-api.net`，區域端點會遵循的 URL 模式。
1. [在流量管理員中設定 API 管理區域狀態端點](../traffic-manager/traffic-manager-monitoring.md)。 例如`https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`，地區狀態端點會遵循的 URL 模式。
1. 指定流量管理員的[路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

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
