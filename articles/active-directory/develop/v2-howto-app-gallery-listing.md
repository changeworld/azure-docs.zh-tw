---
title: 將您的應用程式發佈至 Azure Active Directory 應用程式資源庫
description: 瞭解如何在 Azure Active Directory 應用程式資源庫中列出支援單一登入的應用程式。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 396d6f69673f8758d8d1302f8d10b8a92e5f50b4
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530744"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>將您的應用程式發佈至 Azure AD 應用程式資源庫

您可以在 Azure AD 應用程式資源庫中發佈您的應用程式。 當您的應用程式發佈時，當客戶將應用程式新增至其租使用者時，就會顯示該應用程式的選項。 

將您的應用程式新增至 Azure AD 資源庫的一些優點包括：

- 客戶可以找到您應用程式的最佳單一登入體驗。
- 應用程式的設定相當簡單且基本。
- 快速搜尋可在應用程式庫中找到您的應用程式。
- 免費、基本及進階 Azure AD 的客戶都可使用這項整合。
- 共同客戶可取得逐步設定教學課程。

此外，當您的客戶使用 Azure AD 作為應用程式的身分識別提供者時，有許多優點。 其中包含：

- 為您的使用者提供單一登入。 使用 SSO，您可以透過單一登入讓客戶更輕鬆地降低支援成本。 如果已啟用單鍵 SSO，客戶的 IT 系統管理員就不需要學習如何設定您的應用程式以在其組織中使用。 若要深入了解單一登入，請參閱[什麼是單一登入？](../manage-apps/what-is-single-sign-on.md)。
- 您的應用程式可以在 Microsoft 365 應用程式資源庫、Microsoft 365 應用程式啟動程式，以及在 Office.com 上的 Microsoft 搜尋服務中找到。 
- 整合式應用程式管理。 若要深入瞭解 Azure AD 中的應用程式管理，請參閱 [什麼是應用程式管理？](../manage-apps/what-is-application-management.md)。
- 您的應用程式可以使用 [圖形 API](/graph/) 來存取在 Microsoft 生態系統中驅動使用者生產力的資料。
- 隨 Azure AD 團隊共同產生的應用程式專屬檔，適用于我們的共同客戶可簡化採用。
- 您可以讓您的客戶能夠完全管理其員工和來賓身分識別的驗證與授權。
- 將所有帳戶管理和合規性責任與客戶的身分識別相關聯。
- 為特定身分識別提供者、群組或使用者提供啟用或停用 SSO 的能力，以符合其商務需求。
- 您可以增加 marketability 和 adoptability。 許多大型組織都需要 (或追求，才能) 員工在所有應用程式中都有順暢的 SSO 體驗。 讓 SSO 變得很重要。
- 您可以減少終端使用者的分歧，這可能會增加使用者的使用，並增加您的收入。
- 使用系統進行跨網域身分識別管理的客戶 ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 可以針對相同的應用程式使用布建。
- 當使用者使用 Azure AD SSO 登入應用程式，並移除個別認證的需求時，增加安全性和便利性。

> [!TIP]
> 當您透過購買或訂用帳戶提供其他公司所使用的應用程式時，您可以將應用程式提供給他們自己的 Azure 租使用者中的客戶。 這就是所謂的建立多租使用者應用程式。 如需此概念的總覽，請參閱 Azure 中的多租使用者 [應用程式](../../dotnet-develop-multitenant-applications.md) 和 [Azure Active Directory 中](single-and-multi-tenant-apps.md)的租使用者。

> [!IMPORTANT]
> 若要在 Azure AD 資源庫中發佈您的應用程式，您必須同意特定的條款及條件。 開始之前，請務必閱讀並同意 [條款及條件](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)。

在 Azure AD 應用程式資源庫中發佈應用程式的步驟如下：
1. 為您的應用程式選擇正確的單一登入標準。
2. 在您的應用程式中執行單一登入。
3. 建立您的 Azure 租使用者並測試您的應用程式。
4. 建立和發行檔。
5. 提交您的應用程式。
6. 加入 Microsoft 合作夥伴網路。

## <a name="what-is-the-azure-ad-application-gallery"></a>什麼是 Azure AD 應用程式庫？

- 客戶可找到最佳且適用的單一登入體驗。
- 應用程式的設定相當簡單且基本。
- 快速搜尋可在應用程式庫中找到您的應用程式。
- 免費、基本及進階 Azure AD 的客戶都可使用這項整合。
- 共同客戶可取得逐步設定教學課程。
- 使用系統進行跨網域身分識別管理的客戶 ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 可以針對相同的應用程式使用布建。

