---
title: Azure AD 中的自動化 SaaS 應用程式使用者布建
description: 簡介如何使用 Azure AD 自動佈建、解除佈建，以及跨多個協力廠商 SaaS 應用程式持續更新使用者帳戶。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh, celested
ms.openlocfilehash: 1e72d885858b543999090a4a0521845d556802fd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593109"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-ad"></a>使用 Azure AD 自動化應用程式的使用者布建和解除布建

在 Azure Active Directory （Azure AD）中，**應用程式**布建一詞指的是在使用者需要存取的雲端（[SaaS](https://azure.microsoft.com/overview/what-is-saas/)）應用程式中自動建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動佈建還包括隨著狀態或角色變更，維護和移除使用者身分識別。 常見的案例包括將 Azure AD 使用者佈建到 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)等應用程式。

![布建總覽圖表](./media/user-provisioning/provisioning-overview.png)

這項功能可讓您：

- **自動布建：當**新人員加入您的小組或組織時，自動在正確的系統中建立新帳戶。
- **自動**解除布建：當人員離開小組或組織時，自動停用正確系統中的帳戶。
- **在系統之間同步處理資料：** 確保您應用程式和系統中的身分識別會根據目錄或人力資源系統中的變更保持最新狀態。
- 布建**群組：** 將群組布建至支援它們的應用程式。
- **管理存取權：** 監視並 audit 已布建到您應用程式的人員。
- **在棕色的欄位案例中順暢部署：** 比對系統之間現有的身分識別，並允許輕鬆整合，即使使用者已存在於目標系統中也一樣。
- **使用豐富的自訂：** 利用可自訂的屬性對應，定義哪些使用者資料應該從來源系統流向目標系統。
- **取得重大事件的警示：** 布建服務會提供重大事件的警示，並允許 Log Analytics 整合，您可以在其中定義自訂警示以符合您的商務需求。

## <a name="benefits-of-automatic-provisioning"></a>自動布建的優點

隨著現代化組織中使用的應用程式數目持續成長，IT 系統管理員會負責大規模的存取管理。 安全性判斷提示標記語言（SAML）或 Open ID Connect （OIDC）之類的標準可讓系統管理員快速設定單一登入（SSO），但存取權也需要將使用者布建到應用程式中。 對於許多系統管理員而言，布建表示每週手動建立每個使用者帳戶或上傳 CSV 檔案，但這些程式既耗時又耗費資源，而且容易出錯。 已採用 SAML 及時（JIT）之類的解決方案來自動化布建，但是企業也需要解決方案，在使用者離開組織時取消布建，或不再需要根據角色變更來存取特定應用程式。

使用自動布建的一些常見動機包括：

- 將布建程式的效率和正確性最大化。
- 節省與裝載及維護自訂開發的布建解決方案和腳本相關的成本。
- 藉由在使用者離開組織時立即從關鍵 SaaS 應用程式移除他們的身分識別，來保護您的組織。
- 輕鬆將大量使用者匯入至特定的 SaaS 應用程式或系統。
- 具有一組原則來判斷布建者和誰可以登入應用程式。

Azure AD 的使用者布建有助於解決這些挑戰。 若要深入瞭解客戶如何使用 Azure AD 的使用者布建，您可以閱讀[ASOS 案例研究](https://aka.ms/asoscasestudy)。 下列影片提供 Azure AD 中的使用者布建總覽：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>哪些應用程式和系統可以搭配使用 Azure AD 自動使用者佈建？

Azure AD 為許多熱門的 SaaS 應用程式和人力資源系統提供預先整合的支援，以及可執行[SCIM 2.0 standard](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)特定部分之應用程式的一般支援。

* **預先整合的應用程式（資源庫 SaaS 應用程式）**。 在使用者布建的[應用程式教學課程清單](../saas-apps/tutorial-list.md)中，您可以找到 Azure AD 支援預先整合布建連接器的所有應用程式。 資源庫中列出的預先整合應用程式通常會使用以 SCIM 2.0 為基礎的使用者管理 Api 來進行布建。 

   ![Salesforce 標誌](./media/user-provisioning/gallery-app-logos.png)

   如果您想要要求新的應用程式進行布建，您可以[要求您的應用程式與我們的應用程式庫整合](../develop/howto-app-gallery-listing.md)。 針對使用者布建要求，我們要求應用程式必須具有符合 SCIM 規範的端點。 請要求應用程式廠商遵循 SCIM 標準，讓我們可以快速將應用程式上線至我們的平臺。

* **支援 SCIM 2.0 的應用程式**。 如需如何以一般方式連接可執行 SCIM 2.0 型使用者管理 Api 之應用程式的相關資訊，請參閱[建立 SCIM 端點和設定使用者](use-scim-to-provision-users-and-groups.md)布建。

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>跨網域身分識別管理（SCIM）的系統是什麼？

為了協助自動布建和取消布建，應用程式會公開專屬的使用者和群組 Api。 不過，嘗試在多個應用程式中管理使用者的任何人，都會告訴您，每個應用程式都會嘗試執行相同的簡單動作，例如建立或更新使用者、將使用者新增至群組或解除布建使用者。 不過，所有這些簡單動作的執行方式有點不同，使用不同的端點路徑、指定使用者資訊的不同方法，以及代表每個資訊元素的不同架構。

為了解決這些挑戰，SCIM 規格提供了通用的使用者架構，可協助使用者在應用程式之間移動。 SCIM 已成為布建的現行標準，當與 SAML 或 OpenID Connect 等同盟標準搭配使用時，會為系統管理員提供端對端標準解決方案來進行存取管理。

如需開發 SCIM 端點以自動將使用者和群組布建和取消布建至應用程式的詳細指引，請參閱[建立 SCIM 端點和設定使用者布建](use-scim-to-provision-users-and-groups.md)。 針對資源庫中預先整合的應用程式（時差、Azure Databricks、雪花等），您可以略過開發人員檔，並使用此處提供的教學[課程](../saas-apps/tutorial-list.md)。

## <a name="manual-vs-automatic-provisioning"></a>手動與自動佈建

Azure AD 資源庫中的應用程式支援兩種布建模式之一：

* **手動**布建表示尚未為應用程式提供自動 Azure AD 布建連接器。 您必須手動建立使用者帳戶，例如，將使用者直接新增至應用程式的系統管理入口網站，或上傳包含使用者帳戶詳細資料的試算表。 請參閱應用程式所提供的檔，或洽詢應用程式開發人員以判斷有哪些機制可供使用。

* 「自動」**** 表示已經為此應用程式開發 Azure AD 佈建連接器。 您應遵循設定應用程式布建的特定設定教學課程。 您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程。

在 Azure AD**資源**庫中，支援自動布建的應用程式是由布建圖示指定。 切換至新的資源庫預覽體驗，以查看這些圖示（在 [新增**應用程式] 頁面**頂端的橫幅中，選取 [**按一下這裡以試用全新和改良的應用程式資源庫**] 的連結）。

![應用程式資源庫中的布建圖示](./media/user-provisioning/browse-gallery.png)

將應用程式新增至您的**企業應用**程式之後，應用程式所支援的布建模式也會顯示**在 [布**建] 索引標籤上。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何對應用程式設定自動佈建？

針對資源庫中列出的預先整合應用程式，您可以使用逐步指引來設定自動布建。 請參閱[整合式資源庫應用程式的教學課程清單](../saas-apps/tutorial-list.md)。 下列影片示範如何設定 SalesForce 的自動使用者布建。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

對於支援 SCIM 2.0 的其他應用程式，請依照[建立 SCIM 端點和設定使用者布建一](use-scim-to-provision-users-and-groups.md)文中的步驟進行。


## <a name="related-articles"></a>相關文章

- [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
- [自訂使用者布建的屬性對應](customize-application-attributes.md)
- [撰寫屬性對應的運算式](../app-provisioning/functions-for-customizing-application-data.md)
- [使用者布建的範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [建立 SCIM 端點並設定使用者布建](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同步處理 API 概觀](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
