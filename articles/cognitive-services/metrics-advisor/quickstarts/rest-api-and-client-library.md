---
title: Metrics Advisor 用戶端程式庫 REST API
titleSuffix: Azure Cognitive Services
description: 使用此快速入門將應用程式連線至 Azure 認知服務中的 Metrics Advisor API。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186912"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>快速入門：使用用戶端程式庫 REST API 自訂解決方案

開始使用 Metrics Advisor REST API 或用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

使用 Metrics Advisor 可以：

* 從資料來源新增資料摘要
* 檢查內嵌狀態
* 設定威脅偵測及警示 
* 查詢異常偵測結果
* 診斷異常


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

- [使用入口網站](web-portal.md)
- [將資料摘要上線](../how-tos/onboard-your-data.md)
    - [管理資料摘要](../how-tos/manage-data-feeds.md)
    - [不同資料來源的組態](../data-feeds-from-different-sources.md)
- [設定計量和微調偵測組態](../how-tos/configure-metrics.md)
- [使用意見反應來調整異常偵測](../how-tos/anomaly-feedback.md)