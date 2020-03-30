---
title: 向 SaaS 應用程式報告自動使用者帳戶預配
description: 了解如何檢查使用者帳戶自動佈建作業的狀態，以及如何針對個別使用者的佈建進行疑難排解。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282183"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>教學課程︰關於使用者帳戶自動佈建的報告

Azure 活動目錄 （Azure AD） 包括[使用者帳戶預配服務](user-provisioning.md)，可説明自動預配 SaaS 應用和其他系統中的使用者帳戶，以便進行端到端標識生命週期管理。 Azure AD 支援所有應用程式和系統的預集成使用者預配連接器，[並在此處](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)提供使用者預配教程。

本文會說明如何在佈建作業設定好之後檢查其狀態，以及如何針對個別使用者和群組的佈建進行疑難排解。

## <a name="overview"></a>總覽

我們會使用 [Azure 入口網站](https://portal.azure.com)，依照針對支援之應用程式[所提供的文件](../saas-apps/tutorial-list.md)來設定佈建連接器。 在設定好並開始執行之後，您就可以使用下列兩種方法的其中一種來獲得佈建作業的報告：

* **Azure 門戶**- 本文主要介紹從 Azure[門戶](https://portal.azure.com)檢索報表資訊，該門戶既提供預配摘要報告，也提供給定應用程式的詳細預配稽核記錄。
* **稽核 API** - Azure Active Directory 也會提供稽核 API，以便透過程式擷取詳細的佈建稽核記錄。 如需此 API 專屬的使用說明文件，請參閱 [Azure Active Directory 稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)。 雖然本文未具體說明如何使用 API，但會詳細說明稽核記錄中所記下的佈建事件類型。

### <a name="definitions"></a>定義

本文會使用下列詞彙，其定義如下︰

* **來源系統** - Azure AD 佈建服務用來進行同步處理的來源端使用者存放庫。 已預先整合的佈建連接器大多使用 Azure Active Directory 作為來源系統，但有一些例外 (範例︰Workday 的輸入同步處理)。
* **目標系統** - Azure AD 佈建服務用來進行同步處理的目標端使用者存放庫。 這通常是 SaaS 應用程式（例如：Salesforce、ServiceNow、G Suite、業務 Dropbox），但在某些情況下可以是本地系統，如活動目錄（例如：工作日入站同步到活動目錄）。

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>從 Azure 門戶獲取預配報告

要獲取給定應用程式的預配報表資訊，請從"**活動"** 部分中啟動[Azure 門戶](https://portal.azure.com)和**Azure 活動目錄**&gt;**企業應用**&gt;**預配日誌（預覽）** 開始。 您還可以流覽到為其配置預配的企業應用程式。 例如，如果您要在 LinkedIn Elevate 中佈建使用者，則應用程式詳細資料的導覽路徑為︰

**Azure Active Directory > 企業應用程式 > 所有應用程式 > LinkedIn Elevate**

在此處，您可以訪問預配進度列和預配日誌，如下所述。

## <a name="provisioning-progress-bar"></a>預配進度條

[預配進度列](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)在給定應用程式的**預配**選項卡中可見。 它位於 **"設置**"下方的 **"目前狀態**"部分中，並顯示當前初始或增量週期的狀態。 本節還顯示：

* 已同步處理且目前正在來源系統與目標系統之間的佈建範圍內的使用者和群組總數。
* 上次執行同步處理的時間。 同步通常每 20-40 分鐘發生一次，[初始週期](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)完成後。
* [初始週期](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)是否已完成。
* 佈建程序是否已進入隔離狀態，以及造成隔離狀態的原因為何 (例如，因管理員認證無效而無法與目標系統進行通訊)。

當前**狀態**應該是管理員查看預配作業的操作運行狀況的第一個位置。

 ![摘要報告](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>預配日誌（預覽）

預配服務執行的所有活動都記錄在 Azure AD[預配日誌](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 通過在 **"活動"** 部分中選擇**Azure 活動目錄**&gt;**企業應用**&gt;**預配日誌（預覽），** 可以在 Azure 門戶中訪問預配日誌。 您可以根據使用者名稱或源系統或目標系統中的識別碼搜索預配資料。 有關詳細資訊，請參閱[預配日誌（預覽）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 所記錄的活動事件類型包括︰

## <a name="troubleshooting"></a>疑難排解

預配摘要報告和預配日誌在説明管理員解決各種使用者帳戶預配問題時起關鍵作用。

如需透過案例來獲得如何針對自動使用者佈建進行疑難排解的指引，請參閱[為應用程式設定和佈建使用者時發生問題](../app-provisioning/application-provisioning-config-problem.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用的使用者帳戶預配](configure-automatic-user-provisioning-portal.md)
* [什麼是使用 Azure 活動目錄的應用程式訪問和單一登入？](../manage-apps/what-is-single-sign-on.md)
