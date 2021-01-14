---
title: 文字分析 API 的資料限制
titleSuffix: Azure Cognitive Services
description: Azure 認知服務所提供文字分析 API 的資料限制。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 2adca03a820d02731bca252dee99c76debc85e2e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028126"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>文字分析 API 的資料和速率限制
<a name="data-limits"></a>

請使用本文來尋找可將資料傳送至文字分析 API 的大小和速率限制。 

## <a name="data-limits"></a>資料限制

> [!NOTE]
> * 如果您需要分析比限制所允許的大小還要大的文件，則可以將文字分拆成較小的文字區塊，再將其傳送至 API。 
> * 文件是文字字元的單一字串。  

| 限制 | 值 |
|------------------------|---------------|
| 單一文件的大小上限 | 5,120 個字元，由 [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) 進行測量。 也適用於健康情況的文字分析。 |
| 單一文件的大小上限 (`/analyze` 端點)  | 12.5 萬個字元，由 [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) 進行測量。 不適用於健康情況的文字分析。 |
| 整體要求的大小上限 | 1 MB。 也適用於健康情況的文字分析。 |


如果文件超過字元限制，API 的行為會根據所使用的端點而有所不同：

* `/analyze` 端點：
  * API 會拒絕整個要求，並在其中有任何文件超過大小上限時傳回 `400 bad request` 錯誤。
* 所有其他端點：  
  * API 不會處理超過大小上限的文件，而且會傳回無效文件的錯誤。 如果 API 要求有多份文件，則 API 會繼續處理這些文件 (如果其在字元限制內)。

您可以在單一要求中傳送的文件數目上限取決於所使用的 API 版本和功能，如下表所述。

#### <a name="version-3"></a>[第 3 版](#tab/version-3)

下列限制適用於目前的 v3 API。 超過下列限制會產生 HTTP 400 錯誤碼。


| 功能 | 每個要求的文件數上限 | 
|----------|-----------|
| 語言偵測 | 1000 |
| 情感分析 | 10 |
| 意見挖掘 | 10 |
| 關鍵片語擷取 | 10 |
| 具名實體辨識 | 5 |
| 實體連結 | 5 |
| 健康情況的文字分析  | Web 型 API 10 個，容器 1000 個。 |
| 分析端點 | 所有作業都是 25 個。 |

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

| 功能 | 每個要求的文件數上限 | 
|----------|-----------|
| 語言偵測 | 1000 |
| 情感分析 | 1000 |
| 關鍵片語擷取 | 1000 |
| 具名實體辨識 | 1000 |
| 實體連結 | 1000 |

---

## <a name="rate-limits"></a>速率限制

您的速率限制會隨著[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)而不同。 這些限制在兩個版本的 API 中是相同的。 這些速率限制不適用於健康情況容器的文字分析，因為沒有設定速率限制。

| 層          | 每秒要求 | 每分鐘要求 |
|---------------|---------------------|---------------------|
| S / 多服務 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

要求率會針對每個「文字分析」功能個別進行測量。 您可以同時將適用於定價層的要求數量上限傳送到每個功能。 例如，如果您在 `S` 層，並一次傳送 1000 個要求，則會長達 59 秒無法傳送另一個要求。


## <a name="see-also"></a>另請參閱

* [什麼是文字分析 API](../overview.md)
* [價格詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
