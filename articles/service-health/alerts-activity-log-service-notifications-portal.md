---
title: 使用 Azure 入口網站接收 Azure 服務通知的活動記錄警示
description: 在 Azure 服務發生時透過 SMS、電子郵件或 Webhook 獲得通知。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: b90940c4532370e7742f736708625ddec283aab1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499249"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>使用 Azure 入口網站在服務通知上建立活動記錄警示
## <a name="overview"></a>概觀

本文說明如何使用 Azure 入口網站來設定服務健康狀態通知的活動記錄警示，方法是使用 Azure 入口網站。  

服務健康狀態通知會儲存在[Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中。 由於活動記錄中儲存了大量的資訊，因此有個別的使用者介面，可讓您更輕鬆地查看及設定服務健康狀態通知的警示。 

您可以在 Azure 傳送服務健康狀態通知到您的 Azure 訂用帳戶時接收警示。 您可以針對下列設定警示：

- 服務健康情況通知的類別（服務問題、預定的維護、健康狀態諮詢、資訊安全諮詢）。
- 受影響的訂閱。
- 受影響的服務。
- 受影響的區域。

> [!NOTE]
> 服務健康情況通知不會傳送資源健康狀態事件的警示。

您也可以設定應傳送警示的對象：

- 選取現有的動作群組。
- 建立新動作群組 (可用於未來的警示)。

若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

若要了解如何使用 Azure Resource Manager 範本來設定服務健康情況通知警示，請參閱 [Resource Manager 範本](../azure-monitor/platform/alerts-activity-log.md)。

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>觀看有關設定第一個 Azure 服務健康狀態警示的影片

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>使用 Azure 入口網站建立服務健康狀態警示
1. 在[入口網站](https://portal.azure.com)中，選取 [服務健康情況]****。

    ![「服務健康情況」服務](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. 在 [警示]**** 區段中，選取 [健康情況警示]****。

    ![[健康情況警示] 索引標籤](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. 選取 [**新增服務健康狀態警示**]，並填寫欄位。

    ![「建立服務健康情況警示」命令](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. 選取您想要收到警示的**訂**用帳戶、**服務**和**區域**。

    [![「新增活動記錄警示」對話方塊](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>此訂用帳戶會用來儲存活動記錄警示。 警示資源會部署到此訂用帳戶，並從中監視活動記錄事件。

5. 選擇您想要收到警示的**事件種類**： [*服務問題*]、[*預定的維護*]、[*健康情況諮詢*] 和 [資訊*安全諮詢*]。

6. 按一下 [**選取動作群組**]，選擇現有的動作群組或建立新的動作群組。 如需動作群組的詳細資訊，請參閱在[Azure 入口網站中建立和管理動作群組](../azure-monitor/platform/action-groups.md)。


7. 輸入**警示規則名稱**和**描述**，定義您的警示詳細資料。

8. 選取要儲存警示的**資源群組**。



在幾分鐘之內會啟用警示，開始根據建立時所指定的條件觸發警示。

了解如何[設定現有問題管理系統的 Webhook 通知](service-health-alert-webhook-guide.md)。 如需活動記錄警示之 Webhook 結構描述的資訊，請參閱 [Azure 活動記錄警示的 Webhook](../azure-monitor/platform/activity-log-alerts-webhook.md)。


## <a name="next-steps"></a>後續步驟
- 瞭解[設定 Azure 服務健康狀態警示的最佳做法](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)。
- 瞭解如何[設定 Azure 服務健康狀態的行動推播通知](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)。
- 瞭解如何[設定現有問題管理系統的 webhook 通知](service-health-alert-webhook-guide.md)。
- 深入了解[服務健康狀態通知](service-notifications.md)。
- 瞭解[通知速率限制](../azure-monitor/platform/alerts-rate-limiting.md)。
- 檢查[活動記錄警示 webhook 架構](../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 取得[活動記錄警示的概觀](../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
