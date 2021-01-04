---
title: 快速入門：使用文字分析用戶端程式庫進行文字採礦
titleSuffix: Azure Cognitive Services
description: 使用此快速入門，搭配 Azure 認知服務中的文字分析 API 來執行情緒分析。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: aahi
keywords: 文字採礦, 情感分析, 文字分析
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: f93b7954861bf3351d2930132e1391e1f21b2a6f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655113"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>快速入門：使用文字分析用戶端程式庫和 REST API

使用此文章開始使用文字分析用戶端程式庫和 REST API。 請依照下列步驟試用挖掘文字的範例程式碼：

* 情感分析
* 意見挖掘
* 語言偵測
* 實體辨識
* 個人識別資訊辨識
* 關鍵片語擷取


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
>    * 請務必遵循您所使用版本的指示。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。
> * 如果您想要使用健康情況的文字分析或非同步作業，請參閱 Github 上的 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 或 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 範例

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。
如果您想要使用健康情況的文字分析或非同步作業，請參閱 Github 上的 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 或 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 範例

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
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 針對生產案例，建議您使用批次非同步方法來取得效能和擴充性。 請參閱下列參考文件。 如果您想要使用健康情況的文字分析或非同步作業，請參閱 Github 上的 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)、[Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 或 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) 範例

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * 文字分析 API 的最新穩定版本是 `3.0`。
>    * 請務必遵循您所使用版本的指示。

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

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
