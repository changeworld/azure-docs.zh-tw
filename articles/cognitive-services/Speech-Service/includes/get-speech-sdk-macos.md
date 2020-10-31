---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 963405f0874e23553a118081b080a0f30e1942eb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135865"
---
針對 macOS 進行開發時，有三種可用的語音 Sdk。

- 目標-C 語音 SDK 以原生方式提供作為 CocoaPod 套件
- .NET 語音 SDK 可以與 **Xamarin** 一起使用，因為它會實 .NET Standard 2。0
- Python 語音 SDK 以 PyPI 模組的形式提供

> [!TIP]
> 如需搭配使用目標-C 語音 SDK 與 Swift 的詳細資訊，請參閱將<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">目標 <span class="docon docon-navigate-external x-hidden-focus"></span> c 匯入至 swift </a>。

### <a name="system-requirements"></a>系統需求

- MacOS 10.13 版或更新版本

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        MacOS CocoaPod 套件可供下載，並與<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (或更新版本) <span class="docon docon-navigate-external x-hidden-focus"></span> </a>整合式開發環境 (IDE) 一起使用。 首先，請<a href="https://aka.ms/csspeech/macosbinary" target="_blank">下載二進位 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。 在相同的目錄中，將 pod 解壓縮以供其預定使用、建立 *Podfile* ，並將指定 `pod` 為 `target` 。
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.14.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac 會為 .NET 開發人員公開完整的 macOS SDK，可使用 C# 建置原生 Mac 應用程式。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS 語音 SDK 快速入門目標-C 原始程式碼 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS 語音 SDK 快速入門的原始程式碼 <span class="docon docon-navigate-external x-hidden-focus"></span></a>