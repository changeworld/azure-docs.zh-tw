---
title: Azure 應用配置最佳實踐 |微軟文檔
description: 瞭解如何最好地使用 Azure 應用配置
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
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348666"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 應用配置最佳實踐

本文討論使用 Azure 應用配置時的常見模式和最佳做法。

## <a name="key-groupings"></a>關鍵分組

應用配置提供了兩個用於組織金鑰的選項：

* 鍵首碼
* 標籤

您可以使用一個或兩個選項對金鑰進行分組。

*鍵首碼*是鍵的開頭部分。 在一組鍵的名稱中使用相同的首碼，可以邏輯地對一組鍵進行分組。 首碼可以包含由分隔符號連接的多個元件，例如`/`，類似于 URL 路徑，以形成命名空間。 當您在一個應用配置存儲中存儲許多應用程式、元件服務和環境的金鑰時，此類層次結構非常有用。

需要記住的一個重要問題是，鍵是應用程式代碼引用的，用於檢索相應設置的值。 金鑰不應更改，否則每次更改時都必須修改代碼。

*標籤*是鍵上的屬性。 它們用於創建金鑰的變體。 例如，您可以將標籤分配給金鑰的多個版本。 版本可以是反覆運算、環境或其他上下文資訊。 應用程式可以通過指定另一個標籤請求一組完全不同的鍵值。 因此，代碼中的所有金鑰引用都保持不變。

## <a name="key-value-compositions"></a>鍵值組合

應用配置將與其一起存儲的所有金鑰視為獨立實體。 應用配置不會嘗試推斷鍵之間的任何關係或基於其層次結構繼承鍵值。 但是，通過使用標籤與應用程式代碼中的正確配置堆疊相結合，可以聚合多組金鑰。

讓我們看看以下範例。 假設您有一個名為**Asset1**的設置，其值可能因開發環境而異。 創建名為"Asset1"的鍵，該鍵的標籤為空標籤，標籤名為"開發"。 在第一個標籤中，您將**Asset1**的預設值，並在後者中放置"開發"的特定值。

在代碼中，首先檢索沒有任何標籤的鍵值，然後使用"開發"標籤第二次檢索同一組鍵值。 第二次檢索值時，將覆蓋鍵的先前值。 .NET Core 配置系統允許您相互"堆疊"多組配置資料。 如果多個集中存在一個金鑰，則使用包含該鍵的最後一個集。 使用現代程式設計框架（如 .NET Core），如果您使用本機配置提供程式訪問應用配置，則可以免費獲得此堆疊功能。 以下程式碼片段顯示了如何在 .NET Core 應用程式中實現堆疊：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[使用標籤為不同的環境啟用不同的配置](./howto-labels-aspnet-core.md)提供了一個完整的示例。

## <a name="app-configuration-bootstrap"></a>應用配置引導

要訪問應用配置存儲，可以使用其連接字串，該字串在 Azure 門戶中可用。 由於連接字串包含憑據資訊，因此它們被視為機密。 這些機密需要存儲在 Azure 金鑰保存庫中，並且代碼必須對金鑰保存庫進行身份驗證才能檢索它們。

更好的選項是在 Azure 活動目錄中使用託管標識功能。 使用託管標識時，只需應用配置終結點 URL 來引導對應用配置存儲的訪問。 您可以將 URL 嵌入到應用程式代碼中（例如，在*應用程式設定.json*檔中）。 有關詳細資訊[，請參閱與 Azure 託管標識集成](howto-integrate-azure-managed-service-identity.md)。

## <a name="app-or-function-access-to-app-configuration"></a>應用或功能對應用配置的訪問

您可以使用以下任一方法為 Web 應用或功能提供對應用配置的訪問：

* 通過 Azure 門戶，在應用服務的應用程式設定中輸入到應用配置存儲的連接字串。
* 將連接字串存儲在金鑰保存庫中的應用配置存儲，並從[應用服務中引用它](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 託管標識訪問應用配置存儲。 有關詳細資訊，請參閱與[Azure 託管標識集成](howto-integrate-azure-managed-service-identity.md)。
* 將配置從應用配置推送到應用服務。 應用配置提供一個匯出功能（在 Azure 門戶和 Azure CLI 中），該功能將資料直接發送到應用服務。 使用此方法，您根本不需要更改應用程式代碼。

## <a name="reduce-requests-made-to-app-configuration"></a>減少對應用配置的請求

對應用配置的過多請求可能會導致限制或超額收費。 要減少請求數，

* 增加刷新超時，尤其是在配置值不頻繁更改時。 使用[`SetCacheExpiration`方法](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)指定新的刷新超時。

* 觀看單個*哨點鍵*，而不是觀看單個鍵。 僅當哨點鍵發生更改時，才刷新所有配置。 有關示例[，請參閱在ASP.NET核心應用中使用動態配置](enable-dynamic-configuration-aspnet-core.md)。

* 使用 Azure 事件網格在配置更改時接收通知，而不是經常輪詢任何更改。 有關詳細資訊[，請參閱將 Azure 應用配置事件路由到 Web 終結點](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>將配置資料導入應用配置

應用配置提供了使用 Azure 門戶或 CLI 從當前設定檔批量[導入](https://aka.ms/azconfig-importexport1)配置設置的選項。 您還可以使用相同的選項從應用設定匯出值，例如在相關商店之間。 如果您想設置與 GitHub 存儲庫的持續同步，您可以使用我們的[GitHub 操作](https://aka.ms/azconfig-gha2)，以便您可以繼續使用現有的原始程式碼管理實踐，同時獲得應用配置的好處。

## <a name="next-steps"></a>後續步驟

* [索引鍵和值](./concept-key-value.md)
