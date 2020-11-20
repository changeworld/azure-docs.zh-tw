---
title: 使用 Experian 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何將 Azure AD B2C authentication 與 Experian 整合，以根據使用者屬性進行識別驗證和驗證，以防止詐騙。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 29116d880a51444eb45a351e2118a07d13873043
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953843"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Experian 的教學課程

在此範例教學課程中，我們會提供有關如何將 Azure AD B2C 與 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)整合的指引。 Experian 提供各種不同的解決方案，您可以在 [這裡](https://www.experian.com/)找到。

在此範例中，會使用 Experian 的整合數位身分識別與詐騙風險平臺 **CrossCore** 。 CrossCore 是用來驗證使用者識別的識別碼驗證服務。 它會根據使用者在註冊流程期間所提供的幾項資訊來進行風險分析。 CrossCore 用來判斷是否應該允許使用者繼續登入。 下列屬性可以在 CrossCore 風險分析中使用：

- 電子郵件
- IP 位址
- 名字
- Middle Name
- Surname
- 街道地址
- City
- 州/省
- 郵遞區號
- 國家/地區
- 電話號碼

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的[Azure AD B2C 租](./tutorial-create-tenant.md)使用者。

## <a name="scenario-description"></a>案例描述

Experian 整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者

- Experian – Experian 服務會接受使用者提供的輸入，並驗證使用者的身分識別

- 自訂 Rest API-此 API 會在 Azure AD B2C 與 Experian 服務之間進行整合。

下列架構圖顯示執行。

![experian 的螢幕擷取畫面-架構-圖表](media/partner-experian/experian-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者選取註冊以建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫中介層 API，並傳遞使用者屬性。
| 3. | 仲介層 API 會收集使用者屬性，並將其轉換為 Experian API 可能取用的格式。 然後，將它傳送至 Experian。
| 4. | Experian 會取用此資訊並加以處理，以根據風險分析來驗證使用者識別。 然後，它會將結果傳回至中介層 API。
| 5. | 仲介層 API 會處理資訊，並以正確的 JSON 格式將相關資訊傳回 Azure AD B2C。
| 6. | Azure AD B2C 接收來自仲介層 API 的資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果它顯示成功回應，則會驗證使用者並將其寫入目錄。

## <a name="onboard-with-experian"></a>使用 Experian 上線

1. 若要建立 Experian 帳戶，請聯絡 [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. 建立帳戶之後，您將會收到 API 設定所需的資訊。 下列各節將說明此程式。

## <a name="configure-azure-ad-b2c-with-experian"></a>使用 Experian 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 [API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) 部署至 Azure 服務。 您可以依照下列 [指示](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)，從 Visual Studio 發佈程式碼。

>[!NOTE]
>您將需要已部署之服務的 URL，以使用必要的設定來設定 Azure AD。

### <a name="part-2---deploy-the-client-certificate"></a>第2部分-部署用戶端憑證

Experian API 呼叫受用戶端憑證保護。 Experian 會提供此用戶端憑證。 遵循本 [檔](../app-service/environment/certificates.md#private-client-certificate)中所述的指示，必須將憑證上傳到 Azure App 服務。 範例原則會在此程式中使用這些按鍵步驟：

- 上傳憑證

- `WEBSITE_LOAD_ROOT_CERTIFICATES`使用憑證的憑證指紋來設定金鑰。

### <a name="part-3---configure-the-api"></a>第3部分-設定 API

您可以在 [Azure 中的 App service 中設定](../app-service/configure-common.md#configure-app-settings)應用程式設定。 使用這個方法，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須提供下列設定給 Rest API：

| 應用程式設定 | 來源 | 注意 |
| :-------- | :------------| :-----------|
|CrossCoreConfig： TenantId | Experian 帳戶設定 |     |
|CrossCoreConfig:OrgCode | Experian 帳戶設定 |     |
|CrossCore:ApiEndpoint |Experian 帳戶設定|  |
|CrossCore:ClientReference | Experian 帳戶設定 | |
| CrossCore:ModelCode |Experian 帳戶設定|
| CrossCore:OrgCode | Experian 帳戶設定 |
| CrossCore:SignatureKey  | Experian 帳戶設定 |
| CrossCore： TenantId  | Experian 帳戶設定 |
| CrossCore： CertificateThumbprint | Experian 憑證 |
| BasicAuth:ApiUsername | 定義 API 的使用者名稱 | 用於 ExtId 設定 |
| BasicAuth:ApiPassword | 定義 API 的密碼 | 用於 ExtId 設定

### <a name="part-4---create-api-policy-keys"></a>第4部分-建立 API 原則金鑰

請參閱這 [份檔](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) ，並建立兩個原則金鑰（一個用於 api 使用者名稱），一個用於您針對 HTTP 基本驗證定義的 api 密碼。

>[!NOTE]
>您稍後將需要用來設定原則的金鑰。

### <a name="part-5---replace-the-configuration-values"></a>第5部分-取代設定值

在提供的 [自訂原則](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)中，找出下列預留位置，並取代為您實例中的對應值

|                      預留位置                       |                                   取代值                                 |                   範例                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | 您租使用者的簡短名稱                                                           | 來自 yourtenant.onmicrosoft.com 的 ">yourtenant.msappproxy.net" |
| {your_trustframeworkbase_policy}                       | TrustFrameworkBase 原則的 Azure AD B2C 名稱                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | 您 Azure AD B2C 租使用者中所設定之 IdentityExperienceFramework 應用程式的應用程式識別碼      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | 您 Azure AD B2C 租使用者中所設定之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼 | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租使用者儲存體應用程式的應用程式識別碼                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租使用者儲存體應用程式的物件識別碼                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | 您在[此處](#part-4---create-api-policy-keys)建立的使用者名稱金鑰名稱             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | 您在[此處](#part-4---create-api-policy-keys)建立的密碼金鑰名稱             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | 您已設定之 app service 的 URL                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>第6部分-設定 Azure AD B2C 原則

如需如何設定 Azure AD B2C 租使用者和設定原則的指示，請參閱這 [份檔](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 。

>[!NOTE]
>此範例原則是以 [本機帳戶入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)為基礎。

>[!NOTE]
> 建議的最佳作法是在 [屬性集合] 頁面中，將同意通知新增至客戶。 通知使用者資訊將傳送至協力廠商服務進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 底下選取 [ **使用者流程**]。

2. 選取您先前建立的 **使用者流程**。

3. 選取 [ **執行使用者流程** ]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式 (範例為 JWT) 

   b. **回復 url**：選取重新 **導向 url**

   c. 選取 [執行使用者流程]。

4. 進行註冊流程並建立帳戶

5. 登出

6. 登入流程  

7. 當您進入 [ **繼續**] 之後，CrossCore 拼圖將會出現。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](./custom-policy-overview.md)

- [Azure AD B2C 中的自訂原則入門](./custom-policy-get-started.md?tabs=applications)