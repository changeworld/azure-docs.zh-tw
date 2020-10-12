---
title: Azure 應用程式組態最佳作法 |Microsoft Docs
description: 瞭解使用 Azure 應用程式組態時的最佳做法。 涵蓋的主題包括按鍵群組、索引鍵/值組合、應用程式設定啟動程式等等。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d532b8aab87840f4b6ad90daedba743597f4fe43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588053"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 應用程式組態最佳作法

本文討論當您使用 Azure 應用程式組態時的常見模式和最佳作法。

## <a name="key-groupings"></a>按鍵分組

應用程式設定提供兩個選項來組織金鑰：

* 金鑰首碼
* 標籤

您可以使用其中一種或兩個選項來群組您的金鑰。

*金鑰* 前置詞是索引鍵的開頭部分。 您可以使用名稱中的相同前置詞，以邏輯方式將一組索引鍵分組。 前置詞可以包含多個以分隔符號連接的元件，例如 `/` 類似于 URL 路徑，以形成命名空間。 當您在一個應用程式設定存放區中儲存許多應用程式、元件服務和環境的金鑰時，這類階層會很有用。

請記住，索引鍵是您的應用程式程式碼所參考的索引鍵，以取得對應設定的值。 金鑰不應變更，否則您將必須在每次發生時修改程式碼。

*標籤* 是索引鍵上的屬性。 它們可用來建立金鑰的變體。 例如，您可以將標籤指派給多個金鑰版本。 版本可能是反復專案、環境或某些其他內容資訊。 您的應用程式可以藉由指定另一個標籤來要求一組完全不同的索引鍵值。 因此，您的程式碼中的所有索引鍵參考都會保持不變。

## <a name="key-value-compositions"></a>索引鍵/值組合

應用程式設定會將與它一起儲存的所有金鑰視為獨立的實體。 應用程式設定不會嘗試推斷索引鍵之間的任何關聯性，或根據其階層繼承索引鍵值。 不過，您可以在應用程式程式碼中使用加上適當設定堆疊的標籤，來匯總多組索引鍵。

讓我們看看下列範例。 假設您有一個名為 **Asset1**的設定，其值可能會根據開發環境而有所不同。 您會建立名為 "Asset1" 的機碼，其中包含空白卷標和名為 "開發" 的標籤。 在第一個標籤中，您會放入 **Asset1**的預設值，並在後者放置特定的「開發」值。

在您的程式碼中，您會先取出沒有任何標籤的索引鍵值，然後再次使用「開發」標籤抓取一組相同的索引鍵值。 當您第二次取出值時，就會覆寫先前的索引鍵值。 .NET Core 設定系統可讓您將多組設定資料「堆疊」在彼此之上。 如果索引鍵存在於一個以上的集合中，則會使用包含它的最後一個集合。 使用新式的程式設計架構（例如 .NET Core）時，如果您使用原生設定提供者來存取應用程式設定，則可免費取得此堆疊功能。 下列程式碼片段會示範如何在 .NET Core 應用程式中執行堆疊：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[使用標籤來啟用不同環境的不同](./howto-labels-aspnet-core.md) 設定會提供完整的範例。

## <a name="app-configuration-bootstrap"></a>應用程式設定啟動程式

若要存取應用程式設定存放區，您可以使用 Azure 入口網站中提供的連接字串。 因為連接字串包含認證資訊，所以會被視為秘密。 這些秘密必須儲存在 Azure Key Vault 中，而您的程式碼必須向 Key Vault 驗證才能取出它們。

更好的選項是使用 Azure Active Directory 中的受控識別功能。 使用受控識別時，您只需要應用程式設定端點 URL，就能啟動應用程式設定存放區的存取權。 您可以在應用程式的程式碼中內嵌 URL (例如，在檔案) 的 *appsettings.js* 。 如需詳細資料，請參閱 [整合 Azure 受控](howto-integrate-azure-managed-service-identity.md) 識別。

## <a name="app-or-function-access-to-app-configuration"></a>應用程式設定的應用程式或函數存取

您可以使用下列任何一種方法，為 web 應用程式或功能提供應用程式設定的存取權：

* 透過 Azure 入口網站，在 App Service 的 [應用程式設定] 中，輸入應用程式設定存放區的連接字串。
* 將連接字串儲存到 Key Vault 中的應用程式設定存放區，並 [從 App Service 加以參考](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 受控識別來存取應用程式設定存放區。 如需詳細資訊，請參閱 [整合 Azure 受控](howto-integrate-azure-managed-service-identity.md)識別。
* 將設定從應用程式設定推送至 App Service。 應用程式設定會在 Azure 入口網站中提供匯出函式 (，以及將資料直接傳送到 App Service 的 Azure CLI) 。 使用這個方法時，您完全不需要變更應用程式程式碼。

## <a name="reduce-requests-made-to-app-configuration"></a>減少對應用程式設定所提出的要求

對應用程式設定的要求過多可能會導致節流或超額費用。 若要減少提出的要求數目：

* 增加重新整理的時間，特別是當您的設定值未頻繁變更時。 使用[ `SetCacheExpiration` 方法](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)指定新的重新整理超時。

* 觀賞單一 *sentinel 鍵*，而不是監看個別的按鍵。 只有當 sentinel 金鑰變更時，才重新整理所有設定。 如需範例，請參閱 [在 ASP.NET Core 應用程式中使用動態](enable-dynamic-configuration-aspnet-core.md) 設定。

* 使用 Azure 事件方格可在設定變更時接收通知，而不是持續輪詢任何變更。 如需詳細資訊，請參閱將 [Azure 應用程式組態事件路由至 web 端點](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>將設定資料匯入至應用程式設定

應用程式設定提供選項，可讓您使用 Azure 入口網站或 CLI，從目前的設定檔中大容量 [導入](https://aka.ms/azconfig-importexport1) 您的設定。 您也可以使用相同的選項，從應用程式設定匯出值（例如，在相關的存放區之間）。 如果您想要設定與 GitHub 存放庫進行中的同步處理，您可以使用我們的 [Github 動作](https://aka.ms/azconfig-gha2) ，讓您可以繼續使用現有的原始檔控制作法，同時獲得應用程式設定的優點。

## <a name="multi-region-deployment-in-app-configuration"></a>應用程式設定中的多重區域部署

應用程式設定是區域服務。 針對每個區域具有不同設定的應用程式，將這些設定儲存在一個實例中，可能會產生單一失敗點。 跨區域跨多個區域部署一個應用程式設定實例，可能是較好的選項。 它有助於進列區域嚴重損壞修復、效能和安全性 siloing。 依區域設定也可改善延遲，並使用分開的節流配額，因為節流是針對每個實例。 若要套用嚴重損壞修復的風險降低，您可以使用 [多個](./concept-disaster-recovery.md)設定存放區。 

## <a name="next-steps"></a>接下來的步驟

* [索引鍵和值](./concept-key-value.md)
