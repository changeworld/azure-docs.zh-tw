---
title: Azure 事件方格中的自訂主題
description: 說明 Azure 事件方格中的自訂主題。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8f48824989c8ec51b766385188ad99e9e59cf621
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113780"
---
# <a name="custom-topics-in-azure-event-grid"></a>Azure 事件方格中的自訂主題
事件方格主題提供來源傳送事件的端點。 發行者會建立 Event Grid 主題，並決定事件來源是否需要一個主題或多個主題。 主題可用於相關事件的集合。 若要回應某些類型的事件，訂閱者會決定要訂閱的主題。

**自訂主題**是應用程式和協力廠商的主題。 您建立或指派自訂主題的存取時，會在您的訂用帳戶中看見該自訂主題。 

在設計應用程式時，您將有足夠的彈性可決定要建立多少個主題。 針對大型解決方案，請為**每個類別的相關事件**建立一個**自訂主題**。 例如，請考慮使用應用程式來傳送與修改使用者帳戶和處理訂單有關的事件。 任何事件處理常式不太需要兩種類別的事件。 建立兩個自訂主題，並讓事件處理常式訂閱其感興趣的自訂主題。 對於小型解決方案，您可能會想要將所有事件傳送至單一主題。 事件訂閱者可以篩選出他們想要的事件類型。

## <a name="event-schema"></a>結構描述
如需事件架構的詳細總覽，請參閱[Azure Event Grid 事件架構](event-schema.md)。 若為自訂主題，事件發行者會決定**資料**物件。 最高層級的資料應包含與標準資源定義事件相同的欄位。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

下列各節提供教學課程的連結，可讓您使用 Azure 入口網站、CLI、PowerShell 和 Azure Resource Manager （ARM）範本來建立自訂主題。 


## <a name="azure-portal-tutorials"></a>Azure 入口網站教學課程
|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure 入口網站建立和路由傳送自訂事件](custom-event-quickstart-portal.md) | 示範如何使用入口網站傳送自訂事件。 |
| [快速入門：將自訂事件路由到 Azure 佇列儲存體](custom-event-to-queue-storage.md) | 描述如何將自訂事件傳送至佇列儲存體。 |
| [如何：張貼到自訂主題](post-to-custom-topic.md) | 示範如何將事件張貼到自訂主題。 |


## <a name="azure-cli-tutorials"></a>Azure CLI 教學課程
|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 建立和路由傳送自訂事件](custom-event-quickstart.md) | 示範如何使用 Azure CLI 傳送自訂事件。 |
| [Azure CLI：建立事件方格自訂主題](./scripts/event-grid-cli-create-custom-topic.md)|建立自訂主題的範例指令碼。 指令碼會擷取端點和金鑰。|
| [Azure CLI：訂閱自訂主題的事件](./scripts/event-grid-cli-subscribe-custom-topic.md)|建立自訂主題訂用帳戶的範例指令碼。 它會將事件傳送到 WebHook。|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell 教學課程
|Title  |描述  |
|---------|---------|
| [快速入門：使用 Azure PowerShell 建立和路由傳送自訂事件](custom-event-quickstart-powershell.md) | 示範如何使用 Azure PowerShell 傳送自訂事件。 |
| [PowerShell：建立事件方格自訂主題](./scripts/event-grid-powershell-create-custom-topic.md)|建立自訂主題的範例指令碼。 指令碼會擷取端點和金鑰。|
| [PowerShell：訂閱自訂主題的事件](./scripts/event-grid-powershell-subscribe-custom-topic.md)|建立自訂主題訂用帳戶的範例指令碼。 它會將事件傳送到 WebHook。|

## <a name="arm-template-tutorials"></a>ARM 範本教學課程
|Title  |描述  |
|---------|---------|
| [Resource Manager 範本：自訂主題和 WebHook 端點](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | 建立自訂主題和該自訂主題訂用帳戶的 Resource Manager 範本。 它會將事件傳送到 WebHook。 |
| [Resource Manager 範本：自訂主題和事件中樞端點](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 建立自訂主題訂用帳戶的 Resource Manager 範本。 它會將事件傳送到 Azure 事件中樞。 |

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [系統主題](system-topics.md)
- [網域](event-domains.md)