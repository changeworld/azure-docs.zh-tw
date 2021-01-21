---
title: 影片索引子概念-Azure
titleSuffix: Azure Media Services Video Indexer
description: 本文提供 Azure 媒體服務影片索引子術語和概念的簡短總覽。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633789"
---
# <a name="video-indexer-concepts"></a>影片索引器概念

本文提供 Azure 媒體服務影片索引子術語和概念的簡短總覽。

## <a name="audiovideocombined-insights"></a>音訊/影片/綜合見解

當您將影片上傳到影片索引器時，其會執行不同的 AI 模型來分析視覺效果和音訊。 影片索引器會分析您的影片，這是 AI 模型所擷取的深入解析。 如需詳細資訊，請參閱 [總覽](video-indexer-overview.md)。

## <a name="confidence-scores"></a>信賴分數 

信賴分數表示深入解析的信賴度。 此為介於0.0 到1.0 之間的數位。 分數越高，表示答案的信賴度越好。 例如， 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>內容仲裁

使用文字和視覺內容仲裁模型，讓您的使用者免于不適當的內容安全，並驗證您發佈的內容是否符合您組織的值。 您可以自動封鎖特定影片，或針對該內容向使用者發出警示。 如需詳細資訊，請參閱 [見解：視覺效果和文字內容仲裁](video-indexer-output-json-v2.md#visualcontentmoderation)。 

## <a name="blocks"></a>區塊   

區塊是為了讓您能更輕鬆地瀏覽資料。 例如，區塊的分割點可能會在說話者變更或長時間暫停時。  

## <a name="project-and-editor"></a>專案和編輯器

[影片索引子](https://www.videoindexer.ai/)網站可讓您使用影片的深入見解：尋找正確的媒體內容、找出您感興趣的元件，以及使用結果來建立全新的專案。 一旦建立之後，就可以從影片索引子轉譯和下載專案，並將其用於您自己的編輯應用程式或下游工作流程。

在某些情況下，您可能會發現這項功能很有用： 

* 正在建立尾端的電影反白顯示。
* 使用新聞轉換中的舊影片剪輯。
* 正在建立較短的社交媒體內容。

如需詳細資訊，請參閱 [使用編輯器建立專案](use-editor-create-project.md)。

## <a name="keyframes"></a>關鍵 幀

影片索引子會選取最能代表每一次的框架 (s) 。 主要畫面格是根據美觀屬性從整個影片中選取的代表性框架 (例如對比和 stableness) 。 如需詳細資訊，請參閱[場景、鏡頭和主要畫面格](scenes-shots-keyframes.md)。

## <a name="time-range-vs-adjusted-time-range"></a>時間範圍與調整後的時間範圍   

TimeRange 是原始影片中的時間範圍。 AdjustedTimeRange 是相對於目前播放清單的時間範圍。 由於您可以從不同影片的不同時段建立播放清單，因此您可以取用 1 小時的影片，然後只使用其中的 1 個時段，例如，10:00-10:15。 在該案例中，您的播放清單中將會有 1 個時段，其時間範圍為 10:00-10:15，但 adjustedTimeRange 會是 00:00-00:15。 

## <a name="widgets"></a>小工具

影片索引子支援在您的應用程式中內嵌小工具。 如需詳細資訊，請參閱 [在您的應用程式中內嵌影片索引子小](video-indexer-embed-widgets.md)工具。

## <a name="summarized-insights"></a>深入解析摘要  

深入解析摘要包含以下資料的彙總檢視：臉部、主題、表情。 例如，為避免逐一查看幾千個時間範圍來檢查其中有哪些臉部，深入解析摘要會包含所有臉部，以及每個臉部出現的時間範圍和其顯示時間的百分比。  

## <a name="next-steps"></a>下一步

- [概觀](video-indexer-overview.md)
- [深入解析](video-indexer-output-json-v2.md)
