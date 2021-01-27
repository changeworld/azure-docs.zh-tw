---
title: 使用 Ping 身分識別設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 Ping 身分識別
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 430629f94695f0689422434c8d80fe4e1876e5dd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900130"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>教學課程：使用安全的混合式存取 Azure Active Directory B2C 設定 Ping 身分識別

在此範例教學課程中，您將瞭解如何使用  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 和 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) 擴充 AZURE ACTIVE DIRECTORY (AD) B2C，以啟用安全的混合式存取。

許多現有的 web 屬性（例如電子商務網站和 web 應用程式）會部署在 proxy 系統後方，有時也稱為反向 proxy 系統。 這些 proxy 系統提供各種功能，包括預先驗證、原則強制執行和流量路由。 使用案例範例包括保護 web 應用程式免于輸入的網路流量，並提供跨分散式伺服器部署的統一會話管理。

在大部分情況下，此設定包含從 web 應用程式 externalizes 驗證的驗證轉譯層。 相反地，反向 proxy 會以較簡單的形式（例如，以純文字形式或摘要形式的標頭值），將已驗證的使用者內容提供給 web 應用程式。 在這種設定中，應用程式不會使用任何業界標準權杖，例如安全性聲明標記語言 (SAML) 、OAuth 或 Open ID Connect (OIDC) ，而是取決於 proxy 以提供驗證內容，並與終端使用者代理程式（例如瀏覽器或原生應用程式）維護會話。 Proxy 是在「中間人」中執行的服務，可提供終極的會話控制。 這也表示 proxy 服務應該具有極高效率且可調整性，而不會成為 proxy 服務背後應用程式的瓶頸或單一失敗點。 此圖是典型反向 proxy 執行和通訊流程的描述。

![影像顯示反向 proxy 的執行](./media/partner-ping/reverse-proxy.png)

如果您想要將此類設定中的身分識別平臺現代化，會引發下列問題。

- 如何將應用程式現代化的工作量與身分識別平臺現代化分離？

- 使用現代化 identity service 提供者時，如何使用新式和舊版驗證來建立共存環境？

  a. 如何提高終端使用者體驗的一致性？

  b. 如何跨共存的應用程式提供單一登入體驗？

我們會討論將 Azure AD B2C 與 [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) 和 [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) 技術整合，以解決這類問題的方法。

## <a name="coexistence-environment"></a>共存環境

技術上可行的簡單解決方案，也就是符合成本效益的方法，就是將反向 proxy 系統設定為使用現代化身分識別系統，委派驗證。  
在此情況下，proxy 將支援新式驗證通訊協定，並使用以 (被動) 驗證為基礎的重新導向，以將使用者傳送至新的識別提供者 (IdP) 。

### <a name="azure-ad-b2c-as-an-identity-provider"></a>作為身分識別提供者 Azure AD B2C

Azure AD B2C 能夠定義 **原則** ，以驅動不同的使用者體驗和行為，也就是從伺服器端協調的 **使用者旅程** 。 這類原則會公開可執行驗證的通訊協定端點，就像是 IdP 一樣。 特定原則的應用程式端不需要特殊處理。 應用程式只會對您感興趣的原則所公開的通訊協定特定驗證端點提出產業標準驗證要求。  
Azure AD B2C 可以設定為在每個原則的多個原則或唯一簽發者之間共用相同的簽發者。 每個應用程式都可以藉由進行通訊協定原生驗證要求，並驅動所需的使用者行為（例如登入、註冊和設定檔編輯），來指向其中一或多個原則。 此圖顯示 OIDC 和 SAML 應用程式工作流程。

![影像顯示 OIDC 和 SAML 的執行](./media/partner-ping/azure-ad-identity-provider.png)

雖然所述的案例適用于現代化的應用程式，但繼承應用程式可能很難適當地重新導向使用者，因為應用程式的存取要求可能不會包含使用者體驗的內容。 在大部分情況下，web 應用程式上的 proxy 層或整合式代理程式會攔截存取要求。

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess 做為反向 proxy

許多客戶已將 PingAccess 部署為反向 proxy，以播放一或多個角色（如本文稍早所述）。 PingAccess 可以攔截直接要求，方法是攔截程式，或是從 web 應用程式伺服器上執行的代理程式重新導向。

您可以使用 OIDC、OAuth2 或 SAML 來設定 PingAccess，以對上游驗證提供者執行驗證。 您可以在 PingAccess 伺服器上針對此用途設定單一上游 IdP。 下圖顯示此設定。

![影像顯示 PingAccess with OIDC 執行](./media/partner-ping/authorization-flow.png)

在一般的 Azure AD B2C 部署中，如果有多個原則會公開多個 **idp**，則會帶來挑戰。 因為 PingAccess 只能使用單一上游 IdP 來設定。  

