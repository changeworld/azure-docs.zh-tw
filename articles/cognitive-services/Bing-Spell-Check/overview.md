---
title: 什麼是 Bing 拼字檢查 API？
titleSuffix: Azure Cognitive Services
description: 了解 Bing 拼字檢查 API，其會使用機器學習服務和統計機器翻譯來對內容進行拼字檢查。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: c0453fa99376cb6a5dba1e427cdc0deccb3e03de
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367041"
---
# <a name="what-is-the-bing-spell-check-api"></a>什麼是 Bing 拼字檢查 API？

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing 拼字檢查 API 可讓您對文字的內容執行文法與拼字檢查。 大部分的拼字檢查工具都依賴以字典為基礎的規則集，但 Bing 拼字檢查工具卻是利用機器學習和統計機器翻譯來對內容提供正確的校正。 

## <a name="features"></a>特性

| 功能 | 描述 |
|---------|---------|
|多個拼字檢查模式     | 多個拼字檢查模式可讓您獲得專注於文法和/或拼字的校正。 |
|辨識俚語與非正式用語     | 可辨識文字中所用的常用語和非正式用語。         |
|區分相似字組     | 可在發音類似但意義不同的字組 (例如 "see" 和 "sea") 之間找到正確用法        |
|支援品牌、標題和熱門用法     | 可辨識新品牌、標題和其他新興的熱門用語 |

## <a name="workflow"></a>工作流程

Bing 拼字檢查 API 可輕易地從任何可發出 HTTP 要求及剖析 JSON 回應的程式設計語言呼叫。 此服務可使用 REST API 或 Bing 拼字檢查 SDK 來存取。 

1. 建立具備 Bing 搜尋 API 存取權的[認知服務 API 帳戶](../cognitive-services-apis-create-account.md)。 如果您沒有 Azure 訂用帳戶，您可以建立免費帳戶。 
2. 將要求傳送至 Bing Web 搜尋 API。
3. 剖析 JSON 回應

## <a name="next-steps"></a>後續步驟

首先，請嘗試 Bing 拼字檢查搜尋 API 的[互動式示範](https://azure.microsoft.com/services/cognitive-services/spell-check/)，以了解如何快速地檢查各種不同的文字。

當您準備好要呼叫 API 時，請建立[認知服務 API 帳戶](../../cognitive-services/cognitive-services-apis-create-account.md)。 如果您沒有 Azure 訂用帳戶，可以[建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

您也可以瀏覽 [Bing 搜尋 API 中樞頁面](../bing-web-search/overview.md)來探索其他可用的 API。