---
title: 限制和配額 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 本文說明不同類型的授權金鑰，以及自訂視覺服務的限制和配額。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 3392cc5f3ee9daef1ae8397f6829f4ca7a42373a
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871395"
---
# <a name="limits-and-quotas"></a>限制和配額

自訂視覺服務的金鑰有兩個層級。 您可以透過 Azure 入口網站註冊 F0 (免費) 或 S0 (標準) 訂用帳戶。 請參閱對應的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)，以取得有關定價和交易的詳細資料。

S0 專案預期會在一段時間後增加「每個專案的訓練影像數」和「每個專案的標籤數」。

|因素|**F0**|**S0**|
|-----|-----|-----|
|專案|2|100|
|每個專案的訓練影像數 |5,000|100,000|
|預測/月|10,000 |無限制|
|標記/專案|50|500|
|反覆運算次數 |10|10|
|每個標籤的最小標記影像、分類 (50 + 建議的)  |5|5|
|每個標記的最小標記影像、物件偵測 (50 + 建議的) |15|15|
|預測影像的儲存時間|30 天|30 天|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業及儲存 (每秒交易數)|2|10|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業但不儲存 (每秒交易數)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (每秒 API 呼叫數)|2|10|
|[其他 API 呼叫](https://go.microsoft.com/fwlink/?linkid=865446) (每秒交易數)|10|10|
|接受的影像類型|jpg、png、bmp、gif|jpg、png、bmp、gif|
|最小影像高度/寬度（以圖元為單位）|256 (請參閱附注) |256 (請參閱附注) |
|最大影像高度/寬度（以圖元為單位）|10240|10240|
|最大影像大小 (訓練影像上傳) |6 MB|6 MB|
|最大影像大小 (預測)|4 MB|4 MB|
|每個影像的區域數目上限 (物件偵測) |300|300|
|每個影像的標記數目上限 (分類) |100|100|

> [!NOTE]
> 小於256圖元的影像將被接受，但放大。
> 影像外觀比例不應大於25
