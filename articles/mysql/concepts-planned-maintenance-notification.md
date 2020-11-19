---
title: 規劃的維護通知-適用於 MySQL 的 Azure 資料庫-單一伺服器
description: 本文說明適用於 MySQL 的 Azure 資料庫-單一伺服器中的規劃維護通知功能
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: ff197f8add65782a594d64661ffecdaced4598c2
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919619"
---
# <a name="planned-maintenance-notification-in-azure-database-for-mysql---single-server"></a>適用於 MySQL 的 Azure 資料庫-單一伺服器中規劃的維護通知

瞭解如何在您的適用於 MySQL 的 Azure 資料庫上準備預定的維修事件。

## <a name="what-is-a-planned-maintenance"></a>什麼是規劃的維護？

適用於 MySQL 的 Azure 資料庫服務會執行基礎硬體、OS 和資料庫引擎的自動修補。 修補套裝程式括新的服務功能、安全性和軟體更新。 針對 MySQL 引擎，次要版本升級會自動進行，並包含在修補週期中。 不需要任何使用者動作或組態設定即可進行修補。 修補程式經過廣泛測試，並使用安全的部署實務來推出。

規劃的維護是將這些服務更新部署到指定的 Azure 區域中的伺服器時的維護時段。 在計劃性維護期間，系統會建立通知事件，以在裝載其伺服器的 Azure 區域中部署服務更新時，通知客戶。 兩個預定維護之間的最短持續時間為30天。 您會在下一次維護視窗前 72 小時收到通知。

## <a name="planned-maintenance---duration-and-customer-impact"></a>預定進行的維護-持續時間和客戶影響

針對指定的 Azure 區域進行預定的維護通常預期會執行15小時。 如果有需要，此視窗也包含可執行復原計畫的緩衝區時間。 在規劃的維護期間，可能會有資料庫伺服器重新開機或容錯移轉，這可能會導致使用者短暫無法使用資料庫伺服器。 適用於 MySQL 的 Azure 資料庫伺服器正在容器中執行，因此資料庫伺服器重新開機通常會快速完成，通常會在60-120 秒內完成。 整個規劃的維護事件（包括每個伺服器重新開機）都會由工程團隊進行審慎的監控。 伺服器容錯移轉時間取決於資料庫復原時間，如果您在容錯移轉時，伺服器上有繁重的交易活動，可能會導致資料庫上線。 若要避免較長的重新開機時間，建議您在規劃的維護事件期間，避免任何長時間執行的交易 (大量載入) 。

總而言之，雖然規劃的維護事件會執行15小時，但是根據伺服器上的交易活動，個別伺服器的影響通常會持續60秒。 在規劃的維護開始前，會傳送一則通知到72的行事歷時數，另一個則是針對指定區域進行維護。

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>如何獲得預定維護的通知？

您可以利用規劃的維護通知功能，來接收即將進行之預定維修活動的警示。 您將會收到通知，告知您即將在活動之前的維護72行事歷時數，另一個則是針對指定區域進行維護。

### <a name="planned-maintenance-notification"></a>規劃的維護通知

> [!IMPORTANT]
> 規劃的維護通知目前在所有區域都可供預覽， **但** 美國中西部

**規劃的維護通知** 可讓您接收即將進行的規劃維護事件的警示給您的適用於 MySQL 的 Azure 資料庫。 這些通知會與 [服務健康狀態的](../service-health/overview.md) 規劃維護整合，並可讓您在單一位置查看訂用帳戶的所有排程維護。 它也有助於將通知擴充至不同資源群組的適當物件，因為您可能會有不同的連絡人負責不同的資源。 您將會在活動之前，收到即將進行之維護72行事曆小時的通知。

我們會每次嘗試針對所有事件提供 **預定的維護通知** 72 小時通知。 不過，在重大或安全性修補程式的情況下，可能會在較接近事件的情況下傳送通知，或將其省略。

您可以在 Azure 入口網站上查看預定的維護通知，或設定警示來接收通知。 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>從 Azure 入口網站檢查預定的維修通知

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取 [ **服務健康狀態**]。
2. 選取 **規劃的維護** 索引標籤
3. 選取您要檢查預定維護通知的 **訂** 用帳戶、 **區域** 及 **服務** 。 
   
### <a name="to-receive-planned-maintenance-notification"></a>若要接收預定的維修通知

1. 在[入口網站](https://portal.azure.com)中，選取 [服務健康情況]。
2. 在 [警示] 區段中，選取 [健康情況警示]。
3. 選取 [ **+ 新增服務健康情況警示** ]，然後填入欄位。
4. 填寫必要的欄位。 
5. 選擇 [**事件種類**]，選取 [**預定維修**] 或 [全 **選**]
6. 在 [ **動作群組** ] 中，定義您想要如何接收警示 (取得電子郵件、觸發邏輯應用程式等 )   
7. 確認 [建立時啟用規則] 設為 [是]。
8. 選取 [ **建立警示規則** ] 以完成您的警示

如需有關如何建立 **服務健康狀態警示** 的詳細步驟，請參閱 [建立服務通知的活動記錄警示](../service-health/alerts-activity-log-service-notifications-portal.md)。

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>我可以取消或延後預定的維護嗎？

需要維護，才能讓您的伺服器保持安全、穩定且最新。 規劃的維護事件無法取消或延後。 一旦通知傳送至指定的 Azure 區域，就無法針對該區域中的任何個別伺服器進行修補排程變更。 修補程式會一次針對整個區域推出。 適用於 MySQL 的 Azure 資料庫-單一伺服器服務是針對雲端原生應用程式所設計，不需要細微地控制或自訂服務。 如果您想要能夠為您的伺服器排程維護，建議您考慮有彈性的 [伺服器](./flexible-server/overview.md)。

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>所有 Azure 區域都會同時修補嗎？

否，所有的 Azure 區域都是在部署的時間範圍時間內進行修補。 部署的明智時段通常會在指定的 Azure 區域中，從下午5點（當地時間上午8點）開始延伸。 地理配對的 Azure 區域會在不同的日子修補。 針對資料庫伺服器的高可用性和商務持續性，建議利用 [跨區域讀取複本](./concepts-read-replicas.md#cross-region-replication) 。

## <a name="retry-logic"></a>重試邏輯

暫時性錯誤 (也稱為暫時性故障) 係指會自行解決的錯誤。 維護期間可能會發生[暫時性錯誤](./concepts-connectivity.md#transient-errors)。 這些事件大多數都會由系統在 60 秒內自動解決。 暫時性錯誤應使用 [重試邏輯](./concepts-connectivity.md#handling-transient-errors)來處理。


## <a name="next-steps"></a>後續步驟

- 如有關于使用適用於 MySQL 的 Azure 資料庫的任何問題或建議，請傳送電子郵件給適用於 MySQL 的 Azure 資料庫團隊，網址為： AskAzureDBforMySQL@service.microsoft.com
- 請參閱[如何設定警示](howto-alert-on-metric.md)，取得根據計量來建立警示的指引。
- [針對適用於 MySQL 的 Azure 資料庫的連接問題進行疑難排解-單一伺服器](howto-troubleshoot-common-connection-issues.md)
- [處理暫時性錯誤並有效率地連接到適用於 MySQL 的 Azure 資料庫-單一伺服器](concepts-connectivity.md)
