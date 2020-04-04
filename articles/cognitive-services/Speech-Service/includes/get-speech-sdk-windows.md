---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: dea6a1afaa2348fc5054bee20c534936dcafe5b5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656620"
---
:::row:::
    :::column span="3":::
        語音 SDK 支援 Windows 10 和 Windows 伺服器 2016 或更高版本。 早期版本**不受**官方支援。 可以使用語音 SDK 的某些部分與早期版本的 Windows,儘管不建議這樣做。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>系統需求

Windows 上的語音 SDK 要求系統上<a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">的 Microsoft 可視化 C++可再<span class="docon docon-navigate-external x-hidden-focus"></span>分發 2019。</a>

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">安裝到 x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">為 x64 安裝<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">安裝 ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

對於麥克風輸入，必須安裝媒體基礎程式庫。 這些程式庫是 Windows 10 和 Windows Server 2016 的一部分。 沒有這些程式庫也可能使用語音 SDK，只要麥克風並未作為音訊輸入裝置即可。

在與您的應用程式相同的目錄中，可以部署必要的語音 SDK 檔案。 如此一來，您的應用程式就可以直接存取程式庫。 請確保選擇與您的應用程式匹配的正確版本 (x86/x64)。

| 名稱                                            | 函式                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | 核心 SDK (原生和受控部署所需) |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 受控部署所需                      |

> [!NOTE]
> 從版本 1.3.0`Microsoft.CognitiveServices.Speech.csharp.bindings.dll`開始, 不再需要檔(在以前的版本中提供)。 該功能現已集成到核心 SDK 中。

> [!IMPORTANT]
> 對於 Windows 窗體應用 (.NET Framework) C# 專案,請確保庫包含在專案的部署設置中。 您可以在`Properties -> Publish Section`下 檢查此。 按下這個`Application Files`按鈕並從向下滾動清單中尋找相應的庫。 確保該值設定為`Included`。 可視化工作室將在項目發佈/部署時包含該檔。

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
