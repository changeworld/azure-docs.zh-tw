---
title: Azure 語音 CLI
titleSuffix: Azure Cognitive Services
description: 語音 CLI 是一種命令列工具，可用於在不撰寫任何程式碼的情況下使用語音服務。 語音 CLI 需要最基本的設定，而且可以輕鬆地立即開始試驗語音服務的主要功能，以查看是否能符合您的使用案例。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8f1e5f38e97a1b51a2d919deebbdc452e9daf993
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539775"
---
# <a name="what-is-the-speech-cli"></a>什麼是 Speech CLI？

語音 CLI 是一種命令列工具，可用於在不撰寫任何程式碼的情況下使用語音服務。 語音 CLI 需要最基本的設定，而且可以輕鬆地立即開始試驗語音服務的主要功能，以查看是否能符合您的使用案例。 在幾分鐘內，您可以從檔案的目錄執行簡單的測試工作流程（例如批次語音辨識），或從檔案的字串集合上執行文字轉換語音。 除了簡單的工作流程外，語音 CLI 已可供生產環境使用，並可相應增加以使用自動化或 shell 腳本來執行較大的進程 `.bat` 。

語音 SDK 中的大部分功能都可以在語音 CLI 中使用，而某些先進的功能和自訂功能則會在語音 CLI 中簡化。 請考慮下列指導方針，以決定何時要使用語音 CLI 或語音 SDK。

使用語音 CLI 的時機：
* 您想要以最基本的設定和沒有程式碼來試驗語音服務功能
* 使用語音服務的實際執行應用程式有相當簡單的需求

使用語音 SDK 的時機：
* 您想要在特定語言或平臺內整合語音服務功能 (例如，c #、Python、c + +) 
* 您有可能需要 advanced service 要求或開發自訂行為（包括回應串流）的複雜需求

## <a name="core-features"></a>核心功能

* 語音辨識-從音訊檔案或直接從麥克風轉換語音轉換文字，或轉譯錄製的對話。

* 語音合成-使用文字檔的輸入或直接從命令列輸入，將文字轉換為語音。 使用 [SSML](speech-synthesis-markup.md)設定自訂語音輸出特性，也可以使用 [標準或神經語音](speech-synthesis-markup.md#standard-neural-and-custom-voices)。

* 語音翻譯-以目的語言將來源語言轉譯成文字或音訊。

* 在 Azure 計算資源上執行-傳送語音 CLI 命令，以在 Azure 遠端計算資源上使用執行 `spx webjob` 。

## <a name="get-started"></a>開始使用

若要開始使用「語音 CLI」，請參閱 [快速入門](spx-basics.md)。 本文說明如何執行一些基本命令，也會顯示稍微更先進的命令來執行語音轉換文字和文字轉換語音的批次作業。 閱讀基本文章之後，您應該有足夠的理解語法來開始撰寫一些自訂命令，或將簡單的語音服務作業自動化。

## <a name="next-steps"></a>後續步驟

- 開始使用 [語音 CLI 快速入門](spx-basics.md)
- [設定您的資料存放區](./spx-data-store-configuration.md)
- 瞭解如何 [使用語音 CLI 執行批次作業](./spx-batch-operations.md)
