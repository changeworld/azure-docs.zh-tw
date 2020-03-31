---
title: 使用 Webhook 使用 OpsGenie 發送 Azure 服務運行狀況警報
description: 取得關於 OpsGenie 執行個體服務健康情況事件的個人化通知。
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654132"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>使用 Webhook 使用 OpsGenie 發送 Azure 服務運行狀況警報

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
1. 遵循[使用 Azure 入口網站為新動作群組建立服務健康情況通知的警示](../azure-monitor/platform/alerts-activity-log-service-notifications.md)中的步驟 1 到步驟 8。

1. 在**動作**清單中定義：

    a. **動作類型：** *Webhook*

    b. **詳細資料：** 您先前儲存的 OpsGenie** 整合 URL**。

    c. **名稱：** 的名稱、別名或識別項。

1. 完成後選取 [儲存]**** 以建立警示。

### <a name="for-an-existing-action-group"></a>現有的動作群組：
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [監視]****。

1. 在 [設定]**** 區段上，選取 [動作群組]****。

1. 尋找並選取您要編輯的動作群組。

1. 新增至**動作**清單：

    a. **動作類型：** *Webhook*

    b. **詳細資料：** 您先前儲存的 OpsGenie** 整合 URL**。

    c. **名稱：** 的名稱、別名或識別項。

1. 完成後選取 [儲存]**** 來更新動作群組。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>透過 HTTP POST 要求測試 Webhook 整合
1. 建立您想要傳送的服務健康情況承載。 您可以在[Webhook 中為 Azure 活動日誌警報](../azure-monitor/platform/activity-log-alerts-webhook.md)找到一個示例服務運行狀況 Webhook 負載。

1. 建立 HTTP POST 要求，如下所示：

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. 您應該會收到 `200 OK` 回應和狀態為「成功」的訊息。

1. 移至 [OpsGenie](https://www.opsgenie.com/)，以確認您的整合已設定成功。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[為現有問題管理系統配置 Webhook 通知](service-health-alert-webhook-guide.md)。
- 查看[活動日誌警報 Webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 瞭解[服務運行狀況通知](../azure-monitor/platform/service-notifications.md)。
- 瞭解有關[操作組](../azure-monitor/platform/action-groups.md)的更多。