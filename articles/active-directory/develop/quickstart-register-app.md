---
title: 快速入門：在 Microsoft 身分識別平台註冊應用程式 | Azure
description: 在本快速入門中，您將了解如何向 Microsoft 身分識別平台註冊應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 9cd59d6bf5b9bf6e17cba0786bfac27ed12d7638
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258127"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入門：使用 Microsoft 身分識別平台來註冊應用程式

在本快速入門中，您會在 Azure 入口網站中註冊應用程式，因此 Microsoft 身分識別平台可為您的應用程式和其使用者提供驗證和授權服務。

您想要讓 Microsoft 身分識別平台執行身分識別與存取權管理 (IAM) 的每個應用程式都必須註冊。 無論是用戶端應用程式 (例如 web 或行動應用程式)，或支援用戶端應用程式的 Web API，註冊會在您的應用程式與身分識別提供者 (Microsoft 身分識別平台) 之間建立信任關係。

## <a name="prerequisites"></a>先決條件

* 包含作用中訂用帳戶的 Azure 帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 完成[快速入門：設定租用戶](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>註冊應用程式

註冊應用程式會在您的應用程式與 Microsoft 身分識別平台之間建立信任關係。 信任是單向的：您的應用程式會信任 Microsoft 身分識別平台，反之則不同。

請依照這些步驟建立應用程式註冊：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您有多個租用的存取權，請使用頂端功能表中的**目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/quickstart-register-app/portal-01-directory-subscription-filter.png" border="false"::: 來選取要在其中註冊應用程式的租用戶。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [管理]下選取 [應用程式註冊]，再選取 [新增註冊]。
1. 輸入應用程式的**名稱**。 您的應用程式使用者可能會看到此名稱，您可以稍後再變更。
1. 指定可以使用應用程式的人員，有時也稱為「登入受眾」。

    | 支援的帳戶類型 | 描述 |
    |-------------------------|-------------|
    | **僅此組織目錄中的帳戶** | 如果您要建置的應用程式僅供「您」租用戶中的使用者 (或來賓) 使用，請選取此選項。<br><br>通常稱為「企業營運應用程式」 (LOB) ，這是 Microsoft 身分識別平台中的**單一租用戶**應用程式。 |
    | **任何組織目錄中的帳戶** | 如果您想要讓「任何」Azure AD 租用戶中的使用者都能使用您的應用程式，請選取此選項。 例如，如果要建立要提供給多個組織的軟體即服務 (SaaS) 應用程式，則適合使用此選項。<br><br>這在 Microsoft 身分識別平台中稱為**多租用戶**應用程式。 |
    | **任何組織目錄中的帳戶及個人的 Microsoft 帳戶** | 選取此選項以鎖定最廣泛的一組客戶。<br><br>選取此選項，即表示您註冊的**多租用戶**應用程式也可以支援個人 **Microsoft 帳戶** (MSA)。 |
    | **個人 Microsoft 帳戶** | 如果您要建置的應用程式僅供個人 Microsoft 帳戶中的使用者使用，請選取此選項。 個人 Microsoft 帳戶包括 Skype、Xbox、Live 和 Hotmail 帳戶。 |

1. 請勿在**重新導向 URI (選用)** 中輸入任何項目，您將在下一節中設定。
1. 選取 [註冊] 以完成伺服器初始註冊。

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Web 瀏覽器中的 Azure 入口網站螢幕擷取畫面，其中顯示註冊應用程式窗格。":::

註冊完成時，Azure 入口網站會顯示應用程式註冊的**概觀**窗格，其中包含其**應用程式 (用戶端) 識別碼**。 也稱為僅*用戶端識別碼*，此唯一值可用來在 Microsoft 身分識別平台中識別您的應用程式。

應用程式的程式碼 (或通常是應用程式中使用的驗證程式庫) 也會使用用戶端識別碼作為驗證其從身分識別平台接收之安全性權杖的一個方式。

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Web 瀏覽器中的 Azure 入口網站螢幕擷取畫面，其中顯示註冊應用程式窗格。":::

## <a name="add-a-redirect-uri"></a>新增重新導向 URI

重新導向 URI 是 Microsoft 身分識別平台將使用者重新導向的用戶端，且會在驗證之後傳送安全性權杖的位置。

例如，在生產 Web 應用程式中，重新導向 URI 通常是執行您應用程式的公用端點，例如 `https://contoso.com/auth-response`。 在開發期間，通常也會新增您在本機執行應用程式的端點，例如 `https://127.0.0.1/auth-response` 或 `http://localhost/auth-response`。

您可以藉由設定其[平台設定](#configure-platform-settings)，為已註冊的應用程式新增和修改重新導向 URI。

### <a name="configure-platform-settings"></a>設定平台設定

每種應用程式類型的設定 (包括重新導向 URI) 都是在 Azure 入口網站的**平台組態**中設定。 某些平台 (例如 **Web** 和**單一頁面應用程式**) 需要您手動指定重新導向 URI。 針對其他平台 (例如行動裝置和桌面)，您可以選取在設定其他設定時為您產生的重新導向 URI。

若要根據平台或裝置來設定應用程式設定，您的目標是：

1. 在 Azure 入口網站的**應用程式註冊**中，選取您的應用程式。
1. 在 [管理] 底下，選取 [驗證]。
1. 在 [平台設定] 下，選取 [新增平台]。
1. 在 **設定平台**中，選取應用程式類型 (平台) 的圖格以加以設定。

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Web 瀏覽器中的 Azure 入口網站螢幕擷取畫面，其中顯示註冊應用程式窗格。" border="false":::

    | 平台 | 組態設定 |
    | -------- | ---------------------- |
    | **Web** | 為應用程式輸入**重新導向 URI**，這是 Microsoft 身分識別平台將使用者重新導向的用戶端，且會在驗證之後傳送安全性權杖的位置。<br/><br/>針對在伺服器上執行的標準 Web 應用程式，選取此平台。 |
    | **單一頁面應用程式** | 為應用程式輸入**重新導向 URI**，這是 Microsoft 身分識別平台將使用者重新導向的用戶端，且會在驗證之後傳送安全性權杖的位置。<br/><br/>如果您要以 JavaScript 建立用戶端 Web 應用程式，或使用像是Angular、Vue.js、React.js 或 Blazor WebAssembly 的架構，請選取此平台。 |
    | **iOS / macOS** | 輸入應用程式的**套件組合識別碼**，您可以在 XCode 中的 Info.plist 或組建設定中找到此識別碼。<br/><br/>指定套件組合識別碼時，系統會為您產生重新導向 URI。 |
    | **Android** | 輸入應用程式的**套件名稱**，您可以在 AndroidManifest.xml 檔案中找到，並產生再輸入**簽章雜湊**。<br/><br/>指定這些設定時，系統會為您產生重新導向 URI。 |
    | **行動應用程式與傳統型應用程式** | 選取其中一個**建議的重新導向 URI**，或指定**自訂重新導向 URI**。<br/>針對桌面應用程式，建議您：<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>針對未使用最新 Microsoft 驗證程式庫 (MSAL) 或未使用訊息代理程式的行動應用程式，請選取此平台。 此外，也請為桌面應用程式選取此平台。 |
1. 選取 [設定] 以完成平台組態。

### <a name="redirect-uri-restrictions"></a>重新導向 URI 的限制

您新增至應用程式註冊的重新導向 URI 格式有特定限制。 如需這些限制詳細資訊，請參閱[重新導向 URI (回覆 URL) 的限制](reply-url.md)。

## <a name="add-credentials"></a>新增認證

存取 Web API 的機密用戶端應用程式會使用認證。 機密用戶端的範例包括 Web 應用程式、其他 Web API，或服務和精靈類型的應用程式。 認證可讓您的應用程式以自己的身分進行驗證，不需要在執行階段與使用者進行互動。

您可以將憑證和用戶端密碼 (字串) 當作認證新增至您的機密用戶端應用程式註冊。

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Web 瀏覽器中的 Azure 入口網站螢幕擷取畫面，其中顯示註冊應用程式窗格。":::

### <a name="add-a-certificate"></a>新增憑證

有時也稱為「公開金鑰」，憑證是建議的認證類型，因為憑證提供比用戶端密碼更高的保證層級。

1. 在 Azure 入口網站的**應用程式註冊**中，選取您的應用程式。
1. 選取 [憑證及祕密] > [上傳憑證]。
1. 選取您想要上傳的檔案。 檔案必須是下列其中一種檔案類型：.cer、.pem、.crt。
1. 選取 [新增]  。

### <a name="add-a-client-secret"></a>新增用戶端密碼

用戶端密碼 (也稱為「應用程式密碼」)是一個字串值，您的應用程式可以用來來取代憑證，以識別自己的身分。 這兩種認證類型較容易使用，而且通常會在開發期間使用，但普遍認為其安全性比不上憑證。 您應該在生產環境中執行的應用程式中使用憑證。

1. 在 Azure 入口網站的**應用程式註冊**中，選取您的應用程式。
1. 選取 [憑證及祕密] >  [新增用戶端密碼]。
1. 新增用戶端密碼的描述。
1. 選取持續時間。
1. 選取 [新增]  。
1. **記錄祕密值**以用於您的用戶端應用程式程式碼 - 此值在您離開此頁面後就「不會再次顯示」。

## <a name="next-steps"></a>後續步驟

用戶端應用程式通常需要存取 Web API 中的資源。 除了使用 Microsoft 身分識別平台來保護您的用戶端應用程式，您還可以使用平台來授權範圍，以及授權以權限為基礎的 Web API 存取。

請移至本系列中的下一個快速入門，為您的 Web API 建立另一個應用程式註冊，並公開其範圍。

> [!div class="nextstepaction"]
> [設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)
