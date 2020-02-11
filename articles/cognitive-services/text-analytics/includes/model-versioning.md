---
title: 模型版本設定
titleSuffix: Azure Cognitive Services
description: 在 V3 端點中指定模型版本
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089054"
---
文字分析 API 第 3 版可讓您選擇最新資料的模型版本。 使用選擇性的 `model-version` 參數，選取您要求中所需的模型版本。 如果未指定此參數，API 會預設為 `latest`，即最新的穩定版本。 即使您可以在任何要求中使用最新的模型版本，但每個版本中只會更新部分功能。 下表說明了每個模型版本中已更新的功能：

| 模型版本           | 已更新的功能         | 下列模型的最新版本：           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | 實體辨識                      | 實體辨識                      |
| `2019-10-01`            | 實體辨識、情感分析  | 語言偵測、關鍵片語擷取、情感分析|


v3 端點的每個回應都包含一個 `model-version` 欄位，其指定使用的模型版本。

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
如需這些模型版本更新的詳細資訊，請參閱[新功能](../whats-new.md)。
