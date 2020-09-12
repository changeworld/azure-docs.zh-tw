---
title: 整合所有應用程式與 Azure AD 的五個步驟
description: 本指南說明如何將您的所有應用程式與 Azure AD 整合。 在每個步驟中，我們將說明此值，並提供可說明技術詳細資料的資源連結。
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: bbe813b46b41bf0e2b8bf9c6b3fe3e3e843204ed
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319741"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>整合所有應用程式與 Azure AD 的五個步驟

Azure Active Directory (Azure AD) 是 Microsoft 雲端式身分識別和存取管理服務。 Azure AD 提供安全的驗證和授權解決方案，讓客戶、合作夥伴和員工可以存取他們所需的應用程式。 使用 Azure AD、 [條件式存取](../conditional-access/overview.md)、 [多重要素驗證](../authentication/concept-mfa-howitworks.md)、 [單一登入](../hybrid/how-to-connect-sso.md)和 [自動使用者](../app-provisioning/user-provisioning.md) 布建，讓身分識別和存取管理變得簡單又安全。

如果您的公司有 Microsoft 365 訂用帳戶，您可能 [已經使用](/office365/enterprise/about-office-365-identity) Azure AD。 不過，Azure AD 可用於所有應用程式，並藉由 [集中應用程式管理](../manage-apps/common-scenarios.md) ，您可以在整個應用程式組合中使用相同的身分識別管理功能、工具和原則。 這樣做會提供整合的解決方案，以改善安全性、降低成本、提高生產力，並讓您確保符合規範。 您將可從遠端存取內部部署應用程式。

本指南說明如何將您的所有應用程式與 Azure AD 整合。 在每個步驟中，我們將說明此值，並提供可說明技術詳細資料的資源連結。 我們會依照建議的順序來顯示這些步驟。 不過，您可以跳到程式中的任何部分，開始使用任何為您增加最大價值的部分。

本主題的其他資源，包括深入的商務程式白皮書，可在我們的資源上找到，以將 [應用程式遷移至 Azure Active Directory](../manage-apps/migration-resources.md) 頁面。

## <a name="1-use-azure-ad-for-new-applications"></a>1. 針對新的應用程式使用 Azure AD

首先，將焦點放在新取得的應用程式。 當您的企業開始使用新的應用程式時，請立即 [將其新增至您的 Azure AD 租使用者](../manage-apps/add-application-portal.md) 。 設定公司原則，以便將新的應用程式新增至 Azure AD 是您組織中的標準作法。 這會對現有的商務程式造成最小干擾，並可讓您調查並證明您在整合應用程式時所得到的價值，而不需要變更人們目前在環境中的業務方式。

Azure Active Directory (Azure AD) 有一個資源庫，其中包含數千個預先整合的應用程式，可讓您輕鬆地開始使用。 您可以使用逐步[教學](../saas-apps/tutorial-list.md)課程[將資源庫應用程式新增至您的 Azure AD 組織](../manage-apps/add-application-portal.md)，以整合熱門的應用程式，例如：

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

此外，您可以 [整合不在資源庫中的應用程式](../manage-apps/view-applications-portal.md)，包括您組織中現有的任何應用程式，或任何協力廠商應用程式，而該廠商還不是 Azure AD 資源庫的一部分。 您也可以 [將應用程式新增至資源庫](../azuread-dev/howto-app-gallery-listing.md) （如果沒有的話）。

最後，您也可以整合您在內部開發的應用程式。 本指南的步驟5將討論這一點。

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. 判斷現有的應用程式使用方式並排定工作的優先順序

接下來，探索員工經常使用的應用程式，並設定工作的優先順序以將其與 Azure AD 整合。

您可以開始使用 Microsoft Cloud App Security&#39;的 [Cloud discovery 工具](/cloud-app-security/tutorial-shadow-it) 來探索及管理 &quot; &quot; 網路中的影子 IT (也就是不受 IT 部門) 管理的應用程式。 您可以 [使用 Microsoft Defender 進階威脅防護 (ATP) ](/cloud-app-security/wdatp-integration) 來簡化及擴充探索程式。

