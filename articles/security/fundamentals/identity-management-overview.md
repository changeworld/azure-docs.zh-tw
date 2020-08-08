---
title: 協助進行身分識別管理的 Azure 安全性功能 | Microsoft Docs
description: 瞭解可協助進行身分識別管理的核心 Azure 安全性功能。 請參閱單一登入和反向 proxy 等主題的相關資訊。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: c9483363dcf6b4a5c05d71daa75d680dd25b8308
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002679"
---
# <a name="azure-identity-management-security-overview"></a>Azure 身分識別管理安全性概觀

 身分識別管理即為驗證與授權[安全性主體](/windows/security/identity-protection/access-control/security-principals)的流程。 其中還包含該主體 (識別身分) 資訊的控管。  (身分識別) 的安全性主體可能包括服務、應用程式、使用者、群組等。Microsoft 身分識別與存取管理解決方案可協助 IT 保護整個企業資料中心和雲端中的應用程式和資源存取。 這類保護會啟用其他層級的驗證，例如多重要素驗證和條件式存取原則。 透過進階的安全性報告、稽核和警示來監視可疑活動，有助於緩解潛在的安全性問題。 [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) 可以提供數千個雲端軟體即服務 (SaaS) 應用程式的單一登入 (SSO) 及存取您在內部部署執行的 Web 應用程式。
 
善用 Azure Active Directory (Azure AD) 的安全性優點，您便可以：

* 為您的混合式企業中的每個使用者建立和管理單一身分識別，讓使用者、群組和裝置保持同步。 
* 提供您的應用程式 (包括數千個預先整合的 SaaS 應用程式) 的 SSO 存取。
* 藉由同時對內部部署和雲端應用程式強制以規則為基礎的 Multi-Factor Authentication，啟用應用程式存取安全性。
* 透過 Azure AD 應用程式 Proxy 佈建對內部部署 Web 應用程式的安全遠端存取。

本文的目的是對協助進行身分識別管理的 Azure 安全性功能提供核心的概觀。 我們也會提供文章的連結，更詳細說明每個功能，以讓您深入了解。  

本文著重於下列核心 Azure 身分識別管理功能︰

* 單一登入
* 反向 proxy
* 多重要素驗證
* Azure 角色型存取控制 (Azure RBAC) 
* 安全性監視、警示以及機器學習服務型報告
* 消費者身分識別與存取管理
* 裝置註冊
* Privileged Identity Management
* 身分識別保護
* 混合式身分識別管理/Azure AD connect
* Azure AD 存取權檢閱

## <a name="single-sign-on"></a>單一登入

SSO 表示只要使用單一使用者帳戶登入，就能夠存取所有執行業務所需的應用程式和資源。 登入之後，您可以存取所有需要的應用程式，而不需要再驗證一次 (例如，輸入密碼)。

許多組織依賴 SaaS 應用程式，例如 Office 365、Box 和 Salesforce 來提升使用者生產力。 在過去，IT 人員必須在每個 SaaS 應用程式中個別建立並更新使用者帳戶，使用者則必須記住每個 SaaS 應用程式的密碼。

Azure AD 將內部部署的 Active Directory 環境延伸到雲端，讓使用者不只能夠使用主要的組織帳戶登入加入網域的裝置和公司資源，也能登入作業所需的所有 Web 和 SaaS 應用程式。

不只使用者不需要管理多組使用者名稱和密碼，還可根據自身的組織群組，以及其員工的狀態，自動佈建或解除佈建其應用程式的存取權。 Azure AD 引進了安全性和存取管理控制措施，可讓您集中管理所有 SaaS 應用程式的使用者存取權。

深入了解：