### <a name="pingfederate-as-a-federation-proxy"></a>以同盟 proxy 的形式 PingFederate

PingFederate 是企業身分識別橋接器，可完全設定為驗證提供者或其他多個上游 Idp 的 proxy （如有需要）。 下圖顯示這項功能。

![影像顯示 PingFederate 的執行](./media/partner-ping/pingfederate.png)

這項功能可以用來內容/動態或以宣告方式將輸入要求切換至特定的 Azure AD B2C 原則。 以下是此設定的通訊協定順序流程描述。

![影像顯示 PingAccess 和 PingFederate 工作流程](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>先決條件

若要開始使用，您需要：

- Azure 訂用帳戶。 如果您沒有帳戶，請取得 [免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的 [Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 使用者。

- PingAccess 和 PingFederate 部署在 Docker 容器中，或直接部署在 Azure Vm 上。

## <a name="connectivity"></a>連線能力

檢查下列是否已連線。

- **PingAccess server** -能夠與 PingFederate 伺服器、用戶端瀏覽器、OIDC、OAuth 知名以及 Azure AD B2C Service 和 PingFederate server 發佈的金鑰探索進行通訊。

- **PingFederate server** -能夠與 PingAccess 伺服器、用戶端瀏覽器、OIDC、OAuth 知名以及 Azure AD B2C 服務所發佈的金鑰探索進行通訊。

- **舊版或以標頭為基礎的驗證應用程式** –能夠與 PingAccess 伺服器通訊。

- **SAML 信賴憑證者應用程式** –能夠觸達來自用戶端的瀏覽器流量。 能夠存取 Azure AD B2C 服務所發行的 SAML 同盟中繼資料。

- **新式應用程式** -能夠觸達來自用戶端的瀏覽器流量。 能夠存取 Azure AD B2C 服務所發佈的 OIDC、OAuth 已知和金鑰探索。

- **REST API** –能夠觸達來自原生或 web 用戶端的流量。 能夠存取 Azure AD B2C 服務所發佈的 OIDC、OAuth 已知和金鑰探索。

## <a name="configure-azure-ad-b2c"></a>設定 Azure AD B2C

您可以針對此目的使用基本使用者流程或 advanced Identity enterprise framework (IEF) 原則。 PingAccess 會根據使用 [WebFinger](https://tools.ietf.org/html/rfc7033)型探索慣例的 **簽發者** 值產生中繼資料端點。
若要遵循此慣例，請使用使用者流程中的原則屬性來更新 Azure AD B2C 簽發者更新。

![顯示權杖設定的影像](./media/partner-ping/token-setting.png)

在 [advanced] 原則中，您可以使用 **IssuanceClaimPattern** metadata 專案來設定此專案，以 **AuthorityWithTfp** [JWT 權杖簽發者技術設定檔](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile)中的值。

## <a name="configure-pingaccesspingfederate"></a>設定 PingAccess/PingFederate

下一節涵蓋必要的設定。
此圖說明整合的整體使用者流程。

![影像顯示 PingAccess 和 PingFederate 整合](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>將 PingFederate 設定為權杖提供者

若要將 PingFederate 設定為 PingAccess 的權杖提供者，請確定已建立從 PingFederate 到 PingAccess 的連線，然後再從 PingAccess 到 PingFederate 的連接。  
請參閱 [這篇文章](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) 以瞭解設定步驟。

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>設定 PingAccess 應用程式以進行以標頭為基礎的驗證

您必須為目標 web 應用程式建立 PingAccess 應用程式，以進行以標頭為基礎的驗證。 請遵循下列步驟。

#### <a name="step-1--create-a-virtual-host"></a>步驟1–建立虛擬主機

>[!IMPORTANT]
>若要設定此解決方案，必須為每個應用程式建立虛擬主機。 如需有關設定考慮和其影響的詳細資訊，請參閱 [主要考慮事項](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)。

遵循下列步驟來建立虛擬主機：

1. 移至 **設定**  >  **存取**  >  **虛擬主機**

2. 選取 [**新增虛擬主機**]

3. 在 [主機] 欄位中，輸入應用程式 URL 的 FQDN 部分

4. 在 [埠] 欄位中，輸入 **443**

5. 選取 [儲存]。

#### <a name="step-2--create-a-web-session"></a>步驟 2-建立 web 會話

遵循下列步驟來建立 web 會話：

1. 流覽至 **設定**  >  **存取**  >  **Web 會話**

2. 選取 [**新增 Web 會話**]

3. 提供 web 會話的 **名稱** 。

4. 選取 **Cookie 類型**，即 **已簽署的 jwt** 或加密的 **jwt**

5. 提供 **物件** 的唯一值

6. 在 [ **用戶端識別碼** ] 欄位中，輸入 **Azure AD 的應用程式識別碼**

7. 在 [ **用戶端密碼** ] 欄位中，輸入您在 Azure AD 中為應用程式產生的 **金鑰** 。

8. 選擇性-您可以使用 Microsoft Graph API 來建立和使用自訂宣告。 如果您選擇這樣做，請選取 [ **Advanced** ]，然後取消選取 [ **要求設定檔** ] 和 [重新整理 **使用者屬性** ] 選項。 如需使用自訂宣告的詳細資訊，請參閱 [使用自訂](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#optional---use-a-custom-claim)宣告。

9. 選取 [儲存]。

#### <a name="step-3--create-identity-mapping"></a>步驟 3-建立身分識別對應

>[!NOTE]
>如果有多個應用程式的標頭中有多個應用程式需要相同的資料，則身分識別對應可以搭配多個應用程式使用。

遵循下列步驟來建立身分識別對應：

1. 移至 **設定**  >  **存取** 身分  >  **識別** 對應

2. 選取 [**新增識別對應**]

3. 指定 **名稱**

4. 選取 **標頭身分識別對應的** 識別對應類型

5. 在 [ **屬性對應** ] 資料表中，指定必要的對應。 例如，

   屬性名稱 | 標頭名稱 |
   |-------|--------|
   |upn | x-userprinciplename |
   |電子郵件   |    x-電子郵件  |
   |oid   | x-oid  |
   |scp   |     x 範圍 |
   |amr    |    x-amr    |

6. 選取 [儲存]。

#### <a name="step-4--create-a-site"></a>步驟4–建立網站

>[!NOTE]
>在某些設定中，網站可能包含一個以上的應用程式。 在適當的情況下，網站可以與一個以上的應用程式搭配使用。

遵循下列步驟來建立網站：

1. 前往 **主要**  >  **網站**

2. 選取 [**新增網站**]

3. 指定網站的 **名稱**

4. 輸入網站 **目標**。 目標是裝載應用程式之伺服器的主機名稱：埠配對。 請勿在此欄位中輸入應用程式的路徑。 例如，應用程式的 https://mysite:9999/AppName 目標值會是 [我的最新版： 9999]。

5. 指出目標是否預期安全連線。

6. 如果目標需要安全連線，請將受信任的憑證群組設定為 **信任任何** 一個。

7. 選取 [儲存]。

#### <a name="step-5--create-an-application"></a>步驟 5-建立應用程式

依照下列步驟，在 Azure 中針對您要保護的每個應用程式建立 PingAccess 應用程式。

1. 移至 **主**  >  **應用程式**

2. 選取 [**新增應用程式**]

3. 指定應用程式的 **名稱**

4. （選擇性）輸入應用程式的 **描述**

5. 指定應用程式的 **內容根目錄** 。 例如，應用程式的 https://mysite:9999/AppName 內容根目錄會是/AppName。 內容根目錄的開頭必須是斜線 (/) 、不能以斜線 (/) 結尾，也可以是一個以上的層級深度，例如/Apps/MyApp。

6. 選取您所建立的 **虛擬主機**

   >[!NOTE]
   >虛擬主機和內容根目錄的組合在 PingAccess 中必須是唯一的。

7. 選取您建立的 **web 會話**

8. 選取您建立的 **網站** ，其中包含應用程式

9. 選取您所建立的身分 **識別對應**

10. 選取 [ **已啟用** ] 可在您儲存時啟用網站

11. 選取 [儲存]。

### <a name="configure-the-pingfederate-authentication-policy"></a>設定 PingFederate authentication 原則

設定 PingFederate authentication 原則，以與 Azure AD B2C 租使用者所提供的多個 Idp 同盟

1. 建立合約以橋接 Idp 與 SP 之間的屬性。 如需詳細資訊，請參閱 [同盟中樞和驗證原則合約](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html)。 除非 SP 需要來自每個 IdP 的不同屬性集，否則您可能只需要一個合約。

2. 針對每個 IdP，在 IdP 和 PingFederate 之間建立 IdP 連線（同盟中樞為 SP）。

3. 在 [ **目標會話對應** ] 視窗中，將適用的驗證原則合約新增至 IdP 連接。

4. 在 [ **選取器** ] 視窗中，設定驗證選取器。 例如，請參閱 **識別碼第一個介面卡** 的實例，以將每個 IdP 對應至驗證原則中對應的 IdP 連接。

5. 建立 PingFederate 之間的 SP 連線、同盟中樞作為 IdP，以及 SP。

6. 將對應的驗證原則合約新增至 [ **驗證來源對應** ] 視窗上的 SP 連接。

7. 使用每個 IdP 來連線到 PingFederate，也就是以 SP 作為同盟中樞。

8. 使用 SP 連接至 PingFederate （同盟中樞）作為 IdP。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
