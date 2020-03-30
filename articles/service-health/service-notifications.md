---
title: 使用 Azure 入口網站檢視服務健康情況通知
description: 藉由服務健康情況通知，您可以檢視由 Microsoft Azure 發佈的服務健康情況訊息。
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 21416edc95d345eb183030a9b0cccb7529305d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748646"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站檢視服務健康情況通知

服務運行狀況通知由 Azure 基礎結構發佈到[Azure 活動日誌](../azure-monitor/platform/platform-logs-overview.md)中。  通知包含有關訂閱下資源的資訊。 鑒於活動日誌中存儲的資訊量可能很大，因此有一個單獨的使用者介面，以便更輕鬆地查看和設置服務運行狀況通知上的警報。 

根據不同類別，服務健康情況通知可以是告知性質或是可操作的性質。

有關各種服務運行狀況通知類別的詳細資訊，請參閱[服務運行狀況通知屬性](service-health-notifications-properties.md)。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健康情況通知

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [監視]****。

    ![Azure 入口網站功能表的螢幕擷取畫面與選取的監視器](./media/service-notifications/home-monitor.png)

    Azure 監視器會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔] **** 區段。

1. 選取 [警示] ****。

    ![監視器活動記錄的螢幕擷取畫面與選取的警示](./media/service-notifications/service-health-summary.png)

1. 選取 [+新增活動記錄警示]**** 並設定警示，以確保您會在未來收到服務通知。 如需詳細資訊，請參閱[建立服務通知的活動記錄警示](../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md)。
