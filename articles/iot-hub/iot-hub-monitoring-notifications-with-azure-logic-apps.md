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
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680710"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>搭配連接 IoT 中樞和信箱的 Azure Logic Apps 進行 IoT 遠端監視和通知

![端對端圖表](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)可以協助您跨內部部署和雲端服務、一或多個企業，以及跨各種通訊協定來協調工作流程。 邏輯應用程式會以觸發程式為開頭，後面接著一個或多個可使用內建控制項排序的動作，例如條件和反覆運算器。 這種彈性讓 Logic Apps 適合 IoT 監控案例的 IoT 解決方案。 例如，來自 IoT 中樞端點之裝置的遙測資料抵達時，可以起始邏輯應用程式工作流程，以將 Azure 儲存體 blob 中的資料進行倉儲、傳送電子郵件警示以警告資料異常、排程技術人員造訪裝置是否報告失敗等等。

## <a name="what-you-learn"></a>您學到什麼

您學會如何建立連接 IoT 中樞和信箱的邏輯應用程式用於溫度監視和通知。

在您的裝置上執行的用戶端程式代碼 `temperatureAlert` 會在它傳送至 IoT 中樞的每個遙測訊息上，設定應用程式屬性。 當用戶端程式代碼偵測到高於 30 C 的溫度時，它會將此屬性設定為 `true` ; 否則，它會將屬性設定為 `false` 。

抵達 IoT 中樞的訊息看起來會像下面這樣，其中包含主體中的遙測資料，以及 `temperatureAlert` 應用程式屬性中包含的屬性（系統屬性不會顯示）：

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

若要深入瞭解 IoT 中樞訊息格式，請參閱[建立和讀取 IoT 中樞訊息](iot-hub-devguide-messages-construct.md)。

在本主題中，您會在 IoT 中樞上設定路由，以將屬性為的訊息傳送 `temperatureAlert` `true` 至服務匯流排端點。 接著，您會設定一個邏輯應用程式，在抵達服務匯流排端點的訊息上觸發，並傳送電子郵件通知給您。

## <a name="what-you-do"></a>您要做什麼

* 建立服務匯流排命名空間，並在其中新增服務匯流排佇列。
* 將自訂端點和路由規則新增至您的 IoT 中樞，以將包含溫度警示的訊息路由至服務匯流排佇列。
* 建立、設定及測試邏輯應用程式，以取用來自服務匯流排佇列的訊息，並將通知電子郵件傳送給想要的收件者。

## <a name="what-you-need"></a>您需要什麼

* 完成 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教學課程，或其中一個裝置教學課程；例如，[Raspberry Pi with node.js](iot-hub-raspberry-pi-kit-node-get-started.md)。 其中涵蓋下列需求：

  * 有效的 Azure 訂用帳戶。
  * 位於您訂用帳戶中的 Azure IoT 中樞。
  * 在您的裝置上執行的用戶端應用程式，可將遙測訊息傳送至您的 Azure IoT 中樞。

## <a name="create-service-bus-namespace-and-queue"></a>建立服務匯流排命名空間和佇列

建立服務匯流排命名空間與佇列。 稍後在本主題中，您會在 IoT 中樞中建立路由規則，以將包含溫度警示的訊息導向至服務匯流排佇列，邏輯應用程式會在其中挑選它們，並觸發它來傳送通知電子郵件。

### <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間

