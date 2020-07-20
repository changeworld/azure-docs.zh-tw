---
title: 使用 Azure 入口網站檢視服務健康情況通知
description: 藉由服務健康情況通知，您可以檢視由 Microsoft Azure 發佈的服務健康情況訊息。
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75748646"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站檢視服務健康情況通知

Azure 基礎結構會將服務健康狀態通知發佈到[azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中。  通知會包含您的訂用帳戶下的資源相關資訊。 由於活動記錄中可能會有大量的資訊，因此有個別的使用者介面，可讓您更輕鬆地查看及設定服務健康狀態通知的警示。 

根據不同類別，服務健康情況通知可以是告知性質或是可操作的性質。

如需服務健康狀態通知各種類別的詳細資訊，請參閱[服務健康情況通知屬性](service-health-notifications-properties.md)。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健康情況通知

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [**監視**]。

    ![Azure 入口網站功能表的螢幕擷取畫面與選取的監視器](./media/service-notifications/home-monitor.png)

    Azure 監視器會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔] **** 區段。

1. 選取 [警示] 。

    ![監視器活動記錄的螢幕擷取畫面與選取的警示](./media/service-notifications/service-health-summary.png)

1. 選取 [+新增活動記錄警示]**** 並設定警示，以確保您會在未來收到服務通知。 如需詳細資訊，請參閱[建立服務通知的活動記錄警示](../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md)。
