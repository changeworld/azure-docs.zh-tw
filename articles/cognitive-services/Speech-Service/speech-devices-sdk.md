---
title: 語音設備 SDK - 語音服務
titleSuffix: Azure Cognitive Services
description: 開始使用語音裝置 SDK。 語音服務適用于各種設備和音訊源。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370639"
---
# <a name="what-is-the-speech-devices-sdk"></a>什麼是語音設備 SDK？

[語音服務](overview.md)適用于各種設備和音訊源。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 語音裝置 SDK 是已預先微調的程式庫，可搭配有目的建置的麥克風陣列開發套件。

語音裝置 SDK 可協助您：

- 快速測試新的語音案例。
- 更輕鬆地將雲端式語音服務整合到您的裝置。
- 為客戶建立卓越的使用者體驗。

語音裝置 SDK 會取用[語音 SDK](speech-sdk.md)， 使用我們先進的音訊處理演算法與設備的麥克風陣列發送音訊到[語音服務](overview.md)。 它通過雜訊抑制、回聲消除、波束成形和脫混，提供精確的遠場[語音辨識](speech-to-text.md)。

您還可以使用語音設備 SDK 構建具有自己[自訂關鍵字](speech-devices-sdk-create-kws.md)的環境設備。 自訂關鍵字提供提示，啟動您的品牌獨有的使用者交互。

語音設備 SDK 支援各種支援語音的方案，例如[語音助理](https://aka.ms/bots/speech/va)、磁碟機式排序系統、[對話轉錄](conversation-transcription-service.md)和智慧揚聲器。 您可以利用文字來回應使用者、以預設或[自訂語音](how-to-customize-voice-font.md)回答他們、提供搜尋結果、[翻譯](speech-translation.md)成其他語言等等。 我們期待看到您所打造的不凡成果！

## <a name="get-the-speech-devices-sdk"></a>取得語音裝置 SDK

### <a name="android"></a>Android

對於 Android，設備下載最新版本的[Android 語音設備 SDK](https://aka.ms/sdsdk-download-android)。

### <a name="windows"></a>Windows

對於 Windows，應用程式範例作為跨平臺 JAVA 應用程式提供。 下載最新版本的[JRE語音設備SDK。](https://aka.ms/sdsdk-download-JRE)
此應用程式是使用語音 SDK 套件，以及 64 位元 Windows 上的 Eclipse Java IDE (v4.8) 來建置。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

### <a name="linux"></a>Linux

對於 Linux，應用程式範例作為跨平臺 JAVA 應用程式提供。 下載最新版本的[JRE語音設備SDK。](https://aka.ms/sdsdk-download-JRE)
該應用程式使用語音 SDK 包和 64 位 Linux 上的 Eclipse JAVA IDE （v4） 構建（Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

提供了額外的二進位檔案來支援即將推出的設備[，Roobo v2 DDK，Urbetter](https://aka.ms/sdsdk-download-roobov2) [DDK](https://aka.ms/sdsdk-download-urbetter)和[GGEC揚聲器](https://aka.ms/sdsdk-download-speaker)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [選擇語音設備](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
