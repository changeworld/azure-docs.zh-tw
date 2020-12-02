---
title: 快速入門：適用於 C++ (macOS) 的語音 SDK 平台設定 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南來設定您的平台，以在 macOS 上搭配使用 C++ 與語音服務 SDK。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3ad8eb9564c4d8343a0763cc2f6f5061ee602b72
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188170"
---
本指南說明如何在 macOS 10.13 和更新版本上安裝適用於 C++ 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系統需求

macOS 10.13 和更新版本

## <a name="install-speech-sdk"></a>安裝語音 SDK

1. 選擇語音 SDK 檔案應解壓縮至的目錄，並將 `SPEECHSDK_ROOT` 環境變數設定為指向該目錄。 此變數方便在未來命令中參考此目錄。 例如，如果您想要在主目錄中使用目錄 `speechsdk`，請使用類似下列的命令：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 在該目錄不存在的情況下建立它。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下載並解壓縮包含語音 SDK 架構的 `.zip` 封存：

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 驗證已壓縮套件之最上層目錄的內容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   目錄清單應包含第三方聲明和授權檔案，以及 `MicrosoftCognitiveServicesSpeech.framework` 目錄。

您現在可以繼續進行下列[後續步驟](#next-steps)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]
