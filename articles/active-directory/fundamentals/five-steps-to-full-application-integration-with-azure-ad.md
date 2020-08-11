---
title: 將您的所有應用程式與 Azure AD 整合的五個步驟
description: 本指南說明如何將您的所有應用程式與 Azure AD 整合。 在每個步驟中，我們會說明此值，並提供將說明技術詳細資料的資源連結。
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057150"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>將您的所有應用程式與 Azure AD 整合的五個步驟

Azure Active Directory (Azure AD) 是 Microsoft 雲端式身分識別和存取管理服務。 Azure AD 提供安全的驗證和授權解決方案，讓客戶、合作夥伴和員工可以存取他們所需的應用程式。 有了 Azure AD [、條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)、[多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)、[單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)和[自動使用者](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)布建，讓身分識別和存取管理變得既簡單又安全。

如果您的公司有 Microsoft 365 訂用帳戶，您可能[已經使用](https://docs.microsoft.com/office365/enterprise/about-office-365-identity)Azure AD。 不過，Azure AD 可用於您的所有應用程式，而藉由[集中應用程式管理](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios)，您可以在整個應用程式組合中使用相同的身分識別管理功能、工具和原則。 這麼做會提供整合的解決方案，以改善安全性、降低成本、提高生產力，並讓您確保合規性。 而且您將可從遠端存取內部部署應用程式。

本指南說明如何將您的所有應用程式與 Azure AD 整合。 在每個步驟中，我們會說明此值，並提供將說明技術詳細資料的資源連結。 我們會依照我們建議的順序來顯示這些步驟。 不過，您可以跳到程式的任何部分，以開始使用任何為您增加最大值。

本主題的其他資源，包括深入的商務程式白皮書，可在我們的資源中找到，以將[應用程式遷移至 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) ] 頁面。

## <a name="1-use-azure-ad-for-new-applications"></a>1. 針對新的應用程式使用 Azure AD

