---
title: 使用 Azure 入口網站接收 Azure 服務通知的活動記錄警示
description: 在 Azure 服務發生時透過 SMS、電子郵件或 Webhook 獲得通知。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a8723698cddfb519687525820475517b93219a4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567299"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>使用 Azure 入口網站在服務通知上建立活動記錄警示
## <a name="overview"></a>總覽

本文說明如何使用 Azure 入口網站來設定服務健康狀態通知的活動記錄警示，方法是使用 Azure 入口網站。  

服務健康狀態通知會儲存在[Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)檔中，因為活動記錄中可能有大量的資訊，所以有個別的使用者介面，可讓您更輕鬆地查看及設定服務健康狀態通知的警示。 

您可以在 Azure 傳送服務健康狀態通知到您的 Azure 訂用帳戶時接收警示。 您可以針對下列設定警示：

- 服務健康情況通知的類別 (服務問題、計劃性維護或健康情況摘要報告)。
- 受影響的訂閱。
- 受影響的服務。
- 受影響的區域。

> [!NOTE]
> 服務健康情況通知不會傳送關於資源健康情況事件的警示。

您也可以設定應傳送警示的對象：

- 選取現有的動作群組。
- 建立新動作群組 (可用於未來的警示)。

若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

若要了解如何使用 Azure Resource Manager 範本來設定服務健康情況通知警示，請參閱 [Resource Manager 範本](../azure-monitor/platform/alerts-activity-log.md)。

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>觀看有關設定第一個 Azure 服務健康狀態警示的影片

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>使用 Azure 入口網站來建立警示與新動作群組
1. 在[入口網站](https://portal.azure.com)中，選取 [服務健康情況]****。

    ![「服務健康情況」服務](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. 在 [警示]**** 區段中，選取 [健康情況警示]****。

    ![[健康情況警示] 索引標籤](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. 選取 [建立服務健康情況警示]**** 並填入欄位。

    ![「建立服務健康情況警示」命令](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. 選取您想要警示的**訂用帳戶**、**服務**和**區域**。

    ![「新增活動記錄警示」對話方塊](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > 此訂用帳戶會用來儲存活動記錄警示。 警示資源會部署到此訂用帳戶，並從中監視活動記錄事件。

1. 選擇您想要警示的**事件類型**：*服務問題*、*計劃性維護*和*健康情況諮詢* 

1. 輸入**警示規則名稱**和**描述**，定義您的警示詳細資料。

1. 選取要儲存警示的**資源群組**。

1. 選取**新的動作群組**以建立新的動作群組。 在 [**動作組名**] 方塊中輸入名稱，然後在 [**簡短名稱**] 方塊中輸入名稱。 當此警示發動時，通知中會引用該簡短名稱。

    ![建立新的動作群組](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. 請提供接收者的下列各項，以定義接收者的清單：

    a. **名稱**：輸入接收者的名稱、別名或識別碼。

    b. **動作類型**：選取簡訊、電子郵件、Webhook 和 Azure 應用程式等。

    c. **詳細資料**：根據選擇的動作類型，輸入電話號碼、電子郵件地址或 Webhook URI 等。

1. 選取 [確定]**** 建立動作群組，然後**建立警示規則**完成您的警示。

在幾分鐘之內會啟用警示，開始根據建立時所指定的條件觸發警示。

了解如何[設定現有問題管理系統的 Webhook 通知](service-health-alert-webhook-guide.md)。 如需活動記錄警示之 Webhook 結構描述的資訊，請參閱 [Azure 活動記錄警示的 Webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)。

>[!NOTE]
>在這些步驟中定義的動作群組，會以現有動作群組的形式提供給所有未來的警示定義重複使用。
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>使用 Azure 入口網站以現有的動作群組建立警示

1. 請遵循上一節中的步驟1到6，以建立您的服務健康情況通知。 

1. 在 [定義動作群組]**** 下，按一下 [選取動作群組]**** 按鈕。 選取適當的動作群組。

1. 選取 [新增]**** 新增動作群組，然後**建立警示規則**完成您的警示。

在幾分鐘之內會啟用警示，開始根據建立時所指定的條件觸發警示。


## <a name="next-steps"></a>後續步驟
- 瞭解[設定 Azure 服務健康狀態警示的最佳做法](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)。
- 瞭解如何[設定 Azure 服務健康狀態的行動推播通知](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)。
- 瞭解如何[設定現有問題管理系統的 webhook 通知](service-health-alert-webhook-guide.md)。
- 深入了解[服務健康狀態通知](service-notifications.md)。
- 瞭解[通知速率限制](../azure-monitor/platform/alerts-rate-limiting.md)。
- 檢查[活動記錄警示 webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 取得[活動記錄警示的概觀](../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
