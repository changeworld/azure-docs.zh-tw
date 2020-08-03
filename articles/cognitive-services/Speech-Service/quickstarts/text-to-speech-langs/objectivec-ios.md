---
title: 快速入門：使用語音 SDK 以 Objective-C 合成語音
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 iOS 上以 Objective-C 合成語音
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: fa1b62a987cb6c158c8d7f1fceb57bc47e5800eb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084835"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 iOS 上以 Objective-C 合成語音

在本文中，您將了解如何使用認知服務語音 SDK 以 Objective-C 建立 iOS 應用程式，以從文字合成語音。

## <a name="prerequisites"></a>必要條件

開始之前，以下為必要條件清單：

* 適用於語音服務的[訂用帳戶金鑰](~/articles/cognitive-services/Speech-Service/get-started.md)
* 具有 [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) \(英文\) 或更新版本的 macOS 機器
* 將目標設定為 iOS 9.3 版或更新版本

## <a name="get-the-speech-sdk-for-ios"></a>取得適用於 iOS 的語音 SDK

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

請注意，本教學課程不適用於 1.7.0 之前的 SDK 版本。

適用於 iOS 的認知服務語音 SDK 目前以 Cocoa Framework 的形式散發。
它可在 Xcode 專案中作為 [CocoaPod](https://cocoapods.org/) 使用，或是從 https://aka.ms/csspeech/iosbinary 下載並手動連結。 本指南使用 CocoaPod。

## <a name="create-an-xcode-project"></a>建立 Xcode 專案

啟動 Xcode，然後按一下 [檔案] > [新增] > [專案] 以啟動新專案。
在範本選取對話方塊中，選擇「iOS 單一檢視應用程式」範本。

在後續的對話方塊中，進行下列選取：

1. 專案選項對話方塊
    1. 輸入快速入門應用程式的名稱，例如 `helloworld`。
    1. 如果您已有 Apple 開發人員帳戶，請輸入適當的組織名稱和組織識別碼。 基於測試用途，您可以直接挑選類似於 `testorg` 的任何名稱。 若要簽署應用程式，您需要適當的佈建設定檔。 如需詳細資訊，請參閱 [Apple 開發人員網站](https://developer.apple.com/)。
    1. 確定已選擇 Objective-C 作為專案的語言。
    1. 停用測試和核心資料的所有核取方塊。
    ![專案設定](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. 選取專案目錄
    1. 選擇用來放置專案的主目錄。 這會在您的主目錄中建立 `helloworld` 目錄，其中包含 Xcode 專案檔的所有檔案。
    1. 停用為此範例專案建立 Git 存放庫的功能。

## <a name="install-the-sdk-as-a-cocoapod"></a>將 SDK 安裝為 CocoaPod

1. 依照[安裝指示](https://guides.cocoapods.org/using/getting-started.html)中的說明安裝 CocoaPod 相依性管理員。
1. 瀏覽至範例應用程式的目錄 (`helloworld`)。 在該目錄中放入名為 `Podfile` 的文字檔和下列內容：  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. 瀏覽至終端機的 `helloworld` 目錄，並執行命令 `pod install`。 這將會產生一個 `helloworld.xcworkspace` Xcode 工作區，其中包含範例應用程式和作為相依性的語音 SDK。 此工作區將用於下列作業。

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 在 XCode 中開啟 `helloworld.xcworkspace` 工作區。
1. 將自動產生的 `AppDelegate.m` 檔案內容取代為：  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. 將自動產生的 `ViewController.m` 檔案內容取代為：  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。
1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)取代 `YourServiceRegion` 字串。

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 顯示偵錯輸出 ([檢視] > [偵錯區域] > [啟動主控台])。
1. 在 [產品] > [目的地] 功能表中的清單中，選擇 iOS 模擬器或是連接到您開發電腦的 iOS 裝置，當做應用程式的目的地。
1. 建置範例程式碼，然後從功能表中選取 [產品] > [執行] 或按一下 [播放] 按鈕，在 iOS 模擬器中加以執行。

   ![模擬的 iOS 應用程式](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. 輸入一些文字並按一下應用程式中的按鈕後，您應該會聽到合成音訊的播放。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Objective-C 範例](https://aka.ms/csspeech/samples)

