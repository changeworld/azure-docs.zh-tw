---
title: 快速入門：文字分析用戶端程式庫 v3
titleSuffix: Azure Cognitive Services
description: 使用此快速入門將應用程式連線到 Azure 認知服務中的文字分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 4aba103bcb3593d9baef256ce04a9ab6754ed900
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407657"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>快速入門：使用文字分析用戶端程式庫

開始使用文字分析用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用文字分析用戶端程式庫執行：

* 情感分析
* 語言偵測
* 實體辨識
* 關鍵片語擷取

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
>    * 請務必遵循您所使用版本的指示。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
>    * 請務必遵循您所使用版本的指示。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。
> * 您也可以在[瀏覽器](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)中執行此版本的文字分析用戶端程式庫。

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
>    * 請務必遵循您所使用版本的指示。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>其他語言支援

按下此索引標籤，表示您可能未找到慣用程式設計語言的快速入門。 別擔心，我們還提供了其他快速入門。 您可以使用資料表來尋找您程式設計語言的合適範例。

| Language | 可用版本 | 
|----------|------------------------|
| Ruby     | [2.1 版](ruby-sdk.md) | 
| Go       | [2.1 版](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索解決方案](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體辨識](../how-tos/text-analytics-how-to-entity-linking.md)
* [偵測語言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [辨識語言](../how-tos/text-analytics-how-to-language-detection.md)
