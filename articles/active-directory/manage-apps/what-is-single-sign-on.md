---
title: 單一登入應用程式 - Azure Active Directory | Microsoft Docs
description: 了解在 Azure Active Directory (Azure AD) 中設定應用程式時，如何選擇單一登入方法。 使用單一登入使用者就不需要記住每個應用程式的密碼，且可以簡化帳戶管理。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24a4209869d4c47f8ac73e250699ec55d006296
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253399"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory 中的單一登入應用程式

當使用者登入 Azure Active Directory (Azure AD) 中的應用程式時，單一登入 (SSO) 可增加安全性及便利性。 本文描述單一登入方法，並協助您在設定應用程式時，選擇最適當的 SSO 方法。

- **使用單一登入**，使用者使用一個帳戶登入一次，就能存取已加入網域的裝置、公司資源、軟體即服務 (SaaS) 應用程式和 Web 應用程式。 登入之後，使用者可以從 Office 365 入口網站或 Azure AD MyApps 存取面板啟動應用程式。 系統管理員可將使用者帳戶集中管理，並根據群組成員資格自動新增或移除使用者的應用程式存取權。

- **沒有單一登入**，使用者必須記住應用程式特定的密碼並登入每個應用程式。 IT 人員需要針對每個應用程式 (如 Office 365、Box 和 Salesforce) 建立及更新使用者帳戶。 使用者需要記住其密碼，還要花費時間登入每個應用程式。

## <a name="choosing-a-single-sign-on-method"></a>選擇單一登入方法

有幾種方式可以為應用程式設定單一登入。 選擇單一登入方法會取決於為應用程式設定的驗證方式。

- 雲端應用程式可以使用OpenID Connect、OAuth、SAML、密碼式、已連結或已停用方法進行單一登入。 
- 內部部署應用程式可以使用密碼式、整合式 Windows 驗證、標頭式、已連結或已停用方法進行單一登入。 當應用程式已設定應用程式 Proxy 時，內部部署選擇就可運作。

此流程圖可協助您決定哪一種單一登入方法最適合您的情況。

