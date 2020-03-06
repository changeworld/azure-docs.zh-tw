---
title: 關於語音 SDK-語音服務
titleSuffix: Azure Cognitive Services
description: 語音軟體開發套件 (SDK) 可讓您的應用程式以原生方式存取「語音服務」的功能，使其能夠更輕鬆地開發軟體。 本文提供關於適用於 Windows、Linux 和 Android 的 SDK 其他詳細資料。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331088"
---
# <a name="about-the-speech-sdk"></a>關於語音 SDK

「語音軟體開發套件」(SDK) 可讓您的應用程式存取「語音服務」的功能，使其能夠更輕鬆地開發支援語音的軟體。 目前，Sdk 提供對語音轉換**文字**、**文字到語音** **轉換、語音翻譯**、**意圖**辨識和**Bot Framework Direct Line 語音通道的**存取。

您可以使用語音 SDK，輕鬆地從麥克風捕獲音訊、從串流讀取，或從儲存體存取音訊檔案。 語音 SDK 支援 WAV/PCM 16 位、16 kHz/8 kHz、適用于語音辨識的單聲道音訊。 使用[語音轉換文字 REST 端點](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)或[批次轉譯服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)，可支援其他的音訊格式。

您可以在檔[輸入頁面](https://aka.ms/csspeech)上找到有關功能和支援平臺的一般總覽。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>取得 SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> 語音 SDK 支援 Windows 10 或更新版本。 **不支援**較早的 Windows 版本。

針對 Windows，我們支援下列語言：

* C# (UWP 和 .NET)、C++：您可以參考並使用語音 SDK NuGet 套件的最新版本。 此套件包括 32 位元和 64 位元的用戶端程式庫，以及受控 (.NET) 程式庫。 您可以使用[CognitiveServices](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)，在 Visual Studio 中安裝 SDK。

* Java：您可以參考並使用我們的最新版「語音 SDK」Maven 套件，此版本只支援 Windows x64。 在您的 Maven 專案中，新增 `https://csspeechstorage.blob.core.windows.net/maven/` 作為額外的存放庫，並參考 `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` 作為相依性。

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> 目前，我們只支援下列目標架構上的 Ubuntu 16.04、Ubuntu 18.04、Debian 9、Red Hat Enterprise Linux （RHEL）8和 CentOS 8：
> - x86 （Debian/Ubuntu）、x64、ARM32 （Debian/Ubuntu）和 ARM64 （Debian/Ubuntu）以進行C++開發
> - x64、ARM32 （Debian/Ubuntu）和適用于 JAVA 的 ARM64 （Debian/Ubuntu）
> - 適用于 .NET Core 和 Python 的 x64

執行下列 shell 命令，確定您已安裝必要的程式庫：

在 Ubuntu 上：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

在 Debian 9 上：

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

* C#：您可以參考並使用語音 SDK NuGet 套件的最新版本。 若要參考 SDK，請在專案中新增下列套件參考：

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java：您可以參考並使用語音 SDK Maven 套件的最新版本。 在您的 Maven 專案中，新增 `https://csspeechstorage.blob.core.windows.net/maven/` 作為額外的存放庫，並參考 `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` 作為相依性。

* C++：將 SDK 下載為 [.tar 套件](https://aka.ms/csspeech/linuxbinary)，然後將檔案解壓縮至您選擇的目錄。 下表顯示 SDK 資料夾結構：

  |路徑|描述|
  |-|-|
  |`license.md`|授權|
  |`ThirdPartyNotices.md`|協力廠商通知|
  |`include`|C 和 C++ 的標頭檔|
  |`lib/x64`|與應用程式連結的原生 x64 程式庫|
  |`lib/x86`|與應用程式連結的原生 x86 程式庫|

  若要建立應用程式，請將所需的二進位檔 (和程式庫) 複製或移至您的開發環境。 視需要將它們包含在您的建置程序中。

# <a name="android"></a>[Android](#tab/android)

適用於 Android 的 Java SDK 會封裝成 [AAR (Android 程式庫)](https://developer.android.com/studio/projects/android-library) \(英文\)，其中包含必要的程式庫及所需的 Android 權限。 它會以 `https://csspeechstorage.blob.core.windows.net/maven/` 套件的形式裝載在位於 `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` 的 Maven 存放庫中。

若要從 Android Studio 專案取用套件，請進行下列變更：

* 在專案層級的 build.gradle 檔案中，將下列內容新增至 `repository` 區段：

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* 在模組層級的 build.gradle 檔案中，將下列內容新增至 `dependencies` 區段：

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

Java SDK 也是[語音裝置 SDK](speech-devices-sdk.md) 的一部分。

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) (英文)
