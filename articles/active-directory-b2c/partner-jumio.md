---
title: 使用 Jumio 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 在本教學課程中，您將使用 Jumio 設定 Azure Active Directory B2C，以進行自動識別碼驗證，保護客戶資料。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 4e5becdd026b0a1c9e848b183ebeee5833654461
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259266"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Jumio 的教學課程

在此範例教學課程中，我們會提供有關如何整合 Azure Active Directory B2C (Azure AD B2C) 與 [Jumio](https://www.jumio.com/)的指引。 Jumio 是一項識別碼驗證服務，可啟用即時自動化識別碼驗證，以協助保護客戶資料。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的 [Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 使用者。

## <a name="scenario-description"></a>案例描述

Jumio 整合包含下列元件：

- Azure AD B2C：負責驗證使用者認證的授權伺服器。 也稱為身分識別提供者。

- Jumio：使用使用者提供的識別碼詳細資料並加以驗證的服務。

- 中繼 Rest API：可在 Azure AD B2C 與 Jumio 服務之間整合的 API。

- Azure Blob 儲存體：提供自訂 UI 檔案給 Azure AD B2C 原則的服務。

下列架構圖顯示執行。

![Azure AD B2C 與 Jumio 整合的架構圖表。](./media/partner-jumio/jumio-architecture-diagram.png)

|步驟 | 說明 |
|:-----| :-----------|
| 1. | 使用者抵達頁面，以登入或註冊以建立帳戶。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫中介層 API，並傳遞使用者屬性。
| 3. | 仲介層 API 會收集使用者屬性，並將其轉換成 Jumio API 可以使用的格式。 然後，它會將屬性傳送至 Jumio。
| 4. | 在 Jumio 取用並處理資訊之後，它會將結果傳回至中介層 API。
| 5. | 中介層 API 會處理資訊，並將相關資訊傳送回 Azure AD B2C。
| 6. | Azure AD B2C 接收來自仲介層 API 的資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果它顯示成功回應，則會驗證使用者並將其寫入目錄。

## <a name="sign-up-with-jumio"></a>使用 Jumio 註冊

若要建立 Jumio 帳戶，請聯絡 [Jumio](https://www.jumio.com/contact/)。

## <a name="configure-azure-ad-b2c-with-jumio"></a>使用 Jumio 設定 Azure AD B2C

建立 Jumio 帳戶之後，您可以使用該帳戶來設定 Azure AD B2C。 下列各節會依序描述處理常式。

### <a name="deploy-the-api"></a>部署 API

將提供的 [API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) 部署至 Azure 服務。 您可以依照下列 [指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)，從 Visual Studio 發佈程式碼。

>[!NOTE]
>您將需要已部署之服務的 URL，以使用必要的設定來設定 Azure AD。

### <a name="deploy-the-client-certificate"></a>部署用戶端憑證

1. 用戶端憑證可協助保護 Jumio API 呼叫。 使用下列 PowerShell 範例程式碼來建立自我簽署憑證：

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   然後將憑證匯出至指定的位置 ``{your-local-path}`` 。

3. 遵循 [本文中的](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)指示，將憑證匯入 Azure App Service。

### <a name="create-a-signingencryption-key"></a>建立簽署/加密金鑰

建立長度超過64個字元且只包含字母和數位的隨機字串。

例如： ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

使用下列 PowerShell 腳本來建立字串：

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>設定 API

您可以 [在 Azure App Service 中設定應用程式設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)。 使用這個方法，您可以安全地進行設定，而不需要將它們簽入存放庫。 您必須提供下列設定給 Rest API：

| 應用程式設定 | 來源 | 注意 |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio 帳戶設定 |     |
|JumioSettings:AuthPassword | Jumio 帳戶設定 |     |
|AppSettings： SigningCertThumbprint|已建立之自我簽署憑證的指紋|  |
|AppSettings： IdTokenSigningKey| 使用 PowerShell 建立的簽署金鑰 | |
| AppSettings： IdTokenEncryptionKey |使用 PowerShell 建立的加密金鑰
| AppSettings： IdTokenIssuer | 要用於 JWT 權杖的簽發者 (GUID 值是慣用的)  |
| AppSettings： IdTokenAudience  | 要用於 JWT 權杖的物件 (GUID 值是慣用的)  |
|AppSettings： BaseRedirectUrl | Azure AD B2C 原則的基底 URL | HTTPs：//{您的租使用者名稱}. >b2clogin.com .com/{您的應用程式識別碼}|
| WEBSITE_LOAD_CERTIFICATES| 已建立之自我簽署憑證的指紋 |

### <a name="deploy-the-ui"></a>部署 UI

1. [在您的儲存體帳戶中設定 blob 儲存體容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)。

2. 將 ui 檔案從您 blob 容器中的 [ui 資料夾](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) 儲存。

#### <a name="update-ui-files"></a>更新 UI 檔案

1. 在 UI 檔案中，移至 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)的資料夾。

2. 開啟每個 HTML 檔案。

3. 尋找並取代 `{your-ui-blob-container-url}` 為您 blob 容器的 URL。

4. 尋找並取代 `{your-intermediate-api-url}` 為中繼 API app service 的 URL。

>[!NOTE]
> 建議的最佳作法是在 [屬性集合] 頁面上加入同意通知。 通知使用者資訊將會傳送至協力廠商服務進行身分識別驗證。

### <a name="configure-the-azure-ad-b2c-policy"></a>設定 Azure AD B2C 原則

1. 移至 [原則] 資料夾中的 [Azure AD B2C 原則](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) 。

2. 請遵循 [這篇文章](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 來下載 [LocalAccounts 入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)。

3. 設定 Azure AD B2C 租使用者的原則。

>[!NOTE]
>更新提供的原則，以與您特定的租使用者相關聯。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者。 在 [原則] 之下，選取 [Identity Experience Framework]。

2. 選取您先前建立的 **SignUpSignIn**。

3. 選取 [ **執行使用者流程** ]，然後：

   a. 針對 [ **應用程式**]，選取 (範例為 JWT) 的已註冊應用程式。

   b. 針對 [ **回復 url**]，選取重新 **導向 url**。

   c. 選取 [執行使用者流程]。

4. 請完成註冊流程，並建立帳戶。

5. 建立使用者屬性之後，會在流程期間呼叫 Jumio 服務。 如果流程未完成，請檢查使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
