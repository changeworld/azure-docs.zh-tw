---
title: 什麼是 Azure Active Directory？ - Azure Active Directory | Microsoft Docs
description: 關於 Azure Active Directory 的概觀和概念性資訊，包括術語、可用的授權，以及具有詳細資訊連結的相關功能清單。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd7b412e99526935738c2494d31a16fded7101a6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215806"
---
# <a name="what-is-azure-active-directory"></a>什麼是 Azure Active Directory？

Azure Active Directory (Azure AD) 是 Microsoft 的雲端式身分識別和存取管理服務，可協助員工登入及存取以下資源：

- 外部資源，例如 Microsoft Office 365、Azure 入口網站和其他數千個 SaaS 應用程式。

- 內部資源，例如公司網路和內部網路上的應用程式，以及您自己的組織所開發的任何雲端應用程式。

您可以使用各種 [Microsoft Cloud for Enterprise Architects 系列](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity)的海報，來深入了解 Azure、Azure AD 和 Office 365 中的核心識別服務。

## <a name="who-uses-azure-ad"></a>誰會使用 Azure AD？

Azure AD 的適用對象是：

- **IT 系統管理員。** 身為 IT 系統管理員，您可以使用 Azure AD，根據業務需求來控制應用程式和應用程式資源的存取權。 例如，您可以使用 Azure AD，來要求在存取組織的重要資源時必須進行多重要素驗證。 此外，您也可以使用 Azure AD，自動地在現有 Windows Server AD 和雲端應用程式 (包括 Office 365) 之間佈建使用者。 最後，Azure AD 可提供功能強大的工具，以便自動地協助保護使用者的身分識別和認證，以及符合存取治理需求。 若要開始作業，請註冊[免費的 30 天 Azure Active Directory Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。

- **應用程式開發人員。** 身為應用程式開發人員，您可以使用 Azure AD 作為標準方法，將單一登入 (SSO) 新增至應用程式，讓它與使用者預先存在的認證搭配運作。 Azure AD 也會提供 API，來協助您建置會使用現有組織資料的個人化應用程式體驗。 若要開始作業，請註冊[免費的 30 天 Azure Active Directory Premium 試用版](https://azure.microsoft.com/trial/get-started-active-directory/)。 如需詳細資訊，您也可以參閱[適用於開發人員的 Azure Active Directory](../develop/index.yml)。

- **Microsoft 365、Office 365、Azure 或 Dynamics CRM Online 訂閱者。** 身為訂閱者，您早已在使用 Azure AD。 每個 Microsoft 365、Office365、Azure 和 Dynamics CRM Online 租用戶都會自動成為 Azure AD 租用戶。 您立即就可以開始管理整合式雲端應用程式的存取權。

## <a name="what-are-the-azure-ad-licenses"></a>什麼是 Azure AD 授權？

Office 365 或 Microsoft Azure 等 Microsoft Online 業務服務需要 Azure AD 才能登入，並可用來協助進行身分識別保護。 如果您訂閱任何 Microsoft Online 業務服務，您就會自動取得 Azure AD 並可存取所有免費的功能。

若要增強您的 Azure AD 實作，您也可以升級至 Azure Active Directory Premium P1 或 Premium P2 授權來新增付費功能。 Azure AD 付費授權會建立在您現有的免費目錄上，提供適用於行動使用者的自助服務、增強監視、安全性報告及安全存取。

>[!Note]
>這兩種授權的詳細價格請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。
>
>目前在中國不支援 Azure Active Directory Premium P1 和 Premium P2。 如需有關 Azure AD 定價的詳細資訊，請透過 [Azure Active Directory 論壇](https://azure.microsoft.com/support/community/?product=active-directory)與我們連絡。

- **Azure Active Directory Free。** 提供跨 Azure、Office 365 和許多熱門 SaaS 應用程式的使用者和群組管理、內部部署目錄同步作業、基本報告、雲端使用者的自助式密碼變更和單一登入。

- **Azure Active Directory Premium P1。** 除了 Free 版的功能外，P1 版還會讓混合式使用者可以同時存取內部部署和雲端的資源。 不僅如此，它還支援進階的管理 (例如，動態群組、自助群組管理、Microsoft Identity Manager (此為內部部署身分識別和存取管理套件))，以及雲端回寫功能 (可讓內部部署使用者使用自助密碼重設)。

- **Azure Active Directory Premium P2。** 除了 Free 版和 P1 版的功能外，P2 版還會提供 [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) (以協助針對應用程式和重要的公司資料提供以風險為基礎的條件式存取權) 以及 [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) (以協助探索、限制和監視系統管理員及其對資源的存取權，以及視需要提供 Just-In-Time 存取權)。

- **「隨用隨付方案」功能授權。** 您也可以取得其他功能授權，例如 Azure Active Directory 企業對消費者 (B2C)。 B2C 可協助您提供適用於消費者面向應用程式的身分識別和存取管理解決方案。 如需詳細資訊，請參閱 [Azure Active Directory B2C 文件](../../active-directory-b2c/index.yml)。

如需如何讓 Azure 訂用帳戶與 Azure AD 建立關聯的詳細資訊，請參閱[如何：關聯或新增 Azure 訂用帳戶到 Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)，如需如何將授權指派給使用者的詳細資訊，請參閱[做法：指派或移除 Azure Active Directory 授權](license-users-groups.md)。

## <a name="terminology"></a>詞彙

若要深入了解 Azure AD 及其文件，建議檢閱下列詞彙。

|詞彙或概念|描述|
|---------------|-----------|
|身分識別| 可以獲得驗證的項目。 身分識別可以是具有使用者名稱和密碼的使用者。 身分識別也包含需要透過祕密金鑰或憑證進行驗證的應用程式或其他伺服器。|
|帳戶| 具有相關聯資料的身分識別。 您無法擁有沒有身分識別的帳戶。|
|Azure AD 帳戶| 透過 Azure AD 或其他 Microsoft 雲端服務 (例如 Office 365) 所建立的身分識別。 身分識別會儲存在 Azure AD 中，並可供組織的雲端服務訂用帳戶來存取。 此帳戶有時也稱為公司或學校帳戶。|
|Azure 訂用帳戶| 用來支付 Azure 雲端服務費用。 您可以擁有許多訂用帳戶，而它們都會與信用卡連結。|
|Azure 租用戶| 組織在註冊 Microsoft 雲端服務訂用帳戶 (例如 Microsoft Azure、Microsoft Intune 或 Office 365) 時，所自動建立的專用且受信任 Azure AD 執行個體。 一個 Azure 租用戶代表一個組織。|
|單一租用戶| 如果 Azure 租用戶會存取專用環境中的其他服務，便可將其視為單一租用戶。|
|多租用戶| 如果 Azure 租用戶會存取多個組織所共用環境中的其他服務，便可將其視為多租用戶。|
|Azure AD 目錄|每個 Azure 租用戶都有專用且受信任的 Azure AD 目錄。 Azure AD 目錄包含租用戶的使用者、群組和應用程式，並可用來對租用戶資源執行身分識別和存取管理功能。|
|自訂網域|每個新的 Azure AD 目錄皆隨附初始網域名稱 (domainname.onmicrosoft.com)。 除了初始名稱外，您也可以在清單中新增組織的網域名稱，其中的名稱除了可供營運使用，亦可供使用者用來存取組織的資源。 新增自訂網域名稱可協助您建立使用者熟悉的使用者名稱，例如 alain@contoso.com。|
|帳戶管理員|在概念上，這個傳統的訂用帳戶系統管理員角色是訂用帳戶的計費擁有者。 這個角色可存取 [Azure 帳戶中心](https://account.azure.com/Subscriptions)，並可讓您管理帳戶中的所有訂用帳戶。 如需詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure 角色型存取控制 (RBAC) 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。|
|服務管理員|這個傳統的訂用帳戶系統管理員角色可讓您管理所有 Azure 資源，包括存取權。 這個角色所具有的存取權，與在訂用帳戶範圍獲派擁有者角色的使用者相同。 如需詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。|
|擁有者|這個角色可協助您管理所有 Azure 資源，包括存取權。 這個角色建置在較新的授權系統 (稱為角色型存取控制 (RBAC)) 上，可讓您以更細微的方式管理 Azure 資源的存取權。 如需詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。|
|Azure AD 全域系統管理員|這個系統管理員角色會自動指派給 Azure AD 租用戶的建立者。 全域系統管理員可以針對 Azure AD 和任何與 Azure AD 同盟的服務 (例如，Exchange Online、SharePoint Online 和商務用 Skype Online)，執行所有系統管理功能。 您可以有多個全域系統管理員，但只有全域系統管理員可以對使用者指派系統管理員角色 (包括指派其他全域系統管理員)。<br><br>**注意**<br>在 Azure 入口網站中，這個系統管理員角色稱為全域系統管理員，但在 Microsoft Graph API 和 Azure AD PowerShell 中，則稱為**公司系統管理員**。<br><br>如需各種系統管理員角色的詳細資訊，請參閱 [Azure Active Directory 中的系統管理員角色權限](../users-groups-roles/directory-assign-admin-roles.md)。|
|Microsoft 帳戶 (也稱為 MSA)|此為個人帳戶，可讓您存取消費者導向的 Microsoft 產品和雲端服務，例如 Outlook、OneDrive、Xbox LIVE 或 Office 365。 Microsoft 帳戶會建立並儲存在 Microsoft 所執行的 Microsoft 取用者身分識別帳戶系統中。|

## <a name="which-features-work-in-azure-ad"></a>Azure AD 中有哪些可用功能？

在選擇 Azure AD 授權後，您便可存取組織可用的下列功能 (部分或全部)：

|類別|描述|
|-------|-----------|
|應用程式管理|使用應用程式 Proxy、單一登入、「我的 app」入口網站 (也稱為「存取面板」) 和軟體即服務 (SaaS) 應用程式，來管理雲端和內部部署應用程式。 如需詳細資訊，請參閱[如何為內部部署應用程式提供安全的遠端存取](../manage-apps/application-proxy.md)和[應用程式管理文件](../manage-apps/index.yml)。|
|驗證|管理 Azure Active Directory 自助式密碼重設、Multi-Factor Authentication、自訂的禁用密碼清單與智慧鎖定。 如需詳細資訊，請參閱 [Azure AD 驗證文件](../authentication/index.yml)。|
|企業對企業 (B2B)|在管理來賓使用者和外部合作夥伴的同時，持續掌控住您自己的公司資料。 如需詳細資訊，請參閱 [Azure Active Directory B2B 文件](../b2b/index.yml)。|
|企業對消費者 (B2C)|自訂和控制使用者在使用應用程式時，要如何註冊、登入和管理其設定檔。 如需詳細資訊，請參閱 [Azure Active Directory B2C 文件](../../active-directory-b2c/index.yml)。|
|條件式存取|管理雲端應用程式的存取權。 如需詳細資訊，請參閱 [Azure AD 條件式存取文件](../conditional-access/index.yml)。|
|開發人員適用的 Azure Active Directory|建置應用程式來登入所有 Microsoft 身分識別、取得權杖來呼叫 Microsoft Graph、其他 Microsoft API 或自訂 API。 如需詳細資訊，請參閱 [Microsoft 身分識別平台 (適用於開發人員的 Azure Active Directory)](../develop/index.yml)。|
|裝置管理|管理雲端或內部部署裝置存取公司資料的方式。 如需詳細資訊，請參閱 [Azure AD 裝置管理文件](../devices/index.yml)。|
|網域服務|在不使用網域控制站的情況下，將 Azure 虛擬機器加入網域中。 如需詳細資訊，請參閱 [Azure AD Domain Services 文件](../../active-directory-domain-services/index.yml)。|
|企業使用者|管理授權指派、應用程式的存取權，並使用群組和系統管理員角色來設定委派。 如需詳細資訊，請參閱 [Azure Active Directory 使用者管理文件](../users-groups-roles/index.yml)。|
|混合式身分識別|使用 Azure Active Directory Connect 和 Connect Health 提供單一使用者身分識別，以便對不同位置 (不論是雲端還是內部部署) 的所有資源執行驗證和授權程序。 如需詳細資訊，請參閱[混合式身分識別文件](../hybrid/index.yml)。|
|身分識別治理|管理組織的身分識別，從員工、業務合作夥伴、廠商、服務到應用程式的存取控制。 您也可以執行存取權檢閱。 如需詳細資訊，請參閱 [Azure AD Identity Governance 文件](../governance/identity-governance-overview.md)和 [Azure AD 存取權檢閱](../governance/access-reviews-overview.md)。|
|身分識別保護|偵測會影響組織身分識別的潛在弱點、設定用來回應可疑動作的原則，然後採取適當動作來加以解決。 如需詳細資訊，請參閱 [Azure AD Identity Protection](../identity-protection/index.yml)。|
|適用於 Azure 資源的受控識別|在 Azure AD 中，為 Azure 服務提供受到自動管理的身分識別，以供其用來驗證 Azure AD 所支援的驗證服務，包括 Key Vault。 如需詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](../managed-identities-azure-resources/overview.md)。|
|Privileged Identity Management (PIM)|管理、控制及監視組織內的存取。 這個功能包括存取 Azure AD 和Azure 中的資源，以及其他 Microsoft Online Services (如 Office 365 或 Intune)。 如需詳細資訊，請參閱 [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml)。|
|報告和監視|深入了解環境中的安全性和使用模式。 如需詳細資訊，請參閱 [Azure Active Directory 報告和監視](../reports-monitoring/index.yml)。|

## <a name="next-steps"></a>後續步驟

- [註冊 Azure Active Directory Premium](active-directory-get-started-premium.md)

- [將 Azure 訂用帳戶關聯至 Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [存取 Azure Active Directory 和建立新的租用戶](active-directory-access-create-new-tenant.md)

- [Azure Active Directory Premium P2 功能部署檢查清單](active-directory-deployment-checklist-p2.md)
