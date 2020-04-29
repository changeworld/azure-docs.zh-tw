---
title: 限制和配額 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 本文說明不同類型的授權金鑰，以及有關自訂視覺服務的限制和配額。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79081772"
---
# <a name="limits-and-quotas"></a>限制和配額

自訂視覺服務有兩個金鑰層。 您可以透過 Azure 入口網站註冊 F0 (免費) 或 S0 (標準) 訂用帳戶。 請參閱對應的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)，以取得有關定價和交易的詳細資料。

S0 專案預期會在一段時間後增加「每個專案的訓練影像數」和「每個專案的標籤數」。

||**F0**|**S0**|
|-----|-----|-----|
|專案|2|100|
|每個專案的訓練影像數 |5,000|100,000|
|預測/月|10,000 |無限制|
|標記/專案|50|500|
|反覆運算次數 |10|10|
|每個標記的最小標記影像，分類（建議為 50 +） |5|5|
|每個標記的最小標記影像、物件偵測（建議為 50 +）|15|15|
|預測影像的儲存時間|30 天|30 天|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業及儲存 (每秒交易數)|2|10|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業但不儲存 (每秒交易數)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (每秒 API 呼叫數)|2|10|
|[其他 API 呼叫](https://go.microsoft.com/fwlink/?linkid=865446) (每秒交易數)|10|10|
|接受的映射類型|jpg、png、bmp、gif|jpg、png、bmp、gif|
|最小影像高度/寬度（以圖元為單位）|256（請參閱附注）|256（請參閱附注）|
|影像高度/寬度上限（以圖元為單位）|無限制|無限制|
|最大影像大小 (訓練影像上傳) |6 MB|6 MB|
|最大影像大小 (預測)|4 MB|4 MB|
|每個物件偵測訓練影像的最大區域|300|300|
|每個分類影像的標記上限|100|100|

> [!NOTE]
> 小於256圖元的影像將被接受，但放大。
