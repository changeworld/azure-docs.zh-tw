---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399886"
---
在為 macOS 開發時,有三個語音 SDK 可用。

- 目標 C 語音 SDK 為 CocoaPod 套件提供於本機上提供
- .NET 語音 SDK 可與**Xamarin.Mac**一起使用,因為它實現了 .NET 標準 2.0
- Python 語音 SDK 可作為 PyPI 模組提供

> [!TIP]
> 有關使用帶有 Swift 的目標-C 語音 SDK 的詳細資訊,請參閱<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">將目標<span class="docon docon-navigate-external x-hidden-focus"></span>C 匯入 Swift </a>。

### <a name="system-requirements"></a>系統需求

- macOS 版本 10.13 或更高版本

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS CocoaPod 軟體套件可供下載,可與<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1(或更高版本<span class="docon docon-navigate-external x-hidden-focus"></span>)</a>集成開發環境 (IDE) 一起下載和使用。 首先<a href="https://aka.ms/csspeech/macosbinary" target="_blank">,下載二進位可哥<span class="docon docon-navigate-external x-hidden-focus"></span>盒</a>。 在同一目錄中提取 Pod 以支援其的版本,建立*Podfile*並將`pod`列為`target`。
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[薩馬林.麥克](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac 會為 .NET 開發人員公開完整的 macOS SDK，可使用 C# 建置原生 Mac 應用程式。 有關詳細資訊,請參閱<a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS 語音 SDK 快速入門目標 C 源碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS 語音 SDK 快速入門 Swift 原始碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>