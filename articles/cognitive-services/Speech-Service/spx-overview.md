---
title: Azure 語音 CLI
titleSuffix: Azure Cognitive Services
description: 語音 CLI 是一種命令列工具，可用於使用語音服務，而不需要撰寫任何程式碼。 語音 CLI 需要最少的設定，而且可以輕鬆地立即開始實驗語音服務的主要功能，以查看是否能符合您的使用案例。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067421"
---
# <a name="what-is-the-speech-cli"></a>什麼是 Speech CLI？

語音 CLI 是一種命令列工具，可用於使用語音服務，而不需要撰寫任何程式碼。 語音 CLI 需要最少的設定，而且可以輕鬆地立即開始實驗語音服務的主要功能，以查看是否能符合您的使用案例。 在幾分鐘內，您就可以從檔案目錄執行簡單的測試工作流程，例如 batch 語音辨識，或從檔案的字串集合上文字轉換語音。 除了簡單的工作流程之外，語音 CLI 也可供生產環境使用，而且可以相應增加，以使用自動化或 shell 腳本執行較大的進程 `.bat` 。

語音 SDK 中的大部分主要功能都可在語音 CLI 中取得，而某些先進的功能和自訂則會在語音 CLI 中簡化。 請考慮下列指導方針，以決定何時使用語音 CLI 或語音 SDK。

使用語音 CLI 的時機：
* 您想要使用最少的設定來試驗語音服務功能，而不需程式碼
* 使用語音服務的實際執行應用程式有相當簡單的需求

使用語音 SDK 的時機：
* 您想要整合特定語言或平臺 (中的語音服務功能，例如 c #、Python、c + +) 
* 您有複雜的需求，可能需要 advanced 服務要求，或是開發自訂行為，包括回應串流

## <a name="core-features"></a>核心功能

* 語音辨識-從音訊檔案或直接從麥克風將語音轉換成文字，或轉譯錄製的交談。

* 語音合成-使用文字檔的輸入或直接從命令列輸入，將文字轉換為語音。 使用[SSML](speech-synthesis-markup.md)設定和[標準或類神經語音](speech-synthesis-markup.md#standard-neural-and-custom-voices)來自訂語音輸出特性。

* 語音翻譯-將來源語言的音訊翻譯成目的語言的文字或音訊。

* 在 Azure 計算資源上執行-使用來傳送語音 CLI 命令，以在 Azure 遠端計算資源上執行 `spx webjob` 。

## <a name="get-started"></a>開始使用

若要開始使用語音 CLI，請參閱[基本概念文章](spx-basics.md)。 本文說明如何執行一些基本命令，也會顯示稍微更多的先進命令，用於執行語音轉換文字和文字轉換語音的批次作業。 閱讀基本文章之後，您應該已充分瞭解開始撰寫一些自訂命令或自動化簡單語音服務作業的語法。

## <a name="next-steps"></a>後續步驟

- [語音 CLI 基本概念](spx-basics.md)
- 如果您的使用案例較為複雜，請[取得語音 SDK](speech-sdk.md)
