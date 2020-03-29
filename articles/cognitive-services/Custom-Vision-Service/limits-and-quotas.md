---
title: 限制和配額 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 本文介紹了不同類型的許可金鑰以及自訂視覺服務的限制和配額。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081772"
---
# <a name="limits-and-quotas"></a>限制和配額

自訂視覺服務有兩層金鑰。 您可以透過 Azure 入口網站註冊 F0 (免費) 或 S0 (標準) 訂用帳戶。 請參閱對應的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)，以取得有關定價和交易的詳細資料。

S0 專案預期會在一段時間後增加「每個專案的訓練影像數」和「每個專案的標籤數」。

||**F0**|**S0**|
|-----|-----|-----|
|專案|2|100|
|每個專案的訓練影像數 |5,000|100,000|
|預測/月|10,000 |無限制|
|標籤/ 專案|50|500|
|反覆運算次數 |10|10|
|每個標籤的最小標記圖像，分類（建議 50+） |5|5|
|每個標籤的最小標記圖像，物件檢測（建議 50+）|15|15|
|預測影像的儲存時間|30 天|30 天|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業及儲存 (每秒交易數)|2|10|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業但不儲存 (每秒交易數)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (每秒 API 呼叫數)|2|10|
|[其他 API 呼叫](https://go.microsoft.com/fwlink/?linkid=865446) (每秒交易數)|10|10|
|接受的圖像類型|jpg， png， bmp， gIF|jpg， png， bmp， gIF|
|最小圖像高度/寬度（以圖元為單位）|256 （見注釋）|256 （見注釋）|
|最大圖像高度/寬度（以圖元為單位）|無限制|無限制|
|最大影像大小 (訓練影像上傳) |6 MB|6 MB|
|最大影像大小 (預測)|4 MB|4 MB|
|每個物件檢測訓練圖像的最大區域|300|300|
|每個分類圖像的最大標記|100|100|

> [!NOTE]
> 接受小於 256 圖元的圖像，但會進行放大。
