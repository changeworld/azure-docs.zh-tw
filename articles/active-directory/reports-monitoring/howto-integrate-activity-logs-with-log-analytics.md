---
title: 將 Azure Active Directory 記錄串流至 Azure 監視器記錄 |Microsoft Docs
description: 瞭解如何整合 Azure Active Directory 記錄與 Azure 監視器記錄
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
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
ms.openlocfilehash: f70d1caacfd655c956d4fcc36e3f0d3848d8f0fe
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230563"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>整合 Azure AD 記錄與 Azure 監視器記錄

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure 監視器記錄可讓您查詢資料，以尋找特定事件、分析趨勢，以及執行各種資料來源之間的相互關聯。 藉由將 Azure AD 活動記錄整合到 Azure 監視器記錄中，您可以執行如下的工作：

 * 比較您的 Azure AD 登入記錄與 Azure 資訊安全中心所發佈的安全性記錄

 * 藉由將 Azure Application Insights 中的應用程式效能資料相互關聯，在應用程式的登入頁面上進行效能瓶頸的疑難排解。  

Ignite 工作階段中的下列影片，會示範在特定使用者案例中針對 Azure AD 記錄使用 Azure 監視器記錄的優點。

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

在此文章中，您將了解如何將 Azure Active Directory (Azure AD) 記錄與 Azure 監視器整合。

## <a name="supported-reports"></a>支援的報告

您可以將稽核活動記錄和登入活動記錄路由到 Azure 監視器記錄，以進行深入分析。 

* **稽核記錄**：[稽核記錄活動報告](concept-audit-logs.md)可讓您對每個在租用戶中執行的工作存取歷程記錄。
* **登入記錄**：透過[登入活動報告](concept-sign-ins.md)，您可以判斷是誰執行了稽核記錄中所報告的工作。

> [!NOTE]
> 目前不支援與 B2C 相關的稽核和登入活動記錄。
>

## <a name="prerequisites"></a>先決條件 

若要使用此功能，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以[註冊免費試用](https://azure.microsoft.com/free/)。
* Azure AD 租用戶。
* 屬於 Azure AD 租使用者之 *全域管理員* 或 *安全性系統管理員* 的使用者。
* Azure 訂用帳戶中的 Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](../../azure-monitor/learn/quick-create-workspace.md)。

## <a name="licensing-requirements"></a>授權需求

使用此功能需要 Azure AD Premium P1 或 P2 授權。 若要尋找適用於您需求的正確授權，請參閱[比較 Free、Basic 及 Premium 版本的正式運作功能](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="send-logs-to-azure-monitor"></a>將記錄傳送給 Azure 監視器

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 選取**Azure Active Directory**  >  **診斷**設定  ->  **新增診斷設定**。 您也可以在 [稽核記錄]**** 或 [登入]**** 頁面中選取 [匯出設定]****，以移至診斷設定組態頁面。  
    
3. 在 [診斷設定]**** 功能表中選取 [傳送至 Log Analytics 工作區]**** 核取方塊，然後選取 [設定]****。

4. 選取記錄要傳送到的 Log Analytics 工作區，或在畫面上提供的對話方塊中建立新的工作區。  

5. 執行下列其中一個動作或兩個動作都執行：
    * 若要將稽核記錄傳送到 Log Analytics 工作區，請選取 [AuditLogs]**** 核取方塊。 
    * 若要將登入記錄傳送到 Log Analytics 工作區，請選取 [SignInLogs]**** 核取方塊。

6. 選取 [儲存]**** 以儲存設定。

    ![診斷設定](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. 等候約 15 分鐘，然後確認事件已串流至您的 Log Analytics 工作區。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 監視器記錄來分析 Azure AD 活動記錄](howto-analyze-activity-logs-log-analytics.md)
* [安裝與使用適用於 Azure Active Directory 的記錄分析檢視](howto-install-use-log-analytics-views.md)