---
title: 使用語音 SDK 開發應用程式-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何在支援的平臺上部署使用語音 SDK 的應用程式。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330799"
---
# <a name="ship-an-application"></a>交付應用程式

在散發 Azure 認知服務語音 SDK 時，請查看[語音 SDK 授權](https://aka.ms/csspeech/license201809)及[第三方軟體聲明](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)。 此外，請檢閱 [Microsoft 隱私權聲明](https://aka.ms/csspeech/privacy)。

視平台而定，有不同的相依性存在以執行您的應用程式。

## <a name="windows"></a>Windows

測試認知語音 SDK 會在 Windows 10 和 Windows Server 2016 上進行測試。

認知服務語音 SDK 需要系統上的[Microsoft C++ Visual 可轉散發套件，才能 Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) 。 您可以在此下載最新版 `Microsoft Visual C++ Redistributable for Visual Studio 2019` 的安裝程式：

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

如果您的應用程式使用受控程式碼，則目標電腦上需要 `.NET Framework 4.6.1` 或更新版本。

對於麥克風輸入，必須安裝媒體基礎程式庫。 這些程式庫是 Windows 10 和 Windows Server 2016 的一部分。 沒有這些程式庫也可能使用語音 SDK，只要麥克風並未作為音訊輸入裝置即可。

在與您的應用程式相同的目錄中，可以部署必要的語音 SDK 檔案。 如此一來，您的應用程式就可以直接存取程式庫。 確定您選取與您的應用程式相符的正確版本 (Win32/x64)。

| 名稱 | 函式 |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | 核心 SDK (原生和受控部署所需) |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 受控部署所需                      |

> [!NOTE]
> 從 release 1.3.0 開始，不再需要檔案 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` （在先前的版本中隨附）。 此功能現在已整合到 core SDK 中。

> [!NOTE]
> 針對 Windows Forms 應用程式（.NET Framework） C#專案，請確定程式庫已包含在專案的部署設定中。 您可以在 [`Properties -> Publish Section`] 底下進行檢查。 按一下 [`Application Files`] 按鈕，並從 [向下] 清單尋找對應的程式庫。 請確定 [值] 設定為 [`Included`]。 當發行/部署專案時，Visual Studio 將會包含該檔案。

## <a name="linux"></a>Linux

語音 SDK 目前支援 Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8 發行版本。
對於原生應用程式，您需要提供語音 SDK 程式庫 `libMicrosoft.CognitiveServices.Speech.core.so`。
確定您選取與您的應用程式相符的版本 (x86、x64)。 視 Linux 版本而定，您可能也需要包含下列相依性：

- GNU C 程式庫的共用程式庫 (包括 POSIX 執行緒程式設計程式庫 `libpthreads`)
- OpenSSL 程式庫（`libssl.so.1.0.0` 或 `libssl.so.1.0.2`）
- ALSA 應用程式的共用程式庫 (`libasound.so.2`)

在 Ubuntu 上，預設應該已安裝 GNU C 程式庫。 使用下列命令可以安裝最後三項：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

在 Debian 9 上，安裝下列套件：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

在 RHEL/CentOS 8 上：

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> 在 RHEL/CentOS 8 上，請遵循[如何設定 OpenSSL For Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)的指示。

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) (英文)
