---
title: 使用 Azure 監視器記錄來分析活動記錄 | Microsoft Docs
description: 了解如何使用 Azure 監視器記錄來分析 Azure Active Directory 活動記錄
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a48a72aa021a17c59adb86bece66cec966e234bd
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056135"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>使用 Azure 監視器記錄來分析 Azure AD 活動記錄

在您[整合 Azure AD 活動記錄與 Azure 監視器記錄](howto-integrate-activity-logs-with-log-analytics.md)之後，可以使用 Azure 監視器記錄的強大功能來深入了解環境。 您也可以安裝[適用於 Azure AD 活動記錄的 Log Analytics 檢視](howto-install-use-log-analytics-views.md)，以存取您環境中稽核和登入事件的預建報表。

在本文中，您將了解如何分析 Log Analytics 工作區中的 Azure AD 活動記錄。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites 

若要繼續，您需要：

* Azure 訂用帳戶中的 Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。
* 首先，請完成[將 Azure AD 活動記錄路由至 Log Analytics 工作區](howto-integrate-activity-logs-with-log-analytics.md)的步驟。
*  [存取](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)記錄分析工作區
* Azure Active Directory 中的下列角色 (如果是透過 Azure Active Directory 入口網站存取 Log Analytics)
    - 安全性系統管理員
    - 安全性讀取者
    - 報告讀者
    - 全域管理員
    
## <a name="navigate-to-the-log-analytics-workspace"></a>瀏覽至 Log Analytics 工作區

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 選取 [Azure Active Directory]，然後從 [監視] 區段選取 [記錄]，以開啟 Log Analytics 工作區。 開啟的工作區會包含預設查詢。

    ![預設查詢](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>檢視 Azure AD 活動記錄的結構描述

記錄會推送至工作區中的 **AuditLogs** 和 **SigninLogs** 資料表。 若要檢視這些資料表的結構描述：

1. 從上一節中的預設查詢檢視，選取 [結構描述] 並展開工作區。 

2. 展開 [記錄管理] 區段，然後再展開 [AuditLogs] 或 [SigninLogs] 以檢視記錄結構描述。
    ![稽核記錄](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![登入記錄](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>查詢 Azure AD 活動記錄

現在，您的工作區中已經有記錄，可以對其執行查詢。 例如，若要取得過去一週使用率最高的前幾名應用程式，請以下列內容取代預設查詢，然後選取 [執行]

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

若要取得過去一週發生機率最高的前幾名稽核事件，請使用下列查詢：

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Azure AD 活動記錄資料的警示

您也可以在您的查詢上設定警示。 例如，若要在上週使用超過 10 個應用程式時設定警示：

1. 從工作區中，選取 [設定警示] 來開啟 [建立規則] 頁面。

    ![設定警示](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. 選取警示中建立的預設**警示準則**，並將預設計量中的**閾值**更新為 10。

    ![警示準則](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. 輸入警示的名稱和描述，並選擇嚴重性層級。 在我們的範例中，我們可以將它設定為**資訊**。

4. 選取訊號發生時會收到警示的**動作群組**。 您可以選擇透過電子郵件或文字訊息來通知團隊，或者使用 Webhook、Azure Functions 或 Logic Apps 來自動化動作。 深入了解[在 Azure 入口網站中建立及管理警示群組](../../azure-monitor/platform/action-groups.md)。

5. 一旦設定警示之後，請選取 [建立警示] 來加以啟用。 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>針對 Azure AD 活動記錄使用預先建立的活頁簿

活頁簿會提供數個與常見案例相關的報表，這些案例包括審核、登入和布建事件。 您也可以使用上一節中所述的步驟，在報表所提供的任何資料上設定警示。

* 布建**分析**：此活頁[簿](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics)會顯示與「審核布建」活動相關的報表，例如已布建的新使用者數目和布建失敗、更新的使用者數目和更新失敗，以及取消布建的使用者數目和對應的失敗數目。    
* 登**入事件**：此活頁簿會顯示與監視登入活動相關的最相關報告，例如依應用程式、使用者、裝置的登入，以及追蹤一段時間內登入次數的摘要觀點。
* **條件式存取深入**解析：條件式存取深入解析和報告活頁 [簿](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) 可讓您瞭解您組織中的條件式存取原則在一段時間內的影響。 

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure 監視器記錄中的查詢](../../azure-monitor/log-query/get-started-queries.md)
* [在 Azure 入口網站中建立和管理警示群組](../../azure-monitor/platform/action-groups.md)
* [安裝與使用適用於 Azure Active Directory 的記錄分析檢視](howto-install-use-log-analytics-views.md)
