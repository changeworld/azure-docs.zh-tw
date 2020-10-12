---
title: Azure 平臺記錄總覽 |Microsoft Docs
description: 概要說明 Azure 監視器中的記錄，提供有關 Azure 資源作業的豐富、經常性資料。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ea960e93dba634573ec1ef594f1d2c49be57ca9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84945302"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平台記錄概觀
平臺記錄可針對 Azure 資源及其相依的 Azure 平臺提供詳細的診斷和審核資訊。 雖然您需要設定特定的平臺記錄，以將某些平臺記錄轉送到要保留的一或多個目的地，但它們會自動產生。 本文概述平臺記錄，包括它們所提供的資訊，以及如何設定它們以進行收集和分析。

## <a name="types-of-platform-logs"></a>平臺記錄類型
下表列出不同 Azure 層級可用的特定平臺記錄。

| Log | 層 | 描述 |
|:---|:---|:---|
| [資源記錄](resource-logs.md) | Azure 資源 | 提供在 Azure 資源內執行之作業的深入解析， (*資料平面*) ，例如從 Key Vault 取得秘密或對資料庫提出要求。 資源記錄的內容會依 Azure 服務和資源類型而有所不同。<br><br>*資源記錄之前稱為診斷記錄。*  |
| [活動記錄檔](activity-log.md) | Azure 訂用帳戶 | 除了服務健康狀態事件的更新之外，還可讓您深入瞭解訂用帳戶中的每個 Azure 資源在 (*管理平面*) 的作業。 使用活動記錄檔來判斷任何寫入作業的_內容_ _、物件和__時間_ (PUT、POST、DELETE) 在訂用帳戶中的資源上進行。 每個 Azure 訂用帳戶都有單一的活動記錄。 |
| [Azure Active Directory 記錄檔](../../active-directory/reports-monitoring/overview-reports.md) | Azure 租用戶 |  包含登入活動的歷程記錄，以及在特定租使用者的 Azure Active Directory 中所做變更的審核記錄。   |

> [!NOTE]
> Azure 活動記錄主要是針對 Azure Resource Manager 中發生的活動。 此記錄不會追蹤使用傳統/RDFE 模型的資源。 某些傳統資源類型在 Azure Resource Manager 中有 Proxy 資源提供者 (例如，Microsoft.ClassicCompute)。 如果您透過使用這些 Proxy 資源提供者的 Azure Resource Manager 來與傳統資源類型互動，則作業會顯示在活動記錄。 如果您在 Azure Resource Manager Proxy 之外與傳統資源類型互動，您的動作將只會記錄於「作業記錄」。 可在入口網站的個別區段中，瀏覽作業記錄。

![平台記錄概觀](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>查看平臺記錄
有不同的選項可供您用來查看和分析不同的 Azure 平臺記錄。

- 查看 Azure 入口網站中的活動記錄，並從 PowerShell 和 CLI 存取事件。 請參閱 [查看活動記錄](activity-log.md#view-the-activity-log) 檔以取得詳細資料。 
- 在 Azure 入口網站中查看 Azure Active Directory 的安全性和活動報表。 請參閱 [什麼是 Azure Active Directory 報表？](../../active-directory/reports-monitoring/overview-reports.md)  。
- 資源記錄會由支援的 Azure 資源自動產生，但除非您將其傳送至 [目的地](#destinations)，否則無法加以查看。 

## <a name="destinations"></a>Destinations
您可以根據您的監視需求，將平臺記錄傳送到下表中的一或多個目的地。 藉由 [建立診斷設定](diagnostic-settings.md)來設定平臺記錄檔的目的地。

| Destination | 描述 |
|:---|:---|
| Log Analytics 工作區 | 分析所有 Azure 資源的記錄，並充分利用可用來 [Azure 監視器記錄](data-platform-logs.md) 的所有功能，包括 [記錄查詢](../log-query/log-query-overview.md) 和 [記錄警示](alerts-log.md)。 將記錄查詢的結果釘選到 Azure 儀表板，或將它包含在活頁簿中作為互動式報表的一部分。 |  |
| 事件中樞 | 將平臺記錄資料傳送到 Azure 外部，例如協力廠商 SIEM 或自訂遙測平臺。
| Azure 儲存體 | 封存記錄以進行 audit 或 backup。 |

- 如需建立活動記錄檔或資源記錄檔之診斷設定的詳細資訊，請參閱 [建立診斷設定，以將平臺記錄和計量傳送至不同的目的地](diagnostic-settings.md)。 
- 如需建立 Azure Active Directory 記錄之診斷設定的詳細資訊，請參閱下列文章。
  - [整合 Azure AD 記錄與 Azure 監視器記錄](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [教學課程：將 Azure Active Directory 記錄串流到 Azure 事件中樞](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [教學課程：將 Azure AD 記錄封存至 Azure 儲存體帳戶](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>接下來的步驟

* [閱讀更多有關活動記錄的詳細資料](activity-log.md)
* [閱讀更多有關資源記錄的詳細資料](resource-logs.md)

