---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 33cc9b52c4b687ca8147867b99dc70e8d71a9223
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944357"
---
:::row:::
    :::column span="3":::
        語音 SDK 支援 Windows 10 和 Windows Server 2016 或更新版本。 **未**正式支援舊版。 您可以使用部分的語音 SDK 搭配舊版 Windows，但不建議這麼做。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>系統需求

Windows 上的語音 SDK 需要系統上的 Microsoft Visual C++ 可轉散發套件<a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Visual Studio 2019 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">針對 x86 安裝 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">針對 x64 安裝 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">針對 ARMx64 安裝 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

對於麥克風輸入，必須安裝媒體基礎程式庫。 這些程式庫是 Windows 10 和 Windows Server 2016 的一部分。 沒有這些程式庫也可能使用語音 SDK，只要麥克風並未作為音訊輸入裝置即可。

在與您的應用程式相同的目錄中，可以部署必要的語音 SDK 檔案。 如此一來，您的應用程式就可以直接存取程式庫。 請務必選取符合您應用程式的正確版本 (x86/x64) 。

| 名稱                                            | 函式                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | 核心 SDK (原生和受控部署所需) |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 受控部署所需                      |

> [!NOTE]
> 從版本1.3.0 開始， `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 舊版 (隨附的檔案) 不再需要。 這項功能現已整合到核心 SDK 中。

> [!IMPORTANT]
> 針對 Windows Forms 應用程式 ( .NET Framework) c # 專案，請確定程式庫包含在專案的部署設定中。 您可以在下方檢查 `Properties -> Publish Section` 。 按一下該 `Application Files` 按鈕，然後從下拉式清單中尋找對應的程式庫。 請確定值設定為 `Included` 。 當發行/部署專案時，Visual Studio 會包含檔案。

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
