---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: trbye
ms.openlocfilehash: d50d4c554f47629f6e04adf957e02f8ffcc48fe5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509401"
---
本指南說明如何安裝適用於 Python 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果您想直接開始使用套件名稱，請執行 `pip install azure-cognitiveservices-speech`。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支援的作業系統

- Python 語音 SDK 套件適用於下列作業系統：
  - Windows：x64 和 x86
  - Mac：macOS X 10.12 版或更新版本
  - Linux：請參閱[支援的 Linux 發行版本和目標架構](~/articles/cognitive-services/speech-service/speech-sdk.md)清單。

## <a name="prerequisites"></a>必要條件

- 支援的 Linux 平台會要求安裝特定程式庫 (`libssl` 以取得安全通訊端層支援，`libasound2` 以取得音效支援)。 請參閱以下的發佈，以取得安裝這些程式庫正確版本所需的命令。

  - 在 Ubuntu/Debian 上執行下列命令，以安裝必要的套件：

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    如果 libssl1.0.0 無法使用，請改為安裝 libssl1.0.x (其中 x 大於 0) 或 libssl1.1。

  - 在 RHEL/CentOS 上，執行下列命令以安裝必要的套件：

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，依照[如何設定適用於 Speech SDK 的 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的指示進行。
> - 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

- 在 Windows 上，您需根據平台來選擇[適用於 Visual Studio 2019 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 請注意，第一次安裝時，可能需要重新啟動 Windows，再繼續進行本指南。
- 最後，您將需要 [Python 3.5 到 3.8 版本](https://www.python.org/downloads/)。 若要檢查您的安裝，請開啟命令提示字元，然後輸入命令 `python --version` 並檢查結果。 如果已正確安裝，您會收到「Python 3.5.1」或類似的回應。

## <a name="install-the-speech-sdk-from-pypi"></a>從 PyPI 安裝語音 SDK

如果您要使用自己的環境或組建工具，請執行下列命令，從 [PyPI](https://pypi.org/) 安裝語音 SDK。 如果是 Visual Studio Code 的使用者，請跳至下一個子章節依指示安裝。

```sh
pip install azure-cognitiveservices-speech
```

如果您使用的是 macOS，可能需要執行下列命令，才能讓上述的 `pip` 命令運作：

```sh
python3 -m pip install --upgrade pip
```

成功使用 `pip` 安裝 `azure-cognitiveservices-speech` 之後，您可以藉由將命名空間匯入 Python 專案中，來使用語音 SDK。

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>使用 Visual Studio Code 安裝語音 SDK

1. 針對您的平台，下載並安裝最新支援的 [Python](https://www.python.org/downloads/) 3.5 到 3.8 版本。
   - Windows 使用者在安裝過程中，請務必選取 [將 Python 新增至您的路徑]。
1. 下載並安裝 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 開啟 Visual Studio Code，然後安裝 Python 擴充功能。 從功能表選取 [檔案] > [喜好設定] > [擴充功能]。 搜尋 [Python]，然後按一下 [安裝]。

   ![安裝 Python 擴充功能](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. 此外，您也可以在 Visual Studio Code，從整合式命令列安裝語音 SDK Python 套件：
   1. 開啟終端機 (從下拉式功能表中選取 [終端機] >  [新增終端機])
   1. 在開啟的終端機中，輸入命令 `python -m pip install azure-cognitiveservices-speech`

如果您不熟悉 Visual Studio Code，請參閱較廣泛的 [Visual Studio Code 文件](https://code.visualstudio.com/docs)。 如需 Visual Studio Code 和 Python 的詳細資訊，請參閱 [Visual Studio Code Python 教學課程](https://code.visualstudio.com/docs/python/python-tutorial)。

## <a name="support-and-updates"></a>支援及更新

語音 SDK Python 套件的更新會透過 PyPI 散發，並在[版本資訊](~/articles/cognitive-services/speech-service/releasenotes.md)上宣佈。
如果有新版本可用，您可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令來更新至該版本。
請檢查 `azure.cognitiveservices.speech.__version__` 變數來確認目前所安裝的版本。

如果您有問題或缺少功能，請參閱[支援和協助選項](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]