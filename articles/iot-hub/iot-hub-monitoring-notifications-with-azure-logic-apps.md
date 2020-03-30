---
title: 搭配 Azure Logic Apps 進行 IoT 遠端監視和通知 | Microsoft Docs
description: 使用 Azure Logic Apps 進行 IoT 中樞的 IoT 溫度監視，並對於偵測到的任何異常自動將電子郵件通知寄送到您的信箱。
author: robinsh
keywords: iot 監視、iot 通知、iot 溫度監視
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: ad1fcb67704e79f5aef62a59604e47f477804405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68385726"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>搭配連接 IoT 中樞和信箱的 Azure Logic Apps 進行 IoT 遠端監視和通知

![端對端圖表](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure 邏輯應用](https://docs.microsoft.com/azure/logic-apps/)可説明您協調跨本地和雲服務、一個或多個企業以及各種協定的工作流。 邏輯應用以觸發器開頭，然後啟動一個或多個可以使用內置控制項（如條件和反覆運算器）排序的操作。 這種靈活性使邏輯應用成為 IoT 監視方案的理想 IoT 解決方案。 例如，從設備到達 IoT Hub 終結點的遙測資料可以啟動邏輯應用工作流以將資料存儲在 Azure 存儲 Blob 中，發送電子郵件警報以警告資料異常，安排技術人員訪問設備報告故障，等等。

## <a name="what-you-learn"></a>您學到什麼

您學會如何建立連接 IoT 中樞和信箱的邏輯應用程式用於溫度監視和通知。

在設備上運行的用戶端代碼在其發送到 IoT`temperatureAlert`中心的每個遙測消息上設置應用程式屬性。 當用戶端代碼檢測到溫度超過 30 C 時，它將此屬性設置為`true`;否則，它將屬性設置為`false`。

到達 IoT 中心的消息如下所示，正文中包含遙測資料，應用程式屬性中包含的`temperatureAlert`屬性（不顯示系統屬性）：

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

要瞭解有關 IoT 中心消息格式的更多內容，請參閱[創建和讀取 IoT 中心消息](iot-hub-devguide-messages-construct.md)。

在本主題中，在 IoT 中心設置路由以將`temperatureAlert`屬性位於`true`服務匯流排終結點的郵件發送到該路由。 然後，您設置一個邏輯應用，該應用會觸發到達服務匯流排終結點的郵件，並向您發送電子郵件通知。

## <a name="what-you-do"></a>您要做什麼

* 創建服務匯流排命名空間，並將服務匯流排佇列添加到其中。
* 向 IoT 中心添加自訂終結點和路由規則，以將包含溫度警報的消息路由到服務匯流排佇列。
* 創建、配置和測試邏輯應用以使用來自服務匯流排佇列的郵件，並向所需的收件者發送通知電子郵件。

## <a name="what-you-need"></a>您需要什麼

* 完成[樹莓派線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或設備教程之一;例如，[樹莓派與node.js。](iot-hub-raspberry-pi-kit-node-get-started.md) 這些要求包括以下要求：

  * 有效的 Azure 訂用帳戶。
  * 位於您訂用帳戶中的 Azure IoT 中樞。
  * 在設備上運行的用戶端應用程式，向 Azure IoT 中心發送遙測消息。

## <a name="create-service-bus-namespace-and-queue"></a>創建服務匯流排命名空間和佇列

建立服務匯流排命名空間與佇列。 在本主題的稍後部分，您將在 IoT 中心創建路由規則，以指示包含溫度警報的郵件到服務匯流排佇列，邏輯應用將在其中選取這些消息，並觸發它發送通知電子郵件。

### <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間

1. 在[Azure 門戶](https://portal.azure.com/)上，選擇 **" 創建資源** > **集成** > **服務匯流排**"。

1. 在 **"創建命名空間"** 窗格中，提供以下資訊：

   **名稱**： 服務匯流排命名空間的名稱。 命名空間在整個 Azure 中必須是唯一的。

   **定價層**：從下拉清單中選擇 **"基本**"。 「基本」層足供本教學課程之用。

   **資源組**：使用 IoT 中心使用的相同資源組。

   **位置**：使用 IoT 中樞使用的同一個位置。

   ![在 Azure 入口網站中建立服務匯流排命名空間](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. 選取 [建立]****。 等待部署完成，然後再繼續執行下一步。

### <a name="add-a-service-bus-queue-to-the-namespace"></a>將服務匯流排佇列添加到命名空間

1. 打開服務匯流排命名空間。 到達服務匯流排命名空間的最簡單方法是從資源窗格中選擇**資源組**，選擇資源組，然後從資源清單中選擇服務匯流排命名空間。

1. 在 **"服務匯流排命名空間"** 窗格中，選擇 **" 佇列**"。

1. 輸入佇列的名稱，然後選擇 **"創建**"。 成功創建佇列後，"**創建佇列**"窗格將關閉。

   ![在 Azure 入口網站中新增服務匯流排佇列](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. 返回"**服務匯流排命名空間"** 窗格，在 **"實體"** 下，選擇 **"佇列**"。 從清單中打開服務匯流排佇列，然後選擇**共用訪問策略** > **+ 添加**。

1. 輸入策略的名稱，選中 **"管理**"，然後選擇 **"創建**"。

   ![在 Azure 門戶中添加服務匯流排佇列策略](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>向 IoT 中心添加自訂終結點和路由規則

將服務匯流排佇列的自訂終結點添加到 IoT 中心，並創建消息路由規則，以指示包含溫度警報的消息到該終結點，邏輯應用將在該終結點處選取這些消息。 路由規則使用路由查詢`temperatureAlert = "true"`，根據設備上運行的用戶端代碼設置`temperatureAlert`的應用程式屬性的值轉發消息。 要瞭解更多資訊，請參閱[基於消息屬性的消息路由查詢](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)。

### <a name="add-a-custom-endpoint"></a>添加自訂終結點

1. 開啟 IoT 中樞。 到達 IoT 中心最簡單的方法是從資源窗格中選擇**資源組**，選擇資源組，然後從資源清單中選擇 IoT 中心。

1. 在 **"消息"** 下，選擇**消息路由**。 在 **"消息路由**"窗格中，選擇 **"自訂終結點**"選項卡，然後選擇 **"添加**"。 從下拉清單中，選擇**服務匯流排佇列**。

   ![將端點新增至 Azure 入口網站的 IoT 中樞](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 在"**添加服務匯流排終結點**"窗格中，輸入以下資訊：

   **終結點名稱**：終結點的名稱。

   **服務匯流排命名空間**：選擇您創建的命名空間。

   **服務匯流排佇列**：選擇您創建的佇列。

   ![將端點新增至 Azure 入口網站的 IoT 中樞](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. 選取 [建立]****。 成功創建終結點後，繼續執行下一步。

### <a name="add-a-routing-rule"></a>新增路由規則

1. 返回"**消息路由**"窗格，選擇 **"路由"** 選項卡，然後選擇 **" 添加**"。

1. 在"**添加路由**"窗格中，輸入以下資訊：

   **名稱**：路由規則的名稱。

   **端點**：選取您建立的端點。

   **資料來源**：選擇**設備遙測消息**。

   **路由查詢**：`temperatureAlert = "true"`輸入 。

   ![在 Azure 入口網站中新增路由規格](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. 選取 [儲存]****。 您可以關閉**消息路由**窗格。

## <a name="create-and-configure-a-logic-app"></a>創建和配置邏輯應用

在前面一節中，您設置 IoT 中心，將包含溫度警報的消息路由到服務匯流排佇列。 現在，您設置一個邏輯應用來監視服務匯流排佇列，並在郵件添加到佇列時發送電子郵件通知。

### <a name="create-a-logic-app"></a>建立邏輯應用程式

1. 選擇 **"創建資源** > **集成** > **邏輯應用**"。

1. 輸入以下資訊：

   **名稱**︰ 邏輯應用程式的名稱。

   **資源組**：使用 IoT 中心使用的相同資源組。

   **位置**：使用 IoT 中樞使用的同一個位置。

   ![在 Azure 門戶中創建邏輯應用](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. 選取 [建立]****。

### <a name="configure-the-logic-app-trigger"></a>配置邏輯應用觸發器

1. 打開邏輯應用。 訪問邏輯應用的最簡單方法是從資源窗格中選擇**資源組**，選擇資源組，然後從資源清單中選擇邏輯應用。 選擇邏輯應用時，將打開邏輯應用設計器。

1. 在邏輯應用設計器中，向下滾動到**範本**並選擇**空白邏輯應用**。

   ![在 Azure 入口網站中，從空白的邏輯應用程式開始。](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. 選擇"**全部"** 選項卡，然後選擇 **"服務匯流排**"。

   ![選取服務匯流排，開始在 Azure 入口網站中建立邏輯應用程式](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 在**觸發器**下，選擇**一個或多個消息到達佇列中時（自動完成）。**

   ![在 Azure 門戶中選擇邏輯應用的觸發器](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. 建立服務匯流排連接。
   1. 輸入連接名稱並從清單中選擇服務匯流排命名空間。 下一個螢幕將打開。

      ![在 Azure 入口網站中建立邏輯應用程式的服務匯流排連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. 選擇服務匯流排策略（根管理共用訪問金鑰）。 然後選擇 **"創建**"。

      ![在 Azure 入口網站中建立邏輯應用程式的服務匯流排連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 在最後螢幕上，對於**佇列名稱**，從下拉清單中選擇創建的佇列。 輸入`175`**"最大消息計數**"。

      ![對於邏輯應用程式中的服務匯流排連接指定最大訊息計數](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 在邏輯應用設計器頂部的功能表中選擇 **"保存**"以保存更改。

### <a name="configure-the-logic-app-action"></a>配置邏輯應用操作

1. 建立 SMTP 服務連接。

   1. 選擇 **"新步驟**"。 在 **"選擇操作"中**，選擇"**全部**"選項卡。

   1. 在`smtp`搜索框中鍵入，在搜尋結果中選擇**SMTP**服務，然後選擇 **"發送電子郵件**"。

      ![在 Azure 入口網站的邏輯應用程式中建立 SMTP 連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 輸入郵箱的 SMTP 資訊，然後選擇 **"創建**"。

      ![在 Azure 入口網站的邏輯應用程式中輸入 SMTP 連接資訊](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      取得 [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en) 和 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) 的 SMTP 資訊。

      > [!NOTE]
      > 您可能需要禁用 SSL 才能建立連接。 如果是這種情況，並且希望在建立連接後重新啟用 SSL，請參閱本節末尾的可選步驟。

   1. 從 **"發送電子郵件**"步驟上的 **"添加新參數**"下拉清單，選擇 **"從**"**到**"**主題**和**正文**。 按一下或點擊螢幕上的任意位置以關閉選擇框。

      ![選擇 SMTP 連接電子郵件欄位](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. 輸入**寄件者**和**收件者**的電子郵件地址，並對於**主旨**和**內文**輸入 `High temperature detected`。 如果打開 **"添加來自此流對話方塊中使用的應用和連接器中的動態內容**"，請選擇 **"隱藏"** 以將其關閉。 在本教程中不使用動態內容。

      ![填寫 SMTP 連接電子郵件欄位](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. 選擇 **"保存**"以保存 SMTP 連接。

1. （可選）如果您必須禁用 SSL 才能與電子郵件供應商建立連接，並希望重新啟用它，請按照以下步驟操作：

   1. 在 **"邏輯應用**"窗格中，在 **"開發工具**"下，選擇**API 連接**。

   1. 從 API 連接清單中，選擇 SMTP 連接。

   1. 在**smtp API 連接**窗格中，在 **"常規**"下，選擇 **"編輯 API 連接**"。

   1. 在 **"編輯 API 連接"** 窗格中，選擇 **"啟用 SSL？"**，重新輸入電子郵件帳戶的密碼，然後選擇"**保存**"。

      ![在 Azure 門戶中的邏輯應用中編輯 SMTP API 連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

邏輯應用現在可以處理來自服務匯流排佇列的溫度警報並將通知發送到電子郵件帳戶。

## <a name="test-the-logic-app"></a>測試邏輯應用程式

1. 在設備上啟動用戶端應用程式。

1. 如果您使用的是物理設備，請小心地將熱源靠近熱感應器，直到溫度超過 30 攝氏度。如果使用連線模擬器，用戶端代碼將隨機輸出超過 30 C 的遙測消息。

1. 您應該開始接收邏輯應用發送的電子郵件通知。

   > [!NOTE]
   > 您的電子郵件服務提供者可能需要驗證寄件者身分，確定是您傳送電子郵件。

## <a name="next-steps"></a>後續步驟

您已成功建立連接 IoT 中樞的邏輯應用程式，以及溫度監視和通知的信箱。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
