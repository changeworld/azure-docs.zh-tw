---
title: 瞭解 Azure IoT 模型儲存機制的概念 |Microsoft Docs
description: 作為解決方案開發人員或 IT 專業人員，瞭解 Azure IoT 模型存放庫的基本概念。
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d07257d1e23ee792aa996e31a2c28c17bc23d34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715067"
---
# <a name="azure-iot-model-repository"></a>Azure IoT 模型存放庫

Azure IoT 模型存放庫可讓裝置構建者管理和共用 IoT 隨插即用裝置模型。 裝置型號是使用 [數位 Twins 模型語言 ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)定義的 JSON LD 檔 (DTDL) 。 儲存在模型存放庫服務中的模型，可以透過存取控制或公開地與解決方案開發人員共用，而不需要任何驗證來整合和開發 IoT 隨插即用的雲端解決方案。

> [!NOTE]
> 裝置產生器可以選擇直接在裝置上執行 IoT 隨插即用裝置模型、使用模組或 IoT Edge 模組。

您可以使用下列內容來存取模型存放庫：

- [Azure IoT 模型儲存](https://aka.ms/iotmodelrepo) 機制入口網站
- [Azure IoT 模型儲存機制 REST API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Azure CLI IoT 模型存放庫命令](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>公用模型

儲存在模型存放庫中的公用數位對應項模型可供所有人使用，並在其應用程式中整合，而不需要任何驗證。 此外，公用模型讓裝置產生器和解決方案開發人員能夠共用和重複使用其 IoT 隨插即用裝置模型的開放生態系統。

如需有關如何在模型存放庫中發行模型以使其成為公用模型的指示，請參閱「**公司模型**」下的「[發行模型](#publish-a-model)」一節。

若要使用模型儲存機制入口網站來查看公用模型：

1. 移至 [Azure IoT 模型儲存機制入口網站](https://aka.ms/iotmodelrepo)。

1. 選取 [ **View public 模型**]。

    ![View public 模型](./media/concepts-model-repository/public-models.png)

若要使用 REST API 以程式設計方式查看公用模型，請參閱 [取得模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API 檔。

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

若要使用 CLI 來查看公用模型，請參閱 Azure CLI [取得模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) ] 命令。

## <a name="company-models"></a>公司模型

公司模型存放庫是 Azure IoT 模型存放庫中的租使用者，可供您的組織建立及管理由公司或組織內的使用者所撰寫的數位對應項模型。 公司模型僅適用于貴公司或組織的已驗證使用者。 模型存放庫租使用者系統管理員可以將公司或組織中其他使用者的許可權和控制存取權，指派給公司模型存放庫中的模型。

### <a name="set-up-your-company-model-repository"></a>設定您的公司模型存放庫

使用您的 *工作或學校 Azure Active Directory (Azure AD) 帳戶* 來存取模型存放庫。 如果您的組織已經有 Azure AD 租使用者，您可以使用此 Azure AD 租使用者中的使用者帳戶 (s) 和服務主體。

若要瞭解如何設定 Azure AD 租使用者，以及如何在 Azure AD 租使用者中建立使用者或服務主體的詳細資訊，請參閱 [其他資訊](#additional-information) 一節。

- 如果您是組織中的第一位使用者，以存取模型存放庫或登入入口網站，則會獲得 **租用戶系統管理員** 角色。 此角色可讓您將角色指派給組織的儲存機制租使用者中的其他使用者。

- 您可以透過 **租用戶系統管理員** （例如 **讀取模型** 或 **建立模型**）將其他角色指派給您。

### <a name="understand-access-management"></a>瞭解存取管理

下表摘要說明公司模型存放庫中支援的功能，以及其相關聯的許可權：

| 功能  | 權限| 說明|
|-------------|-----------|------------|
|讀取模型|讀取模型|根據預設，公司租使用者中的所有使用者都可以看到其公司模型。 此外，使用者也可以看到私人模型 () 由其他公司分享給他們。|
|管理存取權|管理存取權|管理使用者角色指派 (針對組織中的其他使用者新增或移除) 。|
|建立模型|建立模型|在公司模型存放庫中建立模型。|
|發行模型|發行模型|發佈模型，讓任何人都能看到模型。|

下表摘要說明可用於存取管理的模型存放庫中支援的角色及其功能。

|角色|功能|
|----|----------|
|TenantAdministrator|管理存取權，讀取模型|
|建立者|建立模型，讀取模型|
|發行者|發行模型，讀取模型|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>使用 REST API 存取公司模型時傳遞安全性權杖

當您呼叫 REST Api 來管理私用或共用的公司模型時，您必須為使用者或服務主體提供 JWT 格式的授權權杖。 請參閱 [其他資訊](#additional-information) 一節，以瞭解如何取得使用者或服務主體的 JWT 權杖。

以公司模型或共用模型為目標時，JWT 權杖必須在 API 的授權 HTTP 標頭中傳遞。 以公用模型為目標時，不需要 JWT 權杖。

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>查看公司或共用模型

您必須是存放庫租使用者 *讀取* 者角色的成員，或必須與您共用模型才能讀取模型。 您可以看到已與您共用的未發行模型清單，以及已與您共用的已發行模型清單。 根據預設，使用者可以讀取其公司的模型、其他公司與他們共用的模型，以及所有公用模型。

若要使用入口網站來查看公司或共用模型：

1. 登入 [Azure IoT 模型儲存機制入口網站](https://aka.ms/iotmodelrepo)。

1. 展開左窗格中的 [ **公司型號** – **未** 發行]

    ![查看公司模型](./media/concepts-model-repository/view-company-models.png)

1. 展開 [ **共用模型-** 在左窗格中解除發佈]

    ![視圖共用模型](./media/concepts-model-repository/view-shared-models.png)

若要使用 REST API 來查看公司或共用模型，請參閱 [取得模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API 檔。 如需如何在 HTTP 要求中傳入 JWT 授權標頭的詳細資訊，請參閱 [使用 REST API 來存取公司模型時傳遞安全性權杖](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 。

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

若要使用 CLI 來查看公司模型或共用模型，請參閱 Azure CLI [取得模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) ] 命令。

### <a name="manage-roles"></a>管理角色

租使用者系統管理員可以將角色指派給存放庫租使用者中的使用者，讓他們可以建立公司或組織私用的模型、發行模型，或管理其他使用者的角色。

若要使用入口網站將使用者新增至模型存放庫租使用者角色：

1. 登入 [Azure IoT 模型儲存機制入口網站](https://aka.ms/iotmodelrepo)。

1. 選取左窗格中的 [ **存取管理** ]，然後選取 [ **+ 新增**]。 在 [ **新增許可權** ] 窗格中，輸入您要新增至角色之使用者的工作位址：

    ![新增工作位址](./media/concepts-model-repository/add-user.png)

1. 從 [ **角色** ] 下拉式清單中選擇您要新增使用者的角色。 然後選取 [ **儲存**]：

    ![選擇角色](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>上傳模型

您必須是存放庫租使用者建立 **者** 角色的成員，才能將模型上傳至公司模型存放庫。

這些模型不會發行，而且只能由組織中的使用者存取。 您也可以與外部使用者共用一或多個未發行的模型。

上傳的模型是不可變的。

在所有已上傳模型的所有存放庫租使用者中，這些模型的模型識別碼必須是全域唯一的。

若要使用入口網站上傳模型：

1. 登入 [Azure IoT 模型儲存機制入口網站](https://aka.ms/iotmodelrepo)。

1. 展開左窗格中的 [ **公司模型** ]，然後選取 [ **建立模型**]。 然後選取 [匯 **入 Json**]。

    ![建立模型](./media/concepts-model-repository/create-model.png)

1. 選取您要上傳的檔案。 如果入口網站成功驗證您的模型，請選取 [ **儲存**]。

若要使用 REST API 上傳模型，請參閱 [建立模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API。 如需如何在 HTTP 要求中傳入 JWT 授權標頭的詳細資訊，請參閱 [使用 REST API 來存取公司模型時傳遞安全性權杖](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 。

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

若要使用 CLI 上傳模型，請參閱 Azure CLI [建立模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) ] 命令。

### <a name="publish-a-model"></a>發佈模型

若要發佈模型，您必須符合下列需求：

1. 您的組織必須是 [Microsoft 合作夥伴網路](https://docs.microsoft.com/partner-center/)的成員才能發佈模型。 若要建立合作夥伴中心帳戶，請參閱[建立合作夥伴中心帳戶](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account)。 帳戶通過核准之後，您就可以發佈您的模型。 如需詳細資訊，請參閱[合作夥伴中心常見問題集](https://support.microsoft.com/help/4340639/partner-center-account-faqs)。

2. 使用者必須是存放庫租使用者之 *發行者* 角色的成員。

您組織中的使用者所建立和發佈的模型，會顯示為 *已發行的模型*。 這些模型是公用的，而且可以由 **公用模型**下的任何人找到。

若要使用入口網站來發佈模型：

1. 登入 [Azure IoT 模型儲存機制入口網站](https://aka.ms/iotmodelrepo)。

2. 展開左窗格中的 [ **公司模型** ]，然後選取您想要發行的模型。 然後選取 [ **發行**]。

    ![發佈模型](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> 如果您收到通知，指出您沒有 Microsoft 合作夥伴 (MPN) 識別碼，請遵循通知中的註冊步驟。 如需詳細資訊，請參閱本節開頭的需求。

若要使用 REST API 發行模型，請參閱 [發行模型](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API 檔。 提供查詢字串參數 `update-metadata=true` ，以使用 REST API 發行模型。 如需如何在 HTTP 要求中傳入 JWT 授權標頭的詳細資訊，請參閱 [使用 REST API 來存取公司模型時傳遞安全性權杖](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) 。

若要使用 CLI 發佈模型，請參閱 Azure CLI [發行模型](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) ] 命令。

> [!NOTE]
> 您必須先在模型存放庫中發佈模型，才能執行認證測試。 若要深入瞭解，請參閱 [如何認證 IoT 隨插即用裝置](howto-certify-device.md)。

### <a name="share-a-model"></a>共用模型

您可以與外部組織的使用者共用您所建立的公司模型。 如此一來，您就可以允許共同作業者使用您的私用公司模型來查看和開發解決方案。

例如，裝置製造商可能會想要維護公司或組織專用的模型。 他們可能會有要求其裝置功能保持機密的客戶。

在公司或組織之間共用模型，可讓您安全地存取非公用的模型。

若要使用入口網站共用公司模型：

- 如果您是模型的建立者，當您在 [**公司模型**] 區段中查看模型時，[**共用**] 和 [**共用方式**] 按鈕就會處於作用中狀態。

    ![共用模型](./media/concepts-model-repository/share-model.png)

- 若要與外部使用者共用模型，請選取 [ **共用**]。 在 [ **共用模型** ] 窗格中，輸入外部使用者的電子郵件地址，然後選取 [ **儲存**]。

- 若要查看與您共用模型的使用者，請選取 [ **共用物件**]。

- 若要停止與特定使用者共用模型，請從 [ **共用的** ] 窗格的使用者清單中選取使用者。 然後選取 [ **移除** ]，然後在出現提示時確認您的選擇。

    ![停止共用](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>其他資訊

使用 Azure AD 時，您可能會發現下列主題很有用：

- 若要建立新的 Azure AD 租使用者，請參閱 [Azure AD 中建立新的租](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)使用者。 大部分的組織都已有 Azure AD 的租使用者。

- 若要將使用者或來賓使用者新增至 Azure AD 租使用者，請參閱 [使用 Azure AD 新增或刪除使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)。

- 若要將服務主體新增至 Azure AD 租使用者，請參閱 [如何使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

- 若要瞭解如何從 Azure AD 取得呼叫 REST Api 時所使用的 JWT 權杖，請參閱 [從 Azure AD 取得授權用戶端應用程式要求的權杖](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)。

## <a name="next-steps"></a>後續步驟

建議的下一個步驟是複習 [IoT 隨插即用架構](concepts-architecture.md)。
