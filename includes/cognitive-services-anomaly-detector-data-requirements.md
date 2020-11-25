---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2019
ms.openlocfilehash: 41ac1478b1028a847fc0d5e7e70802375e910837
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999091"
---
> [!NOTE]
> 使用異常偵測器 API 時若要取得[最佳結果](../articles/cognitive-services/anomaly-detector/concepts/anomaly-detection-best-practices.md)，您的 JSON 格式時間序列資料應該包含：
> * 依據相同間隔分隔的資料點，遺漏的點數目不超過預期的 10%。
> * 如果您的資料沒有清楚的季節模式，則至少有 12 個資料點。
> * 如果您的資料有清楚的季節模式，則至少會發生 4 個模式。 
