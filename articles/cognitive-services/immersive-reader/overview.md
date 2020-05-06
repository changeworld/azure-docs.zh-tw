---
title: 什麼是沈浸式閱讀程式 API？
titleSuffix: Azure Cognitive Services
description: 沈浸式閱讀程式 API 是一項工具，可用來輔助具有學習差異的人士，或協助入門讀者和語言學習者。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 2eab7ab62f6a7a105dab33aa889af9f2be3a3fa3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80052373"
---
# <a name="what-is-immersive-reader"></a>什麼是沈浸式閱讀程式？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術並針對沈浸式讀者、語言學習者及存在學習差異 (例如讀寫障礙) 的人士改善其閱讀理解程度。

您可以使用沈浸式閱讀程式 SDK，在 Web 應用程式中使用沈浸式閱讀程式。

## <a name="what-does-immersive-reader-do"></a>沈浸式閱讀程式有何作用？

沈浸式閱讀程式的設計目的是讓大眾更方便閱讀。

* 在最小的閱讀檢視中顯示內容

  ![沈浸式閱讀程式](./media/immersive-reader.png)

* 顯示常用字詞的圖片

  ![圖片字典](./media/picture-dictionary.png)

* 醒目顯示名詞、動詞、形容和副詞

  ![詞性](./media/parts-of-speech.png)

* 為您大聲朗讀內容

  ![大聲朗讀](./media/read-aloud.png)

* 將您的內容轉譯成另一種語言

  ![翻譯](./media/translation.png)

* 將字詞細分成音節

  ![音節](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>沈浸式閱讀程式如何運作？

沈浸式閱讀程式是獨立 Web 應用程式，使用沈浸式閱讀程式 JavaScript SDK 進行叫用時，會透過 `iframe` 顯示在您現有 Web 應用程式上方。 當您呼叫 API 來啟動沈浸式閱讀程式時，您指定想要在沈浸式閱讀程式中顯示的內容。 我們的 SDK 會處理 `iframe` 的建立和樣式設定，以及與沈浸式閱讀程式後端服務的通訊，這個服務會處理詞性的內容、文字轉換語音、轉譯等等作業。

## <a name="next-steps"></a>後續步驟

開始使用沈浸式閱讀程式：

* 從[快速入門](./quickstart.md)直接進入
* 探索 [GitHub 上的沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)
* 閱讀[沈浸式閱讀程式 SDK 參考](./reference.md)
