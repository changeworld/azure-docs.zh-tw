---
title: 標頭型驗證搭配 Azure AD 應用程式 Proxy 的 PingAccess | Microsoft Docs
description: 使用 PingAccess 與應用程式 Proxy 來發行應用程式可支援標頭型驗證。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f644203b494e5baf087241e2a4fe669b7db07b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85077894"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用應用程式 Proxy 與 PingAccess 的單一登入之標頭式驗證

Azure Active Directory （Azure AD）應用程式 Proxy 已與 PingAccess 合作，讓您的 Azure AD 客戶可以存取您的應用程式。 PingAccess 會展開[現有應用程式 Proxy 供應項目](application-proxy.md)以包含單一登入存取使用標頭進行驗證的應用程式。

## <a name="whats-pingaccess-for-azure-ad"></a>Azure AD 的 PingAccess 為何？

有了 Azure AD 的 PingAccess，您就可以將使用者存取和單一登入（SSO）授與使用標頭進行驗證的應用程式。 應用程式 Proxy 會如同任何其他應用程式一樣處理這些應用程式，使用 Azure AD 驗證存取，然後透過連接器服務傳遞流量。 PingAccess 位於應用程式前方，並將 Azure AD 的存取權杖轉譯為標頭。 然後，應用程式會以可讀取的格式接收驗證。

使用者在登入使用您公司的應用程式時，將不會注意到什麼不同。 這些還是可以在任何裝置上從任何地方運作。 應用程式 Proxy 連接器會將遠端流量導向至所有應用程式，而不考慮其驗證類型，因此它們仍會自動平衡負載。

## <a name="how-do-i-get-access"></a>如何取得存取權？

由於此案例來自 Azure Active Directory 和 PingAccess 之間的合作關係，因此您需要這兩種服務的授權。 不過，Azure Active Directory Premium 訂用帳戶所包含的基本 PingAccess 授權最多可涵蓋 20 個應用程式。 如果您需要發佈 20 個以上的標頭應用程式，可以從 PingAccess 購買額外的授權。

如需詳細資訊，請參閱 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中發佈應用程式

