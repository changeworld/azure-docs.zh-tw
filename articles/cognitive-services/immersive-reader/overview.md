---
title: 什麼是沈浸式閱讀程式？
titleSuffix: Azure Cognitive Services
description: 沈浸式閱讀程式是一項工具，其設計目的是協助具有學習差異的人士，或協助入門讀者和語言學習者的閱讀理解能力。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.custom: cog-serv-seo-aug-2020
keywords: 讀者，語言學習工具，顯示圖片，改善閱讀，閱讀內容，翻譯
ms.openlocfilehash: a537845c0256316f9c47317a8805fa989a624f60
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985742"
---
# <a name="what-is-immersive-reader"></a>什麼是沈浸式閱讀程式？

[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術並針對入門讀者、語言學習者及存在學習差異 (例如讀寫障礙) 的人士改善其閱讀理解程度。 透過沉浸式讀者用戶端程式庫，您可以利用 Microsoft Word 和 Microsoft One Note 中所使用的相同技術，來改善您的 Web 應用程式。 

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>使用沈浸式閱讀程式來改善閱讀協助工具 

沈浸式閱讀程式的設計目的是讓大眾更方便閱讀。 讓我們看看一些沈浸式閱讀程式的核心功能。

### <a name="isolate-content-for-improved-readability"></a>隔離內容以提升可讀性

沈浸式閱讀程式會隔離內容，以改善可讀性。 

  ![使用沈浸式閱讀程式來隔離內容以提升可讀性](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>顯示常用單字的圖片

對於常用詞彙，沈浸式閱讀程式會顯示圖片。

  ![使用沈浸式閱讀程式的圖片字典](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>醒目提示詞性

沈浸式閱讀程式可以透過醒目提示動詞、名詞、代名詞等等，協助學習者了解詞性和文法。

  ![使用沈浸式閱讀程式顯示詞性](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>大聲讀出內容

語音合成 (或文字轉換語音) 內建到沈浸式閱讀程式服務，讓您的讀者選取要大聲讀出的文字。 

  ![使用沈浸式閱讀程式大聲讀出文字](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>即時翻譯文字

沈浸式閱讀程式可以即時將文字轉譯成許多語言。 這有助於改善讀者學習新語言的理解能力。

  ![使用沈浸式閱讀程式翻譯文字](./media/translation.png)

### <a name="split-words-into-syllables"></a>將字詞分割成音節

使用沈浸式閱讀程式，您就可以將字詞細分成音節來改善可讀性，或將新的字詞唸出來。

  ![使用沉浸式閱讀程式將字詞細分成音節](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>沈浸式閱讀程式如何運作？

沈浸式閱讀程式是獨立的 Web 應用程式。 使用沈浸式閱讀程式叫用時，用戶端程式庫會顯示在 `iframe` 中現有 Web 應用程式的頂端。 當您的 Wep 應用程式呼叫沈浸式閱讀程式服務時，您會指定要顯示閱讀程式的內容。 沈浸式閱讀程式用戶端程式庫會處理 `iframe` 的建立和樣式設定，以及與沈浸式閱讀程式後端服務的通訊。 沈浸式閱讀程式服務會處理詞性、文字轉換語音、翻譯等內容。

## <a name="get-started-with-immersive-reader"></a>開始使用沈浸式閱讀程式

沈浸式閱讀程式用戶端程式庫適用於 C#、JavaScript、Java (Android)、Kotlin (Android) 和 Swift (iOS)。 透過以下項目開始使用：

* [快速入門：使用沈浸式閱讀程式用戶端程式庫](quickstarts/client-libraries.md)

## <a name="next-steps"></a>後續步驟

開始使用沈浸式閱讀程式：

* 請參閱[沈浸式閱讀程式用戶端程式庫參考](./reference.md)
* 探索 [GitHub 上的沈浸式閱讀程式用戶端程式庫](https://github.com/microsoft/immersive-reader-sdk)
