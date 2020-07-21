---
title: 使用 webhook 透過 OpsGenie 傳送 Azure 服務健康狀態警示
description: 取得關於 OpsGenie 執行個體服務健康情況事件的個人化通知。
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 112774cb1f9e16b08225471e8dbc1bb79b1bd37d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529092"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>使用 webhook 透過 OpsGenie 傳送 Azure 服務健康狀態警示

本文會示範如何使用 Webhook 來設定 Azure 服務健康情況警示與 OpsGenie。 透過使用 [OpsGenie](https://www.opsgenie.com/) 的 Azure 服務健康情況整合，您可以將 Azure 服務健康情況警示轉送給 OpsGenie。 OpsGenie 可以依據值勤排程來決定要通知的合適人員，其方式是使用電子郵件、簡訊 (SMS)、電話、iOS 和 Android 推播通知及擴大警示，直到警示被接受或關閉。

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>在 OpsGenie 中建立服務健康情況的整合 URL
1.  請確定您已註冊並登入您的 [OpsGenie](https://www.opsgenie.com/) 帳戶。

1.  瀏覽至 OpsGenie 中的**整合**區段。

    ![OpsGenie 中的「整合」區段](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  選取 [Azure 服務健康況] 整合按鈕****。

    ![OpsGenie 中的 [Azure 服務健康情況] 按鈕](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  為您的警示**命名**，並指定 [指派給小組]**** 欄位。

1.  填寫其他欄位，例如 [收件者]****、[已啟用]**** 和 [隱藏通知]****。

1.  複製並儲存**整合 URL**，其應該已包含您的 `apiKey` (附加在結尾)。

    ![OpsGenie 中的「整合 URL」](./media/webhook-alerts/opsgenie-integration-url.png)

1.  選取 [儲存整合]****

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>在 Azure 入口網站使用 OpsGenie 建立警示
### <a name="for-a-new-action-group"></a>新的動作群組：
1. 遵循[使用 Azure 入口網站為新動作群組建立服務健康情況通知的警示](./alerts-activity-log-service-notifications-portal.md)中的步驟 1 到步驟 8。

1. 在**動作**清單中定義：

    a. **動作類型：** *Webhook*

    b. **詳細資料：** 您先前儲存的 OpsGenie** 整合 URL**。

    c. **名稱：** 的名稱、別名或識別項。

1. 完成後選取 [儲存]**** 以建立警示。

### <a name="for-an-existing-action-group"></a>現有的動作群組：
1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取 [**監視**]。

1. 在 [設定]**** 區段上，選取 [動作群組]****。

1. 尋找並選取您要編輯的動作群組。

1. 新增至**動作**清單：

    a. **動作類型：** *Webhook*

    b. **詳細資料：** 您先前儲存的 OpsGenie** 整合 URL**。

    c. **名稱：** 的名稱、別名或識別項。

1. 完成後選取 [儲存]**** 來更新動作群組。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>透過 HTTP POST 要求測試 Webhook 整合
1. 建立您想要傳送的服務健康情況承載。 您可以在[Azure 活動記錄警示的 webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)找到範例服務健康情況 webhook 承載。

1. 建立 HTTP POST 要求，如下所示：

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. 您應該會收到 `200 OK` 回應和狀態為「成功」的訊息。

1. 移至 [OpsGenie](https://www.opsgenie.com/)，以確認您的整合已設定成功。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[設定現有問題管理系統的 webhook 通知](service-health-alert-webhook-guide.md)。
- 檢查[活動記錄警示 webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 深入了解[服務健康狀態通知](./service-notifications.md)。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
