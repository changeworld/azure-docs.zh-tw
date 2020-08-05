---
title: 使用 Onfido 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何整合 Azure AD B2C authentication 與 Onfido，以進行檔識別碼和臉部生物識別驗證
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 580d3cea94e3c550954fc0fa1aa6f2c3a73e9386
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554768"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Onfido 的教學課程

在此範例教學課程中，我們會提供如何整合 Azure AD B2C 與[Onfido](https://onfido.com/)的指引。 Onfido 是檔識別碼和臉部生物識別驗證應用程式。 它可讓公司即時*得知您的客戶*和身分識別需求。 Onfido 使用精密的 AI 型身分識別驗證，它會先驗證相片識別碼，然後將它與臉部生物識別加以比對。 這個解決方案會將數位身分識別與真實世界的人結合，並提供安全的上線體驗，同時減少詐騙。

在此範例中，我們會連接 Onfido 在註冊或登入流程中的服務，以進行身分識別驗證。 根據 Onfido 的結果，決定使用者可以存取的產品和服務的相關通知。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。

- Onfido[試用帳戶](https://onfido.com/signup/)。

## <a name="scenario-description"></a>案例描述

Onfido 整合包含下列元件：

- Azure AD B2C 租使用者–授權伺服器，負責根據租使用者中定義的自訂原則來驗證使用者的認證。 它也稱為身分識別提供者。 它會裝載 Onfido 用戶端應用程式，它會收集使用者檔，並將其傳送至 Onfido API 服務。

- Onfido 用戶端–在其他網頁中部署的可設定 JavaScript 用戶端檔集合公用程式。 會收集檔，並進行初步檢查，例如檔案大小和品質。

- 中繼 Rest API –提供 Azure AD B2C 租使用者的端點，以與 Onfido API 服務進行通訊、處理資料處理，以及遵守兩者的安全性需求。

- Onfido API 服務– Onfido 所提供的後端服務，可儲存並驗證使用者所提供的檔。

下列架構圖顯示執行的方式。

![onfido 的螢幕擷取畫面-架構-圖表](media/partner-onfido/onfido-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者註冊以建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。 Azure AD B2C 中裝載的 Onfido 用戶端應用程式會執行使用者資訊的初步檢查。
| 2. | Azure AD B2C 會呼叫仲介層 API，並在使用者屬性上傳遞。
| 3. | 中介層 API 會收集使用者屬性，並將其轉換成 Onfido API 可能會使用的格式。 然後，將它傳送至 Onfido。
| 4. | Onfido 會取用資訊並加以處理，以驗證使用者識別。 然後，它會將結果傳回到中介層 API。
| 5. | 中介層 API 會處理資訊，並以正確的 JSON 格式將相關資訊傳回 Azure AD B2C。
| 6. | Azure AD B2C 從仲介層 API 接收資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果顯示成功回應，則會驗證使用者並將其寫入目錄。

## <a name="onboard-with-onfido"></a>使用 Onfido 上架

1. 若要建立 Onfido 帳戶，請聯絡[Onfido](https://onfido.com/signup/)。

2. 建立帳戶之後，請建立[API 金鑰](https://documentation.onfido.com/)。 即時金鑰是可計費的，不過，您可以使用[沙箱金鑰來測試](https://documentation.onfido.com/?javascript#sandbox-and-live-differences)方案。 沙箱金鑰會產生與 live 金鑰相同的結果結構，不過，結果一律是預先決定的。 檔不會進行處理或儲存。

>[!NOTE]
> 您稍後將需要金鑰。

如需 Onfido 的詳細資訊，請參閱[ONFIDO API 檔](https://documentation.onfido.com)和[Onfido 開發人員中樞](https://developers.onfido.com)。  

## <a name="configure-azure-ad-b2c-with-onfido"></a>使用 Onfido 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

- 將提供的[API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api)部署至 Azure 服務。 程式碼可以從 Visual Studio 發佈，請遵循這些[指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)。
- 設定 CORS，將允許的**原始來源**新增為 HTTPs：//{your_tenant_name}. .b2clogin.com .com

>[!NOTE]
>您將需要已部署之服務的 URL，才能使用必要的設定來設定 Azure AD。

#### <a name="adding-sensitive-configuration-settings"></a>新增敏感性設定

應用程式設定可以在[Azure 中的 App service](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)中設定。 App service 可讓您安全地設定設定，而不需要將它們簽入存放庫。 Rest API 需要下列設定：

| 應用程式設定名稱 | 來源 | 注意 |
|:-------------------------|:-------|:-------|
|OnfidoSettings： AuthToken| Onfido 帳戶 |

### <a name="part-2---deploy-the-ui"></a>第2部分-部署 UI

#### <a name="configure-your-storage-location"></a>設定您的儲存位置

1. [在您的儲存體帳戶中設定 blob 儲存體容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)

2. 將 ui 檔案從 UI 資料夾儲存到您的 blob 容器。

3. 依照下列指示，允許 CORS 存取您所建立的儲存體容器：

   a. 移至 [**設定**] [  > **允許的來源**]，輸入 `https://{your_tenant_name}.b2clogin.com` 。 將您的租使用者名稱取代為您 Azure AD B2C 租使用者的名稱。 例如， https://fabrikam.b2clogin.com 。 輸入您的租使用者名稱時，請全部使用小寫字母。

   b. 針對**允許的方法**，選取 `GET` 和 `PUT` 。

   c. 選取 [儲存]。

#### <a name="update-ui-files"></a>更新 UI 檔案

1. 在 UI 檔案中，移至資料夾[ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. 開啟每個 html 檔案。

3. 尋找並取代 {您的 ui-blob-容器-url}，其 URL 為您的 UI **ocean_blue**、 **dist**和**資產**資料夾所在的位置

4. 尋找並取代 {您的中繼-api-url} 與中繼 API 應用程式服務的 URL。

#### <a name="upload-your-files"></a>上傳您的檔案

1. 將 ui 檔案從 UI 資料夾儲存到您的 blob 容器。

2. 使用[Azure 儲存體總管](https://docs.microsoft.com/azure/virtual-machines/windows/disks-use-storage-explorer-managed-disks)來管理您的檔案和存取權限。

### <a name="part-3---configure-azure-ad-b2c"></a>第3部分-設定 Azure AD B2C

#### <a name="replace-the-configuration-values"></a>取代設定值

在提供的自訂原則中，尋找下列預留位置，並將取代為您實例中的對應值。

| 預留位置 | 取代值 | 範例  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | 您的租使用者簡短名稱 |  yourtenant.onmicrosoft.com 中的 "yourtenant" |
| {your_tenantID} | Azure AD B2C 租使用者的 TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 租使用者中設定之 IdentityExperienceFramework 應用程式的應用程式識別碼      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 租使用者中設定之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼 | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租使用者儲存體應用程式的應用程式識別碼                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租使用者儲存體應用程式的物件識別碼                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | App insights 實例的檢測金鑰 *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| 您的 UI **ocean_blue**、 **dist**和**資產**資料夾所在位置的 URL | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 您已設定之 app service 的 URL                                             | <https://yourapp.azurewebsites.net>          |

* App insights 可以位於不同的租使用者中。 此為選用步驟。 如果不需要，請移除對應的 TechnicalProfiles 和 OrchestrationSteps。

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第4部分-設定 Azure AD B2C 原則

請參閱這[份檔](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack)，以取得如何設定您的 Azure AD B2C 租使用者和設定原則的指示。

>[!NOTE]
> 我們建議的最佳作法是在 [屬性集合] 頁面中新增同意通知給客戶。 通知使用者資訊將會傳送至協力廠商服務以進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 下選取 [ **Identity Experience Framework**]。

2. 選取您先前建立的**SignUpSignIn**。

3. 選取 [**執行使用者流程**]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式 (範例是 JWT) 

   b. **回復 url**：選取 [重新**導向 url** ]

   c. 選取 [執行使用者流程]。

4. 完成註冊流程並建立帳戶

5. 在建立使用者屬性後的流程期間，將會呼叫 Onfido 服務。 如果流程不完整，請檢查該使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [開始使用 Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
