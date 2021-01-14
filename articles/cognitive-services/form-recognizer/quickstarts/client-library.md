---
title: 快速入門：表單辨識器用戶端程式庫或 REST API
titleSuffix: Azure Cognitive Services
description: 使用表單辨識器用戶端程式庫或 REST API 建立表單處理應用程式，從您的自訂文件中擷取索引鍵/值組和資料表資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 表單處理, 自動化資料處理
ms.openlocfilehash: c915e7753487eba284d89dbb480f9848e90c1ef1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132261"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>快速入門：使用表單辨識器用戶端程式庫或 REST API

以您選擇的語言開始使用表單辨識器。 Azure 表單辨識器是一種認知服務，可讓您使用機器學習技術來建置自動化的資料處理軟體。 從表單文件中識別並擷取文字、索引鍵/值組、選取標記和資料表資料等等&mdash;該服務會輸出結構化資料，其中包含原始檔案中的關聯性。 您可以透過 REST API 或 SDK 來使用表單辨識器。 請遵循下列步驟來安裝 SDK 套件，並試用基本工作的程式碼範例。 

使用表單辨識器來：

* [分析版面配置](#analyze-layout)
* [分析收據](#analyze-receipts)
* [分析名片](#analyze-business-cards)
* [分析發票](#analyze-invoices)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
