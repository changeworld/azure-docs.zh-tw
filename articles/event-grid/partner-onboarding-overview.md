---
title: 作為 Azure 事件方格合作夥伴上線
description: 作為 Azure 事件方格合作夥伴主題類型上線。 了解合作夥伴主題的資源模型和發佈流程。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758821"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>作為 Azure 事件方格合作夥伴上線

本文說明如何私下使用事件方格合作夥伴資源，以及如何成為公開供應的合作夥伴主題類型。

您不需要特殊權限，即可開始使用事件方格合作夥伴發佈事件相關聯的事件方格資源類型。 事實上，您可以立即使用這兩者私下將事件發佈到您自己的 Azure 訂閱，如果考慮成為合作夥伴時可測試資源模型。

## <a name="becoming-an-event-grid-partner"></a>變成事件方格合作夥伴

如果您有意成為公開的事件方格合作夥伴，請先填寫[此表單](https://aka.ms/gridpartnerform)，再連落事件方格小組：[GridPartner@microsoft.com](mailto:gridpartner@microsoft.com)。

## <a name="how-partner-topics-work"></a>合作夥伴主題運作方式
合作夥伴主題會採用事件方格已在使用的現有架構，從儲存體和 IoT 中樞等 Azure 資源發佈事件，並公開這些工具供任何人使用。 根據預設，這些工具僅供您的 Azure 訂閱私人使用。 若要讓您的事件供公開使用，請填寫上述表單，並[聯絡事件方格小組](mailto:gridpartner@microsoft.com)。

合作夥伴主題可讓您將事件發佈到 Azure 事件方格，以供多租用戶使用。

### <a name="onboarding-and-event-publishing-overview"></a>上線和活動發佈概觀

#### <a name="partner-flow"></a>合作夥伴流程

1. 建立 Azure 租用戶 (若尚未擁有)。
1. 使用 CLI 建立新的事件方格 `partnerRegistration`。 此資源包括如顯示名稱、描述、安裝 URI 等資訊。

    ![建立合作夥伴主題](./media/partner-onboarding-how-to/create-partner-registration.png)

1. 在您想要發佈事件的每個區域中，建立一或多個 `partnerNamespaces`。 在此過程中，事件方格服務會佈建發佈端點 (例如 `https://contoso.westus-1.eventgrid.azure.net/api/events`) 和存取金鑰。

    ![建立夥伴命名空間](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. 提供一種方式，讓客戶註冊在想要合作夥伴主題的系統中註冊。
1. 聯絡事件方格小組，讓我們知道您希望將您的合作夥伴主題類型轉為公開。

#### <a name="customer-flow"></a>客戶流程

1. 您的客戶會造訪 Azure 入口網站，記下想要在其中建立合作夥伴主題的訂閱識別碼和資源群組。
1. 客戶會透過您的系統要求合作夥伴主題。 在回應中，您要為您的合作夥伴命名空間建立事件通道。
1. 事件方格會在客戶的 Azure 訂閱和資源群組中建立 **擱置中**的合作夥伴主題。

    ![建立事件通道](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 客戶會透過 Azure 入口網站來啟用合作夥伴主題。 事件現在可能會從您的服務流向客戶的 Azure 訂閱。

    ![啟動合作夥伴主題](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>資源模型

以下是合作夥伴主題的資源模型。

### <a name="partner-registrations"></a>合作夥伴註冊
* 資源：`partnerRegistrations`
* 使用者：合作夥伴
* 描述：擷取 SaaS 合作夥伴的全域中繼資料 (例如名稱、顯示名稱、描述、安裝 URI)。
    
    建立/更新合作夥伴註冊屬於合作夥伴的自助作業。 合作夥伴可透過此自助功能建立並測試完整的端對端流程。
    
    只有 Microsoft 核准的 partnerRegistrations 可供客戶探索。
* 範圍：建立在合作夥伴的 Azure 訂閱中。 中繼資料一旦公開，客戶便可以看到。

### <a name="partner-namespaces"></a>合作夥伴命名空間
* 資源：partnerNamespaces
* 使用者：合作夥伴
* 描述：提供要發佈客戶事件的目標區域資源。 每個合作夥伴命名空間都有發佈端點和驗證金鑰。 命名空間也是合作夥伴為指定的客戶提出合作夥伴主題要求，並列出活動中客戶的方式。
* 範圍：存於合作夥伴的訂閱中。

### <a name="event-channel"></a>事件通道
* 資源：`partnerNamespaces/eventChannels`
* 使用者：合作夥伴
* 描述：事件通道是客戶合作夥伴主題的鏡像。 建立事件通道，並在中繼資料中指定客戶的 Azure 訂閱和資源群組後，就會發出訊號給事件方格，為客戶建立合作夥伴主題。 事件方格會發出 ARM 呼叫，在客戶的訂閱中建立對應的 partnerTopic。 合作夥伴主題將會以「擱置中」狀態建立。 每個 eventTunnel 與 partnerTopic 之間都有 1-1 連結。
* 範圍：存於合作夥伴的訂閱中。

### <a name="partner-topics"></a>合作夥伴主題
* 資源：`partnerTopics`
* 使用者：客戶
* 描述：合作夥伴主題類似於事件方格中的自訂主題和系統主題。 每個合作夥伴主題都會與特定的「來源」(例如 `Contoso:myaccount`) 及特定的合作夥伴主題類型 (例如 "Contoso") 建立關聯。 客戶會在合作夥伴主題上建立事件訂閱，以將事件路由至各種事件處理常式。

    客戶無法直接建立此資源。 建立合作夥伴主題的唯一方式，就是透過建立事件通道的合作夥伴作業。
* 範圍：存於客戶的訂閱中。

### <a name="partner-topic-types"></a>合作夥伴主題類型
* 資源：`partnerTopicTypes`
* 使用者：客戶
* 描述：合作夥伴主題類型是全租用戶的資源類型，可讓客戶探索經核准的合作夥伴主題類型清單。 URL 如下所示： https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* 範圍：全域

## <a name="publishing-events-to-event-grid"></a>將事件發佈至事件格線
當您在 Azure 區域中建立 partnerNamespace 時，會取得區域端點和對應的驗證金鑰。 針對該命名空間中的所有客戶事件通道，將事件批次發佈至此端點。 Azure 事件方格會根據事件中的 [來源] 欄位，將每個事件對應到相對應的合作夥伴主題。

### <a name="event-schema-cloudevents-v10"></a>事件結構描述：CloudEvents v1.0
使用 CloudEvents 1.0 結構描述將事件發佈到 Azure 事件方格。 事件方格支援結構化模式和批次模式。 CloudEvents 1.0 是合作夥伴命名空間唯一支援的事件結構描述。

### <a name="example-flow"></a>範例流程

1.  發佈服務會對 `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` 進行 HTTP POST。
2.  在要求中，包含名為 aeg-sas-key 的標頭值，其中包含用於驗證的金鑰。 此金鑰會在建立 partnerNamespace 時佈建。 例如，有效的標頭值為 aeg-sas-key：VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
3.  將 Content-Type 標頭設定為 “application/cloudevents-batch+json; charset=UTF-8”。
4.  使用相對應於該區域的事件批次，對上述發佈 URL 執行 HTTP POST。 例如：

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

在張貼至 partnerNamespace 端點後，您會收到回應。 回應是標準的 HTTP 回應碼。 一些常見回應有：

| 結果                             | 回應              |
|------------------------------------|-----------------------|
| Success                            | 200 確定                |
| 事件資料的格式不正確    | 400 不正確的要求       |
| 存取金鑰無效                 | 401 未經授權      |
| 端點不正確                 | 404 找不到         |
| 陣列或事件超過大小限制 | 413 承載太大 |

## <a name="reference"></a>參考

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM 範本](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [ARM 範本結構描述](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST APIs](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI 擴充功能](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>後續步驟
- [合作夥伴主題概觀](partner-topics-overview.md)
- [合作夥伴主題上線表單](https://aka.ms/gridpartnerform)
- [Auth0 合作夥伴主題](auth0-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
