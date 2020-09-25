---
title: IDology 與 Azure Active Directory B2C 整合
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure AD B2C 中整合範例 online 付款應用程式與 IDology。 IDology 是具有多個解決方案的身分識別驗證和校對提供者。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cfce5b42d37908d0ba89cff9c4831cb25b968524
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259317"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 IDology 的教學課程 

在此範例教學課程中，我們會提供有關如何將 Azure AD B2C 與 [IDology](https://www.idology.com/solutions/)整合的指引。 IDology 是具有多個解決方案的身分識別驗證和校對提供者。 在此範例中，我們將涵蓋 IDology 的 ExpectID 解決方案。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。

## <a name="scenario-description"></a>案例描述

IDology 整合包含下列元件：

- Azure AD B2C –負責驗證使用者認證的授權伺服器。 也稱為身分識別提供者。
- IDology – IDology 服務會接受使用者提供的輸入，並驗證使用者的身分識別。
- 自訂 Rest API-此 API 會在 Azure AD 與 IDology 服務之間進行整合。

下列架構圖顯示執行。

![IDology 架構圖表](media/partner-idology/idology-architecture-diagram.png)

| 步驟 | 說明 |
|------|------|
|1     | 使用者抵達登入頁面。 |
|2     | 使用者選取註冊選項以建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。 |
|3     | Azure AD B2C 會呼叫中介層 API，並傳遞使用者屬性。 |
|4     | 仲介層 API 會收集使用者屬性，並將其轉換成 IDOlogy API 可以使用的格式。 然後，它會將資訊傳送至 IDology。 |
|5     | IDology 會取用資訊並加以處理，然後將結果傳回至中介層 API。 |
|6     | 仲介層 API 會處理資訊，並將相關資訊傳回 Azure AD B2C。 |
|7     | Azure AD B2C 接收來自仲介層 API 的資訊。 如果顯示 **失敗** 回應，則會向使用者顯示錯誤訊息。 如果它顯示 **成功** 回應，則會驗證使用者並將其寫入目錄。 |
|      |      |

> [!NOTE]
> Azure AD B2C 也可以要求客戶執行逐步驗證，但此案例不在本教學課程的討論範圍內。

## <a name="onboard-with-idology"></a>使用 IDology 上線

1. IDology 提供各種不同的解決方案，您可以在 [這裡](https://www.idology.com/solutions/)找到。 針對此範例，我們會使用 ExpectID。

2. 若要建立 IDology 帳戶，請聯絡 [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)。

3. 建立帳戶之後，您將會收到 API 設定所需的資訊。 下列各節將說明此程式。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 [API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) 部署至 Azure 服務。 您可以依照下列 [指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)，從 Visual Studio 發佈程式碼。

您將需要已部署之服務的 URL，以使用必要的設定來設定 Azure AD。

### <a name="part-2---configure-the-api"></a>第2部分-設定 API 

您可以 [在 Azure 的 App Service 中設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)應用程式設定。 使用這個方法，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須提供下列設定給 Rest API：

| 應用程式設定 | 來源 | 注意 |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology 帳戶設定 |     |
|IdologySettings:ApiPassword | IDology 帳戶設定 |     |
|WebApiSettings:ApiUsername |定義 API 的使用者名稱| 用於 ExtId 設定 |
|WebApiSettings:ApiPassword | 定義 API 的密碼 | 用於 ExtId 設定

### <a name="part-3---create-api-policy-keys"></a>第3部分-建立 API 原則金鑰

遵循本 [檔](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) 以建立兩個原則金鑰：一個用於 api 使用者名稱，另一個用於您在上面定義的 api 密碼。

範例原則會使用這些金鑰名稱：

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第4部分-設定 Azure AD B2C 原則

1. 請依照本 [檔](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 下載 [LocalAccounts 入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) ，並設定 Azure AD B2C 租使用者的原則。 請依照指示進行，直到您完成 [ **測試自訂原則** ] 區段。

2. 請在 [這裡](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)下載兩個範例原則。

3. 更新兩個範例原則：

   1. 開啟這兩個原則：

      1. 在區段中 `Idology-ExpectId-API` ， `ServiceUrl` 以上述部署的 API 位置更新中繼資料專案。

      1. 將 `yourtenant` 取代為您的 Azure AD B2C 租用戶名稱。
      例如，如果您 Azure AD B2C 租使用者的名稱是  `contosotenant` ，請將的所有實例取代為  `yourtenant.onmicrosoft.com`   `contosotenant.onmicrosoft.com` 。

   1. 開啟檔案 TrustFrameworkExtensions.xml：

      1. 尋找元素  `<TechnicalProfile Id="login-NonInteractive">` 。 將兩個實例取代為  `IdentityExperienceFrameworkAppId`   您稍早建立之 IdentityExperienceFramework 應用程式的應用程式識別碼。

      1. 將兩個實例取代為  `ProxyIdentityExperienceFrameworkAppId`   您稍早建立之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼。

4. 以兩個更新的範例原則取代先前上傳至步驟1中 Azure AD B2C 的 SignInorSignUp.xml 和 TrustFrameworkExtensions.xml。

> [!NOTE]
> 建議的最佳作法是在 [屬性集合] 頁面中，將同意通知新增至客戶。 通知使用者資訊將會傳送至協力廠商服務進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [ **原則**] 底下選取 [ **使用者流程**]。

2. 選取您先前建立的 **使用者流程**。

3. 選取 [ **執行使用者流程** ]，然後選取設定：

   1. **應用程式** -選取已註冊的應用程式 (範例為 JWT) 。

   1. **回復 url** -選取重新 **導向 url**。

   1. 選取 [執行使用者流程]。

4. 請完成註冊流程，並建立帳戶。

5. 登出。

6. 經歷登入流程。

7. 當您輸入 **continue**之後，會出現 IDology 謎。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](custom-policy-overview.md)

- [Azure AD B2C 中的自訂原則入門](custom-policy-get-started.md?tabs=applications) 

