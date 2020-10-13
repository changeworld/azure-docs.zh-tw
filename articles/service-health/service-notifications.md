---
title: 使用 Azure 入口網站檢視服務健康情況通知
description: 在 Azure 入口網站中查看您的服務健康情況通知。 Azure 基礎結構會將服務健康狀態通知發佈至 Azure 活動記錄。
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 615d08b6a04aef9e8ef2033154da8ff8caeebe04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967767"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站檢視服務健康情況通知

Azure 基礎結構會將服務健康狀態通知發佈至 [azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)。  通知包含您訂用帳戶下資源的相關資訊。 假設活動記錄中儲存的資訊量可能很大，則有個別的使用者介面可讓您更輕鬆地查看及設定服務健康情況通知的警示。 

根據不同類別，服務健康情況通知可以是告知性質或是可操作的性質。

如需各種服務健康情況通知類別的詳細資訊，請參閱 [服務健康情況通知屬性](service-health-notifications-properties.md)。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 入口網站中檢視您的服務健康情況通知

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [ **監視**]。

    ![Azure 入口網站功能表的螢幕擷取畫面與選取的監視器](./media/service-notifications/home-monitor.png)

    Azure 監視器會將您所有的監視設定和資料結合成一個彙總檢視。 它會先開啟 [活動記錄檔] **** 區段。

1. 選取 [警示] 。

    ![監視器活動記錄的螢幕擷取畫面與選取的警示](./media/service-notifications/service-health-summary.png)

1. 選取 [+新增活動記錄警示]**** 並設定警示，以確保您會在未來收到服務通知。 如需詳細資訊，請參閱[建立服務通知的活動記錄警示](./alerts-activity-log-service-notifications-portal.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解[活動記錄警示](../azure-monitor/platform/activity-log-alerts.md)。
