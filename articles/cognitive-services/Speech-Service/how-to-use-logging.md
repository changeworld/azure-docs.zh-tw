---
title: 語音 SDK 記錄-語音服務
titleSuffix: Azure Cognitive Services
description: '瞭解如何在語音 SDK （c + +、c #、Python、目標-C、JAVA）中啟用記錄。'
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-javascript
ms.openlocfilehash: 21f4494bedd824cef373a391c5635e35ec2600d0
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406872"
---
# <a name="enable-logging-in-the-speech-sdk"></a>在語音 SDK 中啟用記錄功能

[記錄至檔案] 是語音 SDK 的選用功能。 在開發記錄期間，會從語音 SDK 的核心元件提供其他資訊和診斷。 將語音設定物件上的屬性設 `Speech_LogFilename` 為記錄檔的位置和名稱，即可啟用此功能。 當辨識器是從該設定建立而來，且之後無法停用時，將會全域啟用記錄功能。 您無法在執行中的記錄會話期間變更記錄檔的名稱。

> [!NOTE]
> 從所有支援的語音 sdk 程式設計語言1.4.0 的語音 SDK 版本，都可以使用記錄功能，但 JavaScript 除外。

## <a name="sample"></a>範例

記錄檔名稱是在設定物件上指定。 採用 `SpeechConfig` 作為範例，並假設您已建立名為的實例 `config` ：

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

您可以從 config 物件建立辨識器。 這會啟用所有辨識器的記錄。

> [!NOTE]
> 如果您 `SpeechSynthesizer` 從 config 物件建立，則不會啟用記錄。 如果已啟用記錄功能，您也會從接收診斷 `SpeechSynthesizer` 。

## <a name="create-a-log-file-on-different-platforms"></a>在不同的平臺上建立記錄檔

針對 Windows 或 Linux，記錄檔可以位於使用者擁有其寫入權限的任何路徑中。 在其他作業系統中，檔案系統位置的寫入權限預設可能會受到限制或限制。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 應用程式必須將記錄檔放在其中一個應用程式資料位置（本機、漫遊或暫存）中。 您可以在本機應用程式資料夾中建立記錄檔：

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

如需有關 UWP 應用程式的檔案存取權限的詳細資訊，請參閱[這裡](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)。

### <a name="android"></a>Android

您可以將記錄檔儲存至內部儲存體、外部儲存體或快取目錄。 在內部儲存體或快取目錄中建立的檔案是應用程式的私用檔案。 最好是在外部儲存體中建立記錄檔。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

上述程式碼會將記錄檔儲存至應用程式特定目錄根目錄中的外部儲存體。 使用者可以使用檔案管理員來存取檔案（通常是在中 `Android/data/ApplicationName/logfile.txt` ）。 卸載應用程式時，將會刪除檔案。

您也需要 `WRITE_EXTERNAL_STORAGE` 在資訊清單檔案中要求許可權：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

如需 Android 應用程式的資料和檔案儲存體的詳細資訊，請參閱[這裡](https://developer.android.com/guide/topics/data/data-storage.html)。

#### <a name="ios"></a>iOS

只有應用程式沙箱內的目錄可供存取。 檔案可以在檔、文件庫和臨時目錄中建立。 檔目錄中的檔案可以讓使用者使用。 下列程式碼片段示範如何在應用程式檔目錄中建立記錄檔：

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

若要存取已建立的檔案，請將下列屬性新增至 `Info.plist` 應用程式的屬性清單：

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

如需 iOS 檔案系統的詳細資訊，請參閱[這裡](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
