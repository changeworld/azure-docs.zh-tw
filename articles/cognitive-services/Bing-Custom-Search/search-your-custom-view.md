---
title: 搜尋自訂檢視 - Bing 自訂搜尋
titleSuffix: Azure Cognitive Services
description: 設定自訂搜尋體驗之後，您可以在 Bing 自訂搜尋入口網站中測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983107"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>從入口網站呼叫 Bing 自訂搜尋執行個體

設定自訂搜尋體驗之後，您可以在 Bing 自訂搜尋[入口網站](https://customsearch.ai)中測試。 

![必應自訂搜索門戶的螢幕截圖](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>建立搜尋查詢 

登錄到必應自訂搜索[門戶](https://customsearch.ai)後，選擇搜索實例並按一下 **"生產**"選項卡。在**端點**下，選擇 API 終結點（例如，Web API）。 您的訂用帳戶可決定顯示的端點。

若要建立搜尋查詢，請輸入您端點的參數值。 請注意，入口網站中顯示的參數可能會隨著您所選的端點而改變。 有關詳細資訊，請參閱[自訂搜索 API 引用](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)。 要更改搜索實例使用的訂閱，請添加相應的訂閱金鑰，並更新相應的市場和/或語言參數。

一些重要參數如下：


|參數  |描述  |
|---------|---------|
|查詢     | 要搜尋的搜尋字詞。 僅適用於網頁、影像、影片與自動建議端點 |
|自訂組態識別碼 | 所選取自訂搜尋執行個體的組態識別碼。 這是唯讀欄位。 |
|市場     | 結果所源自的市場。 僅適用於網頁、影像、影片與裝載 UI 端點。        |
|訂用帳戶金鑰 | 要用來測試的訂用帳戶金鑰。 您可以從下拉式清單中選取金鑰，或手動輸入一個。          |

按一下 [其他參數]**** 會顯示下列參數：  

|參數  |描述  |
|---------|---------|
|安全搜尋     | 用來篩選成人內容網頁的篩選條件。 僅適用於網頁、影像、影片與裝載 UI 端點。 請注意，必應自訂視頻搜索僅支援兩個`moderate`值`strict`： 和 。        |
|使用者介面語言    | 用於使用者介面字串的語言。 例如，若您在裝載 UI 中啟用影像與影片， [影像]**** 與 [影片]**** 索引標籤會使用指定的語言。        |
|Count     | 回應中傳回的搜尋結果數目。 僅適用於網頁、影像與影片端點。         |
|Offset    | 傳回結果之前要跳過的搜尋結果數目。 僅適用於網頁、影像與影片端點。        |
    
指定所有必要選項之後，請按一下 [呼叫]**** 在右窗格中檢視 JSON 回應。 如果您選取裝載 UI 端點，可以從底部窗格測試搜尋體驗。

## <a name="change-your-bing-custom-search-subscription"></a>更改必應自訂搜索訂閱

您可以更改與必應自訂搜索實例關聯的訂閱，而無需創建新實例。 要向新訂閱發送 API 呼叫並收取費用，請在 Azure 門戶中創建新的必應自訂搜索資源。 在 API 請求中使用新的訂閱金鑰以及實例的自訂配置 ID。

## <a name="next-steps"></a>後續步驟

- [使用 C# 呼叫您的自訂檢視](./call-endpoint-csharp.md)
- [使用 Java 呼叫您的自訂檢視](./call-endpoint-java.md)
- [使用 NodeJs 呼叫您的自訂檢視](./call-endpoint-nodejs.md)
- [使用 Python 呼叫您的自訂檢視](./call-endpoint-python.md)

- [使用 C# SDK 呼叫您的自訂檢視](./sdk-csharp-quick-start.md)
