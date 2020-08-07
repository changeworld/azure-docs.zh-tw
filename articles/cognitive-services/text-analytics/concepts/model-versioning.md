---
title: 在文字分析 v3 中指定模型版本
titleSuffix: Azure Cognitive Services
description: 瞭解如何指定您的資料所使用的文字分析 API 模型。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: b3e8c2c8a5e95cc5c862112410f5073a5e600651
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986758"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>文字分析 API 中的模型版本設定

文字分析 API 的第3版可讓您選擇要在資料上使用的模型版本。 使用選擇性 `model-version` 參數，在您的 API 要求中選取模型的版本。 例如：`<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`。 如果未指定此參數，則 API 會預設為最新的穩定版本。 

## <a name="available-versions"></a>可用的版本

使用下表來尋找每個端點支援的模型版本。


| 端點                        | 支援的版本                                     | 最新版本 |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


您可以在[新功能](../whats-new.md)中找到這些模型更新的詳細資料。

## <a name="text-analytics-for-health"></a>健康情況的文字分析

健康情況容器的[文字分析](../how-tos/text-analytics-for-health.md)使用與上述 API 端點不同的模型版本設定。  請注意，每個容器映射只能使用一個模型版本。

| 端點                        | 容器映射標記                     | 模型版本 |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview`或最新版本 | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體辨識](../how-tos/text-analytics-how-to-entity-linking.md)
