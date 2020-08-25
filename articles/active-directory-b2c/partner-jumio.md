---
title: 使用 Jumio 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 本教學課程說明如何使用 Jumio 設定自動識別碼驗證的 Azure Active Directory B2C，保護客戶資料
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817355"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Jumio 的教學課程

在此範例教學課程中，我們會提供有關如何將 Azure AD B2C 與 [Jumio](https://www.jumio.com/)整合的指引。 Jumio 是一種識別碼驗證服務，可啟用即時自動化識別碼驗證，保護客戶資料。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- [Azure AD B2C 的租](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。 租使用者已連結至您的 Azure 訂用帳戶。

## <a name="scenario-description"></a>案例描述

Jumio 整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者

- Jumio –接受使用者提供的識別碼詳細資料並加以驗證的服務。

- 中繼 Rest API –此 API 會在 Azure AD B2C 與 Jumio 服務之間進行整合。

- Blob 儲存體–用來將自訂 UI 檔案提供給 Azure AD B2C 原則。

下列架構圖顯示執行。

![Jumio 的螢幕擷取畫面-架構-圖表](./media/partner-jumio/jumio-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者選取 [註冊] 以建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫中介層 API，並傳遞使用者屬性。
| 3. | 仲介層 API 會收集使用者屬性，並將其轉換為 Jumio API 可能取用的格式。 然後將它傳送至 Jumio。
| 4. | Jumio 取用並處理資訊之後，它會將結果傳回至中介層 API。
| 5. | 仲介層 API 會處理資訊，並將相關資訊傳送回 Azure AD B2C。
| 6. | Azure AD B2C 接收來自仲介層 API 的資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果它顯示成功回應，則會驗證使用者並將其寫入目錄。

## <a name="onboard-with-jumio"></a>使用 Jumio 上線

1. 若要建立 Jumio 帳戶，請聯絡 [Jumio](https://www.jumio.com/contact/)

2. 建立帳戶之後，就會在 API 設定中使用資訊。 下列各節將說明此程式。

## <a name="configure-azure-ad-b2c-with-jumio"></a>使用 Jumio 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 [API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) 部署至 Azure 服務。 您可以依照下列 [指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)，從 Visual Studio 發佈程式碼。

>[!NOTE]
>您將需要已部署之服務的 URL，以使用必要的設定來設定 Azure AD。

### <a name="part-2---deploy-the-client-certificate"></a>第2部分-部署用戶端憑證

1. Jumio API 呼叫受用戶端憑證保護。 使用下面所述的 PowerShell 範例程式碼來建立自我簽署憑證：

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. 憑證接著會匯出到指定給 {} 的位置 ``your-local-path`` 。

3. 遵循本 [檔](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)中所述的指示，將憑證匯入 Azure App 服務。

### <a name="part-3---create-a-signingencryption-key"></a>第3部分-建立簽署/加密金鑰

建立長度超過64個字元的隨機字串，其中只包含字母或數位。

例如：``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

使用下列 PowerShell 腳本來建立64字元長度的英數位元值。

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>第4部分-設定 API

您可以在 [Azure 中的 App service 中設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)應用程式設定。 使用這個方法，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須提供下列設定給 Rest API：

| 應用程式設定 | 來源 | 備註 |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio 帳戶設定 |     |
|JumioSettings:AuthPassword | Jumio 帳戶設定 |     |
|AppSettings： SigningCertThumbprint|已建立的自我簽署憑證指紋|  |
|AppSettings： IdTokenSigningKey| 使用 PowerShell 建立的簽署金鑰 | |
| AppSettings： IdTokenEncryptionKey |使用 PowerShell 建立的加密金鑰
| AppSettings： IdTokenIssuer | 要用於 JWT 權杖的簽發者 (guid 值是慣用的)  |
| AppSettings： IdTokenAudience  | 要用於 JWT 權杖的物件 (guid 值是慣用的)  |
|AppSettings： BaseRedirectUrl | B2C 原則的基底 url | HTTPs：//{您的租使用者名稱}. >b2clogin.com .com/{您的應用程式識別碼}|
| WEBSITE_LOAD_CERTIFICATES| 已建立的自我簽署憑證指紋 |

### <a name="part-5---deploy-the-ui"></a>第5部分-部署 UI

1. [在您的儲存體帳戶中設定 blob 儲存體容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)。

2. 將 ui 檔案從 [ui 資料夾](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) 儲存至 blob 容器。

#### <a name="update-ui-files"></a>更新 UI 檔案

1. 在 UI 檔案中，移至資料夾 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. 開啟每個 html 檔案。

3. 使用您的 blob 容器 URL 尋找並取代 {您的 ui-blob-容器-url}。

4. 尋找並以中繼 API app service 的 URL 取代 {您的中繼 api-url}。

>[!NOTE]
> 建議的最佳作法是在 [屬性集合] 頁面中，將同意通知新增至客戶。 通知使用者資訊將傳送至協力廠商服務進行身分識別驗證。

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>第6部分-設定 Azure AD B2C 原則

1. 移至 [原則] 資料夾中的 [Azure AD B2C 原則](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) 。

2. 遵循本 [檔](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 以下載 [LocalAccounts 入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. 設定 Azure AD B2C 租使用者的原則。

>[!NOTE]
>更新提供的原則，以與您特定的租使用者相關聯。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 底下選取 [ **Identity Experience Framework**]。

2. 選取您先前建立的 **SignUpSignIn**。

3. 選取 [ **執行使用者流程** ]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式 (範例為 JWT) 

   b. **回復 url**：選取重新 **導向 url**

   c. 選取 [執行使用者流程]。

4. 進行註冊流程並建立帳戶

5. 建立使用者屬性之後，會在流程期間呼叫 Jumio 服務。 如果流程未完成，請檢查使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
