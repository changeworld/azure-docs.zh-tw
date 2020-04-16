---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 2e6be94a7fc83c6da8bf7a46fde6ea0f5ef7c764
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400534"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [建立 LUIS 應用程式並取得端點金鑰](../../../../quickstarts/create-luis.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

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

> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

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
   否則，會顯示可用 Python 直譯器的清單。 開啟命令選擇區 <kbd>Ctrl+Shift+P</kbd>，然後輸入 **Python:Select Interpreter**。 選擇適當的解譯器。
1. 您可以從 Visual Studio Code 內安裝語音 SDK Python 套件。 如果您選取的 Python 直譯器尚未安裝該套件，請按照以下步驟安裝。
   若要安裝語音 SDK 套件，請開啟終端機。 再次開啟命令選擇區 <kbd>Ctrl+Shift+P</kbd>，然後輸入 **Terminal:Create New Integrated Terminal** 來開啟終端機。
   在開啟的終端機中，輸入命令 `python -m pip install azure-cognitiveservices-speech` 或系統所適用的命令。
1. 若要執行範例程式碼，請在編輯器內任意地方按一下滑鼠右鍵。 選取 [在終端機中執行 Python 檔案]  。
   系統會從音訊檔案中辨識語音輸入的前 15 秒，並記錄在主控台視窗中。

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

如果您在遵循這些指示時遇到問題，請參閱更加詳盡的 [Visual Studio Code Python 教學課程](https://code.visualstudio.com/docs/python/python-tutorial) \(英文\)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]