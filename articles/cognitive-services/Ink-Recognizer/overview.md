---
title: 什麼是筆跡辨識器？ - 筆跡辨識器 API
titleSuffix: Azure Cognitive Services
description: 將筆跡辨識器整合到您的應用程式、網站、工具與其他解決方案，以允許將筆跡筆觸資料視為輸入來辨識和使用。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75448158"
---
# <a name="what-is-the-ink-recognizer-api"></a>什麼是筆跡辨識器 API？


筆墨辨識器認知服務會提供雲端式 REST API 來分析及辨識數位筆跡內容。 不同於使用光學字元辨識 (OCR) 的服務，此 API 需要以數位筆跡筆觸資料作為輸入。 數位筆跡筆觸是按時間排序的 2D 位置點 (X,Y 座標) 集合，可表示數位筆或手指等輸入工具的動作。 然後，其會辨識輸入的圖形和手寫內容，並傳回包含所有已辨識實體的 JSON 回應。

![流程圖：描述如何將筆跡筆觸輸入傳送給 API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>特性

使用筆跡辨識器 API 可輕鬆辨識應用程式中的手寫內容。 

|功能  |描述  |
|---------|---------|
| 手寫辨識 | 可辨識 63 種核心[語言和地區設定](language-support.md)的手寫內容。 | 
| 版面配置辨識 | 取得關於數位筆跡內容的結構化資訊。 將內容拆解成書寫區、段落、線條、文字、項目符號清單。 然後，應用程式可以使用版面配置資訊來建置額外的功能，例如自動設定清單格式和圖形對齊。 |
| 圖形辨識 | 可辨識做筆記時最常使用的[幾何圖形](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api)。 |
| 合併的圖形和文字辨識 | 可辨識哪些筆跡筆觸屬於圖形或手寫內容，並分別加以分類。|

## <a name="workflow"></a>工作流程

筆跡辨識器 API 是一種 RESTful Web 服務，因此可輕易地從任何可發出 HTTP 要求及剖析 JSON 的程式設計語言呼叫。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

在註冊後：

1. 取得筆跡筆觸資料並[將其格式化](concepts/send-ink-data.md#sending-ink-data)為有效的 JSON。
1. 將資料透過要求傳送給筆跡辨識器 API。
1. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="next-steps"></a>後續步驟

請嘗試下列語言版本的快速入門，以便開始對筆跡辨識器 API 發出呼叫。
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

若要了解筆跡辨識 API 在數位筆跡應用程式中的運作方式，請看位於 GitHub 上的下列應用程式範例：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 網頁瀏覽器應用程式](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 行動裝置應用程式](https://go.microsoft.com/fwlink/?linkid=2089805)
