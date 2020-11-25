---
title: Azure Active Directory 中的 SAML 權杖加密
description: 了解如何設定 Azure Active Directory SAML 權杖加密。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 820e2cb0d422597f0e649e6934fd8bb11c1521db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997472"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>如何：設定 Azure AD SAML 權杖加密

> [!NOTE]
> 權杖加密是 Azure Active Directory (Azure AD) 的高級功能。 若要深入了解 Azure AD 版本、功能及定價，請參閱 [Azure AD 定價](https://azure.microsoft.com/pricing/details/active-directory/)。

SAML 權杖加密允許將加密的 SAML 判斷提示與支援它的應用程式一起使用。 當針對應用程式進行設定時，Azure AD 會使用從 Azure AD 中儲存的憑證取得的公開金鑰，將針對該應用程式發出的 SAML 判斷提示進行加密。 應用程式必須使用對應的私密金鑰來解密權杖，然後才能將其作為已登入使用者的驗證辨識項。

加密 Azure AD 與應用程式之間的 SAML 判斷提示，可以進一步確保權杖的內容無法攔截，個人或公司資料也不會外洩。

即使沒有權杖加密，Azure AD SAML 權杖也絕不會在網路上傳遞。 Azure AD 要求透過加密的 HTTPS/TLS 通道進行權杖要求/回應交換，以便 IDP、瀏覽器和應用程式之間的通訊透過加密連結進行。 相較於管理其他憑證的額外負荷，請針對您的情況考慮權杖加密的值。   

若要設定權杖加密，您需要將包含公開金鑰的 X.509 憑證檔案，上傳到代表該應用程式的 Azure AD 應用程式物件。 若要取得 X.509 憑證，您可以從應用程式本身下載此憑證，或在應用程式廠商提供加密金鑰的情況下，向應用程式廠商取得此憑證，或如果應用程式預期由您提供私密金鑰，則可以使用加密工具、上傳至應用程式金鑰存放區的私密金鑰部分，以及上傳至 Azure AD 的對應公開金鑰憑證，來建立此憑證。

Azure AD 會使用 AES-256 加密 SAML 判斷提示的資料。

## <a name="configure-saml-token-encryption"></a>設定 SAML 權杖加密

若要設定 SAML 權杖加密，請依照下列步驟進行操作：

1. 取得與應用程式中設定的私用金鑰對應的公開金鑰憑證。

    建立要用於加密的非對稱金鑰組。 或者，如果應用程式會提供用來加密的公開金鑰，請依照應用程式的指示下載 X.509 憑證。

    公開金鑰應該儲存在 .cer 格式的 X.509 憑證檔案中。

    如果應用程式會使用您為執行個體建立的金鑰，請依照應用程式所提供的指示，安裝應用程式將用來從 Azure AD 租用戶解密權杖的私密金鑰。

1. 將憑證新增至 Azure AD 中的應用程式設定。

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>在 Azure 入口網站中設定權杖加密

您可以將公開憑證新增到 Azure 入口網站中的應用程式設定。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

1. 移至 [Azure Active Directory] > [企業應用程式] 刀鋒視窗，然後選取要為其設定權杖加密的應用程式。

1. 在應用程式的頁面上，選取 [權杖加密]。

    ![Azure 入口網站中的 [權杖加密] 選項](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > [權杖加密] 選項僅適用於從 Azure 入口網站中的 [企業應用程式] 刀鋒視窗設定的 SAML 應用程式，可以是應用程式庫或非資源庫應用程式。 對於其他應用程式，此功能表選項已停用。 對於透過 Azure 入口網站中的 [應用程式註冊] 體驗註冊的應用程式，您可以使用應用程式資訊清單、Microsoft Graph 或 PowerShell 為 SAML 權杖設定加密。

1. 在 [ **權杖加密** ] 頁面上，選取 [匯 **入憑證** ] 以匯入包含公用 x.509 憑證的 .cer 檔案。

    ![匯入包含 X.509 憑證的 .cer 檔案](./media/howto-saml-token-encryption/import-certificate-small.png)

1. 匯入憑證，並設定私密金鑰以在應用程式端上使用後，透過選取指紋狀態旁邊的 [...] 啟用加密，然後從下拉式功能表中的選項選取 [啟用權杖加密]。

1. 選取 [是] 確認啟用權杖加密憑證。

1. 確認為應用程式發出的 SAML 判斷提示已加密。

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>在 Azure 入口網站中停用權杖加密

1. 在 Azure 入口網站中，移至 [Azure Active Directory] > [企業應用程式]，然後選取已啟用 SAML 權杖加密的應用程式。

1. 在應用程式頁面上，選取 [權杖加密]，找到憑證，然後選取 [...] 選項，以顯示下拉式功能表。

1. 選取 [停用權杖加密]。

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>使用 Graph API、PowerShell 或應用程式資訊清單設定 SAML 權杖加密

加密憑證儲存在 Azure AD 中的應用程式物件上，並包含 `encrypt` 使用量標記。 您可以設定多個加密憑證，以及由 `tokenEncryptionKeyID` 屬性識別用於加密權杖的作用中憑證。

您需要應用程式的物件識別碼，才能使用 Microsoft Graph API 或 PowerShell 設定權杖加密。 您可以透過程式設計的方式找到此值，也可以移至 Azure 入口網站中應用程式的 [屬性] 頁面並注意 **物件識別碼** 值。

當您使用 Graph、PowerShell 或應用程式資訊清單設定 keyCredential 時，應產生要用於 keyId 的 GUID。

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>使用 Microsoft Graph 設定權杖加密

1. 使用 X.509 憑證來更新應用程式的 `keyCredentials` 以進行加密。 下列範例示範如何執行。

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. 識別用於加密權杖的作用中加密憑證。 下列範例示範如何執行。

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>使用 PowerShell 設定權杖加密

1. 使用最新的 Azure AD PowerShell 模組連接到您的租使用者。

1. 使用 **[AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** 命令設定權杖加密設定。

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. 使用下列命令讀取權杖加密設定。

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>使用應用程式資訊清單設定權杖加密

1. 從 Azure 入口網站，移至 [Azure Active Directory] > [應用程式註冊]。

1. 從下拉式清單中選取 [所有應用程式]，以顯示所有的應用程式，然後選取您要設定的企業應用程式。

1. 在應用程序的頁面中，選取 [資訊清單] 編輯[應用程式資訊清單](../develop/reference-app-manifest.md)。

1. 設定 `tokenEncryptionKeyId` 屬性的值。

    下列範例示範使用兩個加密憑證設定的應用程式資訊清單，並使用 tokenEnryptionKeyId 將第二個選取為作用中憑證。

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>後續步驟

* 了解 [Azure AD 如何使用 SAML 通訊協定](../develop/active-directory-saml-protocol-reference.md)
* 了解 [Azure AD 中 SAML 權杖](../develop/reference-saml-tokens.md)的格式、安全性特性和內容