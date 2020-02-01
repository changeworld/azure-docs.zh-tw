---
title: Azure 應用程式組態最佳做法 |Microsoft Docs
description: 瞭解如何充分利用 Azure 應用程式組態
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
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898643"
---
# <a name="azure-app-configuration-best-practices"></a>Azure 應用程式組態最佳做法

本文討論使用 Azure 應用程式組態時的常見模式和最佳作法。

## <a name="key-groupings"></a>按鍵分組

應用程式組態提供兩個組織索引鍵的選項：

* 金鑰首碼
* 標籤

您可以使用其中一種或兩個選項來分組您的金鑰。

*金鑰首碼*是索引鍵的開頭部分。 您可以使用名稱中的相同前置詞，以邏輯方式分組一組索引鍵。 前置詞可以包含多個以分隔符號連接的元件（例如 `/`，類似于 URL 路徑）以構成命名空間。 當您要在一個應用程式組態存放區中儲存許多應用程式、元件服務和環境的金鑰時，這類階層會很有用。

要記住的重要事項是，您的應用程式程式碼會參考這些金鑰來抓取對應設定的值。 金鑰不應變更，否則您必須在每次發生時修改程式碼。

*標籤*是索引鍵上的屬性。 它們是用來建立索引鍵的變體。 例如，您可以將標籤指派給多個版本的金鑰。 版本可能是反復專案、環境或一些其他內容相關資訊。 您的應用程式可以藉由指定另一個標籤來要求一組完全不同的索引鍵值。 因此，所有的索引鍵參考在您的程式碼中都會保持不變。

## <a name="key-value-compositions"></a>索引鍵/值組合

應用程式組態會將與它一起儲存的所有金鑰視為獨立的實體。 應用程式組態不會嘗試推斷索引鍵之間的任何關聯性，或根據其階層繼承索引鍵值。 不過，您可以匯總多組索引鍵，方法是使用標籤與應用程式程式碼中適當的設定堆疊結合。

讓我們看看以下範例。 假設您有一個名為**Asset1**的設定，其值可能會根據開發環境而有所不同。 您會建立名為 "Asset1" 的機碼，其中包含空白卷標和名為「開發」的標籤。 在第一個標籤中，您會放入**Asset1**的預設值，並在後者中放置「開發」的特定值。

在您的程式碼中，您會先取出索引鍵值，而不使用任何標籤，然後再以「開發」標籤第二次抓取相同的索引鍵值組。 當您第二次取得值時，會覆寫先前的索引鍵值。 .NET Core 設定系統可讓您將多組設定資料「堆疊」在彼此之上。 如果索引鍵存在於一個以上的集合中，則會使用包含它的最後一組。 使用現代化的程式設計架構（例如 .NET Core）時，如果您使用原生設定提供者來存取應用程式組態，就可以免費取得這項堆疊功能。 下列程式碼片段會示範如何在 .NET Core 應用程式中執行堆疊：

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>應用程式組態啟動程式

若要存取應用程式組態存放區，您可以使用它在 Azure 入口網站中提供的連接字串。 由於連接字串包含認證資訊，因此會將它們視為秘密。 這些秘密必須儲存在 Azure Key Vault 中，而您的程式碼必須向 Key Vault 進行驗證，才能取得它們。

較好的選項是使用 Azure Active Directory 中的受控識別功能。 使用受控識別時，您只需要應用程式組態端點 URL，就能啟動應用程式組態存放區的存取權。 您可以將 URL 內嵌在應用程式代碼中（例如，在*appsettings*中）。 如需詳細資訊，請參閱[與 Azure 受控識別整合](howto-integrate-azure-managed-service-identity.md)。

## <a name="app-or-function-access-to-app-configuration"></a>應用程式組態的應用程式或函數存取

您可以使用下列任何方法，為 web 應用程式或函式提供應用程式組態的存取權：

* 透過 Azure 入口網站在 App Service 的 [應用程式設定] 中，輸入應用程式組態存放區的連接字串。
* 將連接字串儲存在 Key Vault 中的應用程式組態存放區，並[從 App Service 加以參考](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)。
* 使用 Azure 受控識別來存取應用程式組態存放區。 如需詳細資訊，請參閱[與 Azure 受控識別整合](howto-integrate-azure-managed-service-identity.md)。
* 將設定從應用程式組態推送至 App Service。 應用程式組態提供匯出函式（在 Azure 入口網站和 Azure CLI 中），可將資料直接傳送到 App Service。 使用此方法時，您完全不需要變更應用程式程式碼。

## <a name="next-steps"></a>後續步驟

* [索引鍵和值](./concept-key-value.md)
