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
ms.openlocfilehash: fa8bf79c047911ca283bf60261bba0cbdd6816a7
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980110"
---
本指南說明如何安裝適用於 JavaScript 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)，以與網頁搭配使用。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>建立新的網站資料夾

建立新的空白資料夾。 如果您想要在 Web 伺服器上裝載範例，請確定 Web 伺服器可以存取該資料夾。

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>將適用於 JavaScript 的語音 SDK 解壓縮到該資料夾

下載 [.zip 套件](https://aka.ms/csspeech/jsbrowserpackage)形式的語音 SDK，並將它解壓縮到新建立的資料夾。 這會讓四個檔案開始解壓縮：
* `microsoft.cognitiveservices.speech.sdk.bundle.js` 易讀版的語音 SDK。
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` 用於針對 SDK 程式碼進行偵錯的對應檔。
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` 用於 TypeScript 的物件定義
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` 縮減版的語音 SDK。

## <a name="create-an-indexhtml-page"></a>建立 index.html 網頁

在資料夾中建立名為 `index.html` 的新檔案，並使用文字編輯器開啟此檔案。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]