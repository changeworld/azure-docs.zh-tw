---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097145"
---
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