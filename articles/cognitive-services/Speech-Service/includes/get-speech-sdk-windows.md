---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399925"
---
:::row:::
    :::column span="3":::
        語音 SDK 支援 Windows 10 和 Windows Server 2016 或更新版本。 舊版**不**受正式支援。 您可以使用部分的語音 SDK 搭配舊版 Windows，但不建議這麼做。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>系統需求

Windows 上的語音 SDK 需要系統上<a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Visual Studio 2019 <span class="docon docon-navigate-external x-hidden-focus"></span>的 Microsoft Visual C++</a>可轉散發套件。

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">安裝 x86 的<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">針對 x64 安裝<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">針對 ARMx64 安裝<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

對於麥克風輸入，必須安裝媒體基礎程式庫。 這些程式庫是 Windows 10 和 Windows Server 2016 的一部分。 沒有這些程式庫也可能使用語音 SDK，只要麥克風並未作為音訊輸入裝置即可。

在與您的應用程式相同的目錄中，可以部署必要的語音 SDK 檔案。 如此一來，您的應用程式就可以直接存取程式庫。 請確定您選取的是符合您應用程式的正確版本（x86/x64）。

| Name                                            | 函式                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | 核心 SDK (原生和受控部署所需) |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 受控部署所需                      |

> [!NOTE]
> 從 release 1.3.0 開始，不再需要`Microsoft.CognitiveServices.Speech.csharp.bindings.dll`檔案（在先前的版本中隨附）。 此功能現在已整合到 core SDK 中。

> [!IMPORTANT]
> 針對 Windows Forms 應用程式（.NET Framework） c # 專案，請確定程式庫已包含在專案的部署設定中。 您可以在`Properties -> Publish Section`底下檢查此選項。 按一下 [ `Application Files` ] 按鈕，並從 [向下] 清單尋找對應的程式庫。 請確定此值設定為`Included`。 當發行/部署專案時，Visual Studio 將會包含該檔案。

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
