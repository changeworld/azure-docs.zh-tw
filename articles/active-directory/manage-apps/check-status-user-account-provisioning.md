---
title: 向 SaaS 應用程式報告自動使用者帳戶布建
description: 了解如何檢查使用者帳戶自動佈建作業的狀態，以及如何針對個別使用者的佈建進行疑難排解。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77865ad3fe23d1413b6b957d351d9d9d92d22fb7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711977"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>教學課程︰關於使用者帳戶自動佈建的報告

Azure Active Directory （Azure AD）包含[使用者帳戶](user-provisioning.md)布建服務，可協助您在 SaaS 應用程式和其他系統中自動布建使用者帳戶，以進行端對端身分識別生命週期管理。 對於 [Azure AD 應用程式庫](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)之 [精選] 區段中的所有應用程式和系統，Azure AD 支援已預先整合的使用者佈建連接器。

本文會說明如何在佈建作業設定好之後檢查其狀態，以及如何針對個別使用者和群組的佈建進行疑難排解。

## <a name="overview"></a>概觀

我們會使用 [Azure 入口網站](https://portal.azure.com)，依照針對支援之應用程式[所提供的文件](../saas-apps/tutorial-list.md)來設定佈建連接器。 在設定好並開始執行之後，您就可以使用下列兩種方法的其中一種來獲得佈建作業的報告：

* **Azure 入口網站**-本文主要說明如何從[Azure 入口網站](https://portal.azure.com)中抓取報告資訊，以提供布建摘要報告，以及特定應用程式的詳細布建審核記錄。
* **稽核 API** - Azure Active Directory 也會提供稽核 API，以便透過程式擷取詳細的佈建稽核記錄。 如需此 API 專屬的使用說明文件，請參閱 [Azure Active Directory 稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)。 雖然本文未具體說明如何使用 API，但會詳細說明稽核記錄中所記下的佈建事件類型。

### <a name="definitions"></a>定義

本文會使用下列詞彙，其定義如下︰

* **來源系統** - Azure AD 佈建服務用來進行同步處理的來源端使用者存放庫。 已預先整合的佈建連接器大多使用 Azure Active Directory 作為來源系統，但有一些例外 (範例︰Workday 的輸入同步處理)。
* **目標系統** - Azure AD 佈建服務用來進行同步處理的目標端使用者存放庫。 這通常是 SaaS 應用程式（例如 Salesforce、ServiceNow、G Suite、Dropbox for Business），但在某些情況下，可以是內部部署系統，例如 Active Directory （例如： Workday 輸入同步處理至 Active Directory）。

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>從 Azure 入口網站取得布建報告

若要取得給定應用程式的布建報告資訊，請先啟動[Azure 入口網站](https://portal.azure.com)，然後**Azure Active Directory** &gt;**企業應用程式**] &gt; [**活動**] 區段中的 [布建**記錄（預覽）**。
 您也可以流覽至已設定布建的企業應用程式。 例如，如果您要在 LinkedIn Elevate 中佈建使用者，則應用程式詳細資料的導覽路徑為︰

**Azure Active Directory > 企業應用程式 > 所有應用程式 > LinkedIn Elevate**

從這裡，您可以存取布建進度列和布建記錄，如下所述。

## <a name="provisioning-progress-bar"></a>布建進度列

布建[進度](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)列會顯示在指定應用程式的 [布**建] 索引**標籤中。 它位於 [**設定**] 底下的 [**目前狀態**] 區段中，並顯示目前初始或累加迴圈的狀態。 本節也會顯示：

* 已同步處理且目前正在來源系統與目標系統之間的佈建範圍內的使用者和群組總數。
* 上次執行同步處理的時間。 [初始迴圈](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)完成後，通常每隔20-40 分鐘就會進行同步處理。
* 是否已完成[初始迴圈](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。
* 佈建程序是否已進入隔離狀態，以及造成隔離狀態的原因為何 (例如，因管理員認證無效而無法與目標系統進行通訊)。

**目前的狀態**應該是系統管理員要查看布建作業的操作健全狀況的第一個位置。

 ![摘要報告](media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>布建記錄（預覽）

布建服務所執行的所有活動都會記錄在 Azure AD 布建[記錄](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)檔中。 您可以在 [**活動**] 區段中選取 [ **Azure Active Directory** &gt;**企業應用程式**] &gt; [布建**記錄（預覽）** ]，以存取 Azure 入口網站中的布建記錄。 您可以根據使用者名稱或來源系統或目標系統中的識別碼來搜尋布建資料。 如需詳細資訊，請參閱布建[記錄（預覽）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 所記錄的活動事件類型包括︰

## <a name="troubleshooting"></a>疑難排解

布建摘要報告和布建記錄扮演重要的角色，協助系統管理員針對各種使用者帳戶布建問題進行疑難排解。

如需透過案例來獲得如何針對自動使用者佈建進行疑難排解的指引，請參閱[為應用程式設定和佈建使用者時發生問題](application-provisioning-config-problem.md)。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](what-is-single-sign-on.md)
