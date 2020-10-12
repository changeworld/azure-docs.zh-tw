---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81400047"
---
:::row:::
    :::column span="3":::
        針對 iOS 進行開發時，有兩個可用的語音 Sdk。 目標 C 語音 SDK 以原生方式提供作為 iOS CocoaPod 套件。 此外，.NET 語音 SDK 可以與 Xamarin 一起使用，因為它會實 .NET Standard 2.0。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 如需搭配使用目標-C 語音 SDK 與 Swift 的詳細資訊，請參閱將<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">目標 <span class="docon docon-navigate-external x-hidden-focus"></span> c 匯入至 swift </a>。

### <a name="system-requirements"></a>系統需求

- MacOS 10.3 版或更新版本
- 目標 iOS 9.3 或更新版本

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        IOS CocoaPod 套件可供下載並與<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (或更新版本 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>搭配使用，) 整合式開發環境 (IDE) 。 首先，請<a href="https://aka.ms/csspeech/iosbinary" target="_blank">下載二進位 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 在相同的目錄中，將 pod 解壓縮以供其預定使用、建立 *Podfile* ，並將指定 `pod` 為 `target` 。
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
        Xamarin.iOS 針對 .NET 開發人員公開完整的 iOS SDK。 在 Visual Studio 中使用 C# 或 F# 建置完整的原生 iOS 應用程式。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span> . iOS </a>。
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS 語音 SDK 快速入門目標-C 原始程式碼 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS 語音 SDK 快速入門的原始程式碼 <span class="docon docon-navigate-external x-hidden-focus"></span></a>