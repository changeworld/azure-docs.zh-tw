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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa952e0a237ca30a3bd109f51ee45c2f4dafa533
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758260"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>使用 Azure 監視器記錄來分析 Azure AD 活動記錄

在您[整合 Azure AD 活動記錄與 Azure 監視器記錄](howto-integrate-activity-logs-with-log-analytics.md)之後，可以使用 Azure 監視器記錄的強大功能來深入了解環境。 您也可以安裝[適用於 Azure AD 活動記錄的 Log Analytics 檢視](howto-install-use-log-analytics-views.md)，以存取您環境中稽核和登入事件的預建報表。

在本文中，您將了解如何分析 Log Analytics 工作區中的 Azure AD 活動記錄。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites 

若要繼續，您需要：

* Azure 訂用帳戶中的 Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。
* 首先，請完成[將 Azure AD 活動記錄路由至 Log Analytics 工作區](howto-integrate-activity-logs-with-log-analytics.md)的步驟。
*  [存取](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)記錄分析工作區
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

4. 選取訊號發生時會收到警示的**動作群組**。 您可以選擇透過電子郵件或文字訊息來通知團隊，或者使用 Webhook、Azure Functions 或 Logic Apps 來自動化動作。 深入了解[在 Azure 入口網站中建立及管理警示群組](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)。

5. 一旦設定警示之後，請選取 [建立警示] 來加以啟用。 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>安裝和使用預建的 Azure AD 活動記錄檢視

您也可以下載 Azure AD 活動記錄的預建記錄分析檢視。 檢視會提供數個常見案例的相關報告，包含稽核和登入事件。 您也可以使用上一節中所述的步驟，在報表所提供的任何資料上設定警示。

* **Azure AD 帳戶佈建事件**：此檢視會顯示與稽核佈建活動有關的報告，例如佈建的新使用者數目和佈建失敗次數、更新的使用者數目和更新失敗次數，以及取消佈建的使用者數目和對應的失敗次數。    
* **登入事件**：此檢視會顯示與監視登入活動相關性最高的報告 (例如依應用程式、使用者、裝置區分的登入)，以及追蹤一段時間內登入次數的摘要檢視。
* **執行同意的使用者**：此檢視會顯示與使用者同意有關的報告，例如依使用者區分的同意授與、依授與同意的使用者區分的登入，以及就所有以同意為基礎的應用程式而言，依應用程式區分的登入。 

了解如何[安裝與使用適用於 Azure AD 活動記錄的記錄分析檢視](howto-install-use-log-analytics-views.md)。 


## <a name="next-steps"></a>後續步驟

* [開始使用 Azure 監視器記錄中的查詢](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [在 Azure 入口網站中建立和管理警示群組](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [安裝與使用適用於 Azure Active Directory 的記錄分析檢視](howto-install-use-log-analytics-views.md)
