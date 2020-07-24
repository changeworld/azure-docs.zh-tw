---
title: 使用 Experian 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 Experian，以根據使用者屬性進行識別驗證和檢查，以防止詐騙。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2a69bc40090e74ad5885c2576c040693f90d5d03
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096153"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Experian 的教學課程

在此範例教學課程中，我們會提供如何整合 Azure AD B2C 與[Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)的指引。 Experian 提供各種不同的解決方案，您可以在[這裡](https://www.experian.com/)找到。

在此範例中，會使用 Experian 的整合式數位身分識別和詐騙風險平臺**CrossCore** 。 CrossCore 是用來驗證使用者識別的識別碼驗證服務。 它會根據使用者在註冊流程期間所提供的幾項資訊來進行風險分析。 CrossCore 是用來判斷是否允許使用者繼續登入。 下列屬性可用於 CrossCore 風險分析：

- 電子郵件
- IP 位址
- 名字
- Middle Name
- Surname
- 街道地址
- 城市
- 州/省
- 郵遞區號
- 國家/地區
- 電話號碼

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。

## <a name="scenario-description"></a>案例描述

Experian 整合包含下列元件：

- Azure AD B2C –授權伺服器，負責驗證使用者的認證，也稱為身分識別提供者

- Experian – Experian 服務會接受使用者提供的輸入，並驗證使用者的身分識別

- 自訂 Rest API –此 API 會實現 Azure AD B2C 與 Experian 服務之間的整合。

下列架構圖顯示執行的方式。

![experian 的螢幕擷取畫面-架構-圖表](media/partner-experian/experian-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者選取 [註冊] 來建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。
| 2. | Azure AD B2C 會呼叫仲介層 API，並在使用者屬性上傳遞。
| 3. | 中介層 API 會收集使用者屬性，並將其轉換成 Experian API 可能會使用的格式。 然後，將它傳送至 Experian。
| 4. | Experian 會取用資訊並加以處理，以根據風險分析來驗證使用者識別。 然後，它會將結果傳回到中介層 API。
| 5. | 中介層 API 會處理資訊，並以正確的 JSON 格式將相關資訊傳回 Azure AD B2C。
| 6. | Azure AD B2C 從仲介層 API 接收資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果顯示成功回應，則會驗證使用者並將其寫入目錄。

## <a name="onboard-with-experian"></a>使用 Experian 上架

1. 若要建立 Experian 帳戶，請聯絡[Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. 建立帳戶之後，您將會收到 API 設定所需的資訊。 下列各節將說明此程式。

## <a name="configure-azure-ad-b2c-with-experian"></a>使用 Experian 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

將提供的 API 程式碼部署至 Azure 服務。 程式碼可以從 Visual Studio 發佈，請遵循這些[指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)。

>[!NOTE]
>您將需要已部署之服務的 URL，才能使用必要的設定來設定 Azure AD。

### <a name="part-2---deploy-the-client-certificate"></a>第2部分-部署用戶端憑證

Experian API 呼叫會受到用戶端憑證的保護。 此用戶端憑證將由 Experian 提供。 遵循本[檔](https://docs.microsoft.com/azure/app-service/environment/certificates#private-client-certificate)中所述的指示，必須將憑證上傳至 Azure App 服務。 範例原則會在程式中使用下列按鍵步驟：

- 上傳憑證

- 將金鑰設定為 `WEBSITE_LOAD_ROOT_CERTIFICATES` 憑證的指紋。

### <a name="part-3---configure-the-api"></a>第3部分-設定 API

應用程式設定可以[在 Azure 中的 App service 中設定](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)。 使用此方法時，可以安全地設定設定，而不需要將它們簽入存放庫。 您必須將下列設定提供給 Rest API：

| 應用程式設定 | 來源 | 附註 |
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

請參閱這[份檔](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys)，並建立兩個原則金鑰-一個用於 api 使用者名稱，另一個用於您在上面針對 HTTP 基本驗證所定義的 api 密碼。

>[!NOTE]
>稍後您將需要用來設定原則的金鑰。

### <a name="part-5---replace-the-configuration-values"></a>第5部分-取代設定值

在提供的自訂原則中，尋找下列預留位置，並將取代為您實例中的對應值

|                      預留位置                       |                                   取代值                                 |                   範例                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | 您的租使用者簡短名稱                                                           | yourtenant.onmicrosoft.com 中的 "yourtenant" |
| {your_trustframeworkbase_policy}                       | Trustframeworkbase.xml 原則的 Azure AD B2C 名稱                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 租使用者中設定之 IdentityExperienceFramework 應用程式的應用程式識別碼      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 租使用者中設定之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼 | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租使用者儲存體應用程式的應用程式識別碼                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租使用者儲存體應用程式的物件識別碼                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | 您在[此處](#part-4---create-api-policy-keys)建立的使用者名稱金鑰名稱             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | 您在[此處](#part-4---create-api-policy-keys)建立的密碼金鑰名稱             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | 您已設定之 app service 的 URL                                             | <https://yourapp.azurewebsites.net>          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>第6部分-設定 Azure AD B2C 原則

請參閱這[份檔](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack)，以取得如何設定您的 Azure AD B2C 租使用者和設定原則的指示。

>[!NOTE]
>此範例原則是以[本機帳戶入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)為基礎。

>[!NOTE]
> 我們建議的最佳作法是在 [屬性集合] 頁面中新增同意通知給客戶。 通知使用者資訊將會傳送至協力廠商服務以進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 [Azure AD B2C 租使用者]，然後在 [原則] 下選取 [**使用者流程**]。

2. 選取您先前建立的**使用者流程**。

3. 選取 [**執行使用者流程**]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式（範例是 JWT）

   b. **回復 url**：選取 [重新**導向 url** ]

   c. 選取 [執行使用者流程]。

4. 完成註冊流程並建立帳戶

5. 登出

6. 完成登入流程  

7. 當您進入 [**繼續**] 之後，CrossCore 謎題會隨即出現。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [開始使用 Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
