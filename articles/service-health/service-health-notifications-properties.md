---
title: 什麼是 Azure 服務健康情況通知？
description: 藉由服務健康情況通知，您可以檢視由 Microsoft Azure 發佈的服務健康情況訊息。
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77653963"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 入口網站檢視服務健康情況通知

Azure 會發佈服務健康情況通知，其中包含您訂用帳戶下資源的相關資訊。 這些通知是活動記錄事件的子類別，在活動記錄上也找得到。 根據不同類別，服務健康情況通知可以是告知性質或是可操作的性質。

服務健康情況通知有多種類別：  

- **需採取動作：** Azure 可能注意到您的帳戶中發生異常狀況，並協助您解決這個問題。 Azure 會傳送通知給您，其中會詳述您需要採取的動作，或 Azure 工程或支援的連絡方式。  
- **附帶事件 (Incident)︰** 影響服務的事件 (event) 目前正在影響您訂用帳戶下一個或多個資源。  
- **維護︰** 可能會影響您訂用帳戶下一個或多個資源的計劃性維護活動。  
- **資訊：** 潛在的最佳化作業，可能有助於提升資源使用效率。 
- **安全性︰** 緊急的安全性相關資訊，關係到 Azure 上執行的解決方案。

每個服務健康狀態通知皆會包含關於範圍以及資源所受影響的詳細資料。 詳細資料包括：

屬性名稱 | 描述
-------- | -----------
通道 | 為下列其中一個值：**Admin** 或 **Operation**。
correlationId | 通常是字串格式的 GUID。 屬於相同動作的事件通常會共用同一個 correlationId。
eventDataId | 事件的唯一識別碼。
eventName | 事件的標題。
等級 | 事件的層級
resourceProviderName | 受影響資源的資源提供者名稱。
resourceType| 受影響資源的資源類型。
子狀態 | 通常是對應 REST 呼叫的 HTTP 狀態碼，但也可以包含其他描述子狀態的字串。 例如：確定 (HTTP 狀態碼︰200)，已建立 (HTTP 狀態碼︰201)、接受 (HTTP 狀態碼︰202)、沒有內容 (HTTP 狀態碼︰204)、不正確的要求 (HTTP 狀態碼︰400)、找不到 (HTTP 狀態碼︰404)，衝突 (HTTP 狀態碼︰409)、內部伺服器錯誤 (HTTP 狀態碼︰500)、服務無法使用 (HTTP 狀態碼︰503) 和閘道逾時 (HTTP 狀態碼︰504)。
eventTimestamp | Azure 服務處理要求 (與事件相對應) 而產生事件時的時間戳記。
submissionTimestamp | 當事件變成可供查詢時的時間戳記。
subscriptionId | 記錄此事件的 Azure 訂用帳戶。
status | 字串，描述作業的狀態。 常見的值包括︰**Started**、**In Progress**、**Succeeded**、**Failed**、**Active** 和 **Resolved**。
operationName | 作業的名稱。
category | 此屬性一律為 **ServiceHealth**。
resourceId | 受影響資源的資源識別碼。
Properties.title | 此通訊的當地語系化標題。 預設語言為英文。
Properties.communication | 與 HTML 標記通訊的詳細資料 (已當地語系化)。 預設語言為英文。
Properties.incidentType | 下列其中一個值： **ActionRequired**、**資訊**、**事件**、**維護**或**安全性**。
Properties.trackingId | 與此事件 (event) 相關聯的附帶事件 (Incident)。 可用此屬性讓與附帶事件 (Incident) 有關的事件 (event) 相關聯。
Properties.impactedServices | 逸出的 JSON blob，描述受到附帶事件 (Incident) 影響的服務和區域。 屬性包含服務清單 (每一份都有 **ServiceName**) 和受影響區域清單 (每一份都有 **RegionName**)。
Properties.defaultLanguageTitle | 英文的通訊。
Properties.defaultLanguageContent | 英文的通訊，如 html 標記或純文字。
Properties.stage | **Incident** 和 **Security** 的可能值為 **Active、** **Resolved** 或 **RCA**。 針對 **ActionRequired** 或 **Informational**，唯一的值為 **Active**。 **維護**的**狀態**包括：使用中、已**規劃** **、執行中、****已取消** **、重新排程、****已解決或已****完成**。
Properties.communicationId | 與此事件相關聯的通訊。

### <a name="details-on-service-health-level-information"></a>服務健康情況層級資訊的詳細資料

**所需的動作** (properties.incidentType == ActionRequired)
- 資訊-需要系統管理員動作，以防止對現有服務的影響。
    
**維護** (properties.incidentType == Maintenance)
- 警告-緊急維護
- 資訊-標準規劃的維護

**資訊** (properties.incidentType == Information)
- 資訊-系統管理員可能需要防止對現有服務的影響。

**安全性** (properties.incidentType == Security)
- 警告-會影響現有服務並可能需要系統管理員動作的安全性諮詢。
- 資訊安全諮詢，會影響現有的服務。

**服務問題** (properties.incidentType == Incident)
- 錯誤 - 跨多個區域存取多個服務的廣泛問題會影響廣泛的一組客戶。
- 警告 - 存取特定服務及/或特定區域的問題會影響一部分客戶。
- 資訊 - 影響管理作業和/或延遲的問題，不會影響服務可用性。
