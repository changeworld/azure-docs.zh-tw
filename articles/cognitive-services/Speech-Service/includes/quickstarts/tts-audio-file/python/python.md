---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: dapine
ms.openlocfilehash: 983a3c38c19d60a2ad890255ab2120ea58776436
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80117103"
---
## <a name="prerequisites"></a>Prerequisites

* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](~/articles/cognitive-services/Speech-Service/get-started.md)。
* [Python 3.5 到 3.8 版本](https://www.python.org/downloads/)。
* Python 語音 SDK 套件適用於下列作業系統：
    * Windows：x64 和 x86。
    * Mac：macOS X 10.12 版或更新版本。
    * Linux：x64 上的 Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8 和 CentOS 8。
* 在 Linux 上，執行下列命令以安裝必要的套件：

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel--centos-8"></a>[RHEL/CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

---

* 在 Windows 上，您需根據平台來選擇[適用於 Visual Studio 2019 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

此命令會從 [PyPI](https://pypi.org/) 安裝語音 SDK 的 Python 套件：

```Bash
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>支援及更新

語音 SDK Python 套件的更新會透過 PyPI 散發，並在[版本資訊](~/articles/cognitive-services/Speech-Service/releasenotes.md)上宣佈。
如果有新版本可用，您可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令來更新至該版本。
請檢查 `azure.cognitiveservices.speech.__version__` 變數來確認目前所安裝的版本。

如果您有問題或缺少功能，請參閱[支援和協助選項](~/articles/cognitive-services/Speech-Service/support.md)。

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>建立使用語音 SDK 的 Python 應用程式

### <a name="run-the-sample"></a>執行範例

您可以將本快速入門中的[程式碼範例](#sample-code)複製到原始程式檔 `quickstart.py`，並在您的 IDE 或主控台中執行它：

```Bash
python quickstart.py
```

或者，也可以從[語音 SDK 範例存放庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) \(英文\) 將本快速入門教學課程下載為 [Jupyter](https://jupyter.org) \(英文\) 筆記本，並以筆記本的形式執行它。

### <a name="sample-code"></a>範例程式碼

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>透過 Visual Studio Code 安裝及使用語音 SDK

1. 在您的電腦上下載並安裝 64 位元版本的 [Python](https://www.python.org/downloads/) \(英文\) (3.5 到 3.8 版本)。
1. 下載並安裝 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 開啟 Visual Studio Code，然後安裝 Python 擴充功能。 從功能表選取 [檔案]   > [喜好設定]   > [擴充功能]  。 搜尋 **Python**。

   ![安裝 Python 擴充功能](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. 建立資料夾來儲存專案。 例如，使用 Windows 檔案總管。
1. 在 Visual Studio Code 中選取 [檔案]  圖示。 然後開啟您所建立的資料夾。

   ![開啟資料夾](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. 選取 [新增檔案] 圖示，以建立新的 Python 來源檔案 `speechsdk.py`。

   ![建立檔案](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. 將 [Python 程式碼](#sample-code)複製並貼上到新建立的檔案中，然後儲存它。
1. 插入您的語音服務訂用帳戶資訊。
1. 如果已經選取，Python 直譯器會顯示在視窗底部狀態列的左側。
   否則，會顯示可用 Python 直譯器的清單。 開啟命令選擇區 (<kbd>Ctrl+Shift+P</kbd>)，然後輸入 **Python:Select Interpreter**。 選擇適當的解譯器。
1. 您可以從 Visual Studio Code 內安裝語音 SDK Python 套件。 如果您選取的 Python 直譯器尚未安裝該套件，請按照以下步驟安裝。
   若要安裝語音 SDK 套件，請開啟終端機。 再次開啟命令選擇區 (<kbd>Ctrl+Shift+P</kbd>)，然後輸入 **Terminal:Create New Integrated Terminal** 來開啟終端機。
   在開啟的終端機中，輸入命令 `python -m pip install azure-cognitiveservices-speech` 或系統所適用的命令。
1. 若要執行範例程式碼，請在編輯器內任意地方按一下滑鼠右鍵。 選取 [在終端機中執行 Python 檔案]  。
   您的文字會轉換成語音，並儲存在指定的音訊資料中。

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

如果您在遵循這些指示時遇到問題，請參閱更加詳盡的 [Visual Studio Code Python 教學課程](https://code.visualstudio.com/docs/python/python-tutorial) \(英文\)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