此外，您可以使用 Azure 入口網站中的 [AD FS 應用程式活動報告](../manage-apps/migrate-adfs-application-activity.md) ，探索組織中的所有 AD FS 應用程式、已登入他們的唯一使用者數目，以及與 Azure AD 整合的相容性。

一旦探索到您現有的環境之後，您會想要 [建立方案](../manage-apps/migration-resources.md) ，並優先處理最高優先順序的應用程式以進行整合。 您可以要求引導此程式的一些範例問題如下：

- 最常使用的應用程式為何？
- 風險最高是什麼？
- 未來將會解除委任哪些應用程式，而不需要進行移動？
- 哪些應用程式需要保持內部部署且無法移至雲端？

當您所有的應用程式都已整合且您不再依賴多個身分識別解決方案時，您會看到最大的好處和成本節約。 但是，當您將逐步移至此目標時，您將會遇到更容易的身分識別管理和安全性提升。 您想要使用此時間來排定工作的優先順序，並決定對您的情況有何意義。

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. 整合依賴其他身分識別提供者的應用程式

在探索過程中，您可能找不到 IT 部門正在追蹤的應用程式，讓您的資料和資源容易受到攻擊。 您也可能有使用替代身分識別解決方案的應用程式，包括 Active Directory 同盟服務 (ADFS) 或其他身分識別提供者。 考慮如何整合您的身分識別與存取管理，以節省成本並提高安全性。 減少您所擁有的身分識別解決方案數目：

- 免除內部部署使用者布建和驗證的需求，以及針對相同服務支付其他雲端身分識別提供者的授權費用，以節省成本。
- 在您的身分識別與存取管理程式中，降低系統管理的額外負荷，並讓安全性更緊密。
- 讓員工可以透過 [MyApps 入口網站](../manage-apps/access-panel-collections.md)，取得所有所需應用程式的安全單一登入存取權。
- 藉由增加應用程式使用量所取得的資料量，改善 Azure AD&#39;s [identity protection](../identity-protection/overview-identity-protection.md) 相關服務的智慧，並將其優點延伸至新增的應用程式。

