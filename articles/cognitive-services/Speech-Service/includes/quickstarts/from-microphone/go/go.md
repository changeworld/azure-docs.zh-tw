---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 99540e8518aa31a2221844aa954ff665609d8217
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374781"
---
## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md)
> * 確定您可以存取麥克風以擷取音訊

## <a name="setup-your-environment"></a>設定您的環境

藉由新增以下這一行，以最新的 SDK 版本更新 go.mod 檔案
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手
1. 將來源檔案 (例如 `sr-quickstart.go`) 的內容取代為下列內容，其中包括：

- 「主要」套件定義
- 從語音 SDK 匯入必要的模組
- 用來儲存訂用帳戶資訊的變數，稍後將在本快速入門中加以取代
- 使用音訊輸入麥克風的簡單實作
- 在語音辨識期間發生的各種事件適用的事件處理常式

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

以語音資源中的實際值取代 `YOUR_SUBSCRIPTION_KEY` 和 `YOUR_SUBSCRIPTIONKEY_REGION` 值。

- 瀏覽至 Azure 入口網站，然後開啟語音資源
- 在左側的 [金鑰] 底下，有兩個可用的訂用帳戶金鑰
    - 使用其中一個作為 `YOUR_SUBSCRIPTION_KEY` 值取代
- 在左側的 [概觀] 底下記下區域，並將其對應至區域識別碼
- 使用區域識別碼來取代 `YOUR_SUBSCRIPTIONKEY_REGION` 值，例如：`"westus"` 代表 [美國西部]

## <a name="code-explanation"></a>程式碼說明
需要語音訂用帳戶金鑰和區域，才能建立語音設定物件。 需要設定物件，才能具現化語音辨識器物件。

辨識器執行個體會公開多種辨識語音的方式。 在此範例中，會持續辨識語音。 此功能可讓語音服務知道您要傳送多個片語以進行辨識，以及程式何時會終止而停止辨識語音。 產生結果後，程式碼會將其寫入至主控台。

## <a name="build-and-run"></a>建置和執行
現在您已完成設定，可使用語音服務建置您的專案及測試語音辨識。
1. 建置您的專案，例如 **"go build"**
2. 執行模組，並對裝置的麥克風說出片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在輸出中。


> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。


## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
