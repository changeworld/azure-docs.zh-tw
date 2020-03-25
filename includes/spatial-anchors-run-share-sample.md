---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882333"
---
## <a name="android"></a>[Android](#tab/Android)

JAVA Android 範例支援跨裝置共用。
從 Android Studio 的範例資料夾開啟 `SharedActivity.java` 檔案。 輸入您在上一個步驟中取得的 URL (來自您的 ASP.NET Web 應用程式 Azure 部署)，做為 `SharingAnchorsServiceUrl` 檔案中 `SharedActivity.java` 的值。 將 URL 中的 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS 範例支援跨裝置共用。
開啟範例資料夾中的檔案 `SharedDemoViewController.m`。 輸入您在上一個步驟中取得的 URL (來自您的 ASP.NET Web 應用程式 Azure 部署)，做為 `SharingAnchorsServiceUrl` 檔案中 `SharedActivity.java` 的值。 將 URL 中的 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android 和 iOS 範例都支援跨裝置共用。
開啟範例資料夾中的檔案 `AccountDetails.cs`。 輸入您在上一個步驟中取得的 URL (來自您的 ASP.NET Web 應用程式 Azure 部署)，做為 `AnchorSharingServiceUrl` 檔案中 `SharedActivity.java` 的值。 將 URL 中的 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>設定 Android 裝置

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>設定 iOS 裝置

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案]  窗格中瀏覽至 `Assets/AzureSpatialAnchorsPlugin/Examples`，然後開啟 `AzureSpatialAnchorsLocalSharedDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在 [專案]  窗格中瀏覽至 `Assets\AzureSpatialAnchors.Examples\Resources`。 選取 `SpatialAnchorSamplesConfig`。 然後，在 [偵測器]  窗格中，輸入 `Sharing Anchors Service url` (來自您的 ASP.NET Web 應用程式 Azure 部署) 作為 `Base Sharing Url` 的值，並將 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

選取 [檔案]   > [儲存]  以儲存場景。

## <a name="deploy-to-your-device"></a>部署至裝置

### <a name="deploy-to-android-device"></a>部署至 Android 裝置

登入 Android 裝置並使用 USB 纜線將它連接到電腦。

藉由選取 [檔案]  **[組建設定]**  >  來開啟 [組建設定]  。

在 [建置中的場景]  底下，確定所有場景旁邊都有勾號。

確定 [匯出專案]  沒有核取記號。 選取 [建置並執行]  。 系統將會提示您儲存 `.apk` 檔案。 您可以將檔案命名為任何名稱。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 裝置

藉由選取 [檔案]  **[組建設定]**  >  來開啟 [組建設定]  。

在 [建置中的場景]  底下，確定所有場景旁邊都有勾號。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

在 Xcode 中，選取 [停止]  來停止應用程式。
