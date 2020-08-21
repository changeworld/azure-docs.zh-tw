---
title: 使用 Onfido 設定 Azure Active Directory B2C 的教學課程
titleSuffix: Azure AD B2C
description: 瞭解如何將 Azure AD B2C authentication 與 Onfido 整合，以進行檔識別碼和臉部生物特徵辨識驗證
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5ff2fe74a0dd5064232fcef3178aec2967ef6812
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683856"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 設定 Onfido 的教學課程

在此範例教學課程中，我們會提供有關如何將 Azure AD B2C 與 [Onfido](https://onfido.com/)整合的指引。 Onfido 是檔識別碼和臉部生物特徵辨識驗證應用程式。 它可讓公司即時符合 *您的客戶* 和身分識別需求。 Onfido 使用精密的 AI 型身分識別驗證，它會先驗證相片識別碼，然後將其與臉部生物識別相符。 此解決方案可將數位身分識別與真實世界的人員結合，並提供安全的上線體驗，同時減少詐騙。

在此範例中，我們會在註冊或登入流程中連接 Onfido 的服務，以進行身分識別驗證。 使用者可以存取的產品和服務的明智決策是根據 Onfido 的結果進行。

## <a name="prerequisites"></a>先決條件

若要開始使用，您需要：

- Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。

- 連結至 Azure 訂用帳戶的[Azure AD B2C 租](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)使用者。

- Onfido [試用帳戶](https://onfido.com/signup/)。

## <a name="scenario-description"></a>案例描述

Onfido 整合包含下列元件：

- Azure AD B2C 租使用者–授權伺服器，負責根據租使用者中定義的自訂原則來驗證使用者的認證。 也稱為身分識別提供者。 它會裝載 Onfido 用戶端應用程式，此應用程式會收集使用者檔，並將其傳送至 Onfido API 服務。

- Onfido 用戶端–在其他網頁中部署的可設定 JavaScript 用戶端檔集合公用程式。 收集檔並進行初步檢查，例如檔案大小和品質。

- 中繼 Rest API-提供端點讓 Azure AD B2C 的租使用者與 Onfido API 服務進行通訊，處理資料處理並遵守兩者的安全性需求。

- Onfido API 服務– Onfido 所提供的後端服務，可儲存並驗證使用者提供的檔。

下列架構圖顯示執行。

![onfido 的螢幕擷取畫面-架構-圖表](media/partner-onfido/onfido-architecture-diagram.png)

|步驟 | 描述 |
|:-----| :-----------|
| 1. | 使用者抵達登入頁面。 使用者註冊以建立新的帳戶，並在頁面中輸入資訊。 Azure AD B2C 會收集使用者屬性。 Onfido Azure AD B2C 中裝載的用戶端應用程式會進行使用者資訊的初步檢查。
| 2. | Azure AD B2C 會呼叫中介層 API，並傳遞使用者屬性。
| 3. | 仲介層 API 會收集使用者屬性，並將其轉換為 Onfido API 可能取用的格式。 然後，將它傳送至 Onfido。
| 4. | Onfido 會取用此資訊，並加以處理以驗證使用者識別。 然後，它會將結果傳回至中介層 API。
| 5. | 仲介層 API 會處理資訊，並以正確的 JSON 格式將相關資訊傳回 Azure AD B2C。
| 6. | Azure AD B2C 接收來自仲介層 API 的資訊。 如果顯示失敗回應，則會向使用者顯示錯誤訊息。 如果它顯示成功回應，則會驗證使用者並將其寫入目錄。

## <a name="onboard-with-onfido"></a>使用 Onfido 上線

1. 若要建立 Onfido 帳戶，請聯絡 [Onfido](https://onfido.com/signup/)。

2. 建立帳戶之後，請建立 [API 金鑰](https://documentation.onfido.com/)。 不過，即時金鑰是可計費的，不過，您可以使用 [沙箱金鑰來測試](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) 方案。 沙箱索引鍵會產生與即時索引鍵相同的結果結構，不過，一律會預先決定結果。 檔不會進行處理或儲存。

>[!NOTE]
> 您稍後將需要此金鑰。

如需 Onfido 的詳細資訊，請參閱 [ONFIDO API 檔](https://documentation.onfido.com) 和 [Onfido 開發人員中樞](https://developers.onfido.com)。  

## <a name="configure-azure-ad-b2c-with-onfido"></a>使用 Onfido 設定 Azure AD B2C

### <a name="part-1---deploy-the-api"></a>第1部分-部署 API

- 將提供的 [API 程式碼](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) 部署至 Azure 服務。 您可以依照下列 [指示](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)，從 Visual Studio 發佈程式碼。
- 設定 CORS，將允許的 **來源** 新增為 HTTPs：//{your_tenant_name} >b2clogin.com .com

>[!NOTE]
>您將需要已部署之服務的 URL，以使用必要的設定來設定 Azure AD。

#### <a name="adding-sensitive-configuration-settings"></a>新增敏感性設定

您可以在 [Azure 中的 App service](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)中設定應用程式設定。 App service 可讓您安全地設定設定，而不需要將它們簽入存放庫。 Rest API 需要下列設定：

| 應用程式設定名稱 | 來源 | 注意 |
|:-------------------------|:-------|:-------|
|OnfidoSettings： AuthToken| Onfido 帳戶 |

### <a name="part-2---deploy-the-ui"></a>第2部分-部署 UI

#### <a name="configure-your-storage-location"></a>設定您的儲存位置

1. [在您的儲存體帳戶中設定 blob 儲存體容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)

2. 將 ui 檔案從 UI 資料夾儲存至 blob 容器。

3. 依照下列指示，允許 CORS 存取您所建立的儲存體容器：

   a. 移至 [允許的**設定**  > **來源**]，輸入 `https://{your_tenant_name}.b2clogin.com` 。 將您的租使用者名稱取代為您 Azure AD B2C 租使用者的名稱。 例如， https://fabrikam.b2clogin.com 。 輸入您的租使用者名稱時，全部使用小寫字母。

   b. 針對 **允許的方法**，請選取 `GET` 和 `PUT` 。

   c. 選取 [儲存]。

#### <a name="update-ui-files"></a>更新 UI 檔案

1. 在 UI 檔案中，移至資料夾[ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. 開啟每個 html 檔案。

3. 尋找並取代 {您的 ui-blob-容器-url}，以及 UI **ocean_blue**、 **dist**和 **資產** 資料夾所在位置的 url

4. 尋找並以中繼 API app service 的 URL 取代 {您的中繼 api-url}。

#### <a name="upload-your-files"></a>上傳您的檔案

1. 將 ui 檔案從 UI 資料夾儲存至 blob 容器。

2. 使用 [Azure 儲存體總管](https://docs.microsoft.com/azure/virtual-machines/windows/disks-use-storage-explorer-managed-disks) 來管理您的檔案和存取權限。

### <a name="part-3---configure-azure-ad-b2c"></a>第3部分-設定 Azure AD B2C

#### <a name="replace-the-configuration-values"></a>取代設定值

在提供的自訂原則中，找出下列預留位置，並取代為您實例中的對應值。

| 預留位置 | 取代值 | 範例  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | 您租使用者的簡短名稱 |  來自 yourtenant.onmicrosoft.com 的 ">yourtenant.msappproxy.net" |
| {your_tenantID} | 您 Azure AD B2C 租使用者的 TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | 您 Azure AD B2C 租使用者中所設定之 IdentityExperienceFramework 應用程式的應用程式識別碼      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | 您 Azure AD B2C 租使用者中所設定之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼 | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租使用者儲存體應用程式的應用程式識別碼                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租使用者儲存體應用程式的物件識別碼                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | App insights 實例的檢測金鑰 *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| UI **ocean_blue**、 **dist**和 **資產** 資料夾所在位置的 URL | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 您已設定之 app service 的 URL                                             | `https://yourapp.azurewebsites.net`          |

* App insights 可以位於不同的租使用者中。 此為選用步驟。 如果不需要，請移除對應的 TechnicalProfiles 和 >orchestrationsteps。

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第4部分-設定 Azure AD B2C 原則

如需如何設定 Azure AD B2C 租使用者和設定原則的指示，請參閱這 [份檔](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) 。

>[!NOTE]
> 建議的最佳作法是在 [屬性集合] 頁面中，將同意通知新增至客戶。 通知使用者資訊將傳送至協力廠商服務進行身分識別驗證。

## <a name="test-the-user-flow"></a>測試使用者流程

1. 開啟 Azure AD B2C 租使用者，然後在 [原則] 底下選取 [ **Identity Experience Framework**]。

2. 選取您先前建立的 **SignUpSignIn**。

3. 選取 [ **執行使用者流程** ]，然後選取設定：

   a. **應用程式**：選取已註冊的應用程式 (範例為 JWT) 

   b. **回復 url**：選取重新 **導向 url**

   c. 選取 [執行使用者流程]。

4. 進行註冊流程並建立帳戶

5. 建立使用者屬性之後，會在流程期間呼叫 Onfido 服務。 如果流程未完成，請檢查使用者是否未儲存在目錄中。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure AD B2C 中的自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自訂原則入門](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
