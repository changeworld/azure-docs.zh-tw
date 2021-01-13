---
title: 什麼是在 Azure AD 中自動佈建 SaaS 應用程式使用者
description: 簡介如何使用 Azure AD 自動佈建、解除佈建，以及跨多個協力廠商 SaaS 應用程式持續更新使用者帳戶。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 01/11/2021
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: c15e92a28de55e5e8bc331fb4398790984439a01
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107424"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>什麼是在 Azure AD 中自動佈建 SaaS 應用程式使用者？

在 Azure Active Directory (Azure AD) 中，**應用程式佈建** 一詞是指在使用者需要存取的雲端 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動佈建還包括隨著狀態或角色變更，維護和移除使用者身分識別。 常見的案例包括將 Azure AD 使用者佈建到 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)等應用程式。

若要深入了解 SCIM 和如何加入技術社群的交談，請參閱[透過 SCIM 技術社群進行佈建](https://aka.ms/scimoverview)。

![佈建概觀圖](./media/user-provisioning/provisioning-overview.png)

此功能可讓您：

- **自動化佈建**：當人員加入您的小組或組織時，在正確的系統中為新的人員自動建立新帳戶。
- **自動化取消佈建：** 當人員離開小組或組織時，在正確的系統中自動停用帳戶。
- **在系統之間同步資料：** 確保您的應用程式和系統中的身分識別，可根據目錄或您人力資源系統中的變更保持在最新狀態。
- **佈建群組：** 將群組佈建至加以支援的應用程式。
- **控管存取：** 監視和稽核已佈建至應用程式的人員。
- **在棕地案例中順暢部署：** 比對系統之間現有的身分識別，並可讓您輕鬆進行整合，即便使用者已存在於目標系統亦然。
- **使用豐富的自訂：** 充分運用可自訂的屬性對應，其中定義了哪些使用者資料應該從來源系統流向目標系統。
- **取得重大事件的警示：** 佈建服務會提供重大事件的警示，並可讓您根據本身的商業需求定義自訂警示，以進行 Log Analytics 整合。

## <a name="benefits-of-automatic-provisioning"></a>自動佈建的優點

隨著現代化組織中使用的應用程式數目持續增長，大規模的存取管理成為 IT 管理員的工作之一。 安全性聲明標記語言 (SAML) 或 Open ID Connect (OIDC) 之類的標準可讓管理員快速設定單一登入 (SSO)，但使用者仍需佈建到應用程式中才能進行存取。 對於許多管理員而言，佈建意味著必須手動建立每個使用者帳戶或每週上傳 CSV 檔案，但這些程序既耗時又耗費資源，且容易出錯。 目前已採用 SAML just-in-time (JIT) 之類的解決方案來自動化佈建，但企業仍需端賴解決方案，以在使用者離開組織時，或隨著角色變更而不再需要存取特定應用程式時，將使用者取消佈建。

使用自動佈建的常見動機包括：

- 盡可能提高佈建程序的效率和正確性。
- 節省與裝載及維護自訂開發的佈建解決方案和指令碼相關的成本。
- 在使用者離開組織時，立即從主要 SaaS 應用程式中移除使用者的身分識別，以保護您的組織。
- 輕鬆將大量使用者匯入至特定的 SaaS 應用程式或系統中。
- 可用一組原則來判斷已佈建的使用者以及可登入應用程式的使用者。

Azure AD 使用者佈建有助於克服這些難題。 若要深入了解客戶使用 Azure AD 使用者佈建的情形，您可以閱讀 [ASOS 案例研究](https://aka.ms/asoscasestudy)。 下列影片提供 Azure AD 中的使用者佈建概觀：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

Azure AD 的特色是可為多種熱門 SaaS 應用程式和人力資源系統提供預先整合的支援，以及為實作 [SCIM 2.0 標準](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)之特定部分的應用程式提供一般支援。

* **預先整合的應用程式 (資源庫 SaaS 應用程式)** 。 您可以在[適用於使用者佈建的應用程式教學課程清單](../saas-apps/tutorial-list.md)中，找到 Azure AD 支援預先整合之佈建連接器的所有應用程式。 資源庫中列出的預先整合應用程式通常會使用 SCIM 2.0 型的使用者管理 API 來進行佈建。 

   ![Salesforce 標誌](./media/user-provisioning/gallery-app-logos.png)

   如果您想要求對新的應用程式進行佈建，您可以[要求應用程式與我們的應用程式庫進行整合](../develop/v2-howto-app-gallery-listing.md)。 針對使用者佈建要求，我們要求應用程式必須具有符合 SCIM 規範的端點。 請要求應用程式廠商遵循 SCIM 標準，以便我們能將應用程式快速上線至我們的平台。

* **支援 SCIM 2.0 的應用程式**。 若要了解如何以一般方式連接可實作 SCIM 2.0 型使用者管理 API 的應用程式，請參閱[建置 SCIM 端點和設定使用者佈建](use-scim-to-provision-users-and-groups.md)。

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>什麼是跨網域身分識別管理系統 (SCIM)？

為了協助您自動執行佈建和取消佈建，應用程式會公開專屬的使用者和群組 API。 不過，任何人只要曾試著管理多個應用程式的使用者，都會告訴您，每個應用程式都會嘗試執行相同的簡單動作，例如建立或更新使用者、將使用者新增至群組，或取消佈建使用者。 然而，這些簡單動作的實作方式全都不盡相同，分別使用不同的端點路徑、以不同的方法指定使用者資訊，並且以不同的結構描述代表每個資訊元素。

為了克服這些難題，SCIM 規格提供了通用的使用者結構描述，可協助使用者在應用程式之間移入、移出和轉移。 SCIM 逐漸成為佈建的既定標準；與 SAML 或 OpenID Connect 等同盟標準搭配使用時，SCIM 可為管理員提供端對端、以標準為基礎的存取管理解決方案。

如需開發 SCIM 端點以將使用者和群組對應用程式的佈建和取消佈建自動化的詳細指引，請參閱[建置 SCIM 端點和設定使用者佈建](use-scim-to-provision-users-and-groups.md)。 對於資源庫中預先整合的應用程式 (Slack、Azure Databricks、Snowflake 等)，您可以略過開發人員文件，並使用[這裡](../saas-apps/tutorial-list.md)提供的教學課程。

## <a name="manual-vs-automatic-provisioning"></a>手動與自動佈建

Azure AD 資源庫中的應用程式支援兩種佈建模式之一：

* **手動** 佈建表示應用程式尚無自動 Azure AD 佈建連接器。 您必須手動建立使用者帳戶，例如，將使用者直接新增至應用程式的系統管理入口網站，或上傳含有使用者帳戶詳細資料的試算表。 請參閱應用程式提供的文件，或洽詢應用程式開發人員，以判斷有哪些機制可供使用。

* 「自動」表示已經為此應用程式開發 Azure AD 佈建連接器。 您應遵循為應用程式設定佈建專用的設定教學課程。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程。

在 Azure AD 資源庫中，支援自動佈建的應用程式會以 **佈建** 圖示指定。 請切換至新的資源庫預覽體驗，以查看這些圖示 (在 [新增應用程式] 頁面頂端的橫幅中，選取顯示為 [按一下這裡試用改良過的新版應用程式資源庫] 的連結)。

![應用程式庫中的佈建圖示](./media/user-provisioning/browse-gallery.png)

當您將應用程式新增至 [企業應用程式] 後，該應用程式支援的佈建模式也會顯示在 [佈建] 索引標籤上。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

針對資源庫中列出的預先整合應用程式，您可以使用逐步指引來設定自動佈建。 請參閱[整合式資源庫應用程式的教學課程清單](../saas-apps/tutorial-list.md)。 下列影片示範如何設定 SalesForce 的自動使用者佈建。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

對於支援 SCIM 2.0 的其他應用程式，請依照[建置 SCIM 端點和設定使用者佈建](use-scim-to-provision-users-and-groups.md)一文中的步驟操作。


## <a name="next-steps"></a>後續步驟

- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
- [自訂使用者佈建的屬性對應](customize-application-attributes.md)
- [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
