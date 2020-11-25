---
title: Azure 通訊服務的計量定義
titleSuffix: An Azure Communication Services concept document
description: 本文件涵蓋 Azure 入口網站中可用的計量定義。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 05/19/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 25c7016f6639df46a9279ef9a9aab2736efd4f95
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888652"
---
# <a name="metrics-overview"></a>計量概觀

Azure 通訊服務目前提供聊天和簡訊的計量。 [Azure 計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)可用來繪製您自己的圖表、調查計量值中的異常狀況，以及使用聊天和簡訊要求所發出的計量資料來了解您的 API 流量。

## <a name="where-to-find-metrics"></a>計量的所在位置

Azure 通訊服務中的聊天和簡訊服務會發出 API 要求的計量。 這些計量可在您的「通訊服務」資源下的 [計量] 刀鋒視窗中找到。 您也可以使用「通訊服務」資源下的 [活頁簿] 刀鋒視窗來建立永久的儀表板。

## <a name="metric-definitions"></a>計量定義

通訊服務計量內會呈現兩種類型的要求：**聊天 API 要求** 和 **簡訊 API 要求**。

聊天和簡訊 API 要求計量都包含三個可供您用來篩選計量資料的維度。 這些維度可使用 `Count` 彙總類型來彙總在一起，並支援所有標準的 Azure 彙總時間序列，包括 `Sum`、`Average`、`Min` 和 `Max`。

如需所支援彙總類型和時間序列彙總的詳細資訊，請參閱 [Azure 計量瀏覽器的進階功能](../../azure-monitor/platform/metrics-charts.md#changing-aggregation)

- **作業** - 可在 ACS 聊天閘道上呼叫的所有作業或路由。
- **狀態碼** - 要求之後所傳送的狀態碼回應。
- **StatusSubClass** - 回應之後所傳送的狀態碼系列。 


### <a name="chat-api-request-metric-operations"></a>聊天 API 要求計量作業

下列作業適用於聊天 API 要求計量：

| 作業/路由    | 說明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | 依訊息識別碼取得訊息。 |
| ListChatMessages     | 從對話取得聊天訊息的清單。 |
| SendChatMessage      | 將聊天訊息傳送至對話。 |
| UpdateChatMessage    | 更新聊天訊息。 |
| DeleteChatMessage    | 刪除聊天訊息。 |
| GetChatThread        | 取得聊天對話。 |
| ListChatThreads      | 取得使用者的聊天對話清單。 |
| UpdateChatThread     | 更新聊天對話的屬性。 |
| CreateChatThread     | 建立聊天對話。 |
| DeleteChatThread     | 刪除對話。 |
| GetReadReceipts      | 取得對話的讀取回條。 |
| SendReadReceipt      | 代表使用者將讀取回條事件傳送至對話。 |
| SendTypingIndicator           | 代表使用者將輸入事件張貼至對話。 |
| ListChatThreadParticipants    | 取得對話的成員。 |
| AddChatThreadParticipants     | 將對話成員新增至對話。 如果成員已存在，則不會發生任何變更。 |
| RemoveChatThreadParticipant   | 從對話中移除成員。 |

:::image type="content" source="./media/chat-metric.png" alt-text="聊天 API 要求計量。":::

如果對無法辨識的作業提出要求，則會收到「路由不正確」值回應。

### <a name="sms-api-requests"></a>簡訊 API 要求

下列作業適用於簡訊 API 要求計量：

| 作業/路由    | 說明                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | 傳送手機簡訊。 |
| SMSDeliveryReportsReceived     | 取得簡訊傳遞報告 |
| SMSMessagesReceived      | 取得手機簡訊。 |


:::image type="content" source="./media/sms-metric.png" alt-text="簡訊 API 要求計量。":::

## <a name="next-steps"></a>後續步驟

- 深入了解[資料平台計量](../../azure-monitor/platform/data-platform-metrics.md)