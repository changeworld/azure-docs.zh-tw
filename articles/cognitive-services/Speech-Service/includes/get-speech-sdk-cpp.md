---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656610"
---
:::row:::
    :::column span="3":::
        C++語音 SDK 在 Windows、Linux 和 macOS 上可用。 有關詳細資訊,請參閱<a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.認知服務.<span class="docon docon-navigate-external x-hidden-focus"></span>語音</a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet 套件

C++語音 SDK 可以從**包管理器**安裝`Install-Package`,並具有以下 命令。

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++二進位檔案和標頭檔

或者,可以從二進位檔安裝C++語音 SDK。 將 SDK 下載為<a href="https://aka.ms/csspeech/linuxbinary" target="_blank">.tar<span class="docon docon-navigate-external x-hidden-focus"></span>套件</a>,並在您選擇的目錄中解壓縮檔案。 此套件的內容(包括 x86 和 x64 目標架構結構的標頭檔)的結構如下:

  | Path                   | 描述                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | 授權                                              |
  | `ThirdPartyNotices.md` | 協力廠商通知                                  |
  | `include`              | C++的標頭檔                                 |
  | `lib/x64`              | 與應用程式連結的原生 x64 程式庫 |
  | `lib/x86`              | 與應用程式連結的原生 x86 程式庫 |

  若要建立應用程式，請將所需的二進位檔 (和程式庫) 複製或移至您的開發環境。 視需要將它們包含在您的建置程序中。

#### <a name="additional-resources"></a>其他資源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows、Linux 和 macOS 快速入門C++原始碼<span class="docon docon-navigate-external x-hidden-focus"></span></a>