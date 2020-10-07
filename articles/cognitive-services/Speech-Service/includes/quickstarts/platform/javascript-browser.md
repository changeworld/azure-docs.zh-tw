---
title: 快速入門：適用於 JavaScript 的語音 SDK (瀏覽器) 平台設定 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南來設定您的平台，以搭配使用 JavaScript (瀏覽器) 與語音服務 SDK。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: 2701b4cd17a132de07c031166bbe4cb1086227e9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324729"
---
本指南說明如何安裝適用於 JavaScript 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，以與網頁搭配使用。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>建立新的網站資料夾

建立新的空白資料夾。 如果您想要在 Web 伺服器上裝載範例，請確定 Web 伺服器可以存取該資料夾。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>將適用於 JavaScript 的語音 SDK 解壓縮到該資料夾

下載 [.zip 套件](https://aka.ms/csspeech/jsbrowserpackage)形式的語音 SDK，並將它解壓縮到新建立的資料夾。 這會讓五個檔案開始解壓縮：
* `microsoft.cognitiveservices.speech.sdk.bundle.js` 易讀版的語音 SDK。
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` 用於針對 SDK 程式碼進行偵錯的對應檔。
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` 用於 TypeScript 的物件定義
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` 縮減版的語音 SDK。
* `speech-processor.js` 程式碼，在某些瀏覽器上改善效能。

## <a name="create-an-indexhtml-page"></a>建立 index.html 網頁

在資料夾中建立名為 `index.html` 的新檔案，並使用文字編輯器開啟此檔案。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]