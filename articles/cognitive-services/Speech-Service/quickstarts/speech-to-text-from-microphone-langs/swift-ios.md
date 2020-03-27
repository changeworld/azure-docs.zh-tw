---
title: 快速入門：從麥克風辨識語音，Swift - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 iOS 上以 Swift 辨識語音
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75380519"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 iOS 上以 Swift 辨識語音

另備有[語音合成](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)的快速入門。

在本文中，您將了解如何使用 Azure 認知服務語音 SDK 以 Swift 建立 iOS 應用程式，以將麥克風錄製的語音轉譯成文字。

## <a name="prerequisites"></a>Prerequisites

開始之前，您需要下列項目：

* 適用於語音服務的[訂用帳戶金鑰](~/articles/cognitive-services/Speech-Service/get-started.md)。
* 已安裝 [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) 或更新版本和 [CocoaPods](https://cocoapods.org/) 的 macOS 機器。

## <a name="get-the-speech-sdk-for-ios"></a>取得適用於 iOS 的語音 SDK

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

本教學課程不適用於 1.6.0 之前的 SDK 版本。

適用於 iOS 的認知服務語音 SDK 會以架構組合的形式散發。 其可在 Xcode 專案中作為 [CocoaPod](https://cocoapods.org/) 使用，或是從 https://aka.ms/csspeech/iosbinary 下載並手動連結。 本文使用 CocoaPod。

## <a name="create-an-xcode-project"></a>建立 Xcode 專案

啟動 Xcode，然後選取 [檔案]   > [新增]   > [專案]  以啟動新專案。
在範本選取對話方塊中，選取 [iOS 單一檢視應用程式]  範本。

在後續的對話方塊中，進行下列選取。

1. 在 [專案選項]  對話方塊中：
    1. 輸入快速入門應用程式的名稱，例如 helloworld  。
    1. 如果您已有 Apple 開發人員帳戶，請輸入適當的組織名稱和組織識別碼。 基於測試目的，請使用 testorg  之類的名稱。 若要簽署應用程式，您需要適當的佈建設定檔。 如需詳細資訊，請參閱 [ 開發人員網站](https://developer.apple.com/)。
    1. 確定已選擇 **Swift** 作為專案的語言。
    1. 停用這些核取方塊以使用分鏡腳本，並建立以文件為基礎的應用程式。 範例應用程式的簡單 UI 將會以程式設計方式建立。
    1. 清除測試和核心資料的所有核取方塊。
1. 選取專案目錄：
    1. 選擇用來放置專案的目錄。 此步驟會在您選擇的目錄中建立 helloworld 目錄，其中包含 Xcode 專案的所有檔案。
    1. 停用為此範例專案建立 Git 存放庫的功能。
1. 應用程式也必須在 `Info.plist` 檔案中宣告麥克風的使用。 選取概觀中的檔案，然後新增「隱私權 - 麥克風使用方式描述」  索引鍵，並使用「語音辨識需要麥克風」  之類的值。

    ![Info.plist 中的設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. 關閉 Xcode 專案。 您後續在設定 CocoaPod 之後將使用其不同的執行個體。

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 在 helloworld 專案內的 helloworld 目錄中放入名為 `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` 的新標頭檔。 請將下列程式碼貼到檔案中：

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. 在 [Objective-C 橋接標頭]  欄位中，將橋接標頭的相對路徑 `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` 新增至 helloworld 目標的 Swift 專案設定。

   ![標頭屬性](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. 使用下列程式碼取代自動產生的 `AppDelegate.swift` 檔案內容：

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. 使用下列程式碼取代自動產生的 `ViewController.swift` 檔案內容：

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. 在 `ViewController.swift` 中，將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。
1. 將字串 `YourServiceRegion` 取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，針對免費試用訂用帳戶使用 `westus`。

## <a name="install-the-sdk-as-a-cocoapod"></a>將 SDK 安裝為 CocoaPod

1. 依照[安裝指示](https://guides.cocoapods.org/using/getting-started.html)中的說明安裝 CocoaPod 相依性管理員。
1. 移至範例應用程式的目錄，也就是 helloworld。 在該目錄中放入名為 Podfile  的文字檔和下列內容：

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. 移至終端機中的 helloworld 目錄，並執行 `pod install` 命令。 此命令會產生一個 `helloworld.xcworkspace` Xcode 工作區，其中包含範例應用程式和作為相依性的語音 SDK。 此工作區將用於下列步驟。

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 在 XCode 中開啟 `helloworld.xcworkspace` 工作區。
1. 藉由選取 [檢視]   > [偵錯區域]   > [啟動主控台]  來顯示偵錯輸出。
1. 在 [產品]   > [目的地]  功能表中的清單中，選擇 iOS 模擬器或是連接到您開發電腦的 iOS 裝置，當做應用程式的目的地。
1. 建置範例程式碼，然後從功能表中選取 [產品]   > [執行]  ，在 iOS 模擬器中加以執行。 您也可以選取 [播放]  按鈕。
1. 選取應用程式中的 [辨識]  按鈕，並說出幾個字後，應該會在螢幕的下半部看到說出的文字。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
