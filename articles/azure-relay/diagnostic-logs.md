---
title: 混合式連線的診斷記錄
description: 本文概述可用於 Azure 轉送的所有活動和診斷記錄。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f79e4aa21fcb9e592f431e252981dc0e7c02817f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314897"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>啟用 Azure 轉送混合式連線的診斷記錄
當您開始使用 Azure 轉送混合式連線時，可能會想要監視接聽程式和傳送者的開啟和關閉方式，以及混合式連線的建立和傳送訊息的方式。 本文提供 Azure 轉送服務所提供之活動和診斷記錄的總覽。 

您可以針對 Azure 轉送，查看兩種記錄類型：

- [活動記錄](../azure-monitor/platform/platform-logs-overview.md)：這些記錄包含對 Azure 入口網站中的命名空間或透過 Azure Resource Manager 範本執行之作業的相關資訊。 這些記錄一律會啟用。 例如：「建立或更新命名空間」、「建立或更新混合式連接」。 
- [診斷記錄](../azure-monitor/platform/platform-logs-overview.md).. 您可以設定診斷記錄，以更豐富的方式查看使用 API 對命名空間進行的作業和動作，或透過語言 SDK 進行的所有操作。

## <a name="view-activity-logs"></a>檢視活動記錄
若要查看 Azure 轉送命名空間的活動記錄，請切換至 Azure 入口網站中的 [**活動記錄**] 頁面。

![Azure 轉送-活動記錄](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>啟用診斷記錄

> [!NOTE]
> 診斷記錄僅適用于混合式連接，不適用於 Windows Communication Foundation （WCF）轉送。

若要啟用診斷記錄，請執行下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com)中，移至您的 Azure 轉送命名空間，然後在 [**監視**] 底下，選取 [**診斷設定**]。
1. 在 [**診斷設定**] 頁面上，選取 [**新增診斷設定**]。  

   ![[新增診斷設定] 連結](./media/diagnostic-logs/add-diagnostic-setting.png)

1. 執行下列步驟來設定診斷設定：
    1. 在 [**名稱**] 方塊中，輸入診斷設定的名稱。  
    2. 針對記錄類型選取 [ **HybridConnectionsEvent** ]。 
    3. 為診斷記錄選取下列三個**目的地**的其中一個：  
        1. 如果您選取 [封存**至儲存體帳戶**]，請設定將儲存診斷記錄的儲存體帳戶。  
        2. 如果您選取 [**串流至事件中樞**]，請設定您想要將診斷記錄串流至其中的事件中樞。
        3. 如果您選取 [**傳送至 Log analytics**]，請指定要將診斷傳送至哪個 Log analytics 實例。  

        ![診斷設定範例](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. 選取工具列上的 [儲存] 來儲存設定。

新的設定會在大約10分鐘內生效。 記錄會顯示在 [**診斷記錄**] 窗格中設定的封存目標中。 如需有關設定診斷設定的詳細資訊，請參閱[Azure 診斷記錄的總覽](../azure-monitor/platform/diagnostic-logs-overview.md)。


## <a name="schema-for-hybrid-connections-events"></a>混合式連接事件的架構
混合式連接事件記錄檔 JSON 字串包括下表所列的元素：

| Name | 說明 |
| ------- | ------- |
| ResourceId | Azure Resource Manager 資源識別碼 |
| ActivityId | 內部識別碼，用來識別指定的作業。 可能也稱為「TrackingId」 |
| 端點 | 轉送資源的位址 |
| OperationName | 要記錄的混合式連接作業類型 |
| EventTimeString | 記錄檔記錄的 UTC 時間戳記 |
| 訊息 | 事件的詳細訊息 |
| 類別 | 事件的類別。 目前只有 `HybridConnectionsEvents` 。 


## <a name="sample-hybrid-connections-event"></a>範例混合式連接事件
以下是 JSON 格式的範例混合式連接事件。 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>診斷記錄中所捕獲的事件和作業

| 作業 | 描述 | 
| --------- | ----------- | 
| AuthorizationFailed | 授權失敗。|
| InvalidSasToken | SAS 權杖無效。 | 
| ListenerAcceptingConnection | 接聽程式正在接受連接。 |
| ListenerAcceptingConnectionTimeout | 接聽程式接受連接已超時。 |
| ListenerAcceptingHttpRequestFailed | 接聽程式接受 HTTP 要求失敗，因為發生例外狀況。 |
| ListenerAcceptingRequestTimeout | 接聽程式接受要求已超時。 |  
| ListenerClosingFromExpiredToken | 因為安全性權杖已過期，所以接聽程式正在關閉。 | 
| ListenerRejectedConnection | 接聽程式已拒絕連接。 |
| ListenerReturningHttpResponse | 接聽程式傳回 HTTP 回應。 |  
| ListenerReturningHttpResponseFailed | 接聽程式傳回 HTTP 回應，失敗碼為。 | 
 ListenerSentHttpResponse | 轉送服務已收到來自接聽程式的 HTTP 回應。 | 
| ListenerUnregistered | 接聽程式已取消註冊。 | 
| ListenerUnresponsive | 傳迴響應時，接聽程式沒有回應。 | 
| MessageSendingToListener | 正在將訊息傳送至接聽程式。 |
| MessageSentToListener | 訊息會傳送至接聽程式。 | 
| NewListenerRegistered | 已註冊新的接聽程式。 |
| NewSenderRegistering | 正在註冊新的寄件者。 | 
| ProcessingRequestFailed | 混合式連接作業的處理失敗。 | 
| SenderConnectionClosed | 傳送者連接已關閉。 |
| SenderListenerConnectionEstablished | 傳送者和接聽程式已成功建立連接。 |
| SenderSentHttpRequest | 寄件者已傳送 HTTP 要求。 | 


## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 轉送，請參閱：

* [Azure 轉送簡介](relay-what-is-it.md)
* [開始使用轉送混合式連線](relay-hybrid-connections-dotnet-get-started.md)
