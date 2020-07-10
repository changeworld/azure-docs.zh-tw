---
title: IDology 與 Azure Active Directory B2C 整合
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure AD B2C 中整合範例線上付款應用程式與 IDology。 IDology 是具有多個解決方案的身分識別驗證和校對提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7567302be8d717cda3627af303128bfb704bd014
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170134"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 IDology 的教學課程 

在此範例教學課程中，我們會提供如何整合 Azure AD B2C 與[IDology](https://www.idology.com/solutions/)的指引。 IDology 是具有多個解決方案的身分識別驗證和校對提供者。 在此範例中，我們將討論 IDology 的 ExpectID 解決方案。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。

## <a name="scenario-description"></a>案例描述

IDology 整合包含下列元件：

- Azure AD B2C –負責驗證使用者認證的授權伺服器。 它也稱為身分識別提供者。
- IDology – IDology 服務會接受使用者提供的輸入，並驗證使用者的身分識別。
- 自訂 Rest API –此 API 會實現 Azure AD 與 IDology 服務之間的整合。

下列架構圖顯示執行的方式。

![IDology 架構圖](media/partner-idology/idology-architecture-diagram.png)

| 步驟 | 描述 |
|------|------|
|1     | 使用者抵達登入頁面。 |
|2     | 使用者選取註冊選項來建立新帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。 |
|3     | Azure AD B2C 會呼叫仲介層 API，並在使用者屬性上傳遞。 |
|4     | 中介層 API 會收集使用者屬性，並將其轉換成 IDOlogy API 可使用的格式。 然後，它會將資訊傳送至 IDology。 |
|5     | IDology 會取用資訊並加以處理，然後將結果傳回中介層 API。 |
|6     | 中介層 API 會處理資訊，並將相關資訊傳回 Azure AD B2C。 |
|7     | Azure AD B2C 從仲介層 API 接收資訊。 如果顯示**失敗**回應，則會向使用者顯示錯誤訊息。 如果顯示**成功**回應，則會驗證使用者並將其寫入目錄。 |
|      |      |

> [!NOTE]
> Azure AD B2C 也可以要求客戶執行「逐步驗證」，但此案例不在本教學課程的範圍內。

## <a name="onboard-with-idology"></a>使用 IDology 上架

1. IDology 提供各種不同的解決方案，您可以在[這裡](https://www.idology.com/solutions/)找到。 在此範例中，我們使用 ExpectID。

2. 若要建立 IDology 帳戶，請聯絡[IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)。

3. 建立帳戶之後，您將會收到 API 設定所需的資訊。 下列各節將說明此程式。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 API 程式碼部署至 Azure 服務。 程式碼可以從 Visual Studio 發佈，請遵循這些[指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)。

您將需要已部署之服務的 URL，才能使用必要的設定來設定 Azure AD。

### <a name="part-2---configure-the-api"></a>第2部分-設定 API 

應用程式設定可以[在 Azure App Service 中設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)。 使用此方法時，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須將下列設定提供給 Rest API：

| 應用程式設定 | 來源 | 附註 |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology 帳戶設定 |     |
|IdologySettings:ApiPassword | IDology 帳戶設定 |     |
|WebApiSettings:ApiUsername |定義 API 的使用者名稱| 用於 ExtId 設定 |
|WebApiSettings:ApiPassword | 定義 API 的密碼 | 用於 ExtId 設定

### <a name="part-3---create-api-policy-keys"></a>第3部分-建立 API 原則金鑰

請遵循這[份檔](secure-rest-api.md#add-rest-api-username-and-password-policy-keys)來建立兩個原則金鑰：一個用於 api 使用者名稱，另一個用於您在上方定義的 api 密碼。

範例原則會使用下列索引鍵名稱：

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第4部分-設定 Azure AD B2C 原則

1. 請遵循此[檔](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)下載[LocalAccounts 入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)，並設定 Azure AD B2C 租使用者的原則。 依照指示進行，直到完成 [**測試自訂原則**] 區段。

2. 在[此](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)下載兩個範例原則。

3. 更新兩個範例原則：

   1. 開啟這兩個原則：

      1. 在區段中 `Idology-ExpectId-API` ，以 `ServiceUrl` 上述部署的 API 位置更新中繼資料專案。

      1. 將 `yourtenant` 取代為您的 Azure AD B2C 租用戶名稱。
      例如，如果您 Azure AD B2C 的租使用者名稱是  `contosotenant` ，請將的所有實例取代為  `yourtenant.onmicrosoft.com`   `contosotenant.onmicrosoft.com` 。

   1. 開啟檔案 TrustFrameworkExtensions.xml：

      1. 尋找元素  `<TechnicalProfile Id="login-NonInteractive">` 。  `IdentityExperienceFrameworkAppId`   以您稍早建立之 IdentityExperienceFramework 應用程式的應用程式識別碼，取代的兩個實例。

      1.  `ProxyIdentityExperienceFrameworkAppId`   以您稍早建立之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼，取代的兩個實例。

4. 使用兩個已更新的範例原則，取代先前上傳至步驟1中 Azure AD B2C 的 SignInorSignUp.xml 和 TrustFrameworkExtensions.xml。

> [!NOTE]
> 我們建議的最佳作法是在 [屬性集合] 頁面中新增同意通知給客戶。 通知使用者資訊將會傳送至協力廠商服務以進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 [Azure AD B2C 租使用者]，然後在 [**原則**] 下選取 [**使用者流程**]。

2. 選取您先前建立的**使用者流程**。

3. 選取 [**執行使用者流程**]，然後選取設定：

   1. **應用程式**-選取已註冊的應用程式 (範例是 JWT) 。

   1. **回復 url** -選取 [重新**導向 url**]。

   1. 選取 [執行使用者流程]。

4. 請完成註冊流程並建立帳戶。

5. 登出。

6. 請完成登入流程。

7. 當您進入 [**繼續**] 之後，就會出現 IDology 謎題。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](custom-policy-overview.md)

- [開始使用 Azure AD B2C 中的自訂原則](custom-policy-get-started.md?tabs=applications) 