本文適用于第一次使用此案例來發行應用程式的人員。 除了詳述發佈步驟，它還會引導您開始使用應用程式 Proxy 和 PingAccess。 如果您已經設定這兩項服務，但想要重新整理髮行步驟，請跳至將[您的應用程式新增至使用應用程式 Proxy Azure AD](#add-your-application-to-azure-ad-with-application-proxy)一節。

> [!NOTE]
> 因為此案例是 Azure AD 和 PingAccess 之間的合作關係，有些指示存在於 Ping 身分識別站台。

### <a name="install-an-application-proxy-connector"></a>安裝應用程式 Proxy 連接器

如果您已啟用應用程式 Proxy 並已安裝連接器，則可以略過本節，並移至[使用應用程式 Proxy 將您的應用程式新增至 Azure AD](#add-your-application-to-azure-ad-with-application-proxy)。

「應用程式 Proxy 連接器」是一種 Windows Server 服務，可將來自遠端員工的流量導向至已發佈的應用程式。 如需更詳細的安裝指示，請參閱[教學課程：在 Azure Active Directory 中新增透過應用程式 Proxy 進行遠端存取的內部部署應用程式](application-proxy-add-on-premises-application.md)。

1. 以應用程式系統管理員身分登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 [ **Azure Active Directory 系統管理中心**] 頁面隨即出現。
1. 選取 [ **Azure Active Directory**  >  **應用程式 proxy**  >  **下載連接器服務**]。 [**應用程式 Proxy 連接器下載**] 頁面隨即出現。

   ![應用程式 proxy 連接器下載](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 遵循安裝指示進行。

下載連接器應該會自動啟用您目錄的應用程式 Proxy，但如果沒有，您可以選取 [**啟用應用程式 proxy**]。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>使用應用程式 Proxy 將您的應用程式新增至 Azure AD

您需要在 Azure 入口網站中採取兩個動作。 首先，您必須使用應用程式 Proxy 發佈應用程式。 然後，您需要收集一些關於應用程式的資訊，以供您在 PingAccess 步驟期間使用。

#### <a name="publish-your-application"></a>發佈您的應用程式

您必須先發佈應用程式。 此動作包含：

- 將內部部署應用程式新增至 Azure AD
- 指派使用者來測試應用程式並選擇以標頭為基礎的 SSO
- 設定應用程式的重新導向 URL
- 授與許可權給使用者和其他應用程式使用您的內部部署應用程式

若要發行您自己的內部部署應用程式：

1. 如果您不在上一節中，請以應用程式系統管理員身分登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。
1. 選取 [**企業應用程式**] [  >  **新增應用**程式] [  >  **加入內部部署應用程式**]。 [**新增您自己的內部部署應用程式**] 頁面隨即出現。

   ![新增您自己的內部部署應用程式](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 填寫必要的欄位，並提供新應用程式的相關資訊。 請使用下列指導方針進行設定。

   > [!NOTE]
   > 如需此步驟的更詳細逐步解說，請參閱[將內部部署應用程式新增至 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。

   1. **內部 URL**.. 當您在公司網路上時，通常會提供 URL 以帶您前往應用程式的登入頁面。 針對此案例，連接器需要將 PingAccess proxy 視為應用程式的首頁。 使用此格式︰`https://<host name of your PingAccess server>:<port>`。 連接埠預設為 3000，但您可以在 PingAccess 中設定它。

      > [!WARNING]
      > 針對這種類型的單一登入，內部 URL 必須使用 `https` 且不能使用 `http` 。

   1. **預先驗證方法**：選擇 [ **Azure Active Directory**]。
   1. **轉譯標頭中的 URL**：選擇 [**否**]。

   > [!NOTE]
   > 如果這是您的第一個應用程式，請在變更 PingAccess 設定時，使用連接埠 3000 啟動並返回以更新這項設定。 針對後續的應用程式，埠必須符合您在 PingAccess 中設定的接聽程式。 深入了解 [PingAccess 中的接聽程式](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)。

1. 選取 [新增]。 新應用程式的 [總覽] 頁面隨即出現。

現在指派應用程式測試的使用者，然後選擇標頭型單一登入：

1. 從 [應用程式] 提要欄位中，選取 [**使用者和群組**]  >  [**新增使用者**  >  **和群組] （已 \<Number> 選取）**。 隨即顯示使用者和群組的清單，供您選擇。

   ![顯示使用者和群組的清單](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. 選取要進行應用程式測試的使用者，然後選取 [**選取**]。 請確定此測試帳戶可存取內部部署應用程式。
1. 選取 [**指派**]。
1. 從 [應用程式] 提要欄位中，選取 [**單一登入**  >  **標頭-型**]。

   > [!TIP]
   > 如果這是您第一次使用標頭式單一登入，您需要安裝 PingAccess。 若要確定您的 Azure 訂用帳戶會與 PingAccess 安裝自動產生關聯，請使用此單一登入頁面上的連結來下載 PingAccess。 您現在可以開啟下載網站，或稍後返回此頁面。

   ![顯示以標頭為基礎的登入畫面和 PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. 選取 [儲存]。

然後，請確定您的 [重新導向 URL] 已設定為您的外部 URL：

1. 從**Azure Active Directory 系統管理中心**] 提要欄位中，選取 [ **Azure Active Directory**  >  **應用程式註冊**]。 應用程式清單隨即出現。
1. 選取您的應用程式。
1. 選取 [重新**導向 uri**] 旁的連結，顯示針對 web 和公用用戶端所設定的重新導向 uri 數目。 [ ** \<application name> -驗證**] 頁面隨即出現。
1. 檢查您稍早指派給應用程式的外部 URL 是否位於 [重新**導向 uri** ] 清單中。 如果不是，請立即新增外部 URL，使用**Web**的重新導向 URI 類型，然後選取 [**儲存**]。

最後，設定您的內部部署應用程式，讓使用者擁有讀取存取權，而其他應用程式則具有讀取/寫入權限：

1. 從應用程式的 [**應用程式註冊**] 提要欄位中，選取 [ **API 許可權**] [  >  **新增**  >  **Microsoft api**  >  **Microsoft Graph**的許可權]。 **Microsoft Graph**的 [**要求 API 許可權**] 頁面隨即出現，其中包含適用于 Windows Azure Active Directory 的 api。

   ![顯示 [要求 API 許可權] 頁面](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. 選取 [**委派的許可權**] [  >  **使用者**]  >  **。**
1. 選取**應用程式許可權**  >  **應用**  >  **程式. ReadWrite. 全部**。
1. 選取 [新增權限]。
1. 在 [ **API 許可權**] 頁面中，選取 **[授 \<your directory name> 與系統管理員同意**]。

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步驟的資訊

您必須收集這三項資訊（所有 Guid），才能使用 PingAccess 來設定您的應用程式：

| Azure AD 欄位的名稱 | PingAccess 欄位的名稱 | 資料格式 |
| --- | --- | --- |
| **應用程式 (用戶端) 識別碼** | **用戶端識別碼** | GUID |
| **目錄 (租用戶) 識別碼** | **Issuer** | GUID |
| `PingAccess key` | **用戶端祕密** | 隨機字串 |

若要收集此資訊：

1. 從**Azure Active Directory 系統管理中心**] 提要欄位中，選取 [ **Azure Active Directory**  >  **應用程式註冊**]。 應用程式清單隨即出現。
1. 選取您的應用程式。 應用程式的 [**應用程式註冊**] 頁面隨即出現。

   ![應用程式的註冊總覽](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. 在 [**應用程式（用戶端）識別碼**] 值旁，選取 [**複製到剪貼**簿] 圖示，然後複製並儲存它。 您稍後可以將此值指定為 PingAccess 的用戶端識別碼。
1. 接著在 [**目錄（租使用者）識別碼**] 值中，選取 [**複製到剪貼**簿]，然後複製並儲存。 您稍後會將此值指定為 PingAccess 的簽發者。
1. 從應用程式的 [**應用程式註冊**] 提要欄位中，選取 [**憑證和秘密**] [  >  **新增用戶端密碼**]。 [**新增用戶端密碼**] 頁面隨即出現。

   ![顯示 [新增用戶端密碼] 頁面](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. 在 [**描述**] 中，輸入 `PingAccess key` 。
1. 在 [**到期**日] 底下，選擇設定 PingAccess 金鑰的方式： **1 年**、 **2 年**或**永不**。
1. 選取 [新增]。 PingAccess 索引鍵會出現在用戶端密碼的資料表中，並在 [**值**] 欄位中會自動填入一個隨機字串。
1. 在 [PingAccess] 索引鍵的 [**值**] 欄位旁，選取 [**複製到剪貼**簿] 圖示，然後複製並儲存它。 您稍後可以將此值指定為 PingAccess 的用戶端密碼。

**更新 `acceptMappedClaims` 欄位：**

1. 以應用程式系統管理員身分登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。
1. 選取 [Azure Active Directory]   > [應用程式註冊]  。 應用程式清單隨即出現。
1. 選取您的應用程式。
1. 從應用程式的 [**應用程式註冊**] 頁面的提要欄位中，選取 [**資訊清單**]。 應用程式註冊的資訊清單 JSON 程式碼隨即出現。
1. 搜尋 `acceptMappedClaims` 欄位，並將值變更為 `True` 。
1. 選取 [儲存]。

### <a name="use-of-optional-claims-optional"></a>使用選擇性宣告（選擇性）

選擇性宣告可讓您新增每個使用者和租使用者都具有的標準-但不包含預設的宣告。 您可以藉由修改應用程式資訊清單，為您的應用程式設定選擇性宣告。 如需詳細資訊，請參閱[瞭解 Azure AD 應用程式資訊清單一文](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

在 PingAccess 將使用的 access_token 中包含電子郵件地址的範例：
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>使用宣告對應原則（選擇性）

AzureAD 中不存在之屬性的[宣告對應原則（預覽）](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) 。 宣告對應可讓您藉由新增 ADFS 或使用者物件所支援的其他自訂宣告，將舊的內部內部部署應用程式遷移至雲端

若要讓您的應用程式使用自訂宣告並包含額外的欄位，請確定您也已[建立自訂宣告對應原則，並將其指派給應用程式](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

> [!NOTE]
> 若要使用自訂宣告，您必須已定義自訂原則且已指派給應用程式。 此原則應包含所有必要的自訂屬性。
>
> 您可以透過 PowerShell 或 Microsoft Graph 執行原則定義和指派。 如果您是在 PowerShell 中執行，您可能需要先使用 `New-AzureADPolicy` ，然後將它指派給具有的應用程式 `Add-AzureADServicePrincipalPolicy` 。 如需詳細資訊，請參閱[宣告對應原則指派](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

範例：
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>啟用 PingAccess 以使用自訂宣告

啟用 PingAccess 以使用自訂宣告是選擇性的，但如果您預期應用程式會取用額外的宣告，則為必要。

當您在下列步驟中設定 PingAccess 時，您將建立的 Web 會話（設定 >的存取 >Web 會話）必須取消選取**要求設定檔**，並將 [**使用者屬性**] 設定為 [**否**]

## <a name="download-pingaccess-and-configure-your-application"></a>下載 PingAccess 並設定您的應用程式

現在您已完成所有的 Azure Active Directory 安裝步驟，可以移至設定 PingAccess。

此案例中 PingAccess 部分的詳細步驟會繼續在 Ping 身分識別檔中進行。 遵循 Configure PingAccess for Azure AD 中的指示來保護在 Ping 身分識別網站上[使用 Microsoft Azure AD 應用程式 Proxy 發行的應用程式](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)，並下載[最新版本的 PingAccess](https://www.pingidentity.com/en/lp/azure-download.html?)。

這些步驟可協助您安裝 PingAccess，並設定 PingAccess 帳戶（如果還沒有的話）。 然後，若要建立 Azure AD OpenID Connect （OIDC）連線，您可以使用您從 Azure AD 入口網站複製的**目錄（租使用者）識別碼**值來設定權杖提供者。 接下來，若要在 PingAccess 上建立 web 會話，請使用**應用程式（用戶端）識別碼**和 `PingAccess key` 值。 之後，您可以設定身分識別對應，並建立虛擬主機、網站和應用程式。

### <a name="test-your-application"></a>測試您的應用程式

當您完成所有這些步驟時，您的應用程式應該會啟動並執行。 若要進行測試，請開啟瀏覽器，然後流覽至您在 Azure 中發佈應用程式時所建立的外部 URL。 使用您指派給應用程式的測試帳戶登入。

## <a name="next-steps"></a>後續步驟

- [設定 PingAccess 以 Azure AD，以保護使用 Microsoft Azure AD 應用程式 Proxy 發佈的應用程式](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)
