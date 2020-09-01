---
title: Azure 監視器中的 Azure Active Directory 活動記錄 |Microsoft Docs
description: Azure 監視器中的 Azure Active Directory 活動記錄簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4253fe52346890eaa993a18e8e9bc9b270bffd7
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229849"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure 監視器中的 Azure AD 活動記錄 \(部分機器翻譯\)

您可以將 Azure Active Directory (Azure AD) 活動記錄路由傳送到數個端點，以進行長期保留和資料深入解析。 此功能可讓您：

* 將 Azure AD 活動記錄封存到 Azure 儲存體帳戶，以長時間保存資料。
* 將 Azure AD 活動記錄串流至 Azure 事件中樞，以便使用 Splunk 和 QRadar 等常用的安全性資訊與事件管理 (SIEM) 工具進行分析。
* 將 Azure AD 活動記錄串流至事件中樞，將其與您自己的自訂記錄解決方案整合。
* 將 Azure AD 活動記錄傳送到 Azure 監視器記錄，以對已連線的資料啟用豐富的視覺效果、監視與警示功能。

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>支援的報告

您可以使用這項功能將 Azure AD 稽核記錄和登入記錄路由至您的 Azure 儲存體帳戶、事件中樞、Azure 監視器記錄或自訂解決方案。 

* **稽核記錄**：[稽核記錄活動報告](concept-audit-logs.md)可讓您對每個在租用戶中執行的工作存取歷程記錄。
* **登入記錄**：透過[登入活動報告](concept-sign-ins.md)，您可以判斷是誰執行了稽核記錄中所報告的工作。

> [!NOTE]
> 目前不支援與 B2C 相關的稽核和登入活動記錄。
>

## <a name="prerequisites"></a>先決條件