![單一登入方法的決策流程圖](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

下表摘要說明單一登入方法，並提供更多詳細資料的連結。

| 單一登入方法 | 應用程式類型 | 使用時機 |
| :------ | :------- | :----- |
| [OpenID Connect 和 OAuth](#openid-connect-and-oauth) | 僅限雲端 | 開發新的應用程式時，請使用 OpenID Connect 和 OAuth。 此通訊協定可簡化應用程式設定、具備容易使用的 SDK，而且可讓您的應用程式使用 MS Graph。
| [SAML](#saml-sso) | 雲端和內部部署 | 請盡可能為不使用 OpenID Connect 或 OAuth 的現有應用程式選擇 SAML。 SAML 適用於使用其中一個 SAML 通訊協定進行驗證的應用程式。|
| [密碼式](#password-based-sso) | 雲端和內部部署 | 當應用程式使用使用者名稱和密碼進行驗證時，請選擇密碼式。 密碼式單一登入可以使用網頁瀏覽器擴充功能或行動應用程式，安全儲存應用程式的密碼以及重新執行。 此方法會使用應用程式提供的現有登入程序，但讓系統管理員可以管理密碼。 |
| [已連結](#linked-sign-on) | 雲端和內部部署 | 當應用程式設定為在另一個身分識別提供者服務中進行單一登入時，請選擇 [連結的登入]。 此選項不會將單一登入新增至應用程式。 不過，應用程式可能已經使用另一個服務 (例如 Active Directory 同盟服務) 來實作單一登入。|
| [Disabled](#disabled-sso) | 雲端和內部部署 | 當應用程式尚未準備好設定單一登入時，請選擇已停用的單一登入。 使用者每次啟動此應用程式時，都需要輸入其使用者名稱和密碼。|
| [整合式 Windows 驗證 (IWA)](#integrated-windows-authentication-iwa-sso) | 僅內部部署 | 請針對使用[整合式 Windows 驗證 (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication) 的應用程式或宣告感知的應用程式，選擇 IWA 單一登入。 至於 IWA，應用程式 Proxy 連接器會使用 Kerberos 限制委派 (KCD) 來向應用程式驗證使用者。 |
| [標頭式](#header-based-sso) | 僅內部部署 | 當應用程式是使用標頭進行驗證時，請使用標頭式單一登入。 標頭式單一登入需要適用於 Azure AD 的 PingAccess。 應用程式 Proxy 會使用 Azure AD 來驗證使用者，然後透過連接器服務傳遞流量。  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect 和 OAuth

在開發新的應用程式時，請使用 OpenID Connect 和 OAuth 等新式通訊協定，來為應用程式實現跨多個裝置平台的最佳單一登入體驗。 OAuth 可讓使用者或系統管理員對受保護的資源（例如[Microsoft Graph](/graph/overview)）[授與同意](configure-user-consent.md)。 我們為您的應用程式提供容易採用的[sdk](../develop/reference-v2-libraries.md) ，此外，您的應用程式也可供使用[Microsoft Graph](/graph/overview)。

如需詳細資訊，請參閱

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft 身分識別平臺開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。

## <a name="saml-sso"></a>SAML SSO

使用 **SAML 單一登入**，Azure AD 會使用使用者的 Azure AD 帳戶向應用程式驗證。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 使用 SAML 單一登入時，您可以根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色。

如果應用程式支援，請選擇 SAML 型單一登入。

使用以下任一個通訊協定的應用程式都支援 SAML 式單一登入：

- SAML 2.0
- WS-同盟

若要設定 SAML 型單一登入的 SaaS 應用程式，請參閱[設定 saml 型單一登入](configure-single-sign-on-non-gallery-applications.md)。 此外，許多軟體即服務 (SaaS) 應用程式的[應用程式專屬教學課程](../saas-apps/tutorial-list.md)，可逐步引導您設定 SAML 型單一登入。

若要設定 WS-同盟的應用程式，請遵循相同的指導方針來設定 SAML 型單一登入的應用程式，請參閱[設定 saml 型單一登入](configure-single-sign-on-non-gallery-applications.md)。 在將應用程式設定為使用 Azure AD 的步驟中，您將需要取代 WS-同盟端點 `https://login.microsoftonline.com/<tenant-ID>/wsfed`的 Azure AD 登入 URL。

若要設定 SAML 型單一登入的內部部署應用程式，請參閱[使用應用程式 Proxy 進行內部部署應用程式的 SAML 單一登入](application-proxy-configure-single-sign-on-on-premises-apps.md)。

如需有關 SAML 通訊協定的詳細資訊，請參閱[單一登入 SAML 通訊協定](../develop/single-sign-on-saml-protocol.md)。

## <a name="password-based-sso"></a>密碼式 SSO

若使用密碼式登入，使用者在第一次存取應用程式時，要以使用者名稱和密碼登入應用程式。 第一次登入之後，Azure AD 就會向應用程式提供使用者名稱和密碼。

密碼式單一登入使用應用程式所提供的現有驗證程序。 當您為應用程式啟用密碼單一登入時，Azure AD 會收集應用程式的使用者名稱和密碼，並安全地儲存。 使用者認證會以加密的狀態儲存在目錄中。

選擇密碼式單一登入的時機：

- 應用程式不支援 SAML 單一登入通訊協定。
- 應用程式使用使用者名稱和密碼進行驗證，而不是使用存取權杖和標頭。

有 HTML 型登入頁面的雲端應用程式支援密碼式單一登入。 使用者可以使用下列任何瀏覽器：

- Windows 7 上的 Internet Explorer 11 或更新版本
   > [!NOTE]
   > Internet Explorer 的支援有限，且不會再收到新的軟體更新。 Microsoft Edge 是建議使用的瀏覽器。

- Windows 10 Anniversary Edition 或更新版本上的 Microsoft Edge
- 適用于 iOS 和 Android 的 Microsoft Edge
- Intune Managed Browser
- 在 Windows 7 或更新版本，和在 MacOS X 或更新版本上的 Chrome
- 在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本上的 Firefox 26.0 或更新版本

若要設定雲端應用程式以進行密碼型單一登入，請參閱[設定密碼單一登入](configure-password-single-sign-on-non-gallery-applications.md)。

若要為內部部署應用程式設定透過應用程式 Proxy 的單一登入，請參閱[使用應用程式 Proxy 之單一登入的密碼保存庫](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>密碼式 SSO 驗證的運作方式

為了向應用程式驗證使用者，Azure AD 會從目錄擷取使用者的認證，並將它們輸入應用程式的登入頁面。  Azure AD 透過網頁瀏覽器擴充功能或行動裝置應用程式，安全地傳遞使用者認證。 此程序可讓系統管理員管理使用者認證，而且使用者不需要記住其密碼。

> [!IMPORTANT]
> 在自動登入程序期間，系統會對使用者模糊處理認證。 不過，使用 Web 偵錯工具可以探索認證。 使用者和系統管理員需要遵循相同的安全性原則，如同認證是直接由使用者輸入一般。

### <a name="managing-credentials-for-password-based-sso"></a>管理密碼式 SSO 的認證

每個應用程式的密碼可由 Azure AD 系統管理員或使用者來管理。

當 Azure AD 系統管理員管理認證時：  

- 使用者不需要重設或記得使用者名稱和密碼。 使用者可以在其存取面板中按一下應用程式，或透過提供的連結來存取應用程式。
- 系統管理員可以對認證執行管理工作。 例如，系統管理員可以根據使用者群組成員資格和員工狀態，來更新應用程式的存取。
- 系統管理員可以使用系統管理認證，提供在許多使用者間共用之應用程式的存取權。 例如，系統管理員可以允許能存取某個應用程式許的每個人，都能存取某個社交媒體或文件共用應用程式。

當使用者管理認證時：

- 使用者可以管理他們的密碼，視需要更新或刪除它們。
- 系統管理員仍可設定應用程式的新認證。

## <a name="linked-sign-on"></a>連結型登入
已連結的登入可讓 Azure AD 對已在其他服務設定單一登入的應用程式提供單一登入。 已連結的應用程式可以在 Office 365 入口網站或 Azure AD MyApps 入口網站中向使用者顯示。 例如，使用者可以從 Office 365 入口網站啟動已在 Active Directory Federation Services 2.0 (AD FS) 中設定單一登入的應用程式。 從 Office 365 入口網站或 Azure AD MyApps 入口網站啟動的已連結的應用程式也可以取得其他報告。 若要設定應用程式以進行連結的登入，請參閱[設定連結](configure-linked-sign-on.md)的登入。

### <a name="linked-sign-on-for-application-migration"></a>應用程式遷移的連結登入

當您在一段時間內遷移應用程式時，連結的登入可以提供一致的使用者體驗。 如果您要將應用程式遷移至 Azure Active Directory，您可以使用連結的登入，快速地將連結發佈到您想要遷移的所有應用程式。  使用者可以在 [MyApps 入口網站](../user-help/active-directory-saas-access-panel-introduction.md)或 [Office 365 應用程式啟動器](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)中找到所有連結。 使用者不會知道他們存取的是已連結的應用程式或已移轉的應用程式。  

使用者與連結的應用程式驗證之後，需要先建立帳戶記錄，系統才會提供使用者單一登入存取權。 佈建此帳戶記錄可以是自動執行，或是由系統管理員手動執行。

## <a name="disabled-sso"></a>已停用的 SSO

停用模式表示單一登入不會用於該應用程式。 當單一登入停用時，使用者可能需要驗證兩次。 首先，使用者向 Azure AD 驗證，然後再登入應用程式。

使用已停用的單一登入模式：

- 如果您尚未準備好將此應用程式與 Azure AD 單一登入整合，或
- 如果您在測試應用程式的其他層面，或
- 作為不需要使用者進行驗證之內部部署應用程式的一層安全性。 停用之後，使用者需要進行驗證。

## <a name="integrated-windows-authentication-iwa-sso"></a>整合式 Windows 驗證 (IWA) SSO

[應用程式 Proxy](application-proxy.md) 會對使用[整合式 Windows 驗證 (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication) 的應用程式，或宣告感知應用程式提供單一登入 (SSO)。 如果您的應用程式使用 IWA，則應用程式 Proxy 會使用 Kerberos 限制委派 (KCD) 來驗證應用程式。 對於信任 Azure Active Directory 的宣告感知應用程式，因為使用者已由 Azure AD 驗證，所以可以使用單一登入。

選擇 [整合式 Windows 驗證單一登入模式]，為使用 IWA 驗證的內部部署應用程式提供單一登入。

為內部部署應用程式設定 IWA，請參閱[適用於單一登入使用應用程式 Proxy 之應用程式的 Kerberos 限制委派](application-proxy-configure-single-sign-on-with-kcd.md)。

### <a name="how-single-sign-on-with-kcd-works"></a>使用 KCD 單一登入的運作方式
此圖表說明使用者存取採用 IWA 之內部部署應用程式時的流程。

![Microsoft Azure AD 驗證流程圖](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. 使用者輸入 URL，以透過應用程式 Proxy 存取內部部署應用程式。
1. 「應用程式 Proxy」將要求重新導向至 Azure AD 驗證服務，以進行預先驗證。 此時，Azure AD 會套用任何適用的驗證和授權原則，例如多重要素驗證。 若使用者通過驗證，Azure AD 會建立權杖並將它傳送給使用者。
1. 使用者將權杖傳遞給「應用程式 Proxy」。
1. 應用程式 Proxy 驗證權杖，並從權杖擷取使用者主體名稱 (UPN)。 它接著透過雙重驗證安全通道，將要求、UPN 和服務主體名稱 (SPN) 傳送到連接器。
1. 連接器會使用 Kerberos 限制委派（KCD）與內部部署 AD 的協商，模擬使用者以取得應用程式的 Kerberos 權杖。
1. Active Directory 會將應用程式的 Kerberos 權杖傳送至連接器。
1. 連接器會使用從 AD 接收的 Kerberos 權杖，將原始要求傳送至應用程式伺服器。
1. 應用程式會將回應傳送至連接器，然後再傳回至應用程式 Proxy 服務，最後再傳回給使用者。

## <a name="header-based-sso"></a>標頭式 SSO

標頭式單一登入適用於使用 HTTP 標頭進行驗證的應用程式。 此登入方法會使用名為 PingAccess 的協力廠商驗證服務。 使用者只需要向 Azure AD 驗證。

為應用程式設定應用程式 Proxy 和 PingAccess 時，請選擇標頭型單一登入。

若要設定標頭式驗證，請參閱[適用於使用應用程式 Proxy 之單一登入的標頭式驗證](application-proxy-configure-single-sign-on-with-ping-access.md)。

### <a name="what-is-pingaccess-for-azure-ad"></a>什麼是 Azure AD 的 PingAccess？

使用適用於 Azure AD 的 PingAccess，使用者就能存取和單一登入至使用標頭驗證的應用程式。 應用程式 Proxy 會如同任何其他應用程式一樣處理這些應用程式，使用 Azure AD 驗證存取，然後透過連接器服務傳遞流量。 驗證發生之後，PingAccess 服務會將 Azure AD 存取權杖翻譯成傳送到應用程式的標頭格式。

使用者在登入使用您公司的應用程式時，將不會注意到什麼不同。 這些還是可以在任何裝置上從任何地方運作。 應用程式 Proxy 連接器將遠端流量導向至所有應用程式，然後它們會繼續自動進行負載平衡。

### <a name="how-do-i-get-a-license-for-pingaccess"></a>如何取得 PingAccess 的授權？

這種情況是透過 Azure AD 和 PingAccess 之間的合作關係提供，因此您會需要這兩種服務的授權。 不過，Azure AD Premium 訂用帳戶所包含的基本 PingAccess 授權最多可涵蓋 20 個應用程式。 如果您需要發佈 20 個以上的標頭應用程式，可以從 PingAccess 取得額外的授權。

如需詳細資訊，請參閱 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="related-articles"></a>相關文章
* [整合 SaaS 應用程式與 Azure Active Directory 的教學課程](../saas-apps/tutorial-list.md)
* [設定 SAML 型單一登入](configure-single-sign-on-non-gallery-applications.md)
* [設定以密碼為基礎的單一登入](configure-password-single-sign-on-non-gallery-applications.md)
* [正在設定連結的登入](configure-linked-sign-on.md)
* [管理應用程式存取簡介](what-is-access-management.md)
* 下載連結︰[單一登入部署計劃](https://aka.ms/SSODeploymentPlan) \(英文\)。
