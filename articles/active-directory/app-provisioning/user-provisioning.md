---
title: 在 Azure AD 中自動化 SaaS 應用程式使用者佈建 | Microsoft Docs
description: 簡介如何使用 Azure AD 自動佈建、解除佈建，以及跨多個協力廠商 SaaS 應用程式持續更新使用者帳戶。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454528"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>使用 Azure 活動目錄自動調配使用者並取消預配到應用程式

在 Azure 活動目錄 （Azure AD） 中，術語**應用預配**是指在使用者需要訪問的雲[（SaaS](https://azure.microsoft.com/overview/what-is-saas/)） 應用程式中自動創建使用者標識和角色。 除了建立使用者身分識別以外，自動佈建還包括隨著狀態或角色變更，維護和移除使用者身分識別。 常見的案例包括將 Azure AD 使用者佈建到 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)等應用程式。

![預配概述圖](./media/user-provisioning/provisioning-overview.png)

此功能允許您：

- **自動預配**：在新人加入您的團隊或組織時，在正確的系統中自動創建新帳戶。
- **自動取消預配：** 當人們離開團隊或組織時，自動停用正確的系統中的帳戶。
- **在系統之間同步資料：** 確保應用和系統中的標識根據目錄或人力資源系統的更改保持最新。
- **預配組：** 將組預配到支援它們的應用程式。
- **管理訪問：** 監視和審核已預配到應用程式中的人員。
- **在棕色欄位方案中無縫部署：** 匹配系統之間的現有標識，並允許輕鬆集成，即使目標系統中已存在使用者也是如此。
- **使用豐富的自訂：** 利用可自訂的屬性對應，這些映射定義使用者資料應從源系統流向目標系統。
- **獲取關鍵事件的警報：** 預配服務為關鍵事件提供警報，並允許進行日誌分析集成，您可以在其中定義自訂警報以滿足業務需求。

## <a name="benefits-of-automatic-provisioning"></a>自動預配的好處

隨著現代組織中使用的應用程式數量持續增長，IT 管理員的任務是大規模進行訪問管理。 安全斷言標記語言 （SAML） 或打開 ID 連接 （OIDC） 等標準允許管理員快速設置單一登入 （SSO），但訪問還要求將使用者預配到應用中。 對於許多管理員，預配意味著每週手動創建每個使用者帳戶或上傳 CSV 檔，但這些過程既耗時、昂貴且容易出錯。 採用 SAML 即時 （JIT） 等解決方案來自動調配，但企業還需要一種解決方案，在使用者離開組織或不再需要基於角色更改訪問某些應用時取消預配。

使用自動預配的一些常見動機包括：

- 最大限度地提高資源調配流程的效率和準確性。
- 節省與託管和維護自訂開發的預配解決方案和腳本相關的成本。
- 通過在使用者離開組織時立即從關鍵 SaaS 應用中刪除其身份，保護組織。
- 輕鬆將大量使用者導入特定的 SaaS 應用程式或系統。
- 具有一群組原則來確定預配誰以及誰可以登錄到應用。

Azure AD 使用者預配可説明應對這些挑戰。 要瞭解有關客戶如何使用 Azure AD 使用者預配的情況，請閱讀[ASOS 案例研究](https://aka.ms/asoscasestudy)。 以下視頻概述了 Azure AD 中的使用者預配：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

Azure AD 具有對許多流行的 SaaS 應用和人力資源系統的預集成支援，以及對實現[SCIM 2.0 標準](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)特定部分的應用的通用支援。

* **預集成應用程式（庫 SaaS 應用程式）。** 您可以在用於[使用者預配的應用程式教程清單中](../saas-apps/tutorial-list.md)找到 Azure AD 支援預集成預配連接器的所有應用程式。 庫中列出的預集成應用程式通常使用基於 SCIM 2.0 的使用者管理 API 進行預配。 

   ![銷售隊伍徽標](./media/user-provisioning/gallery-app-logos.png)

   如果您想請求新的應用程式進行預配，您可以[請求將應用程式與我們的應用程式庫集成](../develop/howto-app-gallery-listing.md)。 對於使用者預配請求，我們要求應用程式具有符合 SCIM 的終結點。 請要求應用程式供應商遵循 SCIM 標準，以便我們可以快速將應用程式登機到我們的平臺。

* **支援 SCIM 2.0**的應用程式。 有關如何通用連接實現基於 SCIM 2.0 的使用者管理 API 的應用程式的資訊，請參閱生成[SCIM 終結點並配置使用者預配](use-scim-to-provision-users-and-groups.md)。

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>什麼是跨域身份管理系統 （SCIM）？

為了説明自動預配和取消預配，應用公開專有使用者和組 API。 但是，嘗試在多個應用中管理使用者的任何人將告訴您，每個應用都嘗試執行相同的簡單操作，例如創建或更新使用者、將使用者添加到組或取消預配使用者。 但是，所有這些簡單操作的實現方式稍有不同，使用不同的終結點路徑、指定使用者資訊的不同方法以及表示每個資訊元素的不同架構。

為了應對這些挑戰，SCIM 規範提供了一個通用使用者架構，可説明使用者進入、退出應用和周圍應用。 SCIM 正在成為預配的實際標準，當與 SAML 或 OpenID Connect 等聯合標準結合使用時，為管理員提供了基於端到端標準的訪問管理解決方案。

有關開發 SCIM 終結點以自動預配和取消向應用程式預配使用者和組的詳細資訊，請參閱[生成 SCIM 終結點並配置使用者預配](use-scim-to-provision-users-and-groups.md)。 對於庫中的預集成應用程式（Slack、Azure 資料塊、雪花等），可以跳過開發人員文檔並使用[此處](../saas-apps/tutorial-list.md)提供的教程。

## <a name="manual-vs-automatic-provisioning"></a>手動與自動佈建

Azure AD 庫中的應用程式支援兩種預配模式之一：

* **手動**預配意味著尚未為應用提供自動 Azure AD 預配連接器。 使用者帳戶必須手動創建，例如，通過將使用者直接添加到應用的監管中心，或上傳包含使用者帳戶詳細資訊的試算表。 請參閱應用提供的文檔，或聯繫應用開發人員以確定可用的機制。

* 「自動」**** 表示已經為此應用程式開發 Azure AD 佈建連接器。 您應該遵循特定于設置應用程式的預配的設置教程。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程。

在 Azure AD 庫中，支援自動預配的應用程式由**預配**圖示指定。 切換到新的庫預覽體驗以查看這些圖示（在 **"添加應用程式"頁面**頂部的橫幅中，選擇顯示"**按一下此處嘗試新的和改進的應用程式庫**"的連結）。

![應用程式庫中的預配圖示](./media/user-provisioning/browse-gallery.png)

將應用程式添加到**企業應用**後，應用程式支援的預配模式也會顯示在**預配**選項卡上。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

對於庫中列出的預集成應用程式，可用於設置自動預配的分步指南。 請參閱[集成庫應用教程清單](../saas-apps/tutorial-list.md)。 以下視頻演示如何為 SalesForce 設置自動使用者預配。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

對於支援 SCIM 2.0 的其他應用程式，請按照文章"[構建 SCIM 終結點"中的步驟進行配置使用者預配](use-scim-to-provision-users-and-groups.md)。


## <a name="related-articles"></a>相關文章

- [有關如何集成 SaaS 應用的教程清單](../saas-apps/tutorial-list.md)
- [自訂使用者預配的屬性對應](customize-application-attributes.md)
- [為屬性對應編寫運算式](../app-provisioning/functions-for-customizing-application-data.md)
- [使用者預配的範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [構建 SCIM 終結點並配置使用者預配](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