1. 在 [ [Azure 入口網站](https://portal.azure.com/)上，選取 [ **+ 建立資源**  >  **整合**]  >  **服務匯流排**。

1. 在 [**建立命名空間**] 窗格中，提供下列資訊：

   **名稱**：服務匯流排命名空間的名稱。 命名空間在整個 Azure 中必須是唯一的。

   **定價層**：從下拉式清單中選取 [**基本**]。 「基本」層足供本教學課程之用。

   **資源群組**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用 IoT 中樞使用的同一個位置。

   ![在 Azure 入口網站中建立服務匯流排命名空間](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. 選取 [建立]。 等待部署完成，再繼續進行下一個步驟。

### <a name="add-a-service-bus-queue-to-the-namespace"></a>將服務匯流排佇列新增至命名空間

1. 開啟服務匯流排命名空間。 若要取得服務匯流排命名空間，最簡單的方法是從 [資源] 窗格中選取 [**資源群組**]，選取您的資源群組，然後從資源清單中選取 [服務匯流排命名空間]。

1. 在 [**服務匯流排命名空間**] 窗格中，選取 [ **+ 佇列**]。

1. 輸入佇列的名稱，然後選取 [**建立**]。 成功建立佇列時，會關閉 [**建立佇列**] 窗格。

   ![在 Azure 入口網站中新增服務匯流排佇列](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. 回到 [**服務匯流排命名空間**] 窗格的 [**實體**] 底下，選取 [**佇列**]。 從清單中開啟 [服務匯流排] 佇列，然後選取 [**共用存取原則**]  >  **+ [新增**]。

1. 輸入原則的名稱，勾選 [**管理**]，然後選取 [**建立**]。

   ![在 Azure 入口網站中新增服務匯流排佇列原則](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>將自訂端點和路由規則新增至您的 IoT 中樞

將服務匯流排佇列的自訂端點新增至您的 IoT 中樞，並建立訊息路由規則，將包含溫度警示的訊息導向至該端點，您的邏輯應用程式將會在其中挑選它們。 路由規則會使用路由查詢， `temperatureAlert = "true"` 根據裝置上執行 `temperatureAlert` 的用戶端程式代碼所設定的 application 屬性值來轉送訊息。 若要深入瞭解，請參閱以[訊息屬性為基礎的訊息路由查詢](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)。

### <a name="add-a-custom-endpoint"></a>新增自訂端點

1. 開啟 IoT 中樞。 進入 IoT 中樞最簡單的方式是從 [資源] 窗格中選取 [**資源群組**]，選取您的資源群組，然後從資源清單中選取您的 IoT 中樞。

1. 在 [**訊息**] 底下，選取 [**訊息路由**]。 在 [**訊息路由**] 窗格中，選取 [**自訂端點**] 索引標籤，然後選取 [ **+ 新增**]。 從下拉式清單中選取 [**服務匯流排佇列**]。

   ![將端點新增至 Azure 入口網站的 IoT 中樞](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 在 [**新增服務匯流排端點**] 窗格中，輸入下列資訊：

   **端點名稱**：端點的名稱。

   **服務匯流排命名空間**：選取您建立的命名空間。

   **服務匯流排佇列**：選取您建立的佇列。

   ![將端點新增至 Azure 入口網站的 IoT 中樞](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. 選取 [建立]。 成功建立端點之後，請繼續進行下一個步驟。

### <a name="add-a-routing-rule"></a>新增路由規則

1. 回到 [**訊息路由**] 窗格，選取 [**路由**] 索引標籤，然後選取 [ **+ 新增**]。

1. 在 [**新增路由**] 窗格中，輸入下列資訊：

   **名稱**：路由規則的名稱。

   **端點**：選取您建立的端點。

   **資料來源**：選取 [**裝置遙測訊息**]。

   **路由查詢**：輸入 `temperatureAlert = "true"` 。

   ![在 Azure 入口網站中新增路由規格](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. 選取 [儲存]。 您可以關閉 [**訊息路由**] 窗格。

## <a name="create-and-configure-a-logic-app"></a>建立和設定邏輯應用程式

在上一節中，您會設定 IoT 中樞，以將包含溫度警示的訊息路由至您的服務匯流排佇列。 現在，您可以設定邏輯應用程式來監視服務匯流排佇列，並在每次將訊息新增至佇列時傳送電子郵件通知。

### <a name="create-a-logic-app"></a>建立邏輯應用程式

1. 選取 [**建立資源**  >  **整合**  >  **邏輯應用程式**]。

1. 輸入以下資訊：

   **名稱**︰ 邏輯應用程式的名稱。

   **資源群組**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用 IoT 中樞使用的同一個位置。

   ![在 Azure 入口網站中建立邏輯應用程式](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. 選取 [建立]。

### <a name="configure-the-logic-app-trigger"></a>設定邏輯應用程式觸發程式

1. 開啟邏輯應用程式。 取得邏輯應用程式最簡單的方式是從 [資源] 窗格中選取 [**資源群組**]，選取您的資源群組，然後從資源清單中選取您的邏輯應用程式。 當您選取邏輯應用程式時，Logic Apps 設計工具隨即開啟。

1. 在 Logic Apps 設計工具中，向下流覽至 [**範本**]，然後選取 [**空白邏輯應用程式**]。

   ![在 Azure 入口網站中，從空白的邏輯應用程式開始。](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. 選取 [**全部**] 索引標籤，然後選取 [**服務匯流排**]。

   ![選取服務匯流排，開始在 Azure 入口網站中建立邏輯應用程式](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 在 [**觸發**程式] 底下，選取**一或多個訊息抵達佇列時（自動完成）**。

   ![在 [Azure 入口網站中選取邏輯應用程式的觸發程式](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. 建立服務匯流排連接。
   1. 輸入連接名稱，並從清單中選取您的服務匯流排命名空間。 下一個畫面隨即開啟。

      ![在 Azure 入口網站中建立邏輯應用程式的服務匯流排連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. 選取 [服務匯流排原則] （RootManageSharedAccessKey）。 然後選取 [**建立**]。

      ![在 Azure 入口網站中建立邏輯應用程式的服務匯流排連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 在最後一個畫面上，針對 [**佇列名稱**]，從下拉式選單選取您建立的佇列。 輸入 `175` 以取得**最大訊息計數**。

      ![對於邏輯應用程式中的服務匯流排連接指定最大訊息計數](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 在 Logic Apps 設計工具頂端的功能表上選取 [**儲存**]，以儲存您的變更。

### <a name="configure-the-logic-app-action"></a>設定邏輯應用程式動作

1. 建立 SMTP 服務連接。

   1. 選取 [新增步驟]。 在 **[選擇動作**] 中，選取 [**全部**] 索引標籤。

   1. 在搜尋方塊中輸入 `smtp` ，在搜尋結果中選取**SMTP**服務，然後選取 [**傳送電子郵件**]。

      ![在 Azure 入口網站的邏輯應用程式中建立 SMTP 連接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 輸入您信箱的 SMTP 資訊，然後選取 [**建立**]。

      ![在 Azure 入口網站的邏輯應用程式中輸入 SMTP 連接資訊](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      取得 [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en) 和 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) 的 SMTP 資訊。

      > [!NOTE]
      > 您可能需要停用 TLS/SSL 以建立連線。 如果是這種情況，而您想要在建立連接之後重新啟用 TLS，請參閱本節結尾的選擇性步驟。

   1. 從 [**傳送電子郵件**] 步驟的 [**加入新的參數**] 下拉式選，選取 [**寄件者** **]、[** 主旨 **] 和 [** **主體**]。 按一下或點擊畫面上的任何位置，以關閉選取方塊。

      ![選擇 SMTP 連接電子郵件欄位](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. 輸入**寄件者**和**收件者**的電子郵件地址，並對於**主旨**和**內文**輸入 `High temperature detected`。 如果 [**從此流程中使用的應用程式和連接器新增動態內容**] 對話方塊隨即開啟，請選取 [**隱藏**] 將其關閉。 在本教學課程中，您不會使用動態內容。

      ![填寫 SMTP 連接電子郵件欄位](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. 選取 [**儲存**] 以儲存 SMTP 連接。

1. 選擇性如果您必須停用 TLS 來建立與電子郵件提供者的連線，而且想要重新啟用它，請遵循下列步驟：

   1. 在 [**邏輯應用程式**] 窗格的 [**開發工具**] 底下，選取 [ **API**連線]。

   1. 從 [API 連線] 清單中，選取 [SMTP] 連接。

   1. 在 [ **SMTP API**連線] 窗格的 **[一般**] 底下，選取 [**編輯 API**連線]。

   1. 在 [**編輯 API**連線] 窗格中，選取 [**啟用 SSL？**]，重新輸入電子郵件帳戶的密碼，然後選取 [**儲存**]。

      ![在 Azure 入口網站的邏輯應用程式中編輯 SMTP API 連線](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

您的邏輯應用程式現在已準備好處理來自服務匯流排佇列的溫度警示，並將通知傳送至您的電子郵件帳戶。

## <a name="test-the-logic-app"></a>測試邏輯應用程式

1. 在您的裝置上啟動用戶端應用程式。

1. 如果您使用實體裝置，請小心將熱來源帶入熱感應器附近，直到溫度超過30° C。如果您使用的是線上模擬器，用戶端程式代碼會隨機輸出超過 30 C 的遙測訊息。

1. 您應該開始接收邏輯應用程式所傳送的電子郵件通知。

   > [!NOTE]
   > 您的電子郵件服務提供者可能需要驗證寄件者身分，確定是您傳送電子郵件。

## <a name="next-steps"></a>後續步驟

您已成功建立連接 IoT 中樞的邏輯應用程式，以及溫度監視和通知的信箱。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
