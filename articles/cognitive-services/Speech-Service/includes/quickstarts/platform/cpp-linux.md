---
title: 快速入門：適用於 C++ (Linux) 的語音 SDK 平台設定 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南，以使用語音服務 SDK 在 Linux 上針對 C++ 設定您的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383801"
---
本指南說明如何安裝適用於 Linux 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系統需求

Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8 和 CentOS 8)

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您將需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* 支援的 Linux 平台會要求安裝特定程式庫 (`libssl` 以取得安全通訊端層支援，`libasound2` 以取得音效支援)。 請參閱以下的發佈，以取得安裝這些程式庫正確版本所需的命令。

   * 在 Ubuntu 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * 在 Debian 9 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * 在 RHEL/CentOS 8 上：

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

## <a name="install-speech-sdk"></a>安裝語音 SDK

適用於 Linux 的語音 SDK 可用來建置 64 位元和 32 位元應用程式。 您可從 https://aka.ms/csspeech/linuxbinary 以 tar 檔案格式下載必要的程式庫和標頭檔。

下載並安裝 SDK，如下所示：

1. 選擇語音 SDK 檔案應解壓縮至的目錄，並將 `SPEECHSDK_ROOT` 環境變數設定為指向該目錄。 此變數方便在未來命令中參考此目錄。 例如，如果您想要在主目錄中使用目錄 `speechsdk`，請使用類似下列的命令：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 在該目錄不存在的情況下建立它。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下載並解壓縮包含語音 SDK 二進位檔的 `.tar.gz` 封存：

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 驗證已壓縮套件之最上層目錄的內容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   目錄清單應包含第三方聲明和授權檔案，以及包含標頭 (`.h`) 檔案的 `include` 目錄和包含程式庫的 `lib` 目錄。

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

您現在可以繼續進行下列[後續步驟](#next-steps)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]