* [單一登入總覽](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [應用程式管理的快速入門系列](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>反向 proxy

Azure AD 應用程式 Proxy 可讓您在私人網路內發佈內部部署應用程式 (例如 [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) 網站、[Outlook Web 應用程式](https://technet.microsoft.com/library/jj657718.aspx)和 [IIS](https://www.iis.net/) 型應用程式)，並提供網路外部使用者的安全存取。 應用程式 Proxy 不僅針對多種內部部署 Web 應用程式提供遠端存取與 SSO，同時還提供 Azure AD 支援的數千個 SaaS 應用程式。 員工可以從家裡使用自己的裝置登入您的應用程式，還可透過這個雲端 Proxy 進行驗證。

深入了解：

* [啟用 Azure AD 應用程式 Proxy](/azure/active-directory/manage-apps/application-proxy-enable)
* [使用 Azure AD 應用程式 Proxy 發佈應用程式](/azure/active-directory/active-directory-application-proxy-publish)
* [使用應用程式 Proxy 進行單一登入](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [使用條件式存取](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>多重要素驗證

Azure Multi-Factor Authentication 是需要使用多種驗證方法，並在使用者登入和交易中新增重要的第二層安全性的驗證方法。 Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時滿足使用者對簡單登入程序的需求。 它可以透過一些驗證選項：例如電話、文字訊息，或行動應用程式通知或驗證代碼，以及第三方 OAuth 權杖，來提供強大的驗證功能。

深入了解：

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [什麼是 Azure Multi-Factor Authentication？](/azure/active-directory/authentication/multi-factor-authentication)
* [Azure Multi-Factor Authentication 的作用](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Azure RBAC

Azure RBAC 是以 Azure Resource Manager 為基礎的授權系統，可提供更細緻的 Azure 資源存取管理。 Azure RBAC 可讓您更精確地控制使用者擁有的存取層級。 舉例來說，您可以限制讓一個使用者只能管理虛擬網路，而另一個使用者只能管理一個資源群組中的資源。 Azure 包含數個供您使用的內建角色。 以下列出四個基本內建角色。 前三個適用於所有資源類型。

- [擁有者](/azure/role-based-access-control/built-in-roles#owner) - 具有所有資源的完整存取權，包括將存取權委派給其他人的權限。 
- [參與者](/azure/role-based-access-control/built-in-roles#contributor) - 可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。
- [讀者](/azure/role-based-access-control/built-in-roles#reader) - 可以檢視現有的 Azure 資源。
- [使用者存取管理員](/azure/role-based-access-control/built-in-roles#user-access-administrator) - 讓您管理使用者對 Azure 資源的存取權。

深入了解：

* [什麼是 Azure 角色型存取控制 (Azure RBAC)？](/azure/role-based-access-control/overview)
* [Azure 內建角色](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>安全性監視、警示以及機器學習服務型報告

安全性監視、警示以及機器學習式報告，會識別不一致的存取模式，可以協助您保護企業安全。 您可以使用 Azure AD 的存取和使用情況報告來了解貴組織的目錄完整性和安全性。 利用此資訊，目錄管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。

在 Azure 入口網站中，報告會按照下列類別加以區分：

* **異常報告**：包含我們發現異常的登入事件。 我們的目標在於使您注意這類活動，並讓您決定事件是否可疑。
* **整合式應用程式報告**：可供深入了解雲端應用程式在組織中的使用方式。 Azure AD 提供與數千個雲端應用程式的整合。
* **錯誤報告**：指出將帳戶佈建至外部應用程式時可能發生的錯誤。
* **使用者特定報告**：顯示特定使用者的裝置登入活動資料。
* **活動記錄**：包含過去 24 小時、過去 7 天或過去 30 天內所有稽核事件的記錄，以及群組活動變更、密碼重設和登錄活動。

深入了解：

* [查看您的存取和使用方式報表](/azure/active-directory/active-directory-view-access-usage-reports)
* [開始使用 Azure Active Directory 報告](/azure/active-directory/active-directory-reporting-getting-started)
* [Azure Active Directory 報告指南](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>消費者身分識別與存取管理

Azure AD B2C 是高可用性的全域身分識別管理服務，可用於處理數億個身分識別的消費者端應用程式。 此服務可跨行動及 Web 平台進行整合。 可自訂的使用經驗讓您的消費者可以使用其現有的社交帳戶，或是建立新的認證來登入您所有的應用程式。

在過去，應用程式開發人員若想要註冊並讓客戶登入其應用程式，他們會編寫自己的程式碼。 而且他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。 Azure AD B2C 為您的組織提供更理想的作法，透過安全且以標準為基礎的平台以及更大的可延伸原則組合，協助將取用者身分識別管理整合至應用程式。

當您使用 Azure AD B2C 時，您的取用者可以使用現有的社交帳戶 (Facebook、Google、Amazon、LinkedIn) 註冊應用程式，或是建立新的認證 (電子郵件地址與密碼，或使用者名稱與密碼)。

深入了解：

* [什麼是 Azure Active Directory B2C？](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C 預覽：在您的應用程式中註冊與登入取用者](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C 預覽：應用程式類型](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>裝置註冊

Azure AD 裝置註冊是裝置型[條件式存取](/azure/active-directory/active-directory-conditional-access-device-registration-overview)案例的基礎。 當裝置已註冊時，Azure AD 裝置註冊會在使用者登入時，對裝置提供用來驗證裝置的身分識別。 接著，您可以使用已驗證的裝置和裝置的屬性，為裝載于雲端和內部部署的應用程式強制執行條件式存取原則。

與 Intune 這類的行動裝置管理解決方案結合時，將會以裝置的其他相關資訊更新 Azure AD 中的裝置屬性。 接著，您可以建立條件式存取規則，以強制從裝置存取，以符合您的安全性和合規性標準。

深入了解：

* [開始使用 Azure AD 裝置註冊](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [自動向 Azure AD 註冊加入網域的 Windows 裝置](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [設定讓已加入網域的 Windows 裝置自動向 Azure AD 註冊](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

有了 Azure AD Privileged Identity Management ，您便可管理、控制和監視特殊權限身分識別，以及存取 Azure AD 和 Office 365 與 Microsoft Intune 等其他 Microsoft Online 服務的資源。

使用者有時候需要在 Azure 或 Office 365 資源或其他 SaaS 應用程式中執行特殊權限的作業。 這類需求通常表示組織必須授與使用者永久的 Azure AD 特殊權限存取權。 這種存取權會提高雲端資源的安全性風險，因為組織無法滴水不漏地監視這些使用者利用其管理員權限的所作所為。 此外，如果具特殊存取權限的使用者帳戶遭到入侵，這個缺口就會影響組織的整體雲端安全性。 Azure AD Privileged Identity Management 有助於降低此風險。

有了 Azure AD Privileged Identity Management，您便可以：

* 查看哪些使用者是 Azure AD 管理員。
* 視需要啟用 Office 365 和 Intune 等 Microsoft 服務的 just-in-time (JIT) 系統管理存取權限。
* 取得有關系統管理員存取記錄與系統管理員指派變更的報告。
* 取得有關特殊權限角色存取的警示。

深入了解：

* [什麼是 Azure AD Privileged Identity Management？](../../active-directory/privileged-identity-management/pim-configure.md)
* [在 PIM 中指派 Azure AD 目錄角色](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>身分識別保護

Azure AD Identity Protection 是一項安全性服務，可為風險偵測和潛在弱點（會影響組織的身分識別）提供匯總的觀點。 Identity Protection 會利用現有的 Azure AD 異常偵測功能，這些功能可透過 Azure AD 異常活動報告取得。 Identity Protection 也引進了可即時偵測異常的新風險偵測類型。

深入了解：

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [第 9 頻道：Azure AD 和身分識別展示：Identity Protection 預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>混合式身分識別管理/Azure AD connect

Microsoft 的身分識別解決方案可跨越內部部署和雲端架構功能，建立單一使用者身分識別以用於所有資源的驗證和授權，不論位於何處。 我們稱之為混合式身分識別。 Azure AD Connect 是一種 Microsoft 工具，其設計目的是要符合並完成混合式身分識別的目標。 這可讓您為與 Azure AD 整合之 Office 365、Azure 和 SaaS 應用程式的使用者提供通用身分識別。 它可提供下列功能：

* Synchronization
* AD FS 和同盟整合
* 通過驗證
* 健康狀態監視

深入了解：

* [混合式身分識別白皮書](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD 小組部落格](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD 存取權檢閱

Azure Active Directory (Azure AD) 存取權檢閱可讓組織有效地管理群組成員資格、對企業應用程式的存取，以及特殊權限角色指派。

深入了解：

* [Azure AD 存取權檢閱](../../active-directory/governance/access-reviews-overview.md)
* [使用 Azure AD 存取權檢閱來管理使用者存取權](../../active-directory/governance/access-reviews-overview.md)