首先，將焦點放在新取得的應用程式。 當您的公司開始使用新的應用程式時，請立即[將其新增至您的 Azure AD 租使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。 設定公司原則，以便將新的應用程式新增至 Azure AD 是貴組織中的標準實務。 這對現有的商務程式造成最小的干擾，並可讓您調查並證明您從整合應用程式得到的價值，而不需要變更人們在您的環境中執行商務的方式。

Azure Active Directory (Azure AD) 有一個資源庫，其中包含數千個預先整合的應用程式，可讓您輕鬆地開始使用。 您可以使用與熱門應用程式整合的逐步[教學](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)課程，[將資源庫應用程式新增至您的 Azure AD 組織](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)，例如：

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

此外，您還可以[整合不在資源庫中的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)，包括任何已存在於您組織中的應用程式，或來自不屬於 Azure AD 資源庫之廠商的任何協力廠商應用程式。 如果[您的應用程式](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing)不存在，您也可以將它新增至資源庫。

最後，您也可以整合您在內部開發的應用程式。 本指南的步驟5將涵蓋這項功能。

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. 判斷現有的應用程式使用方式並排定工作的優先順序

接下來，探索員工經常使用的應用程式，並將工作的優先順序設定為與 Azure AD 整合。

您可以從使用 Microsoft Cloud App Security&#39;[Cloud discovery 工具](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)開始探索和管理 &quot; &quot; 網路中的影子 IT (也就是不是由 IT 部門所管理的應用程式) 。 您可以[使用 Microsoft Defender Advanced 威脅防護 (ATP) ](https://docs.microsoft.com/cloud-app-security/wdatp-integration)來簡化和擴充探索程式。

此外，您可以使用 Azure 入口網站中的 [ [AD FS 應用程式活動] 報告](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)，探索組織中的所有 AD FS 應用程式、已登入他們的唯一使用者數目，以及與 Azure AD 整合的相容性。

一旦探索到您現有的環境之後，您會想要[建立一個方案](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)，並排定最高優先順序的應用程式來進行整合。 您可以要求引導此程式的一些範例問題如下：

- 最常使用哪些應用程式？
- 什麼是風險最高？
- 未來將會解除委任哪些應用程式，而不需要進行移動？
- 哪些應用程式需要保持內部部署，且無法移至雲端？

當您所有的應用程式都已整合，而且您不再依賴多個身分識別解決方案時，您將會看到最大的優點和成本節約。 不過，當您將逐步移至此目標時，您將會更輕鬆地進行身分識別管理，並提高安全性。 您想要使用這段時間來設定工作的優先順序，並決定適合您的情況。

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. 整合依賴其他身分識別提供者的應用程式

在探索過程中，您可能已經找到 IT 部門未追蹤的應用程式，這會讓您的資料和資源容易受到攻擊。 您可能也會有使用替代身分識別解決方案的應用程式，包括 Active Directory 同盟服務 (ADFS) 或其他身分識別提供者。 請考慮如何整合您的身分識別和存取管理，以節省成本並提高安全性。 減少您擁有的身分識別解決方案數目將會：

- 排除內部部署使用者布建和驗證的需求，以及針對相同服務的其他雲端身分識別提供者付費的授權費用，以節省您的成本。
- 透過較少的身分識別和存取管理程式中的冗余，降低系統管理負擔並提高安全性。
- 讓員工透過[MyApps 入口網站](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)，取得所需的所有應用程式的安全單一登入存取。
- 藉由增加從您的應用程式使用量取得的資料量，並將其優點延伸到新增的應用程式，來改善 Azure AD&#39;s [identity protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)相關服務的情報，例如條件式存取。

我們已發佈指引來管理整合應用程式與 Azure AD 的商務流程，包括您可用來讓商務和應用程式擁有者感知和感興趣的[海報](https://aka.ms/AppOnePager)和[簡報](https://aka.ms/AppGuideline)。 您可以使用自己的商標來修改這些範例，並在完成此程式時，透過公司入口網站、電子報或其他媒體將其發佈到您的組織。

最好的起點是評估您使用 Active Directory 同盟服務 (ADFS) 。 許多組織會使用 ADFS 來驗證 SaaS 應用程式、自訂企業營運應用程式，以及 Office 365 和 Azure AD 型應用程式：

![圖表顯示內部部署應用程式、企業營運應用程式、SaaS 應用程式，以及透過 Azure AD，Office 365 全都以點線連接到 Active Directory 和 AD FS。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

您可以使用 Azure AD 作為身分識別管理解決方案[的中心來取代 ADFS](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) ，藉此升級此設定。 這麼做可讓您的員工想要存取的每個應用程式都能登入，並透過[MyApps 入口網站](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)輕鬆地尋找其所需的任何商務應用程式，除了上述的其他優點以外。

![圖表顯示透過 Active Directory 和 AD FS、企業營運應用程式、SaaS 應用程式和 Office 365 的內部部署應用程式，全都以點線連接到 Azure Active Directory。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

一旦 Azure AD 成為中央識別提供者，您可以完全從 ADFS 切換，而不是使用同盟解決方案。 先前使用 ADFS 進行驗證的應用程式現在可以單獨使用 Azure AD。

![圖表顯示內部部署、企業營運應用程式、SaaS 應用程式和 Office 365，全都以點線連接到 Azure Active Directory。 Active Directory 和 AD FS 不存在。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

您也可以將使用不同雲端式識別提供者的應用程式遷移到 Azure AD。 您的組織可能會有多個身分識別存取管理 (IAM) 解決方案。 遷移至一個 Azure AD 基礎結構是降低對 IAM 授權的相依性 (內部部署或雲端) 和基礎結構成本的機會。 如果您已透過 M365 授權支付 Azure AD 費用，則不需要支付另一個 IAM 解決方案的額外成本。

## <a name="4-integrate-on-premises-applications"></a>4. 整合內部部署應用程式

傳統上，應用程式在連線到公司網路時只允許存取，因此保持安全。 不過，在日益連線的世界中，我們想要允許客戶、合作夥伴和/或員工的應用程式存取，而不論他們在世界中的何處。 [Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) 是 Azure AD 的一項功能，可將您現有的內部部署應用程式連線至 Azure AD，而不需要維護 edge server 或其他額外的基礎結構來執行此動作。

![圖表會顯示應用程式 Proxy 服務的實際運作。 使用者存取 " https://sales.contoso.com "，而其要求會透過 "" 重新導向至 https://sales-contoso.msappproxy.net 內部部署位址 "" Azure Active Directory http://sales](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

您可以使用[教學課程：在 Azure Active Directory 中新增內部部署應用程式以透過應用程式 proxy 進行遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)，以啟用應用程式 proxy，並將內部部署應用程式新增至您的 Azure AD 租使用者。

此外，您可以整合應用程式傳遞控制器（例如 F5 Big IP APM 或 Zscaler 私用存取）。 藉由將這些與 Azure AD 整合，您將可取得 Azure AD 的新式驗證和身分識別管理，以及合作夥伴產品的流量管理和安全性功能。 我們稱之為此解決方案[安全的混合式存取](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 如果您目前使用下列任何一項服務，我們會提供教學課程，逐步引導您如何將它們與 Azure AD 整合。

- [Akamai 企業應用程式存取 (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Citrix 應用程式提供控制器 (ADC () ](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)先前稱為 Citrix Netscaler) 
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. 整合開發人員所建立的應用程式

針對公司內建的應用程式，您的開發人員可以使用[Microsoft 身分識別平臺](https://docs.microsoft.com/azure/active-directory/develop/)來執行驗證和授權。 與平臺整合的應用程式與您的組合中的任何其他應用程式一樣，都是以 Azure AD 和管理方式進行[註冊](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)。

開發人員可以將平臺用於內部使用的應用程式和客戶面向的應用程式，並提供使用平臺的其他優點。 [Microsoft 驗證程式庫 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)，這是平臺的一部分，可讓開發人員啟用多重要素驗證等新式體驗，並使用安全性金鑰來存取其應用程式，而不需要自行執行。 此外，與 Microsoft 身分識別平臺整合的應用程式可以存取[Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) -統一的 API 端點，其中提供的 Microsoft 365 資料會描述組織中的生產力、身分識別和安全性模式。 開發人員可以使用此資訊來執行功能，以提高使用者的生產力。 例如，藉由識別使用者最近與之互動的人員，並在應用程式&#39;s UI 中呈現它們。

我們有一[系列影片](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX)，提供平臺的完整簡介，以及支援的語言和平臺中的[許多程式碼範例](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)。

## <a name="next-steps"></a>後續步驟

- [將應用程式移轉至 Azure Active Directory 的資源](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
