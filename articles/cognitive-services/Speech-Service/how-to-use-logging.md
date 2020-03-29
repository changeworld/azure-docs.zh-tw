---
title: 語音 SDK 日誌記錄 - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何在語音 SDK（C++、C#、Python、Objective-C、JAVA）中啟用日誌記錄。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805785"
---
# <a name="enable-logging-in-the-speech-sdk"></a>在語音 SDK 中啟用日誌記錄

登錄到檔是語音 SDK 的可選功能。 在開發日誌記錄期間，從語音 SDK 的核心元件中提供了其他資訊和診斷。 可以通過將語音設定物件`Speech_LogFilename`的屬性設置為日誌檔的位置和名稱來啟用它。 一旦從該配置創建識別器，登錄將全域啟動，之後無法禁用日誌記錄。 在正在運行的日誌記錄會話期間，無法更改日誌檔的名稱。

> [!NOTE]
> 除 JavaScript 外，所有支援的語音 SDK 程式設計語言中都有自語音 SDK 版本 1.4.0 以來的日誌記錄可用。

## <a name="sample"></a>範例

日誌檔案名稱在設定物件上指定。 以`SpeechConfig`以 示例為例，並假定您創建了一個名為`config`的實例：

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

可以從設定物件創建識別器。 這將啟用所有識別器的日誌記錄。

> [!NOTE]
> 如果從設定物件`SpeechSynthesizer`創建 ，則不會啟用日誌記錄。 如果啟用了日誌記錄，您還將從 接收 來自`SpeechSynthesizer`的診斷。

## <a name="create-a-log-file-on-different-platforms"></a>在不同的平臺上創建日誌檔

對於 Windows 或 Linux，日誌檔可以位於使用者具有寫入權限的任何路徑中。 預設情況下，對其他作業系統中檔案系統位置的寫入權限可能會受到限制或限制。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 應用程式需要將日誌檔放在應用程式資料位置之一（本地、漫遊或臨時）。 可以在本地應用程式資料夾中創建日誌檔：

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

有關 UWP 應用程式的檔存取權限的更多資訊，[請訪問此處](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)。

### <a name="android"></a>Android

您可以將日誌檔保存到內部存儲、外部存儲或緩存目錄。 在內部存儲或緩存目錄中創建的檔是應用程式專用的。 最好在外部存儲中創建日誌檔。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

上述代碼將日誌檔保存到特定于應用程式目錄根目錄的外部存儲。 使用者可以使用檔案管理員訪問該檔（通常在 中`Android/data/ApplicationName/logfile.txt`）。 卸載應用程式時，該檔將被刪除。

您還需要在清單檔中`WRITE_EXTERNAL_STORAGE`請求許可權：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

有關 Android 應用程式的資料和檔存儲的更多資訊，[請訪問此處](https://developer.android.com/guide/topics/data/data-storage.html)。

#### <a name="ios"></a>iOS

只能訪問應用程式沙箱中的目錄。 可以在文檔、庫和臨時目錄中創建檔。 文檔目錄中的檔可以提供給使用者。 以下程式碼片段顯示在應用程式文檔目錄中創建日誌檔：

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

要訪問已創建的檔，請將以下屬性添加到應用程式`Info.plist`的屬性清單中：

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

有關 iOS 檔案系統的更多[，請訪問此處](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
