---
title: '合作夥伴上線總覽 (Azure 事件方格) '
description: 提供如何上架為事件方格夥伴的總覽。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 2a2e33395cabd368d5d5d870dd0461e4cbd37e0d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081190"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>合作夥伴上線總覽 (Azure 事件方格) 

本文說明如何私下使用 Azure 事件方格夥伴資源，以及如何成為公開可用的合作夥伴主題類型。

您不需要特殊權限，即可開始使用事件方格合作夥伴發佈事件相關聯的事件方格資源類型。 事實上，您現在可以使用它們來私下將事件發佈至您自己的 Azure 訂用帳戶，並在考慮成為合作夥伴時測試資源模型。

> [!NOTE]
> 如需如何使用 Azure 入口網站作為事件方格夥伴上線的逐步指示，請參閱 [如何將 (Azure 入口網站) 中的事件方格夥伴上架 ](onboard-partner.md)。 

## <a name="how-partner-events-work"></a>夥伴活動的運作方式
「夥伴事件」功能可採用事件方格已用來從 Azure 資源（例如 Azure 儲存體和 Azure IoT 中樞）發佈事件的現有架構，並讓這些工具可公開供任何人使用。 根據預設，使用這些工具僅供您的 Azure 訂用帳戶私用。 若要公開提供您的活動，請填寫表單，並 [與事件方格小組聯繫](mailto:gridpartner@microsoft.com)。

「夥伴事件」功能可讓您將事件發佈至 Azure 事件方格，以進行多租使用者耗用量。

## <a name="onboarding-and-event-publishing-overview"></a>上線和活動發佈概觀

### <a name="partner-flow"></a>合作夥伴流程

1. 建立 Azure 租用戶 (若尚未擁有)。
1. 使用 Azure CLI 來建立新的事件方格 `partnerRegistration` 。 此資源包含顯示名稱、描述、安裝程式 URI 等資訊。

    ![建立合作夥伴主題](./media/partner-onboarding-how-to/create-partner-registration.png)

