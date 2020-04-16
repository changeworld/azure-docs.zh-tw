---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400047"
---
:::row:::
    :::column span="3":::
        為 iOS 開發時,有兩個語音 SDK 可用。 Objective-C 語音 SDK 作為 iOS CocoaPod 包在本機上可用。 或者,.NET 語音 SDK 可以在 Xamarin.iOS 實現 .NET 標準 2.0 時與 Xamarin.iOS 一起使用。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 有關使用帶有 Swift 的目標-C 語音 SDK 的詳細資訊,請參閱<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">將目標<span class="docon docon-navigate-external x-hidden-focus"></span>C 匯入 Swift </a>。

### <a name="system-requirements"></a>系統需求

- macOS 版本 10.3 或更高版本
- 目標 iOS 9.3 或更高版本

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        iOS CocoaPod 軟體套件可供下載,可與<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1(或更高版本<span class="docon docon-navigate-external x-hidden-focus"></span>)</a>集成開發環境 (IDE) 一起下載和使用。 首先<a href="https://aka.ms/csspeech/iosbinary" target="_blank">,下載二進位可哥<span class="docon docon-navigate-external x-hidden-focus"></span>盒</a>。 在同一目錄中提取 Pod 以支援其的版本,建立*Podfile*並將`pod`列為`target`。
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

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS 針對 .NET 開發人員公開完整的 iOS SDK。 在 Visual Studio 中使用 C# 或 F# 建置完整的原生 iOS 應用程式。 有關詳細資訊,請參閱<a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>其他資源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS 語音 SDK 快速入門目標 C 源碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS 語音 SDK 快速入門 Swift 原始碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>