---
title: 媒體服務 v2 至 v3 遷移檢查清單 |Microsoft Docs
description: 本文是一份檢查清單，可協助您將從 Azure 媒體服務 v2 遷移至 v3 的時間最小。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure 媒體服務、遷移、串流、廣播、即時、SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7d9597c16778a4ded7c1e2a7ed3ad5535b75cfde
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690278"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>步驟 3-設定以遷移至 V3 REST API 或用戶端 SDK

![遷移指南標誌](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![遷移步驟2](./media/migration-guide/steps-3.svg)

以下說明設定您的環境以使用媒體服務 V3 API 所採取的步驟。

## <a name="sdk-model"></a>SDK 模型

在 V2 API 中，有兩個不同的用戶端 Sdk （一個用於管理 API），可允許以程式設計方式建立帳戶，另一個用於資源管理。

先前，開發人員會使用 Azure 服務主體用戶端識別碼和用戶端密碼，以及其 AMS 帳戶的特定 V2 REST API 端點。

V3 API 是 Azure 資源管理 (ARM) 基礎。 它會使用 Azure Active Directory (Azure AD) 的服務主體識別碼和金鑰來連線至 API。 開發人員必須建立服務主體或受控識別，才能連接到 API。 在 V3 API 中，API 使用標準 ARM 端點，使用類似且一致的模型來進行所有其他 Azure 服務。

先前使用2015-10-01 版 ARM 管理 API 來管理其 V2 帳戶的客戶，應該使用支援 V3 API 存取的2020-05-01 版 ARM 管理 API。

## <a name="create-a-new-media-services-account-for-testing"></a>建立新的媒體服務帳戶以進行測試

遵循使用 Azure 入口網站 [設定環境](how-to-set-azure-subscription.md?tabs=portal) 的快速入門步驟。 選取 [API 存取和服務主體驗證]，以產生新的 Azure AD 應用程式識別碼和密碼，以與此測試帳戶搭配使用。

[建立媒體服務帳戶](create-account-howto.md?tabs=portal)。
[取得存取媒體服務 API 的認證](access-api-howto.md?tabs=portal)。

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>下載您選擇的用戶端 SDK 並設定您的環境

- 適用于 [.net](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true)、.net Core、 [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true)、 [Python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true)、 [JAVA](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true)、 [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)和 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)的 sdk。
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)  簡易腳本支援的整合。

> [!NOTE]
> Azure 媒體服務在 V3 上無法再使用「社區 PHP SDK」。 如果您是在 V2 上使用 PHP，則應該直接在程式碼中遷移至 REST API。

## <a name="open-api-specifications"></a>Open API 規格

- V3 是以統一的 API 介面為基礎，它會公開以 Azure Resource Manager 為基礎的管理和操作功能。 Azure Resource Manager 範本可以用來建立和部署轉換、串流端點、實況活動等等。

- 先前稱為 Swagger) 檔的 [OpenAPI 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (說明所有服務元件的架構。

- 所有用戶端 Sdk 都是從 GitHub 上發行的 Open API 規格衍生和產生的。 本文發行時，最新的 Open API 規格會在 GitHub 中公開維護。 2020-05-01 版本是 [最新的穩定版本](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)。

## <a name="rest"></a>[REST](#tab/rest)

針對媒體服務 v3 REST API 呼叫使用 [Postman](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) 。
閱讀 [REST API 參考頁面](https://docs.microsoft.com/rest/api/media/)。

您應該在 Postman 集合中使用2020-05-01 版本字串。

## <a name="net"></a>[.NET](#tab/net)

閱讀文章： [使用 .Net 連接到媒體服務 V3 API](configure-connect-dotnet-howto.md) 來設定您的環境。

如果您只想要使用 >packagemanager 安裝最新的 SDK，請使用下列命令：

```Install-Package Microsoft.Azure.Management.Media```

或者，若要使用 .NET CLI 安裝最新的 SDK，請使用下列命令：

```dotnet add package Microsoft.Azure.Management.Media```

此外，Azure 中提供完整的 .NET 範例 [-範例/媒體服務-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) 適用于各種情況。 此存放庫中的專案會示範如何使用 v3 版本來執行不同的 Azure 媒體服務案例。

### <a name="get-started-adjusting-your-code"></a>開始調整您的程式碼

搜尋使用狀況實例的程式碼基底， `CloudMediaContext` 開始升級至 V3 API。

下列程式碼顯示如何使用 v2 .NET SDK 來存取 v2 API。 開發人員會先建立 `CloudMediaContext` ，然後建立具有物件的實例 `AzureAdTokenCredentials` 。

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

閱讀文章： [使用 JAVA 連線至媒體服務 V3 API](configure-connect-java-howto.md) 來設定您的環境。

## <a name="python"></a>[Python](#tab/python)

請參閱文章， [連接至 Azure 媒體服務 V3 API-Python](configure-connect-python-howto.md) 來設定您的環境。

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

請參閱 [連接到 Azure 媒體服務 V3 API Node.js](configure-connect-nodejs-howto.md) 的文章，以設定您的環境。

## <a name="ruby"></a>[Ruby](#tab/ruby)

- 取得 GitHub 上的 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK。

## <a name="go"></a>[Go](#tab/go)

下載 [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK。

---

## <a name="next-steps"></a>後續步驟

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]