我們已發佈用來管理將應用程式與 Azure AD 整合之商務程式的指引，包括您可以用來讓商務和應用程式擁有者感知和感興趣的 [海報](https://aka.ms/AppOnePager) 和 [簡報](https://aka.ms/AppGuideline) 。 您可以使用自己的商標來修改這些範例，並在完成此程式時，透過公司入口網站、電子報或其他媒體將這些範例發佈到您的組織。

最好的起點是評估您使用 Active Directory 同盟服務 (ADFS) 。 許多組織會使用 ADFS 進行 SaaS 應用程式、自訂企業營運應用程式，以及 Office 365 和 Azure AD 型應用程式的驗證：

![圖表會顯示內部部署應用程式、企業營運應用程式、SaaS 應用程式，以及透過 Azure AD，Office 365 全都以虛線連接到 Active Directory 和 AD FS。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

您可以使用 Azure AD 作為身分識別管理解決方案 [的中心，將 ADFS 取代為](../manage-apps/migrate-adfs-apps-to-azure.md) 更新此設定。 這樣做可讓您的員工想要存取的每個應用程式都能登入，並讓員工可以透過 [MyApps 入口網站](../user-help/my-apps-portal-end-user-access.md)輕鬆找到所需的任何商務應用程式，以及上述的其他權益。

![圖表會顯示內部部署應用程式，透過 Active Directory 和 AD FS、企業營運應用程式、SaaS 應用程式和 Office 365 全都以虛線連接到 Azure Active Directory。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

一旦 Azure AD 成為中央身分識別提供者之後，您就可以完全從 ADFS 切換，而不是使用同盟的解決方案。 先前使用 ADFS 進行驗證的應用程式現在可以單獨使用 Azure AD。

![圖表顯示內部部署、企業營運應用程式、SaaS 應用程式和 Office 365 全都以虛線連接到 Azure Active Directory。 Active Directory 和 AD FS 不存在。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

您也可以將使用不同雲端式識別提供者的應用程式遷移至 Azure AD。 您的組織可能會有多個身分識別存取管理 (IAM) 解決方案。 遷移至一個 Azure AD 基礎結構，可讓您降低 (內部部署或雲端) 和基礎結構成本中的 IAM 授權相依性。 如果您可能已透過 M365 授權支付 Azure AD 的費用，就沒有理由支付另一個 IAM 解決方案的額外成本。

## <a name="4-integrate-on-premises-applications"></a>4. 整合內部部署應用程式

傳統上，應用程式只在連線到公司網路時允許存取，因此保持安全。 不過，在日益普及的世界中，我們想要允許客戶、合作夥伴及/或員工的應用程式存取，不論他們在世界各地的何處。 [Azure AD 應用程式 Proxy](../manage-apps/what-is-application-proxy.md) (AppProxy) 是 Azure AD 的一項功能，可將您現有的內部部署應用程式連線到 Azure AD，而不需要維護 edge server 或其他額外的基礎結構來執行這項操作。

![圖表會顯示作用中的應用程式 Proxy 服務。 使用者存取 " https://sales.contoso.com "，其要求會透過 " https://sales-contoso.msappproxy.net " 在 Azure Active Directory 至內部部署位址 "" 中重新 http://sales 導向](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

您可以使用 [教學課程：在 Azure Active Directory 中透過應用程式 Proxy 新增內部部署應用程式以進行遠端存取](../manage-apps/application-proxy-add-on-premises-application.md) ，以啟用應用程式 proxy，並將內部部署應用程式新增至您的 Azure AD 租使用者。

此外，您可以整合應用程式傳遞控制器，例如 F5 大型 IP APM 或 Zscaler 私用存取。 藉由將這些與 Azure AD 整合，您就可以取得 Azure AD 的新式驗證和身分識別管理，以及合作夥伴產品的流量管理和安全性功能。 我們稱之為解決方案 [安全的混合式存取](../manage-apps/secure-hybrid-access.md)。 如果您目前使用下列任何服務，我們會提供教學課程，逐步引導您瞭解如何將它們與 Azure AD 整合。

- [Akamai 企業應用程式存取 (EAA)](../saas-apps/akamai-tutorial.md)
- [Citrix 應用程式提供控制器 (ADC) ](../saas-apps/citrix-netscaler-tutorial.md) (先前稱為 Citrix Netscaler) 
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. 整合開發人員建立的應用程式

針對公司內建的應用程式，您的開發人員可以使用 [Microsoft 身分識別平臺](../develop/index.yml) 來執行驗證和授權。 與平臺整合的應用程式，可向 [Azure AD](../develop/quickstart-register-app.md) 登錄，並如同您組合中的任何其他應用程式一樣進行管理。

開發人員可以將平臺用於內部使用的應用程式和麵向客戶的應用程式，而且有其他使用平臺的優點。 [Microsoft 驗證程式庫 (MSAL) ](../develop/msal-overview.md)（屬於平臺的一部分）可讓開發人員啟用新式體驗，例如多重要素驗證，以及使用安全性金鑰來存取其應用程式，而不需要自行執行。 此外，與 Microsoft 身分識別平臺整合的應用程式可以存取 [Microsoft Graph](../develop/microsoft-graph-intro.md) -統一的 API 端點，提供描述組織中生產力、身分識別和安全性模式的 Microsoft 365 資料。 開發人員可以使用此資訊來執行功能，以提升使用者的生產力。 例如，藉由識別使用者最近與之互動的使用者，並在應用程式&#39;的 UI 中加以呈現。

我們有一個 [影片系列](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) ，可提供平臺的完整簡介，以及支援的語言和平臺的 [許多程式碼範例](../develop/sample-v2-code.md) 。

## <a name="next-steps"></a>接下來的步驟

- [將應用程式移轉至 Azure Active Directory 的資源](../manage-apps/migration-resources.md)