## <a name="prerequisites"></a>必要條件

您需要一個永久的帳戶來進行測試，而且至少已註冊兩個使用者。

- 針對同盟應用程式 (開啟識別碼和 SAML/WS-饋送) ，應用程式必須支援軟體即服務 (SaaS) 模型，才能列在 Azure AD 應用程式庫中。 企業資源庫應用程式必須支援多個客戶設定，而不是任何特定客戶。
- 針對 Open ID Connect，必須 multitenanted 應用程式，而且必須為應用程式正確地執行 [Azure AD 同意架構](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 。 使用者可以將登入要求傳送至一般端點，讓任何客戶都能為應用程式提供同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。
- 針對 SAML 2.0/WS-送出，您的應用程式必須能夠在 SP 或 IDP 模式下進行 SAML/WS-饋送 SSO 整合。 在您提交要求之前，請確定這項功能是否正常運作。
- 若為密碼 SSO，請確定您的應用程式支援表單驗證，才能完成密碼保存，讓單一登入能如預期般運作。
- 您需要一個永久的帳戶來進行測試，而且至少已註冊兩個使用者。

**如何取得開發人員的 Azure AD？**

您可以取得免費的測試帳戶，其中包含所有 premium Azure AD 功能-90 天免費，只要您使用它進行開發，就可以延長： https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>步驟 1-為您的應用程式選擇正確的單一登入標準

若要在 Azure AD 應用程式資源庫中列出應用程式，您必須執行至少一個支援的單一登入選項。 若要瞭解單一登入選項，以及客戶會如何在 Azure AD 中設定這些選項，請參閱 [SSO 選項](../manage-apps/sso-options.md)。

下表比較主要標準： Open Authentication 2.0 (OAuth 2.0) 與 OpenID Connect (OIDC) 、安全性聲明標記語言 (SAML) 和 Web 服務同盟 (WS-送出) 。

| 功能| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Web 單一登入| √| √ |
| 以 Web 為基礎的單一登出| √| √ |
| 以行動裝置為基礎的單一登入| √| √* |
| 以行動裝置為基礎的單一登出| √| √* |
| 行動應用程式的條件式存取原則| √| X |
| 適用于行動應用程式的順暢 MFA 體驗| √| X |
| SCIM 佈建| √| √ |
| 存取 Microsoft Graph| √| X |

* 可能，但 Microsoft 不提供範例或指引。

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 和 OpenID Connect
OAuth 2.0 是適用于授權的 [業界標準](https://oauth.net/2/) 通訊協定。 OpenID Connect (OIDC) 是以 OAuth 2.0 通訊協定為基礎的 [業界標準](https://openid.net/connect/) 身分識別驗證層。 

**選擇 OAuth/OIDC 的原因**
- 這些通訊協定固有的授權可讓您的應用程式透過 Microsoft Graph API 存取和整合豐富的使用者和組織資料。
- 為您的應用程式採用 SSO 時，簡化客戶的終端使用者體驗。 您可以輕鬆地定義必要的許可權集合，然後自動向系統管理員或使用者表示同意。
- 使用這些通訊協定可讓您的客戶使用條件式存取和 Multi-Factor Authentication (MFA) 原則來控制應用程式的存取權。 
- Microsoft 會 [跨多個技術平臺](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) 提供程式庫和程式碼範例，以協助您進行開發。  

**應考慮的事項**
- 如果您已為您的應用程式執行 SAML 型單一登入，則您可能不想要執行新的標準，以在資源庫中取得您的應用程式。

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 或 WS-Fed

SAML 是適用于 web 應用程式的成熟且廣泛採用的 [單一登入標準](https://www.oasis-open.org/standards#samlv2.0) 。 若要深入瞭解 Azure 如何使用 SAML，請參閱 [Azure 如何使用 saml 通訊協定](active-directory-saml-protocol-reference.md)。 

Web 服務同盟 (WS-送) 是一種 [業界標準](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) ，通常用於使用 .net 平臺開發的 web 應用程式。

**選擇 SAML 的原因**
- SAML 2.0 是成熟的標準，且大部分的技術平臺都支援 SAML 2.0 的開放原始碼程式庫。 
- 您可以為客戶提供管理介面來設定 SAML SSO。 他們可以設定 Microsoft Azure AD 的 SAML SSO，以及任何其他支援 SAML 的身分識別提供者。

**應考慮的事項**
- 針對行動應用程式使用 SAML 2.0 或 WSFed 通訊協定時，特定條件式存取原則（包括多重要素驗證） (MFA) 將會有較差的體驗。
- 如果您想要存取 Microsoft Graph，您將需要透過 OAuth 2.0 來執行授權，以產生必要的權杖。 

### <a name="password-based"></a>密碼式
密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 如果有數個使用者需要共用單一帳戶（例如組織的社交媒體應用程式帳戶），這也很有用。


## <a name="step-2---implement-single-sign-on-in-your-app"></a>步驟 2-在您的應用程式中執行單一登入
資源庫中的每個應用程式都必須執行其中一個支援的單一登入選項。 若要深入瞭解支援的選項，請參閱 [SSO 選項](../manage-apps/sso-options.md)。

針對 OAuth 和 OIDC，請參閱 [驗證模式](v2-app-types.md) 和 [Azure active Directory 程式碼範例](sample-v2-code.md)的指引。

針對 SAML 和 WS-送出，您的應用程式必須具備在 SP 或 IDP 模式中進行 SSO 整合的功能。 在您提交要求之前，請確定這項功能是否正常運作。

若要深入瞭解驗證，請參閱 [什麼是驗證？](../azuread-dev/v1-authentication-scenarios.md)。

> [!IMPORTANT]
> 針對 (OpenID 和 SAML/WS-AT) 的同盟應用程式，應用程式必須支援軟體即服務 (SaaS) 模型。 Azure AD 資源庫應用程式必須支援多個客戶設定，而且不應該專屬於任何單一客戶。

### <a name="implement-oauth-20-and-openid-connect"></a>執行 OAuth 2.0 和 OpenID Connect

針對 OpenID Connect，應用程式必須是多重租使用者，而且必須為應用程式正確地執行 [Azure AD 同意架構](consent-framework.md) 。 使用者可以將登入要求傳送至一般端點，讓任何客戶都能為應用程式提供同意。 您可以根據租用戶識別碼和在權杖中收到的使用者 UPN 來控制使用者存取權。

若要查看特定範例，請參閱 [Microsoft 身分識別平臺程式碼範例](sample-v2-code.md)。 

若要查看行動特定的範例，請參閱： 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [通用 Windows 平台](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>執行 SAML 2。0

如果您的應用程式支援 SAML 2.0，您可以直接與 Azure AD 租使用者整合。 若要深入瞭解使用 Azure AD 的 SAML 設定，請參閱 [設定 saml 型單一登入](../manage-apps/configure-saml-single-sign-on.md)。

Microsoft 不會提供（或建議） SAML 執行的程式庫。 有許多可用的開放原始碼程式庫。

### <a name="implement-ws-fed"></a>執行 WS-Fed
若要深入瞭解 ASP.NET Core 中的 WS-Fed，請參閱 [ASP.NET Core 中使用 WS-Federation 來驗證使用者](/aspnet/core/security/authentication/ws-federation)。

### <a name="implement-password-vaulting"></a>執行密碼保存庫

建立具有 HTML 登入頁面的 web 應用程式。 確定您的應用程式支援表單驗證，如此就可以完成密碼保存庫，讓單一登入能夠如預期般運作。

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>步驟 3-在您的應用程式中執行 SCIM 使用者布建
支援 [SCIM](https://aka.ms/scimoverview) 布建是選擇性的，但強烈建議您建立應用程式的步驟。 支援 SCIM 標準很容易，並可讓客戶在您的應用程式中自動建立和更新使用者帳戶，而不需要依賴手動進程，例如上傳 CSV 檔案。 此外，客戶可以自動移除使用者，並讓群組成員資格保持同步，而無法使用 SAML JIT 等解決方案來完成。 

### <a name="learn-about-scim"></a>深入瞭解 SCIM
若要深入瞭解客戶的 SCIM 標準和優點，請參閱布 [建 SCIM-快速入門](https://aka.ms/scimoverview)。

### <a name="understand-the-azure-ad-scim-implementation"></a>瞭解 Azure AD SCIM 的執行
若要深入瞭解 Azure AD SCIM 的執行，請參閱 [建立 SCIM 端點，並使用 Azure AD 來設定使用者布建](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)。

### <a name="implement-scim"></a>執行 SCIM
Azure AD 提供可協助您建立 SCIM 端點的 [參考程式碼](https://aka.ms/scimoverview) 。 您也可以在 github 上找到許多協力廠商程式庫/參考。  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>步驟 4-建立您的 Azure 租使用者並測試您的應用程式

您將需要 Azure AD 租使用者，才能測試您的應用程式。 若要設定您的開發環境，請參閱 [快速入門：設定租](quickstart-create-new-tenant.md)使用者。

或者，Azure AD 租使用者會隨附于每個 Microsoft 365 訂用帳戶。 若要設定免費的 Microsoft 365 開發環境，請參閱 [加入 Microsoft 365 開發人員計畫](/office/developer-program/microsoft-365-developer-program)。

擁有租使用者之後，您需要測試單一登入和布[建。](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client) 

**針對 OIDC 或 Oath 應用** 程式，請將 [您的應用程式註冊](quickstart-register-app.md) 為多租使用者應用程式。 在 [支援的帳戶類型] 中，選取 [任何組織目錄中的帳戶及個人 Microsoft 帳戶] 選項。

**針對 saml 和 WS-送出的應用程式**，您可以在 Azure AD 中使用一般 SAML 範本來 [設定 saml 型單一登入](../manage-apps/configure-saml-single-sign-on.md) 應用程式。

如有必要，您也可以 [將單一租使用者應用程式轉換為多租](howto-convert-app-to-be-multi-tenant.md) 使用者。


## <a name="step-5---create-and-publish-documentation"></a>步驟 5-建立和發佈檔

### <a name="documentation-on-your-site"></a>網站上的檔

簡化採用是企業軟體決策的主要因素。 清除簡單易用的檔可支援客戶在其採用旅程中，並降低支援成本。 Microsoft 與數以千計的軟體廠商合作，看看有什麼作用。

建議您在網站上至少包含下列專案的檔。

* SSO 功能簡介
  * 支援的通訊協定
  * 版本和 SKU
  * 支援的識別提供者清單與檔連結
* 應用程式的授權資訊
* 用於設定 SSO 的角色型存取控制
* SSO 設定步驟
  * 具有提供者預期值之 SAML 的 UI 設定元素
  * 要傳遞給身分識別提供者的服務提供者資訊
* 如果 OIDC/OAuth
  * 以商務理由同意所需的許可權清單
* 試驗使用者的測試步驟
* 疑難排解資訊，包括錯誤碼和訊息
* 客戶的支援機制
* SCIM 端點的詳細資料，包括支援的資源和屬性

### <a name="documentation-on-the-microsoft-site"></a>Microsoft 網站上的檔

當您使用 Azure Active Directory 應用程式庫列出您的應用程式時，它也會將您的應用程式發佈到 Azure Marketplace 中，Microsoft 將會為我們的共同客戶產生說明逐步程式的檔。 您可以在 [這裡](../saas-apps/tutorial-list.md)看到範例。 本檔是根據您提交至資源庫所建立，如果您使用 GitHub 帳戶對應用程式進行變更，就可以輕鬆地加以更新。


## <a name="step-6---submit-your-app"></a>步驟 6-提交您的應用程式

在您測試過應用程式整合是否與 Azure AD 搭配運作之後，請在 [Microsoft 應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交應用程式要求。

當您第一次嘗試登入入口網站時，您會看到兩個畫面中的其中一個。 

如果您收到「無法運作」的訊息，則必須聯絡 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 提供您要用來提交要求的電子郵件帳戶。 慣用的商業電子郵件地址 `name@yourbusiness.com` 。 Azure AD 團隊會在 Microsoft 應用程式網路入口網站中新增該帳戶。

如果您看到 [要求存取] 頁面，請填寫商務理由，然後選取 [ **要求存取**]。

新增帳戶之後，您可以登入 Microsoft 應用程式網路入口網站，然後在首頁上選取 [ **提交要求 (ISV)** ] 磚來提交要求。

![在首頁上提交要求 (ISV) 磚](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>登入入口網站的問題

如果您在登入時看到此錯誤，則以下是問題的詳細資料，而這就是您可以修正問題的方式。

* 如果您的登入遭到封鎖，如下所示：

  ![在資源庫中解析應用程式時發生問題](./media/howto-app-gallery-listing/blocked.png)

**發生了什麼事情：**

來賓使用者會與主租使用者同盟，也就是 Azure AD。 來賓使用者具有高風險。 Microsoft 不允許高風險使用者存取其資源。 所有高風險使用者 (員工或來賓/廠商) 都必須補救/關閉其存取 Microsoft 資源的風險。 若是來賓使用者，此使用者風險來自于 home 租使用者，而此原則來自 (Microsoft 的資源租使用者) 。
 
**安全解決方案：**

* MFA 註冊的來賓使用者會補救自己的使用者風險。 這可以由在其主租使用者執行安全密碼變更或重設 (的來賓使用者執行， https://aka.ms/sspr) (這需要在 home 租使用者) 進行 MFA 和 SSPR。 安全的密碼變更或重設必須在 Azure AD 上起始，而非內部部署。

* 來賓使用者的系統管理員會補救其風險。 在此情況下，系統管理員會執行密碼重設， (暫時性密碼產生) 。 這不需要身分識別保護。 來賓使用者的系統管理員可以前往 [ https://aka.ms/RiskyUsers 重設密碼]，然後按一下 [重設密碼]。

* 來賓使用者的系統管理員會關閉/關閉其風險。 同樣地，這不需要身分識別保護。 系統管理員可以前往 [ https://aka.ms/RiskyUsers 解除使用者風險] 並按一下。 不過，系統管理員必須進行審慎的處理，以確保在關閉使用者風險之前，這是誤報的負風險評估。 否則，他們會藉由隱藏未進行調查的風險評量，將其和 Microsoft 的資源視為風險。

> [!NOTE]
> 如果您有任何存取問題，請洽詢 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

### <a name="implementation-specific-options"></a>執行專用選項
如果您想要使用 OpenID Connect 將應用程式新增至資源庫中的清單，請選取 **OpenID Connect & OAuth 2.0** ，如下所示。

![列出資源庫中的 OpenID Connect 應用程式](./media/howto-app-gallery-listing/openid.png)

如果您想要使用 **saml 2.0** 或 **ws-addressing** 將應用程式新增至資源庫中的清單，請選取 [ **Saml 2.0/ws-送** 出]，如下所示。

![在資源庫中列出 SAML 2.0 或 WS-Fed 應用程式](./media/howto-app-gallery-listing/saml.png)

如果您想要使用密碼 SSO 將應用程式新增至資源庫中的清單，請選取 [ **密碼 sso (使用者名稱 & 密碼)** 如下所示。

![在資源庫中列出密碼 SSO 應用程式](./media/howto-app-gallery-listing/passwordsso.png)

如果您正在執行 [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2.0 端點以進行使用者布建，請選取如下所示的選項。 在上架要求中提供架構時，請遵循 [此處](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration) 的指示來下載您的架構。 我們會使用您在測試非資源庫應用程式時所設定的架構來建立資源庫應用程式。 

   ![要求使用者布建](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>更新或移除現有清單

您可以在 [Microsoft 應用程式網路入口網站](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中更新或移除現有的資源庫應用程式。

![在資源庫中列出 SAML 應用程式](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> 如果您有存取權的任何問題，請參閱上一節的建立帳戶。 如果無法運作，請聯絡 [AZURE AD SSO 整合小組](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

### <a name="list-requests-by-customers"></a>列出客戶的要求

客戶可以藉由選取 **客戶**  >  **提交新要求** 的應用程式要求，提交列出應用程式的要求。

![顯示客戶要求的應用程式圖格](./media/howto-app-gallery-listing/customer-submit-request.png)

以下是客戶要求應用程式的流程。

![顯示客戶要求的應用程式流程](./media/howto-app-gallery-listing/customer-request-2.png)


### <a name="timelines"></a>時間表

在資源庫中列出 SAML 2.0 或 WS-Fed 應用程式的程式時程表是7到10個工作天。

![在資源庫中列出 SAML 應用程式的時程表](./media/howto-app-gallery-listing/timeline.png)

在資源庫中列出 OpenID Connect 應用程式的程式時程表是2至5個工作天。

![在資源庫中列出 OpenID Connect 應用程式的時程表](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>呈報

針對任何呈報，請傳送電子郵件給 [AZURE AD SSO 整合小組](mailto:SaaSApplicationIntegrations@service.microsoft.com)，我們會儘快回應。


## <a name="step-7---join-the-microsoft-partner-network"></a>步驟 7-加入 Microsoft 合作夥伴網路
Microsoft 合作夥伴網路可讓您立即存取專屬資源、程式、工具和連接。 若要加入網路並建立您的「移至市場」方案，請參閱 [觸及商業客戶](https://partner.microsoft.com/explore/commercial#gtm)。


## <a name="next-steps"></a>後續步驟
* [建置 SCIM 端點和設定使用者佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Azure AD 的驗證案例](authentication-flows-app-scenarios.md)