若要使用此功能，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以[註冊免費試用](https://azure.microsoft.com/free/)。
* Azure AD Free、Basic、Premium 1 或 Premium 2 [授權](https://azure.microsoft.com/pricing/details/active-directory/)，用以存取 Azure 入口網站中的 Azure AD 稽核記錄。 
* Azure AD 租用戶。
* 屬於 Azure AD 租使用者之 **全域管理員** 或 **安全性系統管理員** 的使用者。
* Azure AD Premium 1 或 Premium 2 [授權](https://azure.microsoft.com/pricing/details/active-directory/)，用以存取 Azure 入口網站中的 Azure AD 登入記錄。 

根據您要路由稽核記錄資料的位置，您必須要有下列任何一項：

* 您具有 *ListKeys* 權限的 Azure 儲存體帳戶。 建議您使用一般儲存體帳戶，而不要使用 Blob 儲存體帳戶。 如需儲存體價格資訊，請參閱 [Azure 儲存體價格計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)。 
* Azure 事件中樞命名空間，以便與第三方解決方案整合。
* 用來將記錄傳送至 Azure 監視器記錄的 Azure Log Analytics 工作區。

## <a name="cost-considerations"></a>成本考量

如果您已有 Azure AD 授權，您必須要有用來設定儲存體帳戶和事件中樞的 Azure 訂用帳戶。 Azure 訂用帳戶會免費提供，但是您必須付費才能使用 Azure 資源，包括用於封存的儲存體帳戶，和用於串流的事件中樞。 資料量和連帶產生的費用會隨著租用戶大小而有大幅差異。 

### <a name="storage-size-for-activity-logs"></a>適用於活動記錄的儲存體大小

每個稽核記錄事件都會使用約 2 KB 的資料儲存體。 登入事件記錄大約是 4 KB 的資料儲存體。 對具有 100,000 名使用者的租用戶而言，每天大約會產生 150 萬個事件，因此您每天需要約 3 GB 的資料儲存體。 由於寫入會以約 5 分鐘的批次執行，因此您可以預期每個月大概會有 9000 個寫入作業。 


下表包含以租用戶大小為準的估計成本，儲存體帳戶是位於美國西部的一般用途 v2 帳戶，保留期間至少一年。 若要針對您預期應用程式將使用的資料量建立更精確的估計值，請使用 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/details/storage/blobs/)。


| 記錄分類 | 使用者人數 | 每日事件數 | 每月資料量 (估計值) | 每月成本 (估計值) | 每年成本 (估計值) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| 稽核 | 100,000 | 150&nbsp;萬 | 90 GB | $1.93 | $23.12 |
| 稽核 | 1,000 | 15,000 | 900 MB | $0.02 | $0.24 |
| 登入 | 1,000 | 34,800 | 4 GB | $0.13 | $1.56 |
| 登入 | 100,000 | 1500&nbsp;萬 | 1.7 TB | $35.41 | $424.92 |
 









### <a name="event-hub-messages-for-activity-logs"></a>活動記錄的事件中樞訊息

事件會以約五分鐘的間隔分批處理，並以包含該時間範圍內所有事件的單一訊息形式傳送。 事件中樞內的訊息大小上限為 256 KB，如果在時間範圍內所有訊息的總大小超過該數量，就會傳送多個訊息。 

例如，使用者人數超過 100,000 名的大型租用戶每秒通常會有約 18 個事件，相當於每 5 分鐘有 5,400 個事件。 稽核記錄是每個事件約 2 KB，因為這相當於 10.8 MB 的資料。 因此，有 43 則訊息會在該五分鐘間隔內傳送至事件中樞。 

下表包含美國西部基本事件中樞的每月預估成本，視不同的因素（例如使用者登入行為等）而定，視租使用者與租使用者而異的事件資料量而定。若要針對您預期應用程式的資料量計算精確的估計值，請使用「 [事件中樞定價計算機](https://azure.microsoft.com/pricing/details/event-hubs/)」。

| 記錄分類 | 使用者人數 | 每秒事件數 | 每 5 分鐘間隔的事件數 | 每一間隔的數量 | 每一間隔的訊息數 | 每月訊息數 | 每月成本 (估計值) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| 稽核 | 100,000 | 18 | 5,400 | 10.8 MB | 43 | 371,520 | $10.83 |
| 稽核 | 1,000 | 0.1 | 52 | 104 KB | 1 | 8,640 | $10.80 |
| 登入 | 100,000 | 18000 | 5400000 | 10.8 GB | 42188 | 364504320 | $23.9 |  
| 登入 | 1,000 | 178 | 53,400 | 106.8&nbsp;MB | 418 | 3,611,520 | $11.06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Azure 監視器記錄成本考量



| 記錄分類       | 使用者人數 | 每日事件數 | 每月事件 (30 天)  | 每個月的成本，以美元 (est )  |
| :--                | ---             | ---            | ---                        | --:                          |
| 審核和登入 | 100,000         | 16500000     | 495000000                |  $1093.00                       |
| 稽核              | 100,000         | 1500000      | 45,000,000                 |  $246.66                     |
| 登入           | 100,000         | 15,000,000     | 450,000,000                |  $847.28                     |










若要檢閱管理 Azure 監視器記錄工作區的相關成本，請參閱[藉由控制 Azure 監視器記錄中的資料量與保留期來管理成本](../../azure-monitor/platform/manage-cost-storage.md)。

## <a name="frequently-asked-questions"></a>常見問題集

這一節會回答 Azure 監視器中 Azure AD 記錄的常見問題集，以及討論已知的問題。

**問：其中包含哪些記錄？**

**答**：登入活動記錄和稽核記錄都可透過這項功能路由傳送，但目前不包含 B2C 相關稽核事件。 若要找出目前支援哪些類型的記錄和哪些以功能為基礎的記錄，請參閱[稽核記錄結構描述](reference-azure-monitor-audit-log-schema.md)和[登入記錄結構描述](reference-azure-monitor-sign-ins-log-schema.md)。 

---

**問：在執行動作後，多久會在事件中樞內顯示對應的記錄？**

**答**: 在執行動作後，記錄應該會在二到五分鐘內顯示於事件中樞。 如需事件中樞的詳細資訊，請參閱[什麼是 Azure 事件中樞？](../../event-hubs/event-hubs-about.md)。

---

**問：在執行動作後，多久會在儲存體帳戶中顯示對應的記錄？**

**答**: 就 Azure 儲存體帳戶而言，延遲介於執行動作後的 5 到 15 分鐘之間。

---

**問：當系統管理員變更診斷設定的保留期限時，會發生什麼事？**

**答**：新的保留原則將會套用至變更後所收集的記錄。 原則變更前所收集的記錄不受影響。

---

**問: 儲存資料需要多少成本？**

**答**：儲存體成本取決於您的記錄大小，以及您所選擇的保留期間。 如需租用戶的估計成本清單 (取決於所產生的記錄數量)，請參閱[活動記錄的儲存體大小](#storage-size-for-activity-logs)一節。

---

**問: 將資料串流至事件中樞需要多少成本？**

**答**: 串流成本取決於您每分鐘收到的訊息數目。 本文討論如何計算成本並列出成本估計值 (以訊息數目為基礎)。 

---

**問：如何整合 Azure AD 活動記錄與我的 SIEM 系統？**

**答**: 您可以使用兩種方式執行此動作：

- 使用 Azure 監視器與事件中樞將記錄串流至您的 SIEM 系統。 首先，[將記錄串流至事件中樞](tutorial-azure-monitor-stream-logs-to-event-hub.md)，然後使用已設定的事件中樞[設定您的 SIEM 工具](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub)。 

- 使用[報告圖形 API](concept-reporting-api.md) 存取資料，然後使用自己的指令碼將資料推送至 SIEM 系統中。

---

**問: 目前支援哪些 SIEM 工具？** 

**答**： **答**：目前， [Splunk](./howto-integrate-activity-logs-with-splunk.md)、IBM QRadar、 [Sumo 邏輯](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory)、 [ArcSight](./howto-integrate-activity-logs-with-arcsight.md)、LogRhythm 和 Logz.io 都支援 Azure 監視器。 如需連接器運作方式的詳細資訊，請參閱[將 Azure 監視資料串流至事件中樞以供外部工具取用](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)。

---

**問: 如何整合 Azure AD 活動記錄與我的 Splunk 執行個體？**

**答**: 首先，[將 Azure AD 活動記錄路由至事件中樞](./tutorial-azure-monitor-stream-logs-to-event-hub.md)，然後依照步驟[整合活動記錄與 Splunk](./howto-integrate-activity-logs-with-splunk.md)。

---

**問: 如何整合 Azure AD 活動記錄與 Sumo Logic？** 

**答**: 首先，[將 Azure AD 活動記錄路由至事件中樞](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)，然後依照步驟[在 SumoLogic 中安裝 Azure AD 應用程式及檢視儀表板](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)。

---

**問: 是否可在不使用外部 SIEM 工具的情況下從事件中樞存取資料？** 

**答**：是。 若要從自訂應用程式存取記錄，您可以使用[事件中樞 API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。 

---


## <a name="next-steps"></a>後續步驟

* [將活動記錄封存至儲存體帳戶](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [將活動記錄路由至事件中樞](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [將活動記錄與 Azure 監視器整合](howto-integrate-activity-logs-with-log-analytics.md)