1. 在您想要發佈事件的每個區域中，建立一或多個夥伴命名空間。 事件方格服務會布建發行端點 (例如 `https://contoso.westus-1.eventgrid.azure.net/api/events`) 和存取金鑰。

    ![建立夥伴命名空間](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. 提供一種方法讓客戶在您的系統中註冊他們想要的夥伴主題。
1. 請洽詢事件方格小組，讓他們知道您想要讓合作夥伴的主題類型成為公開的。

### <a name="customer-flow"></a>客戶流程

1. 您的客戶造訪 Azure 入口網站，以記下您想要在其中建立夥伴主題的 Azure 訂用帳戶識別碼和資源群組。
1. 客戶透過您的系統要求合作夥伴主題。 在回應中，您會建立夥伴命名空間的事件通道。
1. 事件方格會在客戶的 Azure 訂用帳戶和資源群組中建立 **暫** 止的夥伴主題。

    ![建立事件通道](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 客戶會透過 Azure 入口網站啟用夥伴主題。 事件現在可能會從您的服務流向客戶的 Azure 訂用帳戶。

    ![啟用夥伴主題](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>資源模型
下列資源模型適用于夥伴事件。

### <a name="partner-registrations"></a>合作夥伴註冊
* 資源：`partnerRegistrations`
* 使用者：合作夥伴
* 描述：將軟體即服務的全域中繼資料（ (SaaS) 夥伴）捕獲 (例如，名稱、顯示名稱、描述、安裝程式 URI) 。
    
    建立或更新夥伴註冊是合作夥伴的自助服務作業。 這種自助服務能力可讓合作夥伴建立並測試完整的端對端流程。
    
    客戶只能探索 Microsoft 核准的合作夥伴註冊。
* 範圍：在合作夥伴的 Azure 訂用帳戶中建立。 客戶公開之後，就可以看到中繼資料。

### <a name="partner-namespaces"></a>夥伴命名空間
* 資源：`partnerNamespaces`
* 使用者：合作夥伴
* 描述：提供要發佈客戶事件的目標區域資源。 每個夥伴命名空間都有發行端點和驗證金鑰。 此命名空間也是合作夥伴為指定的客戶要求夥伴主題的方式，並列出作用中的客戶。
* 範圍：位於合作夥伴的訂用帳戶中。

### <a name="event-channel"></a>事件通道
* 資源：`partnerNamespaces/eventChannels`
* 使用者：合作夥伴
* 描述：事件通道是客戶合作夥伴主題的鏡像。 藉由建立事件通道，並在中繼資料中指定客戶的 Azure 訂用帳戶和資源群組，您可以向事件方格發出通知，以建立客戶的夥伴主題。 事件方格發出 Azure Resource Manager 呼叫，以在客戶的訂用帳戶中建立對應的夥伴主題。 夥伴主題會以暫止狀態建立。 每個事件通道與夥伴主題之間都有一對一的連結。
* 範圍：位於合作夥伴的訂用帳戶中。

### <a name="partner-topics"></a>合作夥伴主題
* 資源：`partnerTopics`
* 使用者：客戶
* 描述：合作夥伴主題類似于事件方格中的自訂主題和系統主題。 每個夥伴主題都與特定的來源相關聯 (例如， `Contoso:myaccount`) 和特定的夥伴主題類型 (例如 Contoso) 。 客戶會在夥伴主題上建立事件訂閱，以將事件路由傳送至各種事件處理常式。

    客戶無法直接建立此資源。 建立夥伴主題的唯一方法是透過建立事件通道的夥伴作業。
* 範圍：居住在客戶的訂用帳戶中。

### <a name="partner-topic-types"></a>合作夥伴主題類型
* 資源：`partnerTopicTypes`
* 使用者：客戶
* 描述：合作夥伴主題類型是租使用者範圍的資源類型，可讓客戶探索核准的夥伴主題類型清單。 URL 看起來像 https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* 範圍：全域

## <a name="publish-events-to-event-grid"></a>將事件發佈至事件方格
當您在 Azure 區域中建立夥伴命名空間時，您會取得區域端點和對應的驗證金鑰。 針對該命名空間中的所有客戶事件通道，將事件批次發佈至此端點。 根據事件中的來源欄位，Azure 事件方格會將每個事件與對應的夥伴主題對應。

### <a name="event-schema-cloudevents-v10"></a>事件架構： CloudEvents v 1。0
使用 CloudEvents 1.0 架構將事件發佈至 Azure 事件方格。 事件方格支援結構化模式和批次模式。 CloudEvents 1.0 是唯一支援的夥伴命名空間事件架構。

### <a name="example-flow"></a>範例流程

1.  發佈服務會對 `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` 進行 HTTP POST。
1.  在要求中，包含名為 aeg-sas-key 的標頭值，其中包含用於驗證的金鑰。 此金鑰會在建立夥伴命名空間期間布建。 例如，有效的標頭值為 aeg-sas-key：VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  將 Content-type 標頭設定為 "application/cloudevents-batch + json;字元集 = UTF-8a」。
1.  使用對應至該區域的事件批次，對發行 URL 執行 HTTP POST 查詢。 例如：

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

張貼至夥伴命名空間端點之後，您會收到回應。 回應是標準的 HTTP 回應碼。 一些常見回應有：

| 結果                             | 回應              |
|------------------------------------|-----------------------|
| Success                            | 200 確定                |
| 事件資料的格式不正確    | 400 不正確的要求       |
| 存取金鑰無效                 | 401 未經授權      |
| 端點不正確                 | 404 找不到         |
| 陣列或事件超過大小限制 | 413 承載太大 |

## <a name="references"></a>參考

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM 範本](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM 範本架構](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI 擴充功能](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>後續步驟
- [合作夥伴主題概觀](partner-events-overview.md)
- [合作夥伴主題上架表單](https://aka.ms/gridpartnerform)
- [Auth0 合作夥伴主題](auth0-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
