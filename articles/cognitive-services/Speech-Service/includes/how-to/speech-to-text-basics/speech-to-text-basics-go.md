---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: trbye
ms.openlocfilehash: f87db54b43f0ece49b940521bea34737da676a31
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912375"
---
語音服務的核心功能之一，就是能夠辨識並轉譯人類語音 (通常稱為語音轉文字)。 在本快速入門中，您將了解如何在您的應用程式和產品中使用語音 SDK，以執行高品質的語音轉換文字辨識。

## <a name="skip-to-samples-on-github"></a>跳至 GitHub 上的範例

如果要直接跳到範例程式碼，請參閱 GitHub 上的 [Go 快速入門範例](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer)。

## <a name="prerequisites"></a>必要條件

本文假設您具有 Azure 帳戶和語音服務訂用帳戶。 如果您沒有該帳戶和訂用帳戶，請[免費試用語音服務](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

您必須先安裝[適用於 Go 的語音 SDK](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser)，才能執行動作。

## <a name="speech-to-text-from-microphone"></a>從麥克風進行語音轉換文字

使用下列程式碼範例，從預設裝置麥克風執行語音辨識。 以您的訂用帳戶和區域金鑰取代 `subscription` 和 `region` 變數。 執行指令碼將會在您的預設麥克風和輸出文字啟動辨識工作階段。

```go
package main

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

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
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

如需 [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechConfig) 和 [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechRecognizer) 類別的詳細資訊，請參閱參考文件。

## <a name="speech-to-text-from-audio-file"></a>從音訊檔案進行語音轉換文字

使用下列範例，從音訊檔案執行語音辨識。 以您的訂用帳戶和區域金鑰取代 `subscription` 和 `region` 變數。 另外，以 .wav 檔的路徑取代變數 `file`。 執行指令碼會從檔案辨識語音，並輸出文字結果。

```go
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
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
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

如需 [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechConfig) 和 [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechRecognizer) 類別的詳細資訊，請參閱參考文件。