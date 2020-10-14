---
title: 資料結構描述需求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: c754efef02cdad6edbf047c5de9f1af6d758f137
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043187"
---
計量監視器是執行時間序列異常偵測、診斷和分析的服務。 作為 AI 服務，其會使用您的資料來定型所使用的模型。 服務接受具有下列資料行的彙總資料資料表：

* **量值** (必要)：包含數值的一或多個資料行。
* **時間戳記** (選用)：零或一個類型為 `DateTime` 或 `String` 的資料行。 未設定這個資料行時，時間戳記會設定為每個內嵌期間的開始時間。 將時間戳記格式化為：`yyyy-MM-ddTHH:mm:ssZ`。 
  * **您的時間戳記應符合計量的資料細微性。例如，每日計量之時間戳記上的小時、分鐘和秒應標示為 `00:00:00`** 。
* **維度** (選用)：資料行可以是任何資料類型。 使用大量資料行和值時請小心，以避免處理過多的維度數。

> [!Note]
> 針對每個計量，每個量值應該只有一個時間戳記，對應到一個維度組合。 在上線前彙總資料，或使用查詢來指定要內嵌的資料。