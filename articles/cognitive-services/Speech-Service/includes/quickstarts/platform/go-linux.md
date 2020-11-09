---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 06e4eea32aefcb400c144be98c274e3e4bb4b121
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135473"
---
本指南說明如何安裝適用於 Linux 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系統需求

Linux：請參閱[支援的 Linux 發行版本和目標架構](~/articles/cognitive-services/speech-service/speech-sdk.md)清單。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您將需要：

* gcc
* [Go 二進位檔 (1.13 或更新版本)](https://golang.org/dl/)

* 支援的 Linux 平台會要求安裝特定程式庫 (`libssl` 以取得安全通訊端層支援，`libasound2` 以取得音效支援)。 請參閱以下的發佈，以取得安裝這些程式庫正確版本所需的命令。

   * 在 Ubuntu/Debian 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     如果 libssl1.0.0 無法使用，請改為安裝 libssl1.0.x (其中 x 大於 0) 或 libssl1.1。

   * 在 RHEL/CentOS 上：

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，依照[如何設定適用於 Speech SDK 的 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的指示進行。
> - 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>設定 Go 環境

1. 由於繫結仰賴 `cgo`，因此您必須設定環境變數，讓 Go 可以找到 SDK：

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. 此外，若要執行包括 SDK 在內的應用程式，我們還需要告訴 OS 要到何處尋找程式庫：

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list-go.md)]