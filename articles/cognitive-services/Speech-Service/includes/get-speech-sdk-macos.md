---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: d9c6062e7106f764058d6c96c6f730d235b2ef0c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673028"
---
針對 macOS 進行開發時，有三種語音 Sdk 可供使用。

- CocoaPod 套件是以原生方式提供的目標 C 語音 SDK
- .NET 語音 SDK 可與**Xamarin**搭配使用，因為它會執行 .NET Standard 2。0
- Python 語音 SDK 以 PyPI 模組的形式提供

> [!TIP]
> 如需搭配使用目標-C 語音 SDK 與 Swift 的詳細資訊，請參閱將<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">客觀 <span class="docon docon-navigate-external x-hidden-focus"></span> -c 匯入 swift </a>。

### <a name="system-requirements"></a>系統需求

- MacOS 10.13 或更新版本

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        MacOS CocoaPod 套件可供下載及搭配<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 （或更新版本） <span class="docon docon-navigate-external x-hidden-focus"></span> </a>的整合式開發環境（IDE）使用。 首先，<a href="https://aka.ms/csspeech/macosbinary" target="_blank">下載二進位 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 在相同的目錄中，將 pod 解壓縮以供其用途使用、建立*Podfile* ，並將清單 `pod` 列為 `target` 。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.12.0'
end
```

# <a name="xamarinmac"></a>[Xamarin. Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac 會為 .NET 開發人員公開完整的 macOS SDK，可使用 C# 建置原生 Mac 應用程式。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span> . Mac </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>其他資源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS 語音 SDK 快速入門目標-C 原始程式碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS 語音 SDK 快速入門 Swift 原始程式碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>