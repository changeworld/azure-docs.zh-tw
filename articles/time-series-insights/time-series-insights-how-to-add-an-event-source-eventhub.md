---
title: 添加事件中心事件源 - Azure 時間序列見解 |微軟文檔
description: 了解如何將 Azure 事件中樞的事件來源新增至時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905404"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>新增事件中樞事件來源到您的時間序列深入解析環境

此文章說明如何使用 Azure 入口網站，將從 Azure 事件中樞讀取資料的事件來源新增至您的 Azure 時間序列深入解析環境。

> [!NOTE]
> 本文中描述的步驟既適用于時序見解 GA 和時序見解預覽環境。

## <a name="prerequisites"></a>Prerequisites

- 創建時間序列見解環境，如創建[Azure 時間序列見解環境](./time-series-insights-update-create-environment.md)中所述。
- 建立事件中樞。 使用[Azure 門戶讀取創建事件中心命名空間和事件中心](../event-hubs/event-hubs-create.md)。
- 事件中樞必須要有傳入的作用中訊息事件。 瞭解如何使用[.NET 框架向 Azure 事件中心發送事件](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)。
- 在事件中樞建立專用取用者群組，讓時間序列深入解析環境從中取用。 每個時間序列深入解析事件來源都必須有自身專屬的取用者群組，且不可與任何其他取用者共用。 如果多個讀取器使用來自同一消費者組的事件，則所有讀取器都可能顯示失敗。 另外也限制每一個事件中樞最多有 20 個取用者群組。 有關詳細資訊，請閱讀[事件中心程式設計指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="add-a-consumer-group-to-your-event-hub"></a>將取用者群組新增至事件中樞

應用程式會使用取用者群組從 Azure 事件中樞提取資料。 要可靠地從事件中心讀取資料，請提供專用消費者組，該組僅由此時間序列見解環境使用。

將新的取用者群組新增至事件中樞：

1. 在[Azure 門戶](https://portal.azure.com)中，從事件中心命名空間的 **"概述"** 窗格中查找並打開事件中心實例。 選擇**事件中心>實體**或在**名稱**下查找實例。

    [![打開活動中心命名空間](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. 在事件中心實例中，選擇 **"實體>消費者組**。 然後，選擇 **+ 消費者組**以添加新消費者組。 

   [![事件中心 - 添加消費者組](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   否則，選擇現有消費者組並跳到下一節。

1. 在 [取用者群組]**** 頁面上，在 [名稱]**** 中輸入新的唯一值。  在時間序列深入解析環境中建立新的事件來源時，使用此相同名稱。

1. 選取 [建立]****。

## <a name="add-a-new-event-source"></a>新增事件來源

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 找出您的現有時間序列深入解析環境。 在左側功能表中，選取 [所有資源]****，然後選取您的時間序列深入解析環境。

1. 選擇**事件源**，然後選擇 **"添加**"。

   [![在 [事件來源] 下，選取 [加入] 按鈕](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. 輸入事件**源名稱**的值，該值對於此時間序列見解環境（如`Contoso-TSI-GA-Event-Hub-ES`）是唯一的。

1. 在 [來源]**** 中，選取 [事件中樞]****。

1. 為 [匯入選項]**** 選取適當的值：

   * 若您的其中一個訂用帳戶中有現有的事件中樞，請選取 [來自可用訂閱的使用事件中樞]****。 此選項是最簡單的方法。

     [![選擇事件源導入選項](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  下表說明 [來自可用訂閱的使用事件中樞]**** 選項的必要屬性：

       [![訂閱和事件中心詳細資訊](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | 屬性 | 描述 |
       | --- | --- |
       | 訂用帳戶 | 所需的事件中心實例和命名空間所屬的訂閱。 |
       | 事件中樞命名空間 | 所需事件中心實例所屬的事件中心命名空間。 |
       | 事件中樞名稱 | 所需事件中心實例的名稱。 |
       | 事件中心策略值 | 選擇所需的共用訪問策略。 您可以在事件中心 **"配置"** 選項卡上創建共用訪問策略。每個共用訪問策略都有一個名稱、您設置的許可權和訪問金鑰。 事件來源的共用存取原則必須** 有**讀取**權限。 |
       | 事件中樞原則金鑰 | 從選定的事件中心策略值預填充。 |

    * 若事件中樞對於您的訂用帳戶而言是外部，或您想要選取進階選項，請選取 [手動提供事件中樞設定]****。

       下表說明 [手動提供事件中樞設定]**** 選項的必要屬性：
 
       | 屬性 | 描述 |
       | --- | --- |
       | 訂用帳戶識別碼 | 所需的事件中心實例和命名空間所屬的訂閱。 |
       | 資源群組 | 所需的事件中心實例和命名空間所屬的資源組。 |
       | 事件中樞命名空間 | 所需事件中心實例所屬的事件中心命名空間。 |
       | 事件中樞名稱 | 所需事件中心實例的名稱。 |
       | 事件中心策略值 | 選擇所需的共用訪問策略。 您可以在事件中心 **"配置"** 選項卡上創建共用訪問策略。每個共用訪問策略都有一個名稱、您設置的許可權和訪問金鑰。 事件來源的共用存取原則必須** 有**讀取**權限。 |
       | 事件中樞原則金鑰 | 用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 在這裡輸入主要金鑰或次要金鑰。 |

    * 這兩個選項都共用以下配置選項：

       | 屬性 | 描述 |
       | --- | --- |
       | 事件中樞取用者群組 | 從事件中樞讀取事件的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。 |
       | 事件序列化格式 | 目前，JSON 是目前唯一可用的序列化格式。 事件消息必須採用此格式或無法讀取資料。 |
       | 時間戳記屬性名稱 | 若要判斷此值，您需要了解傳送至事件中樞之訊息資料的訊息格式。 這個值是您想要使用作為事件時間戳記之訊息資料中特定事件屬性的**名稱**。 此值區分大小寫。 若保留空白，會使用事件來源中的**事件加入佇列時間**作為事件時間戳記。 |

1. 新增您新增至事件中樞的專用時間序列深入解析取用者群組名稱。

1. 選取 [建立]****。

   創建事件源後，時間序列見解將自動開始將資料流程式傳輸到您的環境。

## <a name="next-steps"></a>後續步驟

* [定義資料存取原則](time-series-insights-data-access.md)來保護資料。

* [將事件發送到](time-series-insights-send-events.md)事件源。

* 在[時間序列深入解析總管](https://insights.timeseries.azure.com)中存取您的環